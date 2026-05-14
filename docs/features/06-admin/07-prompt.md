# Kumpulan Prompt Reusable: Admin Mode

Gunakan instruksi ini untuk mengeksploitasi potensi optimal fitur pelaporan dan penanganan Admin.

## 1. Prompt Membangun Layar Validasi Submission (Frontend)
> "Tolong buatkan halaman `AdminSubmissionsPage` di Flutter GetX. Ambil data dengan menjalankan operasi HTTP GET (Execution Function API) menuju layanan yang akan memberikan daftar *Submissions*. 
> 
> Komponen wajib:
> 1) Tombol 'Buka Bukti' yang akan memakai plugin `url_launcher` untuk membuka peramban OS pengguna di luar aplikasi.
> 2) Tombol centang *Valid* yang menginisiasi *call update* ke `validate-submission-fn`. 
> Tampilkan pop-up dialog kepastian sebelum Admin benar-benar menekan tombol 'Tandai Sebagai Valid' agar uang tidak sembarangan meleset terkirim."

## 2. Prompt Fungsi Admin Dashboard (Logic/Backend Function)
> "Instruksi untuk membuat skrip server *Node.js* (*Dart*) untuk `admin-stats-fn`:
> Tarik dan gunakan *Appwrite Server API Key* untuk mengeksekusi iterasi hitung (`count`) total dokumen di koleksi `users`. Selanjutnya, hitung total sum dari nominal koleksi `transactions` yang masuk dalam kategori status 'Selesai'. Kirim balasan format HTTP tersebut berupa objek JSON agregasi murni yang telah dihitungkan. Tangkap *error* bila durasi eksekusi memakan lebih dari 10 detik dan kembalikan *fallback JSON* angka 0."

## 3. Prompt Refactoring Dispute Modal
> "Di file `AdminDisputesPage`, ketika baris sengketa di-tap, antarmuka bawah layar (*bottom sheet*) akan terbuka. Tambahkan opsi label merah 'Blacklist Akun'. Saat label ini dieksekusi, perintahkan Appwrite SDK Admin API Key untuk mengambil `$id` User tersebut dan mensabotase akunnya (mematikan status terverifikasinya atau memblokir sesi log masuk dengan merubah flag *Banned* di koleksi)."