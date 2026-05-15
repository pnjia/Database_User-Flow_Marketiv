# Spesifikasi Wireframe Marketiv

Dokumen ini berisi spesifikasi wireframe low-fidelity untuk setiap halaman aplikasi Marketiv guna membantu perencanaan UI/UX dan implementasi frontend.

---

# Halaman: Splash Screen

Route:
`/splash`

Tujuan:
Mengecek sesi aktif Appwrite dan mengarahkan pengguna ke halaman yang sesuai.

Section:
- Main Container

Komponen:
## Main Container
- app logo image
- loading spinner (CircularProgressIndicator)

Data Ditampilkan:
- Indikator pemuatan

Input Pengguna:
- Tidak ada (Otomatis)

Aksi/Interaksi Pengguna:
- Tidak ada, sistem akan langsung menavigasi pengguna setelah sesi dicek.

Integrasi Appwrite:
- collection: users (untuk mengambil data Role)
- Service: Appwrite Account API

Dokumen:
- read: session JWT, document `users`

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Navigasi otomatis ke `/onboarding` (jika pengguna baru/tidak login) atau ke `/login`
- Navigasi otomatis ke Home sesuai role (UMKM/Kreator/Admin) jika sesi aktif.

---

# Halaman: Onboarding

Route:
`/onboarding`

Tujuan:
Memberikan penjelasan singkat tentang aplikasi untuk pengguna baru.

Section:
- Carousel Info
- Navigation Footer

Komponen:
## Carousel Info
- illustration image
- headline text
- description text
- pagination dots

## Navigation Footer
- skip text button
- next primary button
- get started primary button

Data Ditampilkan:
- Teks edukasi/fitur aplikasi (Statis)

Input Pengguna:
- swipe layar
- klik tombol navigasi

Aksi/Interaksi Pengguna:
- Menggeser (swipe) informasi.
- Menekan tombol "Lanjut" atau "Mulai".
- Menavigasi ke halaman Register/Login.

Integrasi Appwrite:
- Tidak ada

Dokumen:
- Tidak ada

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Mengarahkan ke halaman `/login` atau `/role-selection`.

---

# Halaman: Login

Route:
`/login`

Tujuan:
Autentikasi pengguna lama.

Section:
- Header
- Formulir Login
- Bantuan / Opsi Tambahan

Komponen:
## Header
- logo image
- welcome headline text

## Formulir Login
- email text input (AuthTextField)
- password text input (AuthTextField dengan toggle obscure)
- submit primary button
- error message text (conditional)

## Bantuan / Opsi Tambahan
- forgot password text button
- register text button / rich text

Data Ditampilkan:
- Pesan kesalahan (error) jika kredensial salah (dinamis).

Input Pengguna:
- email
- password
- klik masuk
- klik daftar

Aksi/Interaksi Pengguna:
- Mengisi email dan kata sandi.
- Menekan tombol "Masuk".

Integrasi Appwrite:
- collection: users
- Service: Appwrite Account API

Dokumen:
- read: document `users` (untuk mengambil `role`)
- create: Appwrite session (JWT)

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Jika sukses, akan dialihkan ke Home UMKM (`/umkm/home`), Home Kreator (`/kreator/home`), atau Home Admin (`/admin`).

---

# Halaman: Role Selection & Register

Route:
`/register` dan `/role-selection`

Tujuan:
Mendaftarkan akun pengguna baru dengan pemilihan peran UMKM atau Kreator.

Section:
- Header
- Pemilihan Peran (Role)
- Formulir Registrasi
- Footer

Komponen:
## Header
- logo image
- register headline text

## Pemilihan Peran (Role)
- role selection cards (UMKM & Kreator) dengan highlight/border aktif

## Formulir Registrasi
- nama lengkap text input
- email text input
- whatsapp number text input
- password text input
- nama usaha text input (Conditional: Hanya tampil jika role UMKM)
- username kreator text input (Conditional: Hanya tampil jika role Kreator)
- submit primary button

## Footer
- login redirect rich text

