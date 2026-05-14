# Halaman: Chat Room (UMKM & Kreator)

Route: `/umkm/negosiasi/:id` & `/kreator/negosiasi/:id`

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