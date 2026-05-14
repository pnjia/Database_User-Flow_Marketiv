# Backend: Modul Admin & Pelaporan

## Integrasi Appwrite
Sebagian besar fitur panel pelaporan mem-*bypass* metode baca/tulis langsung ke koleksi, dan menggunakan Appwrite Functions untuk menjalankan operasi yang memiliki hak akses mutlak (skrip dibungkus Appwrite Server API Key).

## Service yang Digunakan
- **Appwrite Functions**: Menjadi tumpuan agregasi lintas koleksi yang terlalu membebani Flutter (contoh: Menjumlah total uang di `transactions`).
- **Appwrite Databases**: Menggunakan *query update* bagi status sengketa maupun submission secara spesifik jika skrip sederhana diizinkan.

## Permission
- Semua eksekusi `UPDATE` admin yang menembus *Document-Level Security* koleksi pengguna lain mutlak dilakukan lewat penugasan *Serverless Function*.
- Aplikasi mobile dilarang memberikan hak modifikasi dokumen silang antar pengguna.

## Process Flow (Logika Backend)
1. **Flow Tarik Statistik Dashboard**:
   - Flutter UI melakukan HTTP GET memicu eksekusi `admin-stats-fn`.
   - Function mengkompilasi GMV (Total nominal transaksi `Deposit/Pencairan` dengan status Selesai), menghitung panjang antrean *Pending Submissions*, dan mengirimkan kembali JSON agregatnya.
2. **Flow Eksekusi Sengketa (Dispute)**:
   - Admin mengeklik tombol *Refund* UMKM pada sebuah *Order*.
   - Flutter mengirim ID tersebut ke fungsi `refund-escrow-fn`.
   - Script Server API memeriksa saldo tertahan, mengembalikan data uang ke koleksi `users` (Atribut UMKM terkait), dan melempar mutasi pencatatan baru `Refund` ke tabel `transactions`. Status order di `rate_card_orders` digembok ke *Dibatalkan/Selesai*.