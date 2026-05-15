# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Fitur Autentikasi

Dokumen ini mendeskripsikan implementasi antarmuka dan *logic* sisi klien (Flutter) untuk modul Autentikasi.

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Splash Screen** | `/splash` | Menjalankan *session check* Appwrite di *background*. |
| **Onboarding** | `/onboarding` | Tampilan sambutan untuk pengguna baru. |
| **Login** | `/login` | Formulir masuk email & password. |
| **Register & Role Selection** | `/register`, `/role-selection` | Formulir pendaftaran multi-step. |

## Komponen UI Utama (dari Design System)
- **`AuthTextField`**: Komponen input teks standar dengan border `Grey 300` dan fokus `Primary 500`. Dilengkapi ikon toggle visibilitas untuk password.
- **`PrimaryButton`**: Tombol aksi utama (Masuk, Daftar) dengan warna `Primary 500`. Memiliki *state* loading (berubah menjadi `CircularProgressIndicator` saat proses API).
- **`TextButton` / `RichText`**: Untuk navigasi ke halaman lain (misal: "Lupa Password?", "Daftar di sini").
- **`Role Selection Cards`**: Komponen visual khusus untuk memilih peran UMKM atau Kreator dengan *highlight* border aktif.

## Validasi Form
Validasi dilakukan di sisi klien sebelum melakukan *request* ke Appwrite:
1. **Email**: Harus sesuai format Regex email standar.
2. **Password**: Wajib diisi, minimal 8 karakter (*Asumsi* praktik standar).
3. **Nama Lengkap & WhatsApp**: Wajib diisi (Required).
4. **Validasi Berbasis Role**:
   - Jika Role = UMKM: Input `nama_usaha` **wajib** diisi.
   - Jika Role = KREATOR: Input `username_kreator` **wajib** diisi (tanpa spasi).

## State Management (GetX)
Menggunakan `AuthController` untuk mengelola state keseluruhan:
- `_isLoading.value` (Boolean): Mengatur animasi loading pada tombol.
- `_user.value` (UserEntity): Menyimpan detail identitas dan *role* pengguna setelah sukses login.
- `_errorMessage.value` (String): Menyimpan pesan *error* untuk ditampilkan di antarmuka.

## Interaksi User
1. **Membuka Aplikasi**: Sistem membaca sesi (*Splash*).
2. **Mengisi Form**: Pengguna menginput teks, sistem melakukan validasi struktur (*onChanged* atau *onSubmit*).
3. **Submit**: Pengguna menekan tombol "Masuk" / "Daftar", antarmuka terkunci (loading state), dan memanggil UseCase.
4. **Hasil (Sukses)**: Navigasi (`Get.offAllNamed`) ke *route* tujuan berdasarkan peran.
5. **Hasil (Gagal)**: Menampilkan *snackbar* atau *error text* (misal: "Email atau kata sandi salah").

## Integrasi Design System
- **Warna**: Latar belakang aplikasi menggunakan `Grey 50`. Tombol utama menggunakan `Primary 500`. Pesan error menggunakan semantic color `Danger`.
- **Tipografi**: Judul menggunakan `Newsreader` (H1/H2). Input dan teks standar menggunakan `Inter` (Body Large/Medium).
- **Spasi & Layout**: Form dibungkus dalam *container* dengan *padding* `md` (16px) atau `lg` (24px) sesuai panduan spasi. Semua halaman patuh pada prinsip *Mobile-First*.

## Backend

# Backend: Logika Autentikasi & Integrasi Appwrite

Dokumen ini menjelaskan bagaimana logika sisi klien Flutter berinteraksi dengan BaaS Appwrite. Dalam proyek ini, **tidak ada server Express lokal**, semua bergantung pada Appwrite SDK.

## Alur Integrasi Appwrite Auth
1. **Lapisan DataSource**: `AuthRemoteDataSource` bertugas memanggil *method* spesifik dari `AppwriteService.account`.
2. **Lapisan Repository**: Menangkap hasil dari DataSource dan mengubah `AppwriteException` menjadi struktur `Either<Failure, T>` untuk diteruskan ke UseCase.
3. **Lapisan UseCase**: Mengeksekusi instruksi dari Controller, misal `LoginUseCase` atau `RegisterUseCase`.

