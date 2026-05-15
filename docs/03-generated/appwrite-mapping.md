# Appwrite Mapping & Arsitektur Backend Marketiv

Dokumen ini merinci peta teknis integrasi Flutter dengan BaaS Appwrite yang diterapkan dalam proyek Marketiv. Proyek ini tidak menggunakan SQL konvensional, melainkan mengandalkan struktur NoSQL Documents dan eksekusi Serverless Functions.

## 1. Setup & Konfigurasi Dasar

- **Database ID:** `marketiv_db`
- **Bucket Profil:** `profile_images`
- **Bucket Campaign:** `campaign_assets`
- **Autentikasi:** Email & Password menggunakan Appwrite Account SDK. Sesi dikelola murni oleh SDK Appwrite (JWT tersimpan secara native).

---

## 2. Database Collections (NoSQL)

Berikut adalah rincian detail setiap koleksi (tabel) yang ada di dalam database `marketiv_db`.

### 2.1 Collection: `USERS`
Menyimpan identitas semua aktor sistem (UMKM, Kreator, Admin). Role tidak ditangani oleh label Appwrite Team, melainkan disimpan sebagai atribut dalam koleksi ini.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `user_id` | String | ✅ | — | Sama dengan Appwrite Auth `$id` (link ke account) |
| `role` | Enum | ✅ | — | `UMKM` / `KREATOR` / `ADMIN` |
| `nama_lengkap` | String | ✅ | — | Nama pribadi pemilik akun / staff |
| `nama_usaha` | String | ❌ | null | Nama brand/usaha, wajib jika `role = UMKM` |
| `username_kreator` | String | ❌ | null | Handle/username publik, wajib jika `role = KREATOR` |
| `email` | Email | ✅ | — | Duplikat dari Auth untuk kemudahan query |
| `nomor_whatsapp` | String | ✅ | — | Nomor WhatsApp pengguna |
| `dompet_saldo` | Float | ✅ | 0 | Saldo kreator yang bisa di-withdraw |
| `niche` | String | ❌ | null | Kategori spesifik (kuliner, fesyen, edukasi, dll) |
| `foto_profil_url` | String | ❌ | null | URL gambar dari Appwrite Storage |
| `bio` | String | ❌ | null | Deskripsi singkat pengguna |
| `is_verified` | Boolean | ✅ | false | Status verifikasi email |
| `fcm_token` | String | ❌ | null | Token perangkat untuk notifikasi (Size 255) |
| `unread_count` | Integer | ✅ | 0 | Jumlah notifikasi belum dibaca (Min 0) |

**Pola Izin (Permissions):**
- **CREATE:** `users` (semua yang terautentikasi)
- **READ:** `user:{$id}` (Hanya pemilik dokumen)
- **UPDATE:** `user:{$id}` (Hanya pemilik dokumen)
- **DELETE:** NONE (User tidak bisa menghapus akun sendiri, hanya Admin via Function)

### 2.2 Collection: `CAMPAIGNS`
Menyimpan semua proyek pemasaran (Campaign Mode) yang dibuat oleh UMKM.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `umkm_id` | String | ✅ | — | Appwrite Auth `$id` dari UMKM pembuat |
| `judul_campaign` | String | ✅ | — | max 200 char |
| `deskripsi_brief` | String | ✅ | — | Instruksi pembuatan konten video |
| `url_aset_eksternal`| String | ✅ | — | Link Google Drive/Dropbox berisi raw file |
| `kuota_kreator` | Integer | ✅ | — | Total kreator yang dibutuhkan |
| `kuota_terpakai` | Integer | ✅ | 0 | Jumlah kreator yang sudah mengklaim |
| `harga_per_1000_views`| Float | ✅ | — | Bayaran views (Range: 2000 - 10000) |
| `total_budget_escrow` | Float | ✅ | — | Dana total yang tertahan (di-lock) |
| `niche` | String | ❌ | null | Kategori campaign |
| `status` | Enum | ✅ | `Draft` | `Draft`, `Aktif`, `Penuh`, `Selesai`, `Dibatalkan` |

**Pola Izin (Permissions):**
- **CREATE:** `user:{umkm_id}` (Hanya UMKM pembuat)
- **READ:** `users` (Semua user login bisa melihat)
- **UPDATE:** `user:{umkm_id}` (Atau via Appwrite Function untuk sinkronisasi `kuota_terpakai`)
- **DELETE:** NONE

