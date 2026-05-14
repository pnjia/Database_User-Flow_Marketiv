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
