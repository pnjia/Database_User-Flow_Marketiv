# Pemetaan Data & Halaman (Page Data Mapping)

Dokumen ini mendeskripsikan secara mendalam bagaimana setiap halaman pada sisi antarmuka Flutter berinteraksi dengan infrastruktur Appwrite, baik itu Database Collections, Storage, maupun eksekusi Appwrite Functions.

---

### 1. Halaman: **Register**
- **Tujuan Halaman**: Memfasilitasi pendaftaran pengguna baru (UMKM atau Kreator).
- **Koleksi Terkait**: `users` (Database), Appwrite Account API.
- **Operasi CRUD**: `CREATE`
- **Data yang Dibuat**:
  1. Identitas login (Account Appwrite).
  2. Dokumen profil baru di koleksi `users`.
- **Input Pengguna**:
  - `nama_lengkap` (String)
  - `email` (Email)
  - `password` (String rahasia)
  - `nomor_whatsapp` (String)
  - `role` (Enum: UMKM / KREATOR)
  - `nama_usaha` (String, opsional - wajib bagi UMKM)
  - `username_kreator` (String, opsional - wajib bagi KREATOR)
- **Kebutuhan Autentikasi**: Tidak ada (Halaman Publik)
- **Keterangan Tambahan**: `dompet_saldo` dibuat *default* ke nilai `0`. Pengelolaan JWT Auth ditangani secara otomatis oleh SDK.

---

### 2. Halaman: **Login**
- **Tujuan Halaman**: Autentikasi pengguna lama.
- **Koleksi Terkait**: Appwrite Account API, `users`.
- **Operasi CRUD**: `CREATE` (Session), `READ` (Users).
- **Data yang Dibaca**: Dokumen `users` untuk menentukan _role_ guna navigasi halaman selanjutnya.
- **Data yang Dibuat**: JWT Session yang dikelola SDK.
- **Input Pengguna**:
  - `email` (String)
  - `password` (String)
- **Kebutuhan Autentikasi**: Tidak ada (Halaman Publik)

---

### 3. Halaman: **Home UMKM**
- **Tujuan Halaman**: Menampilkan dasbor metrik untuk UMKM.
- **Koleksi Terkait**: `campaigns`, `transactions`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**:
  - Agregasi dokumen `transactions` (untuk total uang Escrow dan uang Keluar).
  - Jumlah dokumen `campaigns` yang memiliki status `Aktif`.
- **Data yang Diubah**: Tidak ada.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).

---

### 4. Halaman: **Daftar Campaign (UMKM)**
- **Tujuan Halaman**: Mengelola dan memonitor status seluruh kampanye milik UMKM ybs.
- **Koleksi Terkait**: `campaigns`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Seluruh dokumen dari `campaigns` di mana atribut `umkm_id` cocok dengan akun yang terautentikasi.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).

---

### 5. Halaman: **Buat Campaign (Wizard - 4 Langkah)**
- **Tujuan Halaman**: Membuat proyek campaign baru (Performance/Viral based).
- **Koleksi Terkait**: `campaigns`, `campaign_assets` (Storage), `transactions` (via Midtrans-Webhook).
- **Operasi CRUD**: `CREATE`
- **Data yang Dibuat**:
  1. Upload file produk ke Storage Bucket `campaign_assets`.
  2. Dokumen di koleksi `campaigns` dengan `status` awal: `Draft`.
- **Input Pengguna**:
  - `judul_campaign` (String)
  - `deskripsi_brief` (String, dapat dimediasi via `generate-brief-fn` API OpenAI)
  - File Gambar (diupload, < 100MB) atau `url_aset_eksternal` (Link Google Drive)
  - `harga_per_1000_views` (Angka)
  - `kuota_kreator` (Angka)
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).
- **Keterangan Tambahan**: Mengubah status menjadi `Aktif` HANYA JIKA Webhook Midtrans merespon `midtrans-webhook-fn` bahwa deposit escrow sukses dibayar.

---

### 6. Halaman: **Job Pool (Kreator)**
- **Tujuan Halaman**: Etalase lapangan pekerjaan (kampanye UMKM) untuk Kreator.
- **Koleksi Terkait**: `campaigns`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Dokumen `campaigns` yang atribut `status` = `Aktif` DAN `kuota_terpakai` < `kuota_kreator`.
- **Input Pengguna**:
  - Teks Search (String)
  - Filter `niche`
  - Range harga
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).

---

### 7. Halaman: **Detail Job Pool (Klaim Job)**
- **Tujuan Halaman**: Membaca instruksi brief dan mengklaim pekerjaan.
- **Koleksi Terkait**: `campaigns`, `submissions`.
- **Operasi CRUD**: `READ`, `CREATE` (via Function)
- **Data yang Dibaca**: 1 detail spesifik `campaigns`.
- **Data yang Dibuat & Diubah**: 
  - (Appwrite Function: `claim-campaign-fn`) merubah `kuota_terpakai` menjadi +1 pada `campaigns`.
  - Membuat 1 dokumen spesifik `submissions` berstatus `Pending`.
