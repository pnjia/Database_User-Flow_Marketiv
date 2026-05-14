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