### 2.3 Collection: `SUBMISSIONS`
Menyimpan penyerahan bukti hasil pekerjaan kreator terhadap sebuah campaign.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `campaign_id` | String | ✅ | — | Referensi `$id` ke `campaigns` |
| `kreator_id` | String | ✅ | — | Appwrite Auth `$id` dari kreator |
| `url_bukti_tayang` | String | ✅ | — | URL Publik konten (TikTok/Instagram) |
| `jumlah_views_target` | Integer| ❌ | null | Target opsional jika ada |
| `jumlah_views_aktual` | Integer| ✅ | 0 | Jumlah penayangan tercapai |
| `status_validasi` | Enum | ✅ | `Pending`| `Pending`, `Valid`, `Fraud`, `Dispute` |
| `dana_dicairkan` | Float | ✅ | 0 | Total dana yang dikirim ke dompet kreator |

**Pola Izin (Permissions):**
- **CREATE:** `users` (Kreator yang terautentikasi)
- **READ:** `user:{kreator_id}` (UMKM dan Admin membaca ini via eksekusi Appwrite Function)
- **UPDATE:** NONE dari client (Update status dilakukan oleh Appwrite Function)
- **DELETE:** NONE

### 2.4 Collection: `RATE_CARDS`
Menyimpan paket layanan harga pasti (Rate Card Mode) milik Kreator. Maksimal 3 paket per Kreator.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `kreator_id` | String | ✅ | — | Appwrite Auth `$id` dari kreator |
| `nama_paket` | String | ✅ | — | Nama/Judul layanan (max 100 char) |
| `deskripsi_paket` | String | ❌ | null | Rincian layanan |
| `harga_paket` | Float | ✅ | — | Nilai harga jasa tetap |
| `deliverable` | String | ❌ | null | Hasil yang diberikan |
| `estimasi_hari` | Integer| ❌ | null | Estimasi lama pengerjaan |
| `is_active` | Boolean| ✅ | true | Status ketersediaan paket |

**Pola Izin (Permissions):**
- **CREATE:** `user:{kreator_id}`
- **READ:** `any` (Terbuka untuk dibaca semua pengguna/publik)
- **UPDATE:** `user:{kreator_id}`
- **DELETE:** `user:{kreator_id}`

### 2.5 Collection: `RATE_CARD_ORDERS`
Menyimpan rekam pemesanan kerja sama langsung antara UMKM dan Kreator.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `umkm_id` | String | ✅ | — | Appwrite Auth `$id` dari UMKM |
| `kreator_id` | String | ✅ | — | Appwrite Auth `$id` dari kreator |
| `ratecard_id` | String | ❌ | null | Referensi id di koleksi `rate_cards` (opsional) |
| `judul_proyek` | String | ❌ | null | Judul kesepakatan order |
| `scope_pekerjaan` | String | ❌ | null | Ruang lingkup kerja disetujui |
| `harga_final` | Float | ✅ | — | Harga yang disepakati bersama |
| `deadline` | String | ❌ | null | Tenggat waktu (Format: YYYY-MM-DD) |
| `url_collab_post` | String | ❌ | null | Link unggahan yang disetujui |
| `status` | Enum | ✅ | `Negosiasi`| `Negosiasi`, `Menunggu Pembayaran`, `Escrow`, `Revisi`, `Menunggu Verifikasi`, `Selesai`, `Dibatalkan`, `Dispute` |

**Pola Izin (Permissions):**
- **CREATE:** `users` (Hanya UMKM yang bisa memulai)
- **READ:** `user:{umkm_id}`, `user:{kreator_id}`
- **UPDATE:** `user:{umkm_id}`, `user:{kreator_id}` (terbatas pada atribut tertentu melalui Appwrite Function)
- **DELETE:** NONE

### 2.6 Collection: `TRANSACTIONS`
Koleksi sentral untuk riwayat seluruh arus dana.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `user_id` | String | ✅ | — | Appwrite Auth `$id` pemilik transaksi |
| `id_referensi` | String | ❌ | null | Mengacu pada ID dokumen `campaigns` atau `orders` |
| `tipe_referensi` | Enum | ❌ | null | `Campaign` atau `RateCard` |
| `nominal` | Float | ✅ | — | Nilai transaksi |
| `tipe` | Enum | ✅ | — | `Deposit`, `Withdrawal`, `Fee`, `Refund`, `Pencairan` |
| `status` | Enum | ✅ | `Pending` | `Pending`, `Escrow`, `Success`, `Failed`, `Refunded` |
| `midtrans_order_id`| String | ❌ | null | ID spesifik dari API Midtrans |
| `keterangan` | String | ❌ | null | Keterangan transaksi |

