# Overview: Modul Campaign Mode

## Tujuan Fitur
Memfasilitasi UMKM untuk membuat kampanye pemasaran berbasis performa (views) dan memungkinkan Kreator Mikro untuk mengklaim serta mengeksekusi kampanye tersebut.

## Deskripsi Singkat
Sistem *performance-based marketing* di mana UMKM membayar berdasarkan jumlah tayangan yang tervalidasi. Mode ini beroperasi secara asinkron tanpa fitur komunikasi (chat) antara UMKM dan Kreator.

## Daftar Subfitur
- **Buat Campaign**: UMKM mengisi form *multi-step* (wizard) untuk mengatur produk, aset, budget, dan kuota.
- **Job Pool**: Bursa kerja bagi kreator untuk melihat dan memfilter campaign yang aktif.
- **Klaim Job**: Kreator mengklaim campaign (dengan validasi kuota secara *atomic*).
- **Submit Bukti Tayang**: Kreator mengunggah URL video (TikTok/IG Reels) sebagai hasil pekerjaan.

## Dependensi dengan Fitur Lain
- **Keuangan & Escrow**: Saat campaign dibuat, dana masuk ke sistem Escrow. Saat bukti tayang valid, dana cair.
- **AI Features**: Pembuatan instruksi *brief* didukung oleh AI Assistant.
- **Admin Validasi**: Bukti tayang membutuhkan validasi dari Admin sebelum dana dicairkan.