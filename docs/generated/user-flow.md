# Alur Pengguna (User Flow) Marketiv

Dokumen ini mendeskripsikan langkah-langkah *end-to-end* untuk setiap fitur utama di Marketiv beserta percabangannya.

## 1. Alur Autentikasi & Sesi
1. **Aplikasi Dibuka** -> `Splash Screen`
   - *Sistem mengecek session via Appwrite SDK.*
   - **Percabangan (Login):**
     - Jika ADA sesi aktif -> Pindah ke `Home` (berdasarkan Role UMKM / Kreator / Admin).
     - Jika TIDAK ADA sesi -> Pindah ke `Onboarding` (jika baru pertama kali) atau `Login`.
2. **Pendaftaran (Register):**
   - Pengguna memilih tipe peran (UMKM atau Kreator).
   - Mengisi form dasar (nama, email, password, WA) + field khusus (nama_usaha / username_kreator).
   - Klik Daftar -> Appwrite membuat akun dan mengirim verifikasi email.
   - Sistem menyimpan dokumen ke Appwrite Database (`users`).
   - Redirect ke `Login`.
3. **Log Masuk (Login):**
   - Mengisi email dan kata sandi.
   - Autentikasi berhasil -> Token JWT tersimpan di device.
   - Pindah ke `Home` masing-masing role.

## 2. Alur Campaign Mode (Berbasis Views)
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

## 3. Alur Rate Card Mode (Fixed Price / Custom Offer)
*Karakteristik: Negosiasi langsung melalui Live Chat.*

**Fase Discovery & Negosiasi:**
1. UMKM buka menu `Kreator` -> Cari profil kreator -> Lihat paket Rate Card.
2. UMKM klik "Hubungi Kreator". Sistem membuat order dengan status **Negosiasi**.
3. UMKM & Kreator mengobrol di `Chat Room` (Realtime).
4. UMKM membuat kesepakatan final dengan mengirim **Custom Offer** (widget dalam chat berisi detail: judul, scope, harga final).
5. Kreator menerima/menolak *Custom Offer*.
   - Jika *Terima* -> Status order menjadi **Menunggu Pembayaran**.

**Fase Pembayaran & Eksekusi:**
1. UMKM melakukan pembayaran via deposit (Midtrans Snap).
2. Dana ditahan oleh platform, status order menjadi **Escrow**.
3. Kreator memproduksi dan mengunggah video.
4. Kreator menempelkan link hasil (*Collab Post*) di sistem. Status order menjadi **Menunggu Verifikasi**.

**Fase Penyelesaian:**
1. UMKM memeriksa video.
   - *Percabangan A:* UMKM klik "Selesai" -> Dana cair ke dompet kreator.
   - *Percabangan B:* UMKM minta Revisi.
   - *Percabangan C:* UMKM komplain -> Buka Sengketa (Dispute) -> Admin yang akan mengambil keputusan.

## 4. Alur Keuangan & Escrow
1. **Top-Up (UMKM):**
   - UMKM buka `Keuangan` -> Klik `Deposit`.
   - Mengisi jumlah deposit -> Membayar melalui antarmuka Midtrans.
   - Saldo dompet terisi (meskipun secara teknis langsung di-lock menjadi *Escrow* pada transaksi Rate Card/Campaign yang tertunda).
2. **Penarikan / Withdrawal (Kreator):**
   - Kreator buka `Keuangan`.
   - Memastikan `dompet_saldo` > 0.
   - Isi form (nominal, bank, nomor rekening, pemilik).
   - Klik "Tarik Dana" -> Permintaan masuk ke sistem (via Appwrite Function ke Midtrans Iris).
   - Dana masuk ke rekening bank, dompet saldo aplikasi berkurang.