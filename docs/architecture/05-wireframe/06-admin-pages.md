# Halaman: Admin Dashboard & Submissions

Route: `/admin`, `/admin/submissions`, `/admin/disputes`, `/admin/reports`

Tujuan:
Memonitor performa platform dan menyelesaikan masalah validasi serta sengketa.

Section:
- Ringkasan Metrik
- Tabel/List Validasi Submissions
- Daftar Sengketa (Disputes)

Komponen:
## Ringkasan Metrik (Admin Home)
- gmv stat card
- active user stat card
- dispute count alert card

## Validasi Submissions
- filter pending/valid/fraud
- submission list item (Info Kreator, URL Video, Status)
- open url icon button
- mark valid action button
- mark fraud danger action button

## Daftar Sengketa
- dispute list item (Nama UMKM, Nama Kreator, Dana Escrow)
- resolve modal / action sheet (Refund ke UMKM, atau Teruskan ke Kreator)

Data Ditampilkan:
- Agregasi data aplikasi (Total Dana, Jumlah Campaign, dll).
- Dokumen `submissions` dan `rate_card_orders` yang butuh intervensi.

Input Pengguna:
- klik tombol aksi (Valid, Fraud, Refund, dll)

Aksi/Interaksi Pengguna:
- Memastikan tidak ada kecurangan perolehan *views*.
- Mengatur aliran uang Escrow pada kasus bermasalah.

Integrasi Appwrite:
- collection: campaigns, submissions, rate_card_orders, users
- Appwrite Functions: `admin-stats-fn`, `validate-submission-fn`, `refund-escrow-fn`

Dokumen:
- read: Lintas collections
- update: Status dokumen melalui Functions.

Autentikasi:
- Wajib Login (Role: ADMIN)

Catatan Dependency:
- Aksi admin adalah keputusan final (Otoritas tertinggi di platform) yang langsung mengubah aliran dana di tabel `transactions`.