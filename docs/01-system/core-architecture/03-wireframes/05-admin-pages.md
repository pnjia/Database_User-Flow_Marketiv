# Wireframes: Halaman Admin (Dashboard Khusus)

Dokumen ini mendeskripsikan struktur UI (wireframe) untuk halaman yang diakses oleh **Admin** platform Marketiv. Antarmuka ini biasanya dioptimalkan untuk tampilan *Desktop/Web*, namun dapat berstatus *Read-Only* secara responsif jika diakses di Mobile.

## 1. Admin Home (Dashboard Utama)
**Tujuan**: Memberikan pandangan luas (bird's-eye view) terhadap performa dan status platform Marketiv.

**Komponen Utama**:
- **Sidebar Menu**: Dashboard | Disputes | Submissions | Laporan | Pengaturan Platform
- **Header**: Admin Profile, Tombol Logout.
- **Key Metrics (Widget Angka)**:
  - Total GMV (Gross Merchandise Value) bulan ini.
  - Jumlah Pengguna Aktif (UMKM & Kreator).
  - Jumlah Transaksi Escrow yang sedang berjalan.
  - Sengketa Baru (Disputes yang perlu ditindaklanjuti).
- **Grafik / Chart**:
  - Tren pendaftaran pengguna baru.
  - Tren transaksi mingguan.

---

## 2. Disputes (Manajemen Sengketa)
**Tujuan**: Mengelola konflik atau sengketa transaksi pada *Rate Card Mode* antara UMKM dan Kreator.

**Komponen Utama**:
- **Header & Filter**: Cari ID Order, Filter Status (Baru, Sedang Diproses, Selesai).
- **Tabel Sengketa**:
  - Kolom: ID Transaksi, Nama UMKM, Nama Kreator, Tanggal Sengketa, Alasan (Komplain), Status.
  - Aksi: Tombol "Lihat Detail".
- **Halaman Detail Sengketa**:
  - Rangkuman percakapan chat (Chat History log).
  - Bukti pekerjaan dari Kreator (URL Video).
  - Klaim dari UMKM.
  - **Action Panel Admin**: 
    - Tombol "Tahan Dana" (Hold Escrow).
    - Tombol "Refund ke UMKM".
    - Tombol "Teruskan Dana ke Kreator".

---

## 3. Submissions (Validasi Bukti Tayang)
**Tujuan**: Halaman untuk memantau, memvalidasi (jika manual), atau mengawasi proses submission *Campaign Mode*.

**Komponen Utama**:
- **Filter Bar**: Campaign ID, Status (Pending, Valid, Fraud).
- **Tabel Bukti Tayang (Submissions)**:
  - Kolom: Nama Kreator, Link URL Bukti, Jumlah Views Tercatat, Keputusan Sistem.
- **Aksi Cepat (Quick Actions)**:
  - Tombol "Tandai Valid" (Manual override jika sistem otomatis gagal).
  - Tombol "Tandai Fraud" (Jika terdeteksi manipulasi views/bot).

---

## 4. Laporan (Reports)
**Tujuan**: Menghasilkan ringkasan laporan keuangan dan platform.

**Komponen Utama**:
- **Panel Generator Laporan**:
  - Pilih Rentang Waktu (Tanggal Mulai - Tanggal Akhir).
  - Pilih Jenis Laporan (Keuangan, Transaksi Sukses, Pendaftaran User).
- **Tampilan Tabel Ringkasan**: Pratinjau data.
- **Tombol Ekspor**: "Download CSV" atau "Download PDF" untuk pelaporan pajak/bisnis internal Marketiv.