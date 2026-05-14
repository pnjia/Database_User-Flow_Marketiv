# Backend: Modul Rate Card Mode

## Integrasi Appwrite
Logika backend mengandalkan fitur standar CRUD dari **Databases** dan fitur langganan *WebSocket* dari **Realtime** Appwrite.

## Service yang Digunakan
- **Databases**: Penyimpanan profil publik, konfigurasi paket `rate_cards`, *state* pesanan pada `rate_card_orders`, serta log `messages`.
- **Realtime**: `AppwriteService.realtime.subscribe` digunakan untuk mendengarkan (*listen*) perubahan pada koleksi `messages` secara *live*.

## Permission
- **`rate_cards`**: `CREATE`/`UPDATE`/`DELETE` untuk pemilik kreator, `READ` untuk semua.
- **`messages` & `rate_card_orders`**: Akses membaca dan menulis secara eksklusif hanya diberikan kepada `umkm_id` dan `kreator_id` yang terikat pada dokumen tersebut via DLS (*Document-Level Security*).

## Process Flow (Logika Backend)
1. **Inisiasi**: UMKM menekan "Hubungi Kreator", klien melakukan `Databases.createDocument` pada koleksi `rate_card_orders` dengan status `Negosiasi`.
2. **Pesan Realtime**: Klien men-subscribe channel dokumen tersebut. Saat pesan baru dikirim (`createDocument` di `messages`), klien lain otomatis menerima pembaruan di UI.
3. **Pembayaran**: Sama seperti Campaign, deposit dilakukan via integrasi Webhook Midtrans dan Appwrite Function.
4. **Validasi Akhir**: UMKM mengkonfirmasi secara manual bahwa pekerjaan selesai (tanpa otomasi sistem). Klien memanggil fungsi serverless `release-escrow-fn`.