## Service yang Digunakan
Menggunakan fungsi bawaan dari Appwrite `Account` API:
- `Account.create()`: Untuk meregistrasi pengguna baru (Email, Password, Name).
- `Account.createEmailPasswordSession()`: Untuk membuat sesi login.
- `Account.get()`: Untuk memeriksa informasi sesi pengguna yang sedang aktif (sering digunakan di Splash Screen).
- `Account.deleteSession(sessionId: 'current')`: Untuk proses logout.
- `Account.createVerification()`: (*Asumsi*) Digunakan setelah registrasi untuk mengirim email verifikasi.

## Session Management
- Sesi berbentuk token (JWT/Session ID) dikelola dan **disimpan secara otomatis** oleh Appwrite SDK (native cookie/local storage platform).
- Flutter client **tidak menyimpan token mentah** di GetStorage.
- `GetStorage` (lewat `StorageService`) hanya dimanfaatkan untuk membuat *cache* data non-sensitif agar navigasi cepat, seperti:
  - `role` ('UMKM' / 'KREATOR' / 'ADMIN')
  - `user_id`

## Permission & Hak Akses
- Tidak menggunakan pengelompokan `Teams` bawaan Appwrite.
- Logika peran sistem (Role-Based Access Control) sepenuhnya dikendalikan melalui field `role` berupa tipe *String/Enum* di dalam dokumen koleksi `users`.
- Hak akses database (*Document-Level Security*) bergantung pada `user_id` unik milik akun Appwrite.

## Logic Verifikasi User
1. Saat mendaftar dengan `Account.create()`, akun Appwrite terbentuk.
2. Sesaat setelah akun terbentuk, aplikasi klien (atau melalui Appwrite Function) akan melakukan `Databases.createDocument` untuk memasukkan data ekstra (WA, nama usaha, username kreator) ke koleksi `users`.
3. Aplikasi mengecek atribut `is_verified` (Boolean) di koleksi `users` untuk menentukan apakah akun sudah melewati validasi email.

## AI Prompt

# Kumpulan Prompt Reusable: Fitur Autentikasi

Dokumen ini berisi sekumpulan instruksi (*prompts*) terstruktur yang dirancang khusus untuk memandu AI (seperti GitHub Copilot, Cursor, atau sistem chat ini) dalam men-generate, mendebug, atau memodifikasi kode terkait fitur Autentikasi Marketiv.

---

## 1. Prompt Generate Frontend Auth (Login & Register UI)

Gunakan *prompt* ini saat ingin membangun halaman UI login/register dari nol atau melengkapinya.

> **Prompt:**
> "Buatkan antarmuka (UI) Flutter untuk halaman Login dan Register sesuai panduan di `docs/02-features/auth/frontend.md` dan design system `docs/_global/design-system/`. 
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
> "Implementasikan logika integrasi Appwrite untuk fitur Auth di `AuthController` dan lapisan `DataSource`/`Repository`. Baca referensi dari `docs/02-features/auth/backend.md` dan `docs/02-features/auth/appwrite-schema.md`.
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
> Referensi aturan error ada di `docs/02-features/auth/edge-cases.md`.
> Tolong berikan kode perbaikan untuk menangani `AppwriteException` secara spesifik:
> 1. Tangkap error code 401 dan kembalikan pesan ramah 'Email atau kata sandi tidak sesuai'.
> 2. Tangkap error code 409 saat register dan kembalikan pesan 'Email ini sudah terdaftar'.
> 3. Tampilkan pesan ini di UI menggunakan Snackbar GetX berwarna merah (`AppColors.danger`) atau teks di bawah form."

---

## 4. Prompt Refactor & Session Management (Splash Screen)

Gunakan *prompt* ini untuk memperbaiki alur pengecekan sesi saat aplikasi dimulai.

> **Prompt:**
> "Tolong bantu refactor logika `SplashController` untuk memastikan pengecekan sesi yang benar menggunakan Appwrite, merujuk pada `docs/02-features/auth/user-flow.md`.
> 
> Kebutuhan:
> 1. Gunakan `Account.get()` untuk mengecek validitas token JWT.
> 2. Jika sukses, ambil data role dari `StorageService` (jika kosong, lakukan fetch 1 kali ke koleksi `users`).
> 3. Gunakan `Get.offAllNamed()` untuk mem-bypass stack routing ke rute Dashboard yang benar (UMKM / Kreator / Admin).
> 4. Jika error (401), arahkan ke halaman `/login`.
> Tambahkan delay animasi Splash minimal 1.5 detik agar transisi mulus."
