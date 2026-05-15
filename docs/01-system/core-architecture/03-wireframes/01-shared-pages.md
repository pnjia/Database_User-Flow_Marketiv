# Wireframes: Halaman Akses Bersama (Shared Pages)

Dokumen ini mendeskripsikan struktur UI (wireframe) untuk halaman-halaman yang digunakan sebelum pengguna masuk ke dalam sesi spesifik (pra-login) atau digunakan oleh semua tipe pengguna.

## 1. Splash Screen
**Route**: `/splash`

**Tujuan**: Mengecek sesi aktif (session check via Appwrite) dan mengarahkan pengguna ke halaman Home atau Onboarding/Login.

**Komponen Utama**:
- **Tengah Layar**: Logo Marketiv (animasi ringan opsional).
- **Bawah Layar**: Indikator Loading (spinner atau progress bar).

---

## 2. Onboarding
**Route**: `/onboarding`

**Tujuan**: Menjelaskan nilai tambah (value proposition) aplikasi kepada pengguna baru.

**Komponen Utama**:
- **Area Tengah**: Carousel / Slider gambar ilustrasi (misal: "Temukan Kreator Terbaik", "Tingkatkan Penjualan Anda", "Aman dengan Sistem Escrow").
- **Area Bawah**:
  - Indikator halaman (dots).
  - Tombol "Lewati" (Skip).
  - Tombol "Lanjut" (Next).
  - Tombol "Mulai Sekarang" (mengarah ke Role Selection / Register).

---

## 3. Role Selection (Pilih Peran)
**Route**: `/role-selection`

**Tujuan**: Memisahkan alur pendaftaran antara UMKM dan Kreator.

**Komponen Utama**:
- **Header**: Judul "Siapakah Anda?" atau "Pilih Peran Anda".
- **Area Tengah**: 
  - Card 1: "Saya UMKM" (Ikon toko/produk, deskripsi: "Saya ingin mempromosikan produk saya").
  - Card 2: "Saya Kreator" (Ikon video/kamera, deskripsi: "Saya ingin mencari pekerjaan promosi").
- **Area Bawah**: Tombol "Lanjutkan" (Aktif setelah salah satu card dipilih).

---

## 4. Register
**Route**: `/register`

**Tujuan**: Formulir pendaftaran akun baru berdasarkan role yang dipilih.

**Komponen Utama**:
- **Header**: Judul "Daftar sebagai [UMKM/Kreator]".
- **Formulir Dasar**:
  - Input: Nama Lengkap
  - Input: Email
  - Input: Kata Sandi (Password)
  - Input: Nomor WhatsApp
- **Formulir Khusus (Berdasarkan Role)**:
  - *Jika UMKM*: Input Nama Usaha.
  - *Jika Kreator*: Input Username Kreator.
- **Area Bawah**:
  - Checkbox "Saya setuju dengan Syarat & Ketentuan".
  - Tombol "Daftar".
  - Teks Tautan: "Sudah punya akun? Masuk di sini".

---

## 5. Login
**Route**: `/login`

**Tujuan**: Autentikasi pengguna lama untuk masuk ke dalam aplikasi.

**Komponen Utama**:
- **Header**: Logo Marketiv dan Judul "Selamat Datang Kembali".
- **Formulir**:
  - Input: Email
  - Input: Kata Sandi
- **Aksi Tambahan**:
  - Tautan "Lupa Kata Sandi?".
- **Area Bawah**:
  - Tombol "Masuk".
  - Teks Tautan: "Belum punya akun? Daftar di sini" (Mengarah ke Role Selection).