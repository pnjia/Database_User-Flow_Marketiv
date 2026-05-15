# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Modul Keuangan & Escrow

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Keuangan UMKM** | `/umkm/keuangan` | Top-up, status Escrow UMKM. |
| **Keuangan Kreator** | `/kreator/keuangan` | Info Saldo Tersedia, Withdraw. |

## Komponen UI Utama
- **`StatCard` Saldo Utama**: Menampilkan jumlah uang di Escrow (UMKM) atau Dompet Saldo (Kreator).
- **`TransactionCard`**: Representasi daftar riwayat mutasi. Berwarna hijau (masuk) atau merah (keluar).
- **`FilterTabs`**: Untuk menyaring "Semua", "Deposit", "Penarikan", atau "Escrow".
- **`PrimaryButton` Aksi**: Untuk trigger Top-up atau Tarik Dana.

## State Management
- `KeuanganController`: Melakukan pemuatan data (fetching) riwayat transaksi.

## Validasi Form
- Form Penarikan Bank (Kreator) divalidasi dengan batas nominal minimum (contoh Rp 10.000) dan mencukupi sisa `dompet_saldo`.
- Atribut form rekening (Nama Bank, Nomor, Pemilik) tidak boleh kosong.

## Interaksi Pengguna
- **Top-up (UMKM)**: Pengguna menekan bayar, sistem memanggil fungsi untuk meminta Token, UI membuka halaman WebView yang menyajikan antarmuka pembayaran Midtrans Snap.
- **Withdraw (Kreator)**: Pengisi data rekening, menekan tombol Tarik, memunculkan modal *Loading*, dan memberikan peringatan sukses/gagal.
## Backend

# Backend: Modul Keuangan & Escrow

## Integrasi Appwrite & Midtrans
Client Flutter **Dilarang Keras** melakukan modifikasi mandiri ke koleksi `transactions` atau secara manual merubah `dompet_saldo`. Seluruh operasi keuangan dibungkus di belakang server Appwrite Functions dan berinteraksi via API Midtrans.

## Service yang Digunakan
- **Appwrite Functions**: Mengeksekusi permintaan HTTP yang diamankan via variabel env (Client Key dan Server Key Midtrans).
- **Appwrite Databases**: Hanya sebatas izin membaca (READ) historis di sisi pengguna.

## Permission
- Client-side HANYA memiliki hak `READ` pada koleksi `transactions` miliknya (berdasarkan `user_id`).
- Client-side (Kreator) HANYA memiliki hak `READ` pada atribut `dompet_saldo` miliknya di koleksi `users`.
- Otoritas melakukan operasi penulisan (`CREATE`, `UPDATE`) mutlak diberikan kepada Appwrite Functions yang berlapis API Key server-side.

## Process Flow (Logika Backend)
1. **Flow Deposit UMKM**: 
   - Fungsi `midtrans-create-fn` dipanggil. Midtrans Snap URL dikembalikan ke Flutter WebView. 
   - Setelah dibayar, Midtrans mengirim POST ke `midtrans-webhook-fn`. Server Appwrite memverifikasi keaslian webhook, lalu menuliskan rekaman berstatus `Escrow` pada koleksi `transactions`.
2. **Flow Withdrawal Kreator**:
   - Fungsi `withdraw-fn` dipanggil oleh aplikasi klien, membawa data tujuan bank.
   - Skrip server memeriksa apakah sisa `dompet_saldo` mencukupi.
   - Jika cukup, saldo dikurangi (update `users`), rekaman Withdrawal ditulis di `transactions`, dan *Payout API* dari Midtrans dipicu.
## AI Prompt

# Kumpulan Prompt Reusable: Keuangan & Escrow

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur finansial.

## 1. Prompt UI Riwayat Transaksi (Frontend)
> "Tolong buatkan halaman `KeuanganUMKMPage` dan `KeuanganKreatorPage`. Menggunakan `GetX`, ambil data dari `KeuanganController`. Buatkan list ber-scroll vertikal untuk merender data `TransactionModel`. Komponennya harus menyerupai `TransactionCard` yang menampilkan ikon beda warna (hijau untuk dana masuk/Pencairan, merah untuk Deposit/Withdrawal/keluar). Jangan lupa tambahkan widget *TabBar* untuk memfilter (Semua, Deposit, Penarikan)."

## 2. Prompt Integrasi Payment Gateway via Webhook (Backend Logic)
> "Peringatan keamanan: Jangan tulis *Midtrans Server Key* di dalam proyek Flutter. Di `KeuanganController`, buatkan aksi tombol Top-Up yang mengeksekusi `AppwriteService.functions.createExecution` ke id `midtrans-create-fn`. Parameter yang dikirimkan hanya `order_id`, `amount`, dan `item_name`. Jika respons server sukses, jalankan metode `MidtransService.openSnapWebView(snapToken)` dan pantau status WebView-nya."

## 3. Prompt Refactoring Form Withdrawal (Edge Cases)
> "Coba periksa kembali method `submitWithdraw()` di form pencairan kreatif. Pastikan ada pemeriksaan bahwa nominal withdraw wajib lebih besar atau sama dengan Rp 10.000 dan tidak melebihi sisa atribut `dompetSaldo` yang diambil dari data sesi akun pengguna."