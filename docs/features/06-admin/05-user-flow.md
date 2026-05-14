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