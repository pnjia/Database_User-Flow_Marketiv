# Halaman: Dashboard UMKM (Home)

Route: `/umkm/home`

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

Route: `/umkm/campaign`

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

Route: `/umkm/campaign/create`

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

Route: `/umkm/campaign/:id`

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

Route: `/umkm/kreator`

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

Route: `/umkm/kreator/:id`

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

# Halaman: Keuangan (Dompet & Saldo UMKM)

Route: `/umkm/keuangan`

Tujuan:
Melihat mutasi saldo, melakukan top-up deposit (UMKM).

Section:
- Dompet Header
- Aksi Saldo
- Riwayat Transaksi

Komponen:
## Dompet Header
- saldo escrow text (UMKM)
- riwayat pemasukan/pengeluaran total label

## Aksi Saldo
- (UMKM) top up deposit primary button

## Riwayat Transaksi
- filter tabs (Semua, Deposit, Penarikan, Escrow)
- list view transaction items
- transaction card (Tipe, Nominal merah/hijau, Status Badge, Tanggal, Keterangan)

Data Ditampilkan:
- Atribut `dompet_saldo` dari `users`.
- Daftar dokumen `transactions` milik pengguna.

Input Pengguna:
- (UMKM) Input nominal Top-Up.

Aksi/Interaksi Pengguna:
- Mengisi saldo ke sistem Escrow Marketiv.

Integrasi Appwrite:
- collection: users
- collection: transactions
- Appwrite Functions: `midtrans-create-fn`

Dokumen:
- read: dokumen `users`, dokumen `transactions`.

Autentikasi:
- Wajib Login (Role: UMKM)

Catatan Dependency:
- Semua proses manipulasi dana dilakukan melalui Server Functions. Transaksi deposit membuka WebView Midtrans.