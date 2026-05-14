# Halaman Role Kreator

### 1. Halaman: **Job Pool (Kreator)**
- **Tujuan Halaman**: Etalase lapangan pekerjaan (kampanye UMKM) untuk Kreator.
- **Koleksi Terkait**: `campaigns`.
- **Operasi CRUD**: `READ`
- **Data yang Dibaca**: Dokumen `campaigns` yang atribut `status` = `Aktif` DAN `kuota_terpakai` < `kuota_kreator`.
- **Input Pengguna**:
  - Teks Search (String)
  - Filter `niche`
  - Range harga
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).

### 2. Halaman: **Detail Job Pool (Klaim Job)**
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

### 3. Halaman: **Pekerjaan Aktif & Submit Bukti (Kreator)**
- **Tujuan Halaman**: Kreator melihat pekerjaannya yang sedang berjalan dan menyerahkan URL (TikTok/IG) sebagai bukti kerja.
- **Koleksi Terkait**: `submissions`, `campaigns`.
- **Operasi CRUD**: `READ`, `UPDATE`
- **Data yang Dibaca**: `submissions` yang `kreator_id` merupakan miliknya yang terhubung relasional logis dengan dokumen di `campaigns`.
- **Data yang Diubah**: Modifikasi pada atribut `url_bukti_tayang`. Pembaruan `status_validasi` dan penarikan escrow dilarang dilakukan client, melainkan oleh Admin (`validate-submission-fn`).
- **Input Pengguna**:
  - `url_bukti_tayang` (URL String: TikTok/Instagram).
- **Kebutuhan Autentikasi**: Ya (Role: KREATOR).

### 4. Halaman: **Kelola Rate Card (Kreator)**
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