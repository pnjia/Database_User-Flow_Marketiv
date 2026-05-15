# Alur Pengguna (User Flow): AI Assistant

## Alur UMKM (Menulis Deskripsi Campaign)
1. UMKM berada di langkah penyusunan informasi awal (*Step 1*) pada mode Pembuatan Kampanye Baru.
2. Mengisi teks Nama Produk: "Pecel Lele Mak Nyus Cabang Sudirman".
3. Karena literasi UMKM masih minim mengenai gaya penulisan kampanye bagi gen-Z, UMKM mengabaikan form raksasa "Tulis Detail Instruksi".
4. Menekan tombol "✨ Bantu Saya dengan AI" yang berada di ujung form kosong tersebut.
5. Indikator pemuatan berkedip sekitar 3 hingga 5 detik.
6. Kolom seketika berisi skenario konten: 
   *"Hai Kreator, tolong buat video review produk Pecel Lele Mak Nyus ini! Fokuskan pada pedasnya sambal, letakkan efek dramatis saat mengunyah lele gorengnya. Targetkan audiens anak muda di Sukabumi..."*
7. UMKM menekan langkah selanjutnya untuk mengunggah gambar produk dengan perasaan puas.

## Alur Admin (Memeriksa Anomali Bukti - Asumsi Peningkatan)
1. Kreator mengunggah hasil kinerja URL penayangan.
2. Di balik layar, skrip server AI memindai metrik tautan. Jika diindikasi penayangan mencurigakan (menggunakan bot pembelanjaan tayangan secara *spam*), AI otomatis memberi tempelan stempel label peringatan merah.
3. Saat Admin membuka layar `Submissions`, peringatan itu menyertakan rasionalisasi: *"Catatan AI: Lonjakan views terjadi dalam 30 detik tanpa dibarengi retensi (like/comment)."*
4. Mengacu panduan ini, Admin dengan percaya diri mengeklik tombol "Tandai Sebagai Fraud".

## Reference Wireframes & User Flows

# Alur Campaign Mode (Berbasis Views)

*Karakteristik: Tidak ada fitur chat.*

**Fase UMKM (Pembuatan):**
1. Buka menu `Kampanye` -> Klik `Buat Campaign`.
2. **Step 1:** Isi data produk & niche. (Opsional: klik "Bantu dengan AI" untuk merangkai instruksi brief).
3. **Step 2:** Upload gambar aset (≤ 100MB) atau tempel link Google Drive.
4. **Step 3:** Atur bayaran per 1.000 views dan kuota kreator maksimal.
5. **Step 4:** Review rincian anggaran. Klik "Bayar Sekarang via Escrow".
6. Dialihkan ke `WebView Midtrans` untuk transaksi.
7. Jika sukses, status campaign menjadi **Aktif** dan tayang di Job Pool.

**Fase Kreator (Klaim & Eksekusi):**
1. Kreator membuka menu `Job Pool`.
2. Filter daftar berdasarkan niche / harga. Klik salah satu campaign.
3. Membaca brief dan klik "Klaim Job Ini".
   - *Percabangan:* Jika kuota sudah penuh, tombol *disabled* dan klaim ditolak.
4. Jika berhasil, campaign pindah ke menu `Pekerjaan Aktif`.
5. Kreator mengerjakan video (di luar app) dan mengunggahnya ke TikTok / Instagram pribadi.
6. Kreator kembali ke aplikasi -> Klik pekerjaan -> Tempel URL publik video -> Klik "Submit Bukti Tayang".
7. Status submission menjadi **Pending**.

**Fase Validasi & Pencairan (Sistem/Admin):**
1. Sistem/Admin memeriksa validitas views (Appwrite Function).
2. Jika valid -> Dana dilepaskan dari status Escrow ke `dompet_saldo` kreator. Campaign selesai (jika kuota & validasi terpenuhi).