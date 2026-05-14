# Overview: Modul Profil & Edit Profil

## Tujuan Fitur
Menyediakan antarmuka manajemen akun bagi pengguna untuk mempersonalisasi halaman publik mereka dan mengelola identitas dasar serta preferensi personal di dalam platform Marketiv.

## Deskripsi Singkat
Modul ini berfungsi sebagai identitas utama UMKM (berfokus pada representasi merek/produk mereka) dan Kreator (berfokus pada portofolio dan *niche* keahliannya). Data dari modul profil merupakan sumber referensi sentral untuk fitur Direktori, Job Pool, serta Order yang mengandalkan atribusi kreator/UMKM.

## Daftar Subfitur
- **Pengaturan Avatar**: Modifikasi foto profil yang dihosting di Storage.
- **Biodata Dasar**: Pembaruan nama pribadi dan nomor kontak (WhatsApp).
- **Pengaturan Ekstra UMKM**: Modifikasi informasi representasi *Nama Usaha*.
- **Pengaturan Ekstra Kreator**: Penyesuaian `username` publik, kategori `niche`, dan catatan biografi/portofolio.
- **System Logout**: Akses keluar sesi kredensial akun.

## Dependensi dengan Fitur Lain
- **Autentikasi (Auth)**: Profil bergantung pada ID yang dicetuskan saat Account baru saja didaftarkan.
- **Direktori Kreator**: Menjadikan isi portofolio ini sebagai etalase publik yang ditelaah oleh calon UMKM penyewa (di Rate Card Mode).