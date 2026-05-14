# Skema Appwrite: Modul Rate Card Mode

## Collection Terkait
- **`rate_cards`**: Definisi paket harga buatan kreator.
- **`rate_card_orders`**: Metadata pesanan antara 2 belah pihak.
- **`messages`**: Log komunikasi di dalam pesanan tersebut.

## Attributes & Relationships
### `rate_cards`
- `kreator_id` (Relasi ke `users`)
- `nama_paket`, `deskripsi_paket`, `deliverable` (String)
- `harga_paket` (Float)
- `estimasi_hari` (Integer)
- `is_active` (Boolean)

### `rate_card_orders`
- `umkm_id`, `kreator_id` (Relasi ganda ke `users`)
- `ratecard_id` (Referensi ke `rate_cards`)
- `judul_proyek`, `scope_pekerjaan`, `url_collab_post` (String)
- `harga_final` (Float)
- `status` (Enum: Negosiasi, Menunggu Pembayaran, Escrow, Menunggu Verifikasi, Selesai, Dispute)

### `messages`
- `order_id` (Relasi ke `rate_card_orders`)
- `sender_id` (Pengirim pesan)
- `tipe_pesan` (Enum: Text, CustomOffer, System)
- `konten`, `offer_data` (String - `offer_data` menyimpan JSON berformat khusus)
- `is_read` (Boolean)

## Functions
Tidak ada *Function* khusus untuk chat. Modul ini mengandalkan `midtrans-webhook-fn` untuk Escrow dan `release-escrow-fn` (atau `refund-escrow-fn`) di akhir pesanan.