# Overview: Fitur Autentikasi (Auth)

## Tujuan Fitur
Fitur Autentikasi bertujuan untuk mengamankan akses ke dalam aplikasi Marketiv, memastikan identitas pengguna valid, dan mengelompokkan pengguna ke dalam peran (role) yang tepat (UMKM, Kreator, Admin) agar mereka mendapatkan akses halaman dan fungsionalitas yang sesuai.

## Deskripsi Singkat
Modul ini merupakan gerbang utama aplikasi Marketiv. Menggunakan integrasi langsung dengan **Appwrite Account API** sebagai Backend-as-a-Service (BaaS), modul ini menangani seluruh siklus hidup sesi pengguna mulai dari aplikasi pertama kali dibuka (Splash Screen), pendaftaran, proses masuk, hingga keluar dari aplikasi.

## Daftar Subfitur
- **Pengecekan Sesi Otomatis (Splash Screen)**: Memvalidasi apakah token JWT masih aktif di penyimpanan lokal saat aplikasi dibuka.
- **Registrasi Akun (Register)**: Pendaftaran pengguna baru.
- **Pemilihan Peran (Role Selection)**: Penentuan jenis akun (UMKM atau Kreator) sebelum menyelesaikan registrasi.
- **Masuk Akun (Login)**: Autentikasi menggunakan Email dan Password.
- **Keluar Akun (Logout)**: Menghapus sesi JWT dari perangkat dan server.
- **Verifikasi Email (*Asumsi*)**: Pengiriman email validasi setelah registrasi sukses untuk keamanan akun (terkait flag `is_verified`).
- **Reset Password (*Asumsi*)**: Mekanisme standar Appwrite untuk memulihkan kata sandi yang lupa.

## Dependensi dengan Fitur Lain
- **Navigasi Utama (Routing)**: Hasil dari proses login dan cek sesi menentukan rute awal (Home UMKM, Home Kreator, atau Dashboard Admin).
- **Profil Pengguna**: Setelah registrasi di Appwrite Account, sistem wajib membuat dokumen detail profil di *collection* `users`.
- **Database & Permissions**: Sesi Appwrite Auth menentukan hak akses *Document-Level Security* di seluruh *collection* database.
