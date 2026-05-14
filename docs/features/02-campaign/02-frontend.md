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
