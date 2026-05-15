# Alur Pengguna (User Flow): Admin & Pelaporan

## 1. Alur Memeriksa Dasbor Statistik
1. Admin mengakses aplikasi `/admin`.
2. Halaman dasbor dimuat, menampilkan indikator *loading shimmer* pada kartu metrik (GMV, Pengguna Aktif).
3. Data selesai ditarik (dari integrasi `admin-stats-fn`).
4. Admin mengevaluasi performa harian/bulanan.
5. Admin dapat mengeklik tombol tautan untuk diantarkan memuat laporan Excel di web.

## 2. Alur Memvalidasi Bukti Kerja (Submissions)
1. Admin membuka halaman `Submissions`.
2. Admin mengeklik filter `Pending` untuk menyortir sisa daftar kerjaan yang belum diulas.
3. Di dalam satu baris, Admin mengeklik URL Video TikTok, membuka peramban OS, mengecek video konten.
4. Kembali ke aplikasi, Admin menekan tombol hijau *Tandai Valid*.
5. Baris (*List Item*) tersebut berubah menjadi keterangan sukses dan secara internal di sisi server, uang dari Escrow UMKM dikucurkan kepada Kreator.

## 3. Alur Penyelesaian Sengketa (Dispute)
1. Admin membuka tab `Dispute`.
2. Admin mempelajari order Rate Card Mode yang diadukan.
3. Admin memanggil pihak terkait di luar aplikasi atau menganalisis *log message* di Database (via panel web).
4. Jika diputuskan **Kreator Gagal Menepati Janji**:
   - Admin mengeklik tombol "Kembalikan Dana ke UMKM".
   - Sistem memanggil fungsi `refund-escrow-fn`.
5. Jika diputuskan **UMKM Lalai Menyelesaikan / Kreator Benar**:
   - Admin mengeklik tombol "Cairkan Paksa ke Kreator".
   - Sistem melepas status tunda secara asinkron.
6. Order terkait tertutup. Sengketa dinyatakan `Selesai`.

## Reference Wireframes & User Flows

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