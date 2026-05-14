# Skema Appwrite: Modul Profil

## Collection Terkait
- **`users`**: Lokasi utama pemukiman struktur profil.

## Attributes Utama (`users`)
- `nama_lengkap`, `nomor_whatsapp` (Informasi universal).
- `nama_usaha` (Khusus UMKM).
- `username_kreator`, `niche`, `bio` (Khusus KREATOR).
- `foto_profil_url` (Kaitan metadata terhadap Storage).

*(Tidak ada atribut khusus tambahan yang tidak diterangkan di dokumentasi modul Auth atau `DATABASE.md`).*

## Storage Bucket
- **`profile_images`**: Bucket khusus penampungan foto profil. 
  - Batas limit maksimal per berkas adalah 5 MB.
  - Akses baca (*Read*): Terbuka secara Publik agar avatar ini bisa dimuat di Job Pool dan pesan Chat bagi semua orang.
  - Akses tulis (*Write*): Pemilik (*Owner*).

## Functions
- Tidak membutuhkan Appwrite Functions dalam operasional modifikasi reguler `users` (dapat dilakukan *direct client query* berkat perlindungan izin profil terotentikasi).