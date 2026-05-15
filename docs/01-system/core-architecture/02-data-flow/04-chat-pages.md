# Halaman Chat & Negosiasi

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