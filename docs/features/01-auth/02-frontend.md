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
