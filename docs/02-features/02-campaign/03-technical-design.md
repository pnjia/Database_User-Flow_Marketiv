# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Modul Campaign Mode

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Daftar Campaign** | `/umkm/campaign` | Menampilkan riwayat campaign UMKM. |
| **Buat Campaign** | `/umkm/campaign/create` | Wizard 4 langkah pembuatan campaign. |
| **Detail Campaign** | `/umkm/campaign/:id` | Status campaign dan daftar submission kreator. |
| **Job Pool** | `/kreator/job-pool` | Bursa kerja bagi kreator. |
| **Detail Job Pool** | `/kreator/job-pool/:id` | Melihat brief sebelum klaim. |
| **Pekerjaan Aktif** | `/kreator/pekerjaan-aktif` | Pekerjaan berjalan kreator. |

## Komponen UI Utama
- **`Progress Stepper`**: Indikator langkah pada wizard.
- **`Card` & `ListView`**: Menampilkan Job Pool dan Daftar Campaign.
- **`WarningBanner`**: Menampilkan peringatan (seperti ukuran maksimal aset 100MB).
- **`PrimaryButton` & `SecondaryButton`**: Aksi klaim, submit, atau "Bantu dengan AI".

## State Management
- `CampaignController` dan `CreateCampaignController` mengelola proses *wizard*.
- Kalkulator finansial reaktif menggunakan `GetBuilder` atau `Rx` untuk menampilkan total harga (*budget* + 15% komisi).

## Validasi
- Formulir mewajibkan semua kolom *required*.
- Aset file dibatasi maksimal 100MB sebelum diunggah; jika lebih, diarahkan menggunakan link eksternal (Google Drive/Dropbox).
- URL Bukti Tayang diwajibkan menggunakan format `https://www.tiktok.com/` atau `https://www.instagram.com/`.

## Interaksi Pengguna
- UMKM melewati 4 tahap wizard dan membayar melalui WebView Midtrans.
- Kreator menggunakan filter di Job Pool, membuka detail, dan mengklik "Klaim Job Ini". Tombol *disabled* jika kuota penuh.
- Kreator *paste* URL video dan submit.

## Backend

# Backend: Modul Campaign Mode

## Integrasi Appwrite
Fitur Campaign sepenuhnya berinteraksi dengan **Databases**, **Storage**, dan **Functions** di Appwrite. Klien tidak pernah berinteraksi langsung dengan sistem pembayaran pihak ketiga.

## Service yang Digunakan
- **Databases**: Untuk membuat, membaca, dan memperbarui dokumen `campaigns` dan `submissions`.
- **Storage**: `campaign_assets` bucket untuk menyimpan logo atau foto produk UMKM.
- **Functions**: `claim-campaign-fn`, `midtrans-create-fn`, `generate-brief-fn`.

## Permission
- **UMKM**: Diberikan hak `CREATE` dan `UPDATE` (miliknya sendiri) pada dokumen `campaigns`.
- **Kreator**: Diberikan hak `READ` pada `campaigns` yang aktif, dan `CREATE` pada `submissions`.

## Process Flow (Logika Backend)
1. **Penyimpanan Aset**: Gambar diunggah ke Storage, dikembalikan URL-nya.
2. **Pembuatan Campaign**: Disimpan ke Databases dengan status `Draft`.
3. **Pembayaran & Aktivasi**: Appwrite Function `midtrans-create-fn` dipanggil. Saat Webhook konfirmasi sukses dari Midtrans diterima (`midtrans-webhook-fn`), status campaign berubah menjadi `Aktif`.
4. **Pencegahan Race Condition**: Saat kreator klik klaim, klien tidak memodifikasi `kuota_terpakai` secara langsung. Eksekusi dilakukan via `claim-campaign-fn` yang melakukan *atomic update* di server untuk memastikan kuota tidak bocor (jebol).

## AI Prompt

# Kumpulan Prompt Reusable: Campaign Mode

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur Campaign Mode.

## 1. Prompt Generate UI Wizard (Frontend)
> "Tolong buatkan UI Flutter untuk Wizard Pembuatan Campaign di Marketiv. Buat 4 langkah menggunakan GetX untuk navigasinya (Info Produk, Upload Aset, Anggaran & Kuota, Review). Referensinya ada di `docs/02-features/02-campaign/02-frontend.md`. Pastikan form reaktif menghitung estimasi biaya secara otomatis di Langkah 3, di mana total anggaran = (Harga * Target) + 15% fee platform."

## 2. Prompt Integrasi Klaim Job (Backend/Logic)
> "Implementasikan logika klaim campaign di `JobPoolController` dan `CampaignRemoteDataSource`. Jangan gunakan `databases.createDocument` secara langsung. Gunakan `AppwriteService.functions.createExecution()` untuk memanggil function `claim-campaign-fn` dengan parameter `campaignId` dan `kreatorId`. Jika *execution* melempar JSON error, tangkap dan tampilkan ke UI lewat *Snackbar* sesuai `docs/02-features/02-campaign/06-edge-cases.md`."

## 3. Prompt Refactor / Edge Cases Form
> "Periksa kode `CreateCampaignController`. Tambahkan validasi agar input file image *picker* ditolak jika ukurannya lebih dari 100MB dan aktifkan UI *WarningBanner* untuk meminta pengguna memakai kolom URL Eksternal."
