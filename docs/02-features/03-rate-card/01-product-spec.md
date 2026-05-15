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

## Edge Cases

# Edge Cases: Modul Rate Card Mode

## Skenario Error & Penanganan

1. **Membuat Paket Lebih dari Batas (Rate Card)**
   - **Kondisi**: Kreator berusaha menambahkan paket ke-4 melalui eksploitasi UI atau glitch.
   - **Tindakan UI/Logika**: Di `RateCardController`, paksa hitung ulang jumlah dokumen yang terasosiasi dengan `kreator_id`. Jika `count >= 3`, cegah pemanggilan `createDocument` dan munculkan *Snackbar* peringatan: "Kamu hanya bisa memiliki maksimal 3 paket Rate Card."

2. **Koneksi Terputus Saat Live Chat**
   - **Kondisi**: Jaringan UMKM/Kreator tidak stabil, koneksi WebSocket Appwrite Realtime terputus.
   - **Tindakan/Fallback**: SDK Appwrite akan mencoba *reconnect* otomatis. UI disarankan menunjukkan indikator kecil di App Bar (misal tulisan `Connecting...`) saat status *stream* *offline*. Pesan yang gagal terkirim harus bisa di-tap untuk *retry*.

3. **Format Data `CustomOffer` Gagal di-Parse**
   - **Kondisi**: String JSON di atribut `offer_data` koleksi `messages` rusak (*corrupt*).
   - **Tindakan UI**: Aplikasi harus membungkus proses dekoding `jsonDecode` menggunakan `try-catch`. Jika gagal, render *fallback widget* yang bertuliskan: "Gagal memuat penawaran." ketimbang membuat layar aplikasi putih/crash.

4. **UMKM Menggantung Pekerjaan Selesai**
   - **Kondisi**: Kreator sudah *submit* URL konten, namun UMKM tidak pernah menekan tombol "Selesai" untuk merilis Escrow berhari-hari.
   - **Tindakan (Asumsi Masa Depan)**: Admin dapat melakukan *force release* setelah batas waktu habis (misal > 7 hari) jika tidak ada komplain yang diajukan.