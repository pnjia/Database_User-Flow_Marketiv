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