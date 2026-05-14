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