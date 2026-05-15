# Alur Pengguna (User Flow): Autentikasi

Berikut adalah langkah-langkah berurutan dari skenario autentikasi pengguna di Marketiv.

## 1. Alur Pengecekan Sesi (Splash Screen)
1. Pengguna membuka aplikasi.
2. Tampil Splash Screen.
3. Controller memanggil `AppwriteService.account.get()`.
4. **Jika sukses (ada sesi)**: Sistem membaca data `role` dari `GetStorage` atau dari koleksi `users`.
   - Mengarahkan pengguna langsung ke Dasbor Home sesuai dengan perannya.
5. **Jika gagal / error 401**: Sistem menganggap tidak ada sesi aktif.
   - Mengarahkan pengguna ke halaman Onboarding (jika instalasi baru) atau halaman Login.

## 2. Alur Login (Masuk Akun)
1. Pengguna berada di halaman `/login`.
2. Pengguna memasukkan alamat Email dan Kata Sandi.
3. Pengguna menekan tombol "Masuk".
4. Tampilan memunculkan indikator *loading*.
5. Sistem memanggil fungsi pembuatan sesi `createEmailPasswordSession()`.
6. **Jika kredensial benar**:
   - SDK menyimpan token sesi.
   - Sistem mengambil dokumen profil dari `users` untuk menentukan `role`.
   - Menghilangkan *loading*, lalu navigasi ke beranda peran terkait.
7. **Jika kredensial salah**:
   - Menghilangkan *loading*.
   - Menampilkan *Snackbar* berwarna merah dengan teks peringatan (misal: "Email atau sandi salah").

## 3. Alur Register (Pendaftaran Akun Baru)
1. Dari halaman Login, pengguna menekan tombol "Daftar di sini".
2. Tampil halaman Pemilihan Peran. Pengguna memilih kartu "UMKM" atau "Kreator".
3. Mengarahkan ke form pendaftaran. Form disesuaikan berdasarkan peran yang dipilih (contoh: memunculkan *input* Nama Usaha jika memilih UMKM).
4. Pengguna mengisi Nama, Email, WhatsApp, Kata Sandi, dan identitas usaha/publik.
5. Pengguna menekan tombol "Daftar".
6. Sistem memanggil `Account.create()` di Appwrite.
7. Setelah pembuatan akun berhasil, sistem secara berantai memanggil `Databases.createDocument` untuk memasukkan detail ke koleksi `users`.
8. *(Asumsi)* Appwrite mengirimkan *email* verifikasi melalui `Account.createVerification()`.
9. Menampilkan pesan sukses dan mengarahkan pengguna kembali ke halaman Login.

## 4. Alur Logout (Keluar Akun)
1. Pengguna membuka tab "Profil".
2. Pengguna menekan tombol "Logout" (Keluar).
3. Muncul pop-up konfirmasi. Pengguna menyetujui.
4. Sistem memanggil `Account.deleteSession(sessionId: 'current')`.
5. Sistem membersihkan *cache* lokal dari `StorageService` (`GetStorage.erase()`).
6. Navigasi ulang pengguna ke halaman `/login`.

## 5. Alur Verifikasi Email (*Asumsi*)
1. Pengguna membuka aplikasi email mereka.
2. Pengguna menekan tautan verifikasi yang dikirim oleh Appwrite.
3. Tautan membuka *Deep Link* ke aplikasi atau halaman Web Appwrite yang menyelesaikan proses token.
4. Atribut `is_verified` berubah dari `false` menjadi `true`.

## 6. Alur Reset Password (*Asumsi*)
1. Dari halaman Login, pengguna menekan "Lupa Password?".
2. Pengguna memasukkan email dan menekan "Kirim Tautan Reset".
3. Aplikasi memanggil `Account.createRecovery()`.
4. Pengguna menerima email dan mengeklik tautan untuk mengganti kata sandi.


## Reference Wireframes & User Flows

# Alur Autentikasi & Sesi

1. **Aplikasi Dibuka** -> `Splash Screen`
   - *Sistem mengecek session via Appwrite SDK.*
   - **Percabangan (Login):**
     - Jika ADA sesi aktif -> Pindah ke `Home` (berdasarkan Role UMKM / Kreator / Admin).
     - Jika TIDAK ADA sesi -> Pindah ke `Onboarding` (jika baru pertama kali) atau `Login`.
2. **Pendaftaran (Register):**
   - Pengguna memilih tipe peran (UMKM atau Kreator).
   - Mengisi form dasar (nama, email, password, WA) + field khusus (nama_usaha / username_kreator).
   - Klik Daftar -> Appwrite membuat akun dan mengirim verifikasi email.
   - Sistem menyimpan dokumen ke Appwrite Database (`users`).
   - Redirect ke `Login`.
3. **Log Masuk (Login):**
   - Mengisi email dan kata sandi.
   - Autentikasi berhasil -> Token JWT tersimpan di device.
   - Pindah ke `Home` masing-masing role.