Data Ditampilkan:
- Pesan kesalahan validasi form.
- Konfirmasi email verifikasi.

Input Pengguna:
- pilihan role
- nama lengkap
- email
- nomor whatsapp
- password
- nama usaha / username kreator
- klik daftar

Aksi/Interaksi Pengguna:
- Memilih kartu peran (Role).
- Mengisi formulir data diri dan bisnis/kreator.
- Menekan tombol "Daftar".

Integrasi Appwrite:
- collection: users
- Service: Appwrite Account API

Dokumen:
- create: account document
- create: document `users` baru

Autentikasi:
- Publik (Tidak wajib login)

Catatan Dependency:
- Jika sukses, akan dialihkan kembali ke `/login`.

---

# Halaman: Dashboard UMKM (Home)

Route:
`/umkm/home`

Tujuan:
Menampilkan dasbor metrik dan ringkasan data untuk UMKM.

Section:
- Profil Header
- Ringkasan Statistik
- Campaign Aktif (Shortcut)

Komponen:
## Profil Header
- user avatar image
- greeting text
- notification icon button

## Ringkasan Statistik
- total pengeluaran stat card
- total campaign aktif stat card

## Campaign Aktif
- section title
- list campaign aktif (horizontal scroll/list tile)
- view all text button

Data Ditampilkan:
- Data agregasi pengeluaran UMKM.
- Jumlah dan daftar `campaigns` yang berstatus `Aktif`.
- Nama UMKM.

Input Pengguna:
- klik notifikasi
- klik campaign aktif
- klik lihat semua

Aksi/Interaksi Pengguna:
- Meninjau pengeluaran dan status proyek pemasaran.
- Menavigasi ke detail campaign.

Integrasi Appwrite:
- collection: campaigns
- collection: transactions

Dokumen:
- read: dokumen `campaigns` (filter `umkm_id`)
- read: agregasi dokumen `transactions` (via Appwrite Function/Client Filter)

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Merupakan root dari Bottom Navigation UMKM.

---

# Halaman: Daftar Campaign UMKM

Route:
`/umkm/campaign`

Tujuan:
Menampilkan semua campaign yang pernah atau sedang dibuat oleh UMKM.

Section:
- Header Action
- Filter Tabs
- Daftar Campaign

Komponen:
## Header Action
- page title text
- create campaign primary button (+ Icon)

## Filter Tabs
- tab bar (Semua, Aktif, Selesai, Draft)

## Daftar Campaign
- campaign list view
- campaign card (berisi: thumbnail, judul, status badge, harga, tanggal)
- empty state widget (jika kosong)

Data Ditampilkan:
- Detail ringkas dari masing-masing campaign (judul, status, biaya, kuota).

Input Pengguna:
- klik tab filter
- klik tombol buat campaign baru
- klik kartu campaign

Aksi/Interaksi Pengguna:
- Melihat riwayat campaign.
- Menavigasi ke Wizard Pembuatan Campaign.
- Menavigasi ke Detail Campaign.

Integrasi Appwrite:
- collection: campaigns

Dokumen:
- read: dokumen `campaigns` berdasarkan `umkm_id`

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Menuju `/umkm/campaign/create` atau `/umkm/campaign/:id`.

---

# Halaman: Buat Campaign (Wizard)

Route:
`/umkm/campaign/create`

Tujuan:
Formulir multi-step untuk membuat proyek Campaign Mode.

Section:
- Progress Stepper
- Step 1: Info Produk
- Step 2: Upload Aset
- Step 3: Anggaran & Kuota
- Step 4: Ringkasan & Pembayaran
- Navigasi Wizard

Komponen:
## Progress Stepper
- step indicator (1, 2, 3, 4)

## Step 1: Info Produk
- judul campaign text input
- niche dropdown menu
- deskripsi brief multiline text input
- generate AI brief secondary button (Aksi AI)

## Step 2: Upload Aset
- image picker area / upload button
- external link text input (Google Drive/Dropbox)
- warning banner (Info limit 100MB)

