# Halaman Role UMKM

### 1. Halaman: **Home UMKM**
- **Tujuan Halaman**: Menampilkan dasbor metrik untuk UMKM.
- **Koleksi Terkait**: `campaigns`, `transactions`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**:
  - Agregasi dokumen `transactions` (untuk total uang Escrow dan uang Keluar).
  - Jumlah dokumen `campaigns` yang memiliki status `Aktif`.
- **Data yang Diubah**: Tidak ada.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).

### 2. Halaman: **Daftar Campaign (UMKM)**
- **Tujuan Halaman**: Mengelola dan memonitor status seluruh kampanye milik UMKM ybs.
- **Koleksi Terkait**: `campaigns`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Seluruh dokumen dari `campaigns` di mana atribut `umkm_id` cocok dengan akun yang terautentikasi.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).

### 3. Halaman: **Buat Campaign (Wizard - 4 Langkah)**
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

### 4. Halaman: **Direktori Kreator**
- **Tujuan Halaman**: Pencarian dan kurasi portofolio kreator.
- **Koleksi Terkait**: `users`, `rate_cards`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Seluruh profil `users` dengan peran Kreator, serta *nested query* logis ke dokumen `rate_cards` yang terkait dan berstatus `is_active` = `true`.
- **Kebutuhan Autentikasi**: Ya (Role: UMKM).