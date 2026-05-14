# Alur Pengguna (User Flow): Profil & Edit Profil

## 1. Alur Membaca & Navigasi
1. Pengguna membuka navigasi dasar (*Bottom Navigation Bar*).
2. Menekan ikon **Profil** di ujung kanan.
3. Halaman menampilkan ringkasan profil saat ini tanpa membutuhkan tarikan *loading* agresif karena aplikasi semestinya mem-*fetch* profil sejak *Splash Screen/InitialBinding*.

## 2. Alur Memperbarui Profil
1. Pengguna mengetik atau mengubah isian *text field* (misalnya memperbaiki Bio Portofolio).
2. Pengguna menekan tombol "Simpan Perubahan".
3. Tombol memunculkan efek indikator proses (*Loading Spinner*).
4. Sinyal jaringan *Update* dilemparkan ke peladen Appwrite `users` collection.
5. Indikator lenyap, digantikan notifikasi pop-up hijau: "Profil berhasil diperbarui".

## 3. Alur Ganti Foto (Avatar)
1. Pengguna mengeklik area pasfoto (Avatar).
2. Tampil opsi pemilih gambar OS (Kamera / Galeri).
3. Pengguna memilih satu berkas citra `jpg` / `png`.
4. Berkas ditekan ukurannya, lalu dikirim ke keranjang `profile_images`.
5. URL publik citra baru disuntikkan ke dokumen profil dan langsung merender gambar baru di layar.

## 4. Alur Pemutusan Sesi (Logout)
1. Di bagian paling bawah bilah menu Pengaturan, pengguna mengeklik *Logout* (Berwarna merah).
2. Muncul modul dialog konfirmasi.
3. Menekan tombol penegasan ("Ya, keluar").
4. Sistem membersihkan SDK dan mengalihkan pengguna ke luar layar (Halaman *Login*).