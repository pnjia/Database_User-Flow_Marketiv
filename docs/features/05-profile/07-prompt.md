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