## Step 3: Anggaran & Kuota
- bayaran per 1000 views number input
- kuota kreator number input / stepper
- estimasi biaya card (Total budget = bayaran * target + fee platform)

## Step 4: Ringkasan & Pembayaran
- ringkasan data statis
- total bayar display
- pay via midtrans primary button

## Navigasi Wizard
- back text button
- next primary button

Data Ditampilkan:
- Data masukan sementara di setiap tahap.
- Hasil teks draft AI.
- Kalkulasi total pembayaran Escrow.

Input Pengguna:
- mengisi form teks & angka
- memilih niche
- mengunggah gambar / link
- memicu *AI Brief*
- navigasi maju/mundur
- klik bayar

Aksi/Interaksi Pengguna:
- Melengkapi spesifikasi proyek langkah demi langkah.
- Menggunakan bantuan AI.
- Membayar deposit ke Escrow via Midtrans WebView.

Integrasi Appwrite:
- collection: campaigns
- Service: Storage (bucket `campaign_assets`)
- Appwrite Functions: `generate-brief-fn`, `midtrans-create-fn`

Dokumen:
- create: dokumen `campaigns` (status awal: `Draft`)
- write: file aset ke storage

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Bergantung pada keberhasilan Webhook Midtrans (`midtrans-webhook-fn`) untuk merubah status dari Draft menjadi Aktif.

---

# Halaman: Detail Campaign UMKM

Route:
`/umkm/campaign/:id`

Tujuan:
Melihat status rinci sebuah campaign dan daftar bukti tayang dari kreator.

Section:
- Informasi Campaign
- Statistik Kuota
- Daftar Submission Kreator

Komponen:
## Informasi Campaign
- campaign image thumbnail
- campaign title text
- status badge
- brief description text
- external asset link button

## Statistik Kuota
- progress bar kuota (terpakai vs total)
- ringkasan budget

## Daftar Submission Kreator
- list view submissions
- kreator profile row (avatar, nama)
- bukti tayang url button
- views count text
- status validasi badge

Data Ditampilkan:
- Judul, brief, link aset, status, harga, dan kuota dari `campaigns`.
- Daftar `submissions` dari berbagai kreator yang terkait dengan ID ini.

Input Pengguna:
- klik link aset
- klik link bukti tayang

Aksi/Interaksi Pengguna:
- Memantau progres jumlah kreator yang klaim.
- Memeriksa tautan video TikTok/IG hasil karya kreator.

Integrasi Appwrite:
- collection: campaigns
- collection: submissions

Dokumen:
- read: 1 dokumen `campaigns`
- read: n dokumen `submissions`

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Data submission diupdate oleh Kreator, status validasinya diatur oleh Admin.

---

# Halaman: Direktori Kreator

Route:
`/umkm/kreator`

Tujuan:
Mencari profil dan Rate Card kreator untuk proyek negosiasi (fixed-price).

Section:
- Search & Filter Bar
- Daftar Profil Kreator

Komponen:
## Search & Filter Bar
- search text input
- niche filter dropdown / chips
- sort by dropdown (harga/rating)

## Daftar Profil Kreator
- kreator grid/list view
- kreator card (avatar, nama_kreator, niche, list harga rate card mini)

Data Ditampilkan:
- Daftar `users` (Role=Kreator).
- Potongan ringkas dari `rate_cards` milik kreator.

Input Pengguna:
- mengetik pencarian
- memfilter niche
- klik kartu kreator

Aksi/Interaksi Pengguna:
- Menjelajahi talenta kreator mikro.
- Masuk ke Detail Profil Kreator.

Integrasi Appwrite:
- collection: users
- collection: rate_cards

Dokumen:
- read: daftar dokumen `users` (Role: Kreator)
- read: dokumen `rate_cards` (is_active: true)

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Menavigasi ke `/umkm/kreator/:id`.

---

# Halaman: Profil Kreator (View dari UMKM)

Route:
`/umkm/kreator/:id`

Tujuan:
Melihat portofolio, bio, dan detail paket Rate Card seorang kreator.

Section:
- Header Profil
- Bio & Portofolio
- Daftar Paket Layanan (Rate Card)
- Floating Action Bar

