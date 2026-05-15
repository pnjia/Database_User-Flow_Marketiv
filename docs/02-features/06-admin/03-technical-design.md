# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Modul Admin & Pelaporan

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Admin Home** | `/admin` | Ringkasan metrik performa aplikasi. |
| **Validasi Submissions** | `/admin/submissions` | Mengatur daftar pengajuan tayang video. |
| **Sengketa** | `/admin/disputes` | Menangani problem transaksi bermasalah. |
| **Laporan** | `/admin/reports` | Area *read-only* atau jembatan ke Web Export. |

## Komponen UI Utama
- **`AlertCard` / `WarningBanner`**: Digunakan khusus untuk mewarnai peringatan keras jika terdapat status *Dispute* yang belum terpecahkan lebih dari hari tertentu.
- **`SubmissionListItem`**: Tampilan UI spesifik untuk Submissions, di mana daftar itu dibekali tombol *shortcut* "Buka Tautan Eksternal".
- **`ModalResolveDispute`**: Botom sheet pop-up yang menyodorkan tombol "Refund ke UMKM" atau "Cairkan ke Kreator" saat admin membuka sengketa.

## State Management
- `AdminController` mengendalikan data besar dari *Function* Dasbor.

## Validasi Form
- Di panel mobile tidak ada isian form teks panjang; semua bersifat eksekusi *button trigger*.

## Interaksi Pengguna
- Admin melakukan *scroll* panjang untuk melihat submission di `/admin/submissions`.
- Mengeklik tautan URL pekerjaan, aplikasi akan melempar admin secara *pop-up external browser*.
- Melakukan verifikasi *Approve/Reject*, daftar secara reaktif akan menghilang atau merubah warna indikatornya.
## Backend

# Backend: Modul Admin & Pelaporan

## Integrasi Appwrite
Sebagian besar fitur panel pelaporan mem-*bypass* metode baca/tulis langsung ke koleksi, dan menggunakan Appwrite Functions untuk menjalankan operasi yang memiliki hak akses mutlak (skrip dibungkus Appwrite Server API Key).

## Service yang Digunakan
- **Appwrite Functions**: Menjadi tumpuan agregasi lintas koleksi yang terlalu membebani Flutter (contoh: Menjumlah total uang di `transactions`).
- **Appwrite Databases**: Menggunakan *query update* bagi status sengketa maupun submission secara spesifik jika skrip sederhana diizinkan.

## Permission
- Semua eksekusi `UPDATE` admin yang menembus *Document-Level Security* koleksi pengguna lain mutlak dilakukan lewat penugasan *Serverless Function*.
- Aplikasi mobile dilarang memberikan hak modifikasi dokumen silang antar pengguna.

## Process Flow (Logika Backend)
1. **Flow Tarik Statistik Dashboard**:
   - Flutter UI melakukan HTTP GET memicu eksekusi `admin-stats-fn`.
   - Function mengkompilasi GMV (Total nominal transaksi `Deposit/Pencairan` dengan status Selesai), menghitung panjang antrean *Pending Submissions*, dan mengirimkan kembali JSON agregatnya.
2. **Flow Eksekusi Sengketa (Dispute)**:
   - Admin mengeklik tombol *Refund* UMKM pada sebuah *Order*.
   - Flutter mengirim ID tersebut ke fungsi `refund-escrow-fn`.
   - Script Server API memeriksa saldo tertahan, mengembalikan data uang ke koleksi `users` (Atribut UMKM terkait), dan melempar mutasi pencatatan baru `Refund` ke tabel `transactions`. Status order di `rate_card_orders` digembok ke *Dibatalkan/Selesai*.
## AI Prompt

# Kumpulan Prompt Reusable: Admin Mode

Gunakan instruksi ini untuk mengeksploitasi potensi optimal fitur pelaporan dan penanganan Admin.

## 1. Prompt Membangun Layar Validasi Submission (Frontend)
> "Tolong buatkan halaman `AdminSubmissionsPage` di Flutter GetX. Ambil data dengan menjalankan operasi HTTP GET (Execution Function API) menuju layanan yang akan memberikan daftar *Submissions*. 
> 
> Komponen wajib:
> 1) Tombol 'Buka Bukti' yang akan memakai plugin `url_launcher` untuk membuka peramban OS pengguna di luar aplikasi.
> 2) Tombol centang *Valid* yang menginisiasi *call update* ke `validate-submission-fn`. 
> Tampilkan pop-up dialog kepastian sebelum Admin benar-benar menekan tombol 'Tandai Sebagai Valid' agar uang tidak sembarangan meleset terkirim."

## 2. Prompt Fungsi Admin Dashboard (Logic/Backend Function)
> "Instruksi untuk membuat skrip server *Node.js* (*Dart*) untuk `admin-stats-fn`:
> Tarik dan gunakan *Appwrite Server API Key* untuk mengeksekusi iterasi hitung (`count`) total dokumen di koleksi `users`. Selanjutnya, hitung total sum dari nominal koleksi `transactions` yang masuk dalam kategori status 'Selesai'. Kirim balasan format HTTP tersebut berupa objek JSON agregasi murni yang telah dihitungkan. Tangkap *error* bila durasi eksekusi memakan lebih dari 10 detik dan kembalikan *fallback JSON* angka 0."

## 3. Prompt Refactoring Dispute Modal
> "Di file `AdminDisputesPage`, ketika baris sengketa di-tap, antarmuka bawah layar (*bottom sheet*) akan terbuka. Tambahkan opsi label merah 'Blacklist Akun'. Saat label ini dieksekusi, perintahkan Appwrite SDK Admin API Key untuk mengambil `$id` User tersebut dan mensabotase akunnya (mematikan status terverifikasinya atau memblokir sesi log masuk dengan merubah flag *Banned* di koleksi)."