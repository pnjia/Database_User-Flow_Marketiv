# Wireframes: Halaman UMKM

Dokumen ini mendeskripsikan struktur UI (wireframe) untuk halaman-halaman yang digunakan oleh pengguna dengan role **UMKM**.

## 1. Home (Dashboard)
**Tujuan**: Memberikan ringkasan performa kampanye dan keuangan UMKM.

**Komponen Utama**:
- **Header**: Sapaan "Halo, [Nama Usaha]!" dan Ikon Notifikasi.
- **Kartu Ringkasan (Top Section)**:
  - Total Saldo Escrow (Dana tertahan).
  - Total Pengeluaran Bulan Ini.
  - Estimasi ROI (opsional, jika ada data analitik).
- **Section Campaign Berjalan**:
  - Carousel atau list horizontal campaign yang statusnya "Aktif".
  - Tiap item menampilkan: Judul Campaign, Progress Kuota (misal: 5/10 Kreator).
- **Aksi Cepat (Quick Actions)**:
  - Tombol "Buat Campaign Baru".
  - Tombol "Cari Kreator".
- **Bottom Navigation**: Beranda | Kampanye | Kreator | Keuangan | Profil

---

## 2. Daftar Campaign (Campaign Mode)
**Tujuan**: Menampilkan seluruh riwayat campaign yang pernah atau sedang dibuat.

**Komponen Utama**:
- **Header**: Judul "Kampanye Saya".
- **Tabs Filter**: "Semua", "Aktif", "Draft", "Selesai".
- **List Campaign**:
  - Item List: Thumbnail aset, Judul Campaign, Status, Info Kuota.
- **Floating Action Button (FAB)**: Tombol "+" untuk membuat campaign baru.

---

## 3. Buat Campaign Wizard (Pembuatan Campaign)
**Tujuan**: Formulir multi-step untuk membuat proyek *Campaign Mode*.

**Komponen Utama**:
- **Step 1: Info Dasar**
  - Input: Nama Produk/Campaign.
  - Dropdown: Kategori Niche.
  - Textarea: Brief/Instruksi (Terdapat tombol *magic* "Bantu dengan AI").
- **Step 2: Upload Aset**
  - Area unggah file (Drag & Drop atau klik, maks 100MB).
  - Input Link Alternatif (Google Drive/Dropbox).
- **Step 3: Anggaran & Target**
  - Input: Bayaran per 1.000 views (Rp).
  - Input: Batas Maksimal Kuota Kreator.
- **Step 4: Review & Bayar**
  - Ringkasan dari step 1-3.
  - Rincian Total Anggaran (termasuk biaya platform jika ada).
  - Tombol "Bayar Sekarang via Escrow" (Redirect ke Midtrans).

---

## 4. Detail Campaign
**Tujuan**: Melihat detail satu campaign spesifik dan memantau kreator yang mengerjakan.

**Komponen Utama**:
- **Header**: Judul Campaign & Status (Badge).
- **Info Ringkas**: Anggaran, Kuota (Terpakai/Total), Aset (Link).
- **Daftar Submission (Kreator yang mengklaim)**:
  - List Kreator: Nama Kreator, Status (Mengerjakan, Menunggu Validasi, Selesai).
  - *Jika status Menunggu Validasi*: Terdapat link URL Bukti Tayang.

---

## 5. Direktori Kreator (Rate Card Mode)
**Tujuan**: Mencari profil kreator untuk proyek negosiasi langsung.

**Komponen Utama**:
- **Header**: Search Bar (Cari nama atau username).
- **Filter Bar**: Niche, Range Harga, Rating.
- **Grid / List Kreator**:
  - Card Kreator: Foto Profil, Nama, Niche Utama, Harga Mulai Dari (Harga Rate Card termurah), Rating.

---

## 6. Profil Kreator (Tampilan UMKM)
**Tujuan**: Melihat detail portofolio kreator dan harga jasanya.

**Komponen Utama**:
- **Header**: Foto Cover, Foto Profil, Nama, Bio Singkat.
- **Section Portofolio / URL Sosial Media**: Link ke TikTok/IG kreator.
- **Section Rate Card (Daftar Paket Jasa)**:
  - Item Paket (misal: Paket Video 1 Menit, Rp 500.000, Revisi 1x).
- **Floating Area (Bawah)**: Tombol "Hubungi Kreator" (Masuk ke Chat/Negosiasi).

---

## 7. Keuangan
**Tujuan**: Manajemen dana deposit UMKM.

**Komponen Utama**:
- **Kartu Saldo**:
  - Total Saldo Escrow (Dana yang sedang ditahan untuk campaign/rate card aktif).
  - Total Saldo Tersedia (Jika ada sistem deposit di awal).
- **Tombol Aksi Utama**: "Deposit Dana" (Top-up).
- **Riwayat Transaksi**:
  - List transaksi: Deposit Masuk, Pembayaran Campaign X, Pembayaran Rate Card Y.

---

## 8. Profil UMKM
**Tujuan**: Manajemen informasi bisnis UMKM.

**Komponen Utama**:
- **Informasi Akun**: Foto Profil Usaha, Nama Usaha, Email, Nomor WA.
- **Menu Pengaturan**:
  - Edit Profil.
  - Ganti Kata Sandi.
  - Syarat & Ketentuan.
  - Bantuan / Support.
- **Tombol Logout** (Keluar akun).