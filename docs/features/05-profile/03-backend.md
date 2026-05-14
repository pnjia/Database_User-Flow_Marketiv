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