- **Input Pengguna**: Menekan tombol (Aksi "Klaim Job").
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).
- **Keterangan Tambahan**: Menghindari *race condition*, client dilarang membuat `submissions` secara mandiri tanpa *atomic increment* di sisi Appwrite Function.

---

### 8. Halaman: **Pekerjaan Aktif & Submit Bukti (Kreator)**
- **Tujuan Halaman**: Kreator melihat pekerjaannya yang sedang berjalan dan menyerahkan URL (TikTok/IG) sebagai bukti kerja.
- **Koleksi Terkait**: `submissions`, `campaigns`.
- **Operasi CRUD**: `READ`, `UPDATE`
- **Data yang Dibaca**: `submissions` yang `kreator_id` merupakan miliknya yang terhubung relasional logis dengan dokumen di `campaigns`.
- **Data yang Diubah**: Modifikasi pada atribut `url_bukti_tayang`. Pembaruan `status_validasi` dan penarikan escrow dilarang dilakukan client, melainkan oleh Admin (`validate-submission-fn`).
- **Input Pengguna**:
  - `url_bukti_tayang` (URL String: TikTok/Instagram).
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).

---

### 9. Halaman: **Direktori Kreator**
- **Tujuan Halaman**: Pencarian dan kurasi portofolio kreator.
- **Koleksi Terkait**: `users`, `rate_cards`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Seluruh profil `users` dengan peran Kreator, serta *nested query* logis ke dokumen `rate_cards` yang terkait dan berstatus `is_active` = `true`.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).

---

### 10. Halaman: **Chat Room & Negosiasi**
- **Tujuan Halaman**: Sinkronisasi komunikasi realtime (Live Chat) dan pengiriman Custom Offer.
- **Koleksi Terkait**: `messages`, `rate_card_orders`.
- **Operasi CRUD**: `READ`, `CREATE`, `UPDATE` (hanya `is_read` flag)
- **Data yang Dibaca**: History `messages` dan struktur `rate_card_orders`.
- **Data yang Dibuat**: Obrolan ke koleksi `messages`.
- **Input Pengguna**:
  - Pesan teks biasa.
  - JSON string diatribut `offer_data` saat memicu `CustomOffer`.
- **Kebutuhan Autentikasi**: Ya (Hanya kedua pihak UMKM dan KREATOR terkait).

---

### 11. Halaman: **Kelola Rate Card (Kreator)**
- **Tujuan Halaman**: Pengaturan paket harga layanan kreatif (Maks. 3 Paket).
- **Koleksi Terkait**: `rate_cards`.
- **Operasi CRUD**: `READ`, `CREATE`, `UPDATE`, `DELETE`
- **Data yang Dibuat / Diubah**: Dokumen spesifik di `rate_cards`.
- **Input Pengguna**:
  - `nama_paket` (String)
  - `harga_paket` (Float)
  - `deskripsi_paket` (String)
  - `deliverable` (String)
  - `estimasi_hari` (Int)
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).
- **Keterangan Tambahan**: Validasi pengecekan > 3 dokumen dicegat di level state Controller Flutter.

---

### 12. Halaman: **Keuangan / Saldo**
- **Tujuan Halaman**: Melacak histori aliran dana, top-up, dan withdraw.
- **Koleksi Terkait**: `transactions`, `users`.
- **Operasi CRUD**: `READ` (Koleksi), `CREATE` (via Server Function)
- **Data yang Dibaca**: Nilai `dompet_saldo` dari `users` dan array `transactions` historis.
- **Data yang Dibuat**: Transaksi *Deposit* atau *Withdrawal*. Modifikasi ini dipicu oleh `midtrans-create-fn` dan `withdraw-fn`.
- **Input Pengguna**:
  - Angka nominal (Int/Float).
  - Form bank tujuan untuk withdraw.
- **Kebutuhan Autentikasi**: Ya (Seluruh aktor sistem).

---

### 13. Halaman: **Admin Home / Submissions / Disputes**
- **Tujuan Halaman**: Panel dasbor sentral dan penengah perselisihan (Read-Only/Basic actions di mobile app).
- **Koleksi Terkait**: `campaigns`, `submissions`, `users`, `transactions`, `rate_card_orders`.
- **Operasi CRUD**: `READ`, `UPDATE` (via API Server)
- **Data yang Dibaca**: Agregat statistik (melalui `admin-stats-fn`) untuk membaca laporan *fraud*, GMV, dan *active disputes*.
- **Data yang Diubah**: Eksekusi penentuan *dispute* (`refund-escrow-fn`) dan penandaan kualitas video (`validate-submission-fn`).
- **Kebutuhan Autentikasi**: Ya (Role: ADMIN).