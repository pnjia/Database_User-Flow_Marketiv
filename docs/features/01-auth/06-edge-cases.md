# Edge Cases: Fitur Autentikasi

Dokumen ini mencatat berbagai skenario pengecualian (Edge Cases) dan bagaimana antarmuka (frontend) serta *logic* harus meresponsnya.

## 1. Kredensial Tidak Valid (Invalid Credentials)
- **Skenario**: Pengguna menekan tombol "Masuk" dengan kombinasi email dan password yang salah, atau pengguna tidak ada di database Appwrite.
- **Respons Sistem**: Appwrite melempar `AppwriteException` dengan kode `401 Unauthorized`.
- **Tindakan UI**: `AuthController` menangkap *exception*, menghentikan *loading*, dan memunculkan *Snackbar* atau pesan peringatan: "Email atau kata sandi tidak sesuai." (Tidak spesifik memberitahu mana yang salah untuk keamanan).

## 2. Email Sudah Terdaftar (Duplicate Email)
- **Skenario**: Saat pendaftaran, pengguna memasukkan email yang sudah digunakan oleh akun lain.
- **Respons Sistem**: Appwrite melempar `AppwriteException` kode `409 Conflict`.
- **Tindakan UI**: Batalkan proses registrasi, hilangkan *loading*, tampilkan pesan: "Email ini sudah terdaftar. Silakan gunakan email lain atau masuk dengan akun tersebut."

## 3. Sesi Kedaluwarsa (Session Expired / Invalidated)
- **Skenario**: Pengguna membiarkan aplikasi tanpa aktivitas hingga token kedaluwarsa, atau pengguna menghapus sesi dari perangkat lain (jika didukung).
- **Respons Sistem**: Panggilan ke Appwrite API yang membutuhkan autentikasi akan melempar kode `401 Unauthorized`.
- **Tindakan UI**: Jika ini terjadi di Splash Screen (`Account.get()` gagal), navigasi diarahkan ke halaman `/login`. Jika terjadi di tengah penggunaan aplikasi, tampilkan peringatan "Sesi telah habis, silakan login kembali" dan tendang (redirect) pengguna ke halaman Login.

## 4. Koneksi Jaringan Gagal (Network Error)
- **Skenario**: Pengguna mencoba login/register ketika internet terputus atau *endpoint* Appwrite tidak dapat dijangkau (timeout).
- **Respons Sistem**: Muncul *exception* terkait `SocketException` atau HTTP Timeout.
- **Tindakan UI**: Hentikan *loading*. Tampilkan pesan: "Gagal terhubung ke server. Periksa koneksi internet Anda dan coba lagi."

## 5. Input Data Tidak Lengkap atau Format Salah (Validation Error)
- **Skenario**: Pengguna menekan "Daftar" namun kolom *password* hanya 3 karakter, atau email tanpa format `@`.
- **Tindakan UI**: Cegah pemanggilan API ke server (*Client-side validation*). Tampilkan teks peringatan merah (error label) di bawah `AuthTextField` yang bermasalah. Contoh: "Kata sandi minimal 8 karakter".

## 6. Kegagalan Parsial saat Registrasi
- **Skenario**: `Account.create()` sukses (akun Auth tercipta), tetapi koneksi putus tepat sebelum `Databases.createDocument()` mengeksekusi penyimpanan profil ke koleksi `users`.
- **Dampak**: Akun ada di Auth Appwrite, namun aplikasi akan *crash* atau *stuck* karena kehilangan data `role` di *collection*.
- **Tindakan Ideal (*Asumsi/Saran*)**: Jika insert dokumen profil gagal, sistem harus memiliki mekanisme pembersihan (menghapus akun auth yang terlanjur dibuat) atau mengarahkan pengguna ke halaman "Lengkapi Profil" jika mereka mencoba login dengan akun tersebut di kemudian hari.

## 7. Pengguna Belum Verifikasi Email (*Asumsi*)
- **Skenario**: Fitur mewajibkan verifikasi email, pengguna login tanpa memverifikasinya.
- **Respons Sistem**: Atribut `is_verified` di dokumen `users` bernilai `false`.
- **Tindakan UI**: Mengizinkan pengguna masuk, tetapi menampilkan peringatan (Warning Banner) persisten di atas dasbor beranda: "Email Anda belum diverifikasi. Harap cek kotak masuk Anda."
