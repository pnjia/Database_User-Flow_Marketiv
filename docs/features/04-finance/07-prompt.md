# Kumpulan Prompt Reusable: Keuangan & Escrow

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur finansial.

## 1. Prompt UI Riwayat Transaksi (Frontend)
> "Tolong buatkan halaman `KeuanganUMKMPage` dan `KeuanganKreatorPage`. Menggunakan `GetX`, ambil data dari `KeuanganController`. Buatkan list ber-scroll vertikal untuk merender data `TransactionModel`. Komponennya harus menyerupai `TransactionCard` yang menampilkan ikon beda warna (hijau untuk dana masuk/Pencairan, merah untuk Deposit/Withdrawal/keluar). Jangan lupa tambahkan widget *TabBar* untuk memfilter (Semua, Deposit, Penarikan)."

## 2. Prompt Integrasi Payment Gateway via Webhook (Backend Logic)
> "Peringatan keamanan: Jangan tulis *Midtrans Server Key* di dalam proyek Flutter. Di `KeuanganController`, buatkan aksi tombol Top-Up yang mengeksekusi `AppwriteService.functions.createExecution` ke id `midtrans-create-fn`. Parameter yang dikirimkan hanya `order_id`, `amount`, dan `item_name`. Jika respons server sukses, jalankan metode `MidtransService.openSnapWebView(snapToken)` dan pantau status WebView-nya."

## 3. Prompt Refactoring Form Withdrawal (Edge Cases)
> "Coba periksa kembali method `submitWithdraw()` di form pencairan kreatif. Pastikan ada pemeriksaan bahwa nominal withdraw wajib lebih besar atau sama dengan Rp 10.000 dan tidak melebihi sisa atribut `dompetSaldo` yang diambil dari data sesi akun pengguna."