# Kumpulan Prompt Reusable: Fitur Autentikasi

Dokumen ini berisi sekumpulan instruksi (*prompts*) terstruktur yang dirancang khusus untuk memandu AI (seperti GitHub Copilot, Cursor, atau sistem chat ini) dalam men-generate, mendebug, atau memodifikasi kode terkait fitur Autentikasi Marketiv.

---

## 1. Prompt Generate Frontend Auth (Login & Register UI)

Gunakan *prompt* ini saat ingin membangun halaman UI login/register dari nol atau melengkapinya.

> **Prompt:**
> "Buatkan antarmuka (UI) Flutter untuk halaman Login dan Register sesuai panduan di `docs/features/auth/frontend.md` dan design system `docs/_global/design-system/`. 
> 
> Syarat:
> 1. Gunakan GetX untuk state management (`AuthController`).
> 2. Implementasikan komponen reusable seperti `AuthTextField` dan `PrimaryButton`.
> 3. Terapkan validasi form sisi klien (email regex, password > 8 char).
> 4. Pastikan layout Mobile-First (responsif, dibungkus SafeArea, spacing sesuai `AppSpacing`).
> 5. Halaman Register harus memiliki kondisional rendering: Jika Rx String `role` bernilai 'UMKM', tampilkan input 'Nama Usaha', jika 'KREATOR', tampilkan input 'Username Kreator'.
> Jangan implementasi logika Appwrite dulu, cukup UI dan pemanggilan fungsi kosong di Controller."

---

## 2. Prompt Generate Integrasi Appwrite Auth (Logic & Backend)

Gunakan *prompt* ini untuk menyambungkan UI dengan layanan Appwrite Account dan Databases.

> **Prompt:**
> "Implementasikan logika integrasi Appwrite untuk fitur Auth di `AuthController` dan lapisan `DataSource`/`Repository`. Baca referensi dari `docs/features/auth/backend.md` dan `docs/features/auth/appwrite-schema.md`.
> 
> Langkah kerja:
> 1. Buat metode di `AuthRemoteDataSource` untuk login (`Account.createEmailPasswordSession()`) dan register (`Account.create()`).
> 2. Pada metode register, pastikan setelah `Account.create()` sukses, segera jalankan `Databases.createDocument()` untuk menyimpan detail user ke collection 'users' (Database ID: `marketiv_db`, Collection ID: `users`).
> 3. Simpan nilai `role` dan `user_id` ke cache menggunakan GetStorage (`StorageService`).
> 4. Bungkus semua Appwrite call dengan blok try-catch dan ubah `AppwriteException` menjadi struktur `Either<Failure, T>` sesuai arsitektur Clean Code proyek ini.
> Hasilkan kode untuk Controller, UseCase, Repository, dan DataSource."

---

## 3. Prompt Debug Auth & Error Handling

Gunakan *prompt* ini saat menghadapi *bug* atau perlu menyempurnakan penanganan *error*.

> **Prompt:**
> "Saya mengalami masalah / ingin menambahkan Edge Cases pada proses login/register. Tolong tinjau ulang kode `AuthController` dan `AuthRemoteDataSource` saya.
> 
> Referensi aturan error ada di `docs/features/auth/edge-cases.md`.
> Tolong berikan kode perbaikan untuk menangani `AppwriteException` secara spesifik:
> 1. Tangkap error code 401 dan kembalikan pesan ramah 'Email atau kata sandi tidak sesuai'.
> 2. Tangkap error code 409 saat register dan kembalikan pesan 'Email ini sudah terdaftar'.
> 3. Tampilkan pesan ini di UI menggunakan Snackbar GetX berwarna merah (`AppColors.danger`) atau teks di bawah form."

---

## 4. Prompt Refactor & Session Management (Splash Screen)

Gunakan *prompt* ini untuk memperbaiki alur pengecekan sesi saat aplikasi dimulai.

> **Prompt:**
> "Tolong bantu refactor logika `SplashController` untuk memastikan pengecekan sesi yang benar menggunakan Appwrite, merujuk pada `docs/features/auth/user-flow.md`.
> 
> Kebutuhan:
> 1. Gunakan `Account.get()` untuk mengecek validitas token JWT.
> 2. Jika sukses, ambil data role dari `StorageService` (jika kosong, lakukan fetch 1 kali ke koleksi `users`).
> 3. Gunakan `Get.offAllNamed()` untuk mem-bypass stack routing ke rute Dashboard yang benar (UMKM / Kreator / Admin).
> 4. Jika error (401), arahkan ke halaman `/login`.
> Tambahkan delay animasi Splash minimal 1.5 detik agar transisi mulus."