**Pola Izin (Permissions):**
- **CREATE:** NONE dari Client (HANYA ditulis via Appwrite Function backend)
- **READ:** `user:{user_id}` (Hanya pemilik yang dapat melihat riwayat transaksinya)
- **UPDATE:** NONE dari Client
- **DELETE:** NONE

### 2.7 Collection: `MESSAGES`
Koleksi yang memfasilitasi pesan Live Chat di dalam Rate Card Mode.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `order_id` | String | ✅ | — | Tautan ke koleksi `rate_card_orders` |
| `sender_id` | String | ✅ | — | Pengirim pesan |
| `tipe_pesan` | Enum | ✅ | `Text` | `Text`, `CustomOffer`, `System` |
| `konten` | String | ✅ | — | Isi obrolan teks |
| `offer_data` | String | ❌ | null | Disimpan sebagai tipe String JSON karena Appwrite tidak mendukung tipe JSONB |
| `is_read` | Boolean| ✅ | false | Penanda sudah terbaca |

**Pola Izin (Permissions):**
- **CREATE:** `user:{umkm_id_order}`, `user:{kreator_id_order}`
- **READ:** `user:{umkm_id_order}`, `user:{kreator_id_order}`
- **UPDATE:** `user:{sender_id}` (Untuk memperbarui flag `is_read`)
- **DELETE:** NONE

---

## 3. Relasi Antar Collection (Konsep Logis)
Appwrite tidak men-support *Foreign Key Constraint*. Relasi bergantung pada logika client dan integritas di Appwrite Function:
- **`users` -> `campaigns`**: 1 to N (`umkm_id`)
- **`users` -> `submissions`**: 1 to N (`kreator_id`)
- **`campaigns` -> `submissions`**: 1 to N (`campaign_id`)
- **`users` -> `rate_cards`**: 1 to Max(3) (`kreator_id`)
- **`users` -> `rate_card_orders`**: Terlibat di UMKM (`umkm_id`) & Kreator (`kreator_id`)
- **`rate_card_orders` -> `messages`**: 1 to N (`order_id`)
- **`users` -> `transactions`**: 1 to N (`user_id`)

---

## 4. Storage Buckets
- **`profile_images`**: Maks 5 MB. Izin Write: Pemilik. Izin Read: Publik.
- **`campaign_assets`**: Maks 100 MB. File video tidak ditolerir di sini (diarahkan ke Google Drive eksternal). Izin Write: UMKM. Izin Read: Publik.

---

## 5. Appwrite Functions (Serverless Backend)

Seluruh logika kritis, modifikasi saldo, dan pemanggilan API pihak ketiga diamankan di sisi server dengan *Appwrite Functions*:

| Function ID | Pemicu (Trigger) | Peran Kunci & Tanggung Jawab |
|---|---|---|
| `generate-brief-fn` | HTTP POST (Client)| Mengakses OpenAI `gpt-4o-mini` API Key. Menghasilkan draft brief text. |
| `claim-campaign-fn` | HTTP POST (Client)| Mengakses `campaigns`, mengecek `kuota_kreator`, dan atomic increment `kuota_terpakai` lalu insert data `submissions`. Menghindari race-condition kuota jebol. |
| `midtrans-create-fn` | HTTP POST (Client)| Mengirim request pesanan ke Server Midtrans untuk mendapatkan token Snap WebView. |
| `midtrans-webhook-fn`| HTTP Webhook | Dipanggil otomatis oleh Midtrans saat UMKM sukses bayar. Merubah status transaksi ke `Escrow`. |
| `validate-submission-fn` | HTTP POST (Admin) | Titik awal Admin menandai Valid / Fraud. |
| `release-escrow-fn` | HTTP POST (Server)| Dipanggil usai validasi. Mencabut status Escrow di koleksi `transactions` dan menyuntikkan (increment) saldo ke atribut `dompet_saldo` milik kreator. |
| `refund-escrow-fn` | HTTP POST (Admin) | Digunakan admin saat menengahi Dispute untuk merefund dana UMKM. |
| `withdraw-fn` | HTTP POST (Client)| Memeriksa ketersediaan saldo, menarik saldo dompet kreator, dan memanggil API Disbursement Midtrans (Iris). |
| `admin-stats-fn` | HTTP GET (Admin) | Mengeksekusi query lintas koleksi ber-privilese penuh untuk menghasilkan kalkulasi angka GMV, total pengeluaran, rate fraud, dan status retensi. |