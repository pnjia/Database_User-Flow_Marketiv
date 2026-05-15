# Alur Pengguna (User Flow): Campaign Mode

## Alur UMKM (Pembuatan Campaign)
1. Buka menu **Kampanye** -> Klik **Buat Campaign**.
2. **Step 1 (Info Produk)**: Isi nama, deskripsi, dan niche. (Gunakan AI Assistant opsional).
3. **Step 2 (Aset)**: Unggah file dari perangkat atau berikan URL Google Drive.
4. **Step 3 (Budget)**: Tentukan harga per 1.000 views dan kuota maksimal kreator.
5. **Step 4 (Review)**: Periksa total biaya (termasuk fee platform 15%).
6. Klik **Bayar Sekarang**. WebView Midtrans terbuka.
7. UMKM menyelesaikan pembayaran.
8. Sistem mengubah status campaign dari `Draft` menjadi `Aktif`. Muncul di Job Pool.

## Alur Kreator (Klaim & Eksekusi)
1. Buka menu **Job Pool**.
2. Jelajahi campaign, filter berdasarkan niche/harga.
3. Klik campaign untuk membaca *brief* dan melihat tautan aset.
4. Klik **Klaim Job Ini**.
   - *Branch*: Jika kuota penuh, tombol *disabled* dan klaim gagal.
5. Jika sukses, pekerjaan pindah ke menu **Pekerjaan Aktif**.
6. Kreator memproduksi video dan mengunggahnya ke medsos pribadi.
7. Kembali ke aplikasi, buka pekerjaan tersebut, tempelkan URL publik video.
8. Klik **Submit Bukti Tayang**.
9. Menunggu validasi admin.


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