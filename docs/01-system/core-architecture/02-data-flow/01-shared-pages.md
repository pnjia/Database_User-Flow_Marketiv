# Halaman Bersama (Shared / Auth / Profil)

### 1. Halaman: **Register**
- **Tujuan Halaman**: Memfasilitasi pendaftaran pengguna baru (UMKM atau Kreator).
- **Koleksi Terkait**: `users` (Database), Appwrite Account API.
- **Operasi CRUD**: `CREATE`
- **Data yang Dibuat**:
  1. Identitas login (Account Appwrite).
  2. Dokumen profil baru di koleksi `users`.
- **Input Pengguna**:
  - `nama_lengkap` (String)
  - `email` (Email)
  - `password` (String rahasia)
  - `nomor_whatsapp` (String)
  - `role` (Enum: UMKM / KREATOR)
  - `nama_usaha` (String, opsional - wajib bagi UMKM)
  - `username_kreator` (String, opsional - wajib bagi KREATOR)
- **Kebutuhan Autentikasi**: Tidak ada (Halaman Publik)
- **Keterangan Tambahan**: `dompet_saldo` dibuat *default* ke nilai `0`. Pengelolaan JWT Auth ditangani secara otomatis oleh SDK.

### 2. Halaman: **Login**
- **Tujuan Halaman**: Autentikasi pengguna lama.
- **Koleksi Terkait**: Appwrite Account API, `users`.
- **Operasi CRUD**: `CREATE` (Session), `READ` (Users).
- **Data yang Dibaca**: Dokumen `users` untuk menentukan _role_ guna navigasi halaman selanjutnya.
- **Data yang Dibuat**: JWT Session yang dikelola SDK.
- **Input Pengguna**:
  - `email` (String)
  - `password` (String)
- **Kebutuhan Autentikasi**: Tidak ada (Halaman Publik)

### 3. Halaman: **Profil & Edit Profil**
- **Tujuan Halaman**: Memperbarui informasi biodata, identitas bisnis, dan kredensial akun.
- **Koleksi Terkait**: `users`
- **Operasi CRUD**: `READ`, `UPDATE`
- **Data yang Dibaca**: Data `users` saat ini.
- **Data yang Diubah**: Dokumen `users` saat update profile.
- **Input Pengguna**: Form Identitas Dasar, Avatar Editor, System Logout.
- **Kebutuhan Autentikasi**: Ya