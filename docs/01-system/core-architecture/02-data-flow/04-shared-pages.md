# Halaman Bersama (Shared)

### 1. Halaman: **Chat Room & Negosiasi**
- **Tujuan Halaman**: Sinkronisasi komunikasi realtime (Live Chat) dan pengiriman Custom Offer.
- **Koleksi Terkait**: `messages`, `rate_card_orders`.
- **Operasi CRUD**: `READ`, `CREATE`, `UPDATE` (hanya `is_read` flag)
- **Data yang Dibaca**: History `messages` dan struktur `rate_card_orders`.
- **Data yang Dibuat**: Obrolan ke koleksi `messages`.
- **Input Pengguna**:
  - Pesan teks biasa.
  - JSON string diatribut `offer_data` saat memicu `CustomOffer`.
- **Kebutuhan Autentikasi**: Ya (Hanya kedua pihak UMKM dan KREATOR terkait).

### 2. Halaman: **Keuangan / Saldo**
- **Tujuan Halaman**: Melacak histori aliran dana, top-up, dan withdraw.
- **Koleksi Terkait**: `transactions`, `users`.
- **Operasi CRUD**: `READ` (Koleksi), `CREATE` (via Server Function)
- **Data yang Dibaca**: Nilai `dompet_saldo` dari `users` dan array `transactions` historis.
- **Data yang Dibuat**: Transaksi *Deposit* atau *Withdrawal*. Modifikasi ini dipicu oleh `midtrans-create-fn` dan `withdraw-fn`.
- **Input Pengguna**:
  - Angka nominal (Int/Float).
  - Form bank tujuan untuk withdraw.
- **Kebutuhan Autentikasi**: Ya (Seluruh aktor sistem).

### 3. Halaman: **Profil & Edit Profil**
- **Tujuan Halaman**: Memperbarui informasi biodata, identitas bisnis, dan kredensial akun.
- **Koleksi Terkait**: `users`
- **Operasi CRUD**: `READ`, `UPDATE`
- **Data yang Dibaca**: Data `users` saat ini.
- **Data yang Diubah**: Dokumen `users` saat update profile.
- **Input Pengguna**: Form Identitas Dasar, Avatar Editor, System Logout.
- **Kebutuhan Autentikasi**: Ya
