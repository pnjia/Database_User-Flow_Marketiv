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

## Edge Cases

# Edge Cases: Modul Campaign Mode

## Skenario Error & Penanganan

1. **Berkas Aset Lebih dari 100MB**
   - **Kondisi**: UMKM mencoba mengunggah video resolusi tinggi langsung ke aplikasi.
   - **Tindakan UI**: Form memblokir unggahan (client-side validation), menampilkan *error text*: "File melebihi 100MB. Gunakan link Google Drive."

2. **Race Condition Saat Klaim (Kuota Jebol)**
   - **Kondisi**: 2 kreator mencoba menekan tombol "Klaim" secara bersamaan di saat sisa kuota tinggal 1.
   - **Tindakan/Fallback**: Tidak boleh dieksekusi via SDK `createDocument` biasa. Harus via `claim-campaign-fn`. Fungsi di server akan melakukan cek, klaim pertama berhasil, klaim kedua akan mendapat balasan error JSON, dan UI memunculkan *Snackbar*: "Maaf, kuota campaign ini baru saja penuh."

3. **URL Bukti Tayang Tidak Valid**
   - **Kondisi**: Kreator menaruh URL asal (misal `google.com`) di form Submit Bukti Tayang.
   - **Tindakan UI**: Validasi regex klien mencegah tombol ditekan sampai format string dimulai dengan `https://www.tiktok.com/` atau `https://www.instagram.com/`.

4. **Kreator Mencoba Klaim Campaign yang Sama Dua Kali**
   - **Kondisi**: Kreator sudah memiliki submission aktif, tapi mencoba klaim lagi.
   - **Tindakan/Fallback**: Appwrite Function akan menolak permintaan karena mendeteksi relasi duplikat `campaign_id` dan `kreator_id`.
