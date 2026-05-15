# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Modul Profil & Edit Profil

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Profil User (Read Only / Public)**| `/umkm/kreator/:id` | Diimplementasikan di Modul Rate Card sebagai Direktori (Khusus Kreator). |
| **Edit Profil & Pengaturan** | `/profile/edit` | Tampilan form input modifikasi data akun saat ini. Merupakan halaman tab di Bottom Navigation. |

## Komponen UI Utama
- **`AvatarPicker` / `ProfilePicture`**: Penampil *CachedNetworkImage* berbentuk sirkuler dengan placeholder inisial nama, dilengkapi dengan ikon pensil untuk mengubah gambar.
- **`Form Identitas Dasar`**: Sekumpulan komponen standar (`TextFormField`, *dropdown* Niche).
- **`LogoutButton`**: Komponen dengan rona gaya `Danger` merah untuk memutus sesi secara mencolok.

## State Management
- `ProfileController` menampung state Rx `<UserEntity>` saat ini (hasil persinggungan data global `InitialBinding`).
- Perubahan apa pun di UI (*onSave*) akan menyalurkan modifikasi parameter secara berantai hingga lapisan *Use Case*.

## Validasi Form
- Form tidak memodifikasi `role` dan `email` (Biasanya dibuat statis / read-only).
- Jika ada *update username*, divalidasi apakah mengandung spasi atau karakter ilegal (tergantung limitasi).

## Interaksi Pengguna
- Pengguna mengganti foto, menekan gambar memicu pembukaan galeri OS (menggunakan paket `image_picker`). Setelah itu akan merespons animasi unggahan hingga status berhasil.
- Pengguna menekan *Logout*, aplikasi meminta persetujuan melalui Dialog *pop-up*.
## Backend

# Backend: Modul Profil & Edit Profil

## Integrasi Appwrite
Proses pengaturan profil melibatkan penyelarasan *document* yang ada dalam struktur basis data (Databases) dan berkas media fisik (Storage) Appwrite.

## Service yang Digunakan
- **Appwrite Databases**: Meng-update dokumen pengguna dengan menggunakan method `Databases.updateDocument()`.
- **Appwrite Storage**: Menyimpan berkas gambar baru dengan `Storage.createFile()` ke wadah *bucket* spesifik lalu memperoleh string tautan URL publik.

## Permission
- Fitur ini merupakan implementasi utama dari hak *Document-Level Security* di mana klien pengguna terautentikasi (`user:{$id}`) berhak penuh melakukan modifikasi `UPDATE` terhadap profilnya sendiri yang ada dalam koleksi `users`.
- Tidak ada hak akses yang mengizinkan *user A* melakukan *overwrite* atas dokumen milik *user B*.

## Process Flow (Logika Backend)
1. **Flow Edit Teks**: Klien mengirim instruksi perbaikan form `updateDocument()`. Jika berhasil, entitas *local cache* (`GetStorage` dan `State`) langsung ditimpa (disamakan).
2. **Flow Ganti Foto**: 
   - Gambar dari perangkat dipangkas (*compressed*) oleh *Flutter_Image_Compress*.
   - Gambar dikirim ke `profile_images`. Jika user sudah punya foto, mungkin foto lama dibiarkan tertebar atau dihapus jika sistemnya mendukung.
   - String `url_foto` baru yang merujuk pada *bucket* disimpan menggantikan `url_foto` lama di dalam `users`.
## AI Prompt

# Kumpulan Prompt Reusable: Profil & Pengaturan

Gunakan instruksi di bawah untuk menunjang pengembangan subrutin profil.

## 1. Prompt Generate UI Profile (Frontend)
> "Rancanglah halaman `EditProfilePage` dengan Flutter GetX berpandukan UI Rules `docs/_global/design-system/`. Sediakan struktur form berlapis `SafeArea` dan bisa di-scroll vertikal. Buatkan widget Avatar yang memiliki ikon 'edit' *floating*. Jika pengguna `role` adalah 'UMKM', rendermunculkan TextFormField berlabel 'Nama Usaha', namun jika 'KREATOR' tampilkan dropdown 'Niche' dan input 'Bio'. Tombol simpan letakkan stasioner di posisi *bottom* (bawah) atau di ujung layar *scroll*."

## 2. Prompt Fungsi Upload Foto & Kompresi (Logic)
> "Tolong buatkan fungsi di dalam `ProfileRemoteDataSource` untuk `uploadProfileImage()`. Skenarionya adalah pengguna mengunggah `File`. Instruksinya: 
> 1) Gunakan package pendukung `flutter_image_compress` untuk mengecilkan size gambar sebelum diproses (Kualitas 70%).
> 2) Panggil `AppwriteService.storage.createFile` menggunakan bucket ID `profile_images`. 
> 3) Atur relasi *Permission* di fungsi itu agar URL dibebaskan ke publik, tetapi akses Edit dilokalisir hanya untuk pengguna tersebut. 
> 4) Hasilkan string konkat URL akses hasil."

## 3. Prompt Refactoring Widget Reactive State
> "Review kode `ProfileController` saya. Pada metode `onInit`, controller membaca variabel dari `InitialBinding` secara reaktif dan memasukannya ke isian `TextEditingController`. Namun, saat ditekan 'Simpan' dan API Appwrite merespons sukses, form UI tidak ikut tersetel (state tidak direfresh ulang secara menyeluruh). Tolong integrasikan mekanisme `_user.refresh()` atau `.update()` agar seluruh antarmuka yang mengonsumsi state avatar dan nama otomatis berganti seketika."