Komponen:
## Header Profil
- besar avatar image
- nama kreator text
- niche label

## Bio & Portofolio
- bio description text

## Daftar Paket Layanan
- list view rate cards
- rate card item (nama paket, deskripsi, harga, estimasi hari)

## Floating Action Bar
- contact creator primary button (Hubungi Kreator)

Data Ditampilkan:
- Profil detail `users`.
- Daftar 3 dokumen (maksimal) `rate_cards`.

Input Pengguna:
- klik hubungi kreator

Aksi/Interaksi Pengguna:
- Mempelajari layanan kreator.
- Memulai negosiasi pesanan baru (berpindah ke Chat Room).

Integrasi Appwrite:
- collection: users
- collection: rate_cards

Dokumen:
- read: 1 dokumen `users`
- read: n dokumen `rate_cards`

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Aksi "Hubungi Kreator" akan meng-create `rate_card_orders` berstatus 'Negosiasi', lalu navigasi ke `/umkm/negosiasi/:order_id`.

---

# Halaman: Chat Room (UMKM & Kreator)

Route:
`/umkm/negosiasi/:id` & `/kreator/negosiasi/:id`

Tujuan:
Berkomunikasi realtime, negosiasi, dan menyepakati "Custom Offer".

Section:
- App Bar
- Message List Area
- Chat Input Area

Komponen:
## App Bar
- back button
- nama lawan bicara text
- status order badge (Negosiasi / Escrow / dsb.)

## Message List Area
- list view pesanan (scrollable, reverse)
- message bubble (Teks)
- custom offer bubble (Khusus berisi: Judul Proyek, Scope, Harga, Tombol Terima/Tolak untuk Kreator)
- system message text (Di tengah)

## Chat Input Area
- send custom offer icon button (+ action menu di UMKM)
- message text input
- send icon button

Data Ditampilkan:
- Obrolan `messages` (Teks biasa, Penawaran Khusus, Info Sistem).
- Status transaksi `rate_card_orders`.

Input Pengguna:
- mengetik pesan
- mengirim pesan
- (UMKM) mengisi form popup "Custom Offer" (Judul, Scope, Harga, Deadline)
- (Kreator) klik Terima/Tolak Offer
- (UMKM) klik Bayar / Selesai

Aksi/Interaksi Pengguna:
- Mengobrol secara Realtime.
- Mengirim dan merespons penawaran kerja sama.

Integrasi Appwrite:
- collection: messages
- collection: rate_card_orders
- Service: Appwrite Realtime

Dokumen:
- read/write: dokumen `messages` (Realtime subscription)
- read/update: dokumen `rate_card_orders`

Autentikasi:
- Wajib Login (Dua belah pihak)

Catatan Dependency:
- Bergantung pada WebSocket Appwrite Realtime.
- Saat UMKM membayar deposit, terhubung ke Midtrans.

---

# Halaman: Job Pool (Kreator)

Route:
`/kreator/job-pool`

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

Route:
`/kreator/job-pool/:id`

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

Route:
`/kreator/pekerjaan-aktif` & `/kreator/pekerjaan-aktif/:id`

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

Route:
`/kreator/rate-card`

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

# Halaman: Keuangan (Dompet & Saldo)

Route:
`/umkm/keuangan` & `/kreator/keuangan`

Tujuan:
Melihat mutasi saldo, melakukan top-up deposit (UMKM), atau menarik dana (Kreator).

Section:
- Dompet Header
- Aksi Saldo
- Riwayat Transaksi

Komponen:
## Dompet Header
- saldo escrow text (UMKM) / saldo tersedia text (Kreator)
- riwayat pemasukan/pengeluaran total label

## Aksi Saldo
- (UMKM) top up deposit primary button
- (Kreator) tarik dana (withdrawal) primary button

## Riwayat Transaksi
- filter tabs (Semua, Deposit, Penarikan, Escrow)
- list view transaction items
- transaction card (Tipe, Nominal merah/hijau, Status Badge, Tanggal, Keterangan)

