# Wireframes: Halaman Kreator

Dokumen ini mendeskripsikan struktur UI (wireframe) untuk halaman-halaman yang digunakan oleh pengguna dengan role **Kreator**.

## 1. Home (Dashboard)
**Tujuan**: Memberikan ringkasan performa pendapatan dan pekerjaan kreator.

**Komponen Utama**:
- **Header**: Sapaan "Halo, [Nama Kreator]!" dan Ikon Notifikasi.
- **Kartu Statistik (Top Section)**:
  - Estimasi Pendapatan (Bulan ini atau total dana belum ditarik).
  - Rating Rata-rata.
  - Jumlah Job Selesai.
- **Section Pekerjaan Aktif**:
  - List pekerjaan (Campaign Mode atau Rate Card) yang sedang berjalan dan butuh diselesaikan (Submit Bukti).
- **Bottom Navigation**: Beranda | Job Pool | Pekerjaan | Saldo | Profil

---

## 2. Job Pool (Campaign Mode)
**Tujuan**: Bursa kerja untuk mencari dan mengklaim campaign dari UMKM.

**Komponen Utama**:
- **Header**: Judul "Cari Pekerjaan (Job Pool)" dan Search Bar.
- **Filter**: Kategori/Niche, Urutkan (Terbaru, Harga Tertinggi).
- **Daftar Campaign**:
  - Card Campaign: Nama Produk UMKM, Harga per 1.000 Views, Sisa Kuota (misal: 2/10 tersisa), Label Niche.

---

## 3. Detail Job Pool
**Tujuan**: Melihat detail brief sebelum mengklaim pekerjaan.

**Komponen Utama**:
- **Header**: Judul Campaign UMKM.
- **Detail Harga & Kuota**: Badge Harga/Views, Info Kuota Tersedia.
- **Deskripsi & Brief**: Teks instruksi dari UMKM.
- **Aset Produk**: Tombol "Lihat Aset" (Membuka gambar atau link eksternal).
- **Floating Area (Bawah)**: 
  - Tombol "Klaim Job Ini" (Akan *disabled* jika kuota penuh).

---

## 4. Pekerjaan Aktif
**Tujuan**: Melacak semua pekerjaan yang telah diklaim (Campaign) atau disetujui (Rate Card).

**Komponen Utama**:
- **Tabs Filter**: "Semua", "Sedang Dikerjakan", "Menunggu Validasi".
- **Daftar Pekerjaan**:
  - Card Pekerjaan: Judul, Deadline (jika ada), Status.
  - Aksi pada Card (jika status Sedang Dikerjakan): Tombol "Submit Bukti Tayang".

---

## 5. Submit Bukti Tayang
**Tujuan**: Menyerahkan hasil kerja berupa URL video.

**Komponen Utama**:
- **Header**: Judul "Submit Bukti Tayang".
- **Informasi Singkat**: Nama Campaign yang akan disubmit.
- **Formulir**:
  - Input: URL Publik (Link TikTok, Instagram Reels, atau YouTube Shorts).
  - *Pratinjau Link (opsional, jika URL valid).*
- **Tombol Aksi**: "Kirim Bukti Tayang".

---

## 6. Saldo / Keuangan
**Tujuan**: Manajemen saldo dompet dan proses penarikan dana (Withdrawal).

**Komponen Utama**:
- **Kartu Saldo**:
  - Saldo Tersedia (Dana yang bisa ditarik).
  - Saldo Tertunda (Dana masih di Escrow / Menunggu Validasi UMKM/Admin).
- **Aksi Utama**: Tombol "Tarik Dana" (Withdraw).
- **Riwayat Transaksi**:
  - List transaksi: Dana Masuk dari Job X, Penarikan Berhasil, dsb.
- **Modal/Halaman Tarik Dana (Withdraw)**:
  - Input: Nominal Penarikan.
  - Dropdown: Pilih Bank.
  - Input: Nomor Rekening.
  - Tombol: "Proses Penarikan".

---

## 7. Profil Kreator
**Tujuan**: Manajemen biodata publik dan Rate Card.

**Komponen Utama**:
- **Informasi Akun**: Foto Cover, Foto Profil, Nama, Username, Bio Singkat.
- **Link Sosial Media**: Input/Tampilan link akun TikTok, IG, dll.
- **Menu Navigasi**:
  - "Kelola Rate Card" (Masuk ke halaman manajemen harga jasa).
  - Pengaturan Akun (Ganti Password, dll).
  - Tombol Logout.

---

## 8. Kelola Rate Card
**Tujuan**: Membuat, mengedit, atau menghapus paket jasa.

**Komponen Utama**:
- **Header**: "Kelola Paket Rate Card" (Info: Maksimal 3 Paket).
- **Daftar Paket Saat Ini**:
  - Card Paket: Nama Paket (Misal: Video Review), Harga, Deskripsi Singkat.
  - Aksi Card: Ikon Edit, Ikon Hapus.
- **Tombol Aksi**: "+ Tambah Paket Baru" (Menampilkan formulir tambah paket: Nama, Deskripsi, Harga).