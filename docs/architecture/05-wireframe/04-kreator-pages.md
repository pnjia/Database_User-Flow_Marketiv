# Halaman: Job Pool (Kreator)

Route: `/kreator/job-pool`

Tujuan:
Menampilkan daftar pekerjaan Campaign Mode yang siap diklaim.

Section:
- Filter Bar
- Daftar Pekerjaan

Komponen:
## Filter Bar
- search input
- niche filter chips

## Daftar Pekerjaan
- job card list view
- job card (thumbnail, nama brand, harga/1000 views, kuota progress bar, label status)
- claim primary button (Disabled jika kuota penuh)

Data Ditampilkan:
- Daftar `campaigns` (Status `Aktif`, Kuota belum penuh).

Input Pengguna:
- pencarian / filter
- klik job card untuk detail

Aksi/Interaksi Pengguna:
- Mencari campaign yang cocok dengan niche mereka.

Integrasi Appwrite:
- collection: campaigns

Dokumen:
- read: dokumen `campaigns`

Autentikasi:
- Wajib Login (Role: KREATOR)

Catatan Dependency:
- Navigasi ke `/kreator/job-pool/:id`.

---

# Halaman: Detail Job Pool & Klaim (Kreator)

Route: `/kreator/job-pool/:id`

Tujuan:
Membaca instruksi proyek UMKM sebelum mengklaim pekerjaan.

Section:
- Header Campaign
- Instruksi Brief
- Resource / Aset
- Floating Claim Action

Komponen:
## Header Campaign
- campaign image thumbnail
- campaign title text
- brand name text
- price info text

## Instruksi Brief
- brief description text block

## Resource / Aset
- download / view asset link button

## Floating Claim Action
- remaining quota text info
- claim job primary button (Bisa berubah "Kuota Penuh" / Disabled)

Data Ditampilkan:
- Informasi detail `campaigns`.

Input Pengguna:
- klik tombol klaim job
- klik lihat aset eksternal

Aksi/Interaksi Pengguna:
- Mempelajari kebutuhan konten.
- Mengamankan pekerjaan (Atomic Claim).

Integrasi Appwrite:
- collection: campaigns
- collection: submissions
- Appwrite Functions: `claim-campaign-fn`

Dokumen:
- read: dokumen `campaigns`
- create: dokumen `submissions` (dijalankan oleh Function Server)

Autentikasi:
- Wajib Login (Role: KREATOR)

Catatan Dependency:
- Jika klaim berhasil, dialihkan ke `/kreator/pekerjaan-aktif`.

---

# Halaman: Pekerjaan Aktif & Submit Bukti (Kreator)

Route: `/kreator/pekerjaan-aktif` & `/kreator/pekerjaan-aktif/:id`

Tujuan:
Melacak tugas berjalan dan formulir untuk mengirimkan link bukti tayang konten.

Section:
- Daftar Pekerjaan Aktif (List)
- Form Submit Bukti (Detail)

Komponen:
## Daftar Pekerjaan Aktif
- list view pekerjaan berjalan (campaign maupun rate card yang escrow)
- status indicator

## Form Submit Bukti (Pada Halaman Detail)
- informasi tugas header
- url bukti tayang text input (Validasi regex TikTok/IG)
- submit URL primary button
- validation status badge (Pending / Valid / Fraud)

Data Ditampilkan:
- Detail `submissions` dan hubungannya dengan `campaigns`.
- Status validasi admin.

Input Pengguna:
- copy/paste URL konten TikTok/IG
- klik submit

Aksi/Interaksi Pengguna:
- Menyerahkan pekerjaan.
- Menunggu validasi untuk pencairan dana.

Integrasi Appwrite:
- collection: submissions

Dokumen:
- read/update: dokumen `submissions` (Modifikasi URL)

Autentikasi:
- Wajib Login (Role: KREATOR)

Catatan Dependency:
- Status "Valid" akan dieksekusi oleh Admin via `validate-submission-fn` yang memicu pemindahan saldo otomatis.

---

# Halaman: Kelola Rate Card (Kreator)

Route: `/kreator/rate-card`

Tujuan:
Menambah, mengedit, atau menghapus paket jasa kreator (Maks 3).

Section:
- Daftar Paket Harga
- Form Modal / Bottom Sheet (Edit/Tambah)

Komponen:
## Daftar Paket Harga
- add new rate card outline button (Hilang jika sudah 3)
- rate card item card (Judul, Harga, Deskripsi, Switch Aktif/Nonaktif)
- edit icon button
- delete icon button

## Form Modal
- nama paket text input
- deskripsi layanan multiline input
- harga paket number input
- deliverable text input
- estimasi waktu number input
- save primary button

Data Ditampilkan:
- Dokumen `rate_cards` milik pengguna saat ini.

Input Pengguna:
- teks & harga layanan
- toggle aktif/nonaktif
- klik simpan/hapus

Aksi/Interaksi Pengguna:
- Mengatur harga jasa tetap untuk etalase direktori.

Integrasi Appwrite:
- collection: rate_cards

Dokumen:
- read, create, update, delete: dokumen `rate_cards`

Autentikasi:
- Wajib Login (Role: KREATOR)

Catatan Dependency:
- Data ini yang akan dibaca oleh UMKM di Halaman Direktori Kreator.

---

# Halaman: Keuangan (Dompet & Saldo Kreator)

Route: `/kreator/keuangan`

Tujuan:
Melihat mutasi saldo, menarik dana (Kreator).

Section:
- Dompet Header
- Aksi Saldo
- Riwayat Transaksi

Komponen:
## Dompet Header
- saldo tersedia text (Kreator)
- riwayat pemasukan/pengeluaran total label

## Aksi Saldo
- (Kreator) tarik dana (withdrawal) primary button

## Riwayat Transaksi
- filter tabs (Semua, Deposit, Penarikan, Escrow)
- list view transaction items
- transaction card (Tipe, Nominal merah/hijau, Status Badge, Tanggal, Keterangan)

Data Ditampilkan:
- Atribut `dompet_saldo` dari `users`.
- Daftar dokumen `transactions` milik pengguna.

Input Pengguna:
- (Kreator) Input form rekening penarikan Bank.
- (Kreator) Input nominal withdraw.

Aksi/Interaksi Pengguna:
- Mencairkan pendapatan ke rekening bank pribadi.

Integrasi Appwrite:
- collection: users
- collection: transactions
- Appwrite Functions: `withdraw-fn`

Dokumen:
- read: dokumen `users`, dokumen `transactions`.

Autentikasi:
- Wajib Login (Role: KREATOR)

Catatan Dependency:
- Semua proses manipulasi dana dilakukan melalui Server Functions.