Data Ditampilkan:
- Atribut `dompet_saldo` dari `users`.
- Daftar dokumen `transactions` milik pengguna.

Input Pengguna:
- (UMKM) Input nominal Top-Up.
- (Kreator) Input form rekening penarikan Bank.
- (Kreator) Input nominal withdraw.

Aksi/Interaksi Pengguna:
- Mengisi saldo ke sistem Escrow Marketiv.
- Mencairkan pendapatan ke rekening bank pribadi.

Integrasi Appwrite:
- collection: users
- collection: transactions
- Appwrite Functions: `midtrans-create-fn`, `withdraw-fn`

Dokumen:
- read: dokumen `users`, dokumen `transactions`.

Autentikasi:
- Wajib Login (Sesuai Role)

Catatan Dependency:
- Semua proses manipulasi dana dilakukan melalui Server Functions. Transaksi deposit membuka WebView Midtrans.

---

# Halaman: Profil & Edit Profil

Route:
`/profile/edit`

Tujuan:
Memperbarui informasi biodata, identitas bisnis, dan kredensial akun.

Section:
- Avatar Editor
- Form Identitas Dasar
- Auth Management
- System Logout

Komponen:
## Avatar Editor
- profile picture image / placeholder
- change photo text button

## Form Identitas Dasar
- nama lengkap text input
- nomor whatsapp text input
- nama usaha text input (Hanya UMKM)
- username kreator text input (Hanya Kreator)
- niche dropdown (Kreator)
- bio multiline text input
- save profile primary button

## System Logout
- logout danger button

Data Ditampilkan:
- Data `users` saat ini.

Input Pengguna:
- unggah foto
- edit teks form
- klik simpan
- klik logout

Aksi/Interaksi Pengguna:
- Memodifikasi representasi profil kepada pengguna lain.
- Keluar dari sesi aplikasi.

Integrasi Appwrite:
- collection: users
- Service: Storage (`profile_images`), Account API

Dokumen:
- read/update: dokumen `users`
- write: storage bucket avatar
- update/delete: session di Account API

Autentikasi:
- Wajib Login

Catatan Dependency:
- Merupakan navigasi menu paling ujung di Bottom Navigation Bar semua role.

---

# Halaman: Admin Dashboard & Submissions

Route:
`/admin`, `/admin/submissions`, `/admin/disputes`, `/admin/reports`

Tujuan:
Memonitor performa platform dan menyelesaikan masalah validasi serta sengketa.

Section:
- Ringkasan Metrik
- Tabel/List Validasi Submissions
- Daftar Sengketa (Disputes)

Komponen:
## Ringkasan Metrik (Admin Home)
- gmv stat card
- active user stat card
- dispute count alert card

## Validasi Submissions
- filter pending/valid/fraud
- submission list item (Info Kreator, URL Video, Status)
- open url icon button
- mark valid action button
- mark fraud danger action button

## Daftar Sengketa
- dispute list item (Nama UMKM, Nama Kreator, Dana Escrow)
- resolve modal / action sheet (Refund ke UMKM, atau Teruskan ke Kreator)

Data Ditampilkan:
- Agregasi data aplikasi (Total Dana, Jumlah Campaign, dll).
- Dokumen `submissions` dan `rate_card_orders` yang butuh intervensi.

Input Pengguna:
- klik tombol aksi (Valid, Fraud, Refund, dll)

Aksi/Interaksi Pengguna:
- Memastikan tidak ada kecurangan perolehan *views*.
- Mengatur aliran uang Escrow pada kasus bermasalah.

Integrasi Appwrite:
- collection: campaigns, submissions, rate_card_orders, users
- Appwrite Functions: `admin-stats-fn`, `validate-submission-fn`, `refund-escrow-fn`

Dokumen:
- read: Lintas collections
- update: Status dokumen melalui Functions.

Autentikasi:
- Wajib Login (Role: ADMIN)

Catatan Dependency:
- Aksi admin adalah keputusan final (Otoritas tertinggi di platform) yang langsung mengubah aliran dana di tabel `transactions`.
