# Overview: Modul Rate Card Mode

## Tujuan Fitur
Memfasilitasi transaksi layanan pemasaran digital dengan skema harga tetap (*fixed price*) antara UMKM dan Kreator, yang diawali dengan proses negosiasi dan kesepakatan secara komunikatif.

## Deskripsi Singkat
Berbeda dengan *Campaign Mode*, *Rate Card Mode* bersifat direktori dan konsultatif. UMKM mencari kreator, bernegosiasi via *Live Chat*, dan kreator bekerja berdasarkan paket layanan (*Rate Card*) mereka setelah deposit diamankan di Escrow. 

## Daftar Subfitur
- **Direktori Kreator**: Halaman katalog publik untuk mencari dan menyaring kreator.
- **Kelola Rate Card**: Antarmuka bagi Kreator untuk mengatur maksimal 3 paket layanan mereka.
- **Live Chat (Negosiasi)**: Obrolan secara realtime (menggunakan *Appwrite Realtime*).
- **Custom Offer**: Widget khusus di dalam obrolan untuk mengunci rincian harga, *scope*, dan *deadline*.

## Dependensi dengan Fitur Lain
- **Autentikasi**: Dibutuhkan profil `users` dengan `role = KREATOR` untuk tampil di direktori.
- **Keuangan & Escrow**: *Custom Offer* yang diterima akan memicu pembayaran Escrow oleh UMKM sebelum pekerjaan dimulai.