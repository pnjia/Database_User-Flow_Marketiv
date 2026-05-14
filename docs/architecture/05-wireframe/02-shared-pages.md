# Halaman: Splash Screen

Route: `/splash`

Tujuan:
Mengecek sesi aktif Appwrite dan mengarahkan pengguna ke halaman yang sesuai.

Section:
- Main Container

Komponen:
## Main Container
- app logo image
- loading spinner (CircularProgressIndicator)

Data Ditampilkan:
- Indikator pemuatan

Input Pengguna:
- Tidak ada (Otomatis)

Aksi/Interaksi Pengguna:
- Tidak ada, sistem akan langsung menavigasi pengguna setelah sesi dicek.

Integrasi Appwrite:
- collection: users (untuk mengambil data Role)
- Service: Appwrite Account API

Dokumen:
- read: session JWT, document `users`

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Navigasi otomatis ke `/onboarding` (jika pengguna baru/tidak login) atau ke `/login`
- Navigasi otomatis ke Home sesuai role (UMKM/Kreator/Admin) jika sesi aktif.

---

# Halaman: Onboarding

Route: `/onboarding`

Tujuan:
Memberikan penjelasan singkat tentang aplikasi untuk pengguna baru.

Section:
- Carousel Info
- Navigation Footer

Komponen:
## Carousel Info
- illustration image
- headline text
- description text
- pagination dots

## Navigation Footer
- skip text button
- next primary button
- get started primary button

Data Ditampilkan:
- Teks edukasi/fitur aplikasi (Statis)

Input Pengguna:
- swipe layar
- klik tombol navigasi

Aksi/Interaksi Pengguna:
- Menggeser (swipe) informasi.
- Menekan tombol "Lanjut" atau "Mulai".
- Menavigasi ke halaman Register/Login.

Integrasi Appwrite:
- Tidak ada

Dokumen:
- Tidak ada

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Mengarahkan ke halaman `/login` atau `/role-selection`.

---

# Halaman: Login

Route: `/login`

Tujuan:
Autentikasi pengguna lama.

Section:
- Header
- Formulir Login
- Bantuan / Opsi Tambahan

Komponen:
## Header
- logo image
- welcome headline text

## Formulir Login
- email text input (AuthTextField)
- password text input (AuthTextField dengan toggle obscure)
- submit primary button
- error message text (conditional)

## Bantuan / Opsi Tambahan
- forgot password text button
- register text button / rich text

Data Ditampilkan:
- Pesan kesalahan (error) jika kredensial salah (dinamis).

Input Pengguna:
- email
- password
- klik masuk
- klik daftar

Aksi/Interaksi Pengguna:
- Mengisi email dan kata sandi.
- Menekan tombol "Masuk".

Integrasi Appwrite:
- collection: users
- Service: Appwrite Account API

Dokumen:
- read: document `users` (untuk mengambil `role`)
- create: Appwrite session (JWT)

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Jika sukses, akan dialihkan ke Home UMKM (`/umkm/home`), Home Kreator (`/kreator/home`), atau Home Admin (`/admin`).

---

# Halaman: Role Selection & Register

Route: `/register` dan `/role-selection`

Tujuan:
Mendaftarkan akun pengguna baru dengan pemilihan peran UMKM atau Kreator.

Section:
- Header
- Pemilihan Peran (Role)
- Formulir Registrasi
- Footer

Komponen:
## Header
- logo image
- register headline text

## Pemilihan Peran (Role)
- role selection cards (UMKM & Kreator) dengan highlight/border aktif

## Formulir Registrasi
- nama lengkap text input
- email text input
- whatsapp number text input
- password text input
- nama usaha text input (Conditional: Hanya tampil jika role UMKM)
- username kreator text input (Conditional: Hanya tampil jika role Kreator)
- submit primary button

## Footer
- login redirect rich text

Data Ditampilkan:
- Pesan kesalahan validasi form.
- Konfirmasi email verifikasi.

Input Pengguna:
- pilihan role
- nama lengkap
- email
- nomor whatsapp
- password
- nama usaha / username kreator
- klik daftar

Aksi/Interaksi Pengguna:
- Memilih kartu peran (Role).
- Mengisi formulir data diri dan bisnis/kreator.
- Menekan tombol "Daftar".

Integrasi Appwrite:
- collection: users
- Service: Appwrite Account API

Dokumen:
- create: account document
- create: document `users` baru

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Jika sukses, akan dialihkan kembali ke `/login`.

---

# Halaman: Profil & Edit Profil

Route: `/profile/edit`

Tujuan:
Memperbarui informasi biodata, identitas bisnis, dan kredensial akun.

Section:
- Avatar Editor
- Form Identitas Dasar
- Auth Management
- System Logout

Komponen:
## Avatar Editor
- profile picture image / placeholder
- change photo text button

## Form Identitas Dasar
- nama lengkap text input
- nomor whatsapp text input
- nama usaha text input (Hanya UMKM)
- username kreator text input (Hanya Kreator)
- niche dropdown (Kreator)
- bio multiline text input
- save profile primary button

## System Logout
- logout danger button

Data Ditampilkan:
- Data `users` saat ini.

Input Pengguna:
- unggah foto
- edit teks form
- klik simpan
- klik logout

Aksi/Interaksi Pengguna:
- Memodifikasi representasi profil kepada pengguna lain.
- Keluar dari sesi aplikasi.

Integrasi Appwrite:
- collection: users
- Service: Storage (`profile_images`), Account API

Dokumen:
- read/update: dokumen `users`
- write: storage bucket avatar
- update/delete: session di Account API

Autentikasi:
- Wajib Login

Catatan Dependency:
- Merupakan navigasi menu paling ujung di Bottom Navigation Bar semua role.