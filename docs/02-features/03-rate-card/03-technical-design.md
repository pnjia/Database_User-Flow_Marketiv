# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

# Frontend: Modul Rate Card Mode

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Direktori Kreator** | `/umkm/kreator` | Grid/List portofolio kreator. |
| **Profil Kreator** | `/umkm/kreator/:id` | Detail paket Rate Card. |
| **Chat Room** | `/umkm/negosiasi/:id` & `/kreator/negosiasi/:id` | Room obrolan Live Chat. |
| **Kelola Rate Card** | `/kreator/rate-card` | Manajemen paket oleh kreator. |

## Komponen UI Utama
- **`KreatorCard`**: Menampilkan avatar, nama kreator, niche, dan tarif mulai dari (*starting price*).
- **`CustomOfferBubble`**: Gelembung pesan interaktif di layar Chat yang memiliki tombol aksi ("Terima" / "Tolak") dan informasi struktural (Scope, Harga).
- **`BottomSheet` (Filter)**: Digunakan di direktori untuk menyaring berdasarkan Niche.

## State Management
- `RateCardController` mengatur operasi CRUD Rate Card milik Kreator (dengan validasi max 3).
- `ChatController` mengatur sinkronisasi daftar `MessageModel` secara *realtime* (menggunakan *stream* atau `RxList`).

## Validasi & Interaksi
- Di layar "Kelola Rate Card", tombol "Tambah" disembunyikan/di-*disable* jika data di *list* sudah mencapai 3.
- Obrolan ter-*scroll* otomatis ke bawah ketika ada pesan baru masuk.
- Saat UMKM mengirim penawaran, muncul form Modal (*BottomSheet*) untuk memasukkan `judul`, `scope`, dan `harga`.
## Backend

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
## AI Prompt

# Kumpulan Prompt Reusable: Rate Card Mode

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur Rate Card Mode.

## 1. Prompt Generate UI Chat & Realtime (Frontend + Backend)
> "Tolong buatkan layar `ChatRoomPage` di Flutter dengan GetX. Gunakan ListView yang dapat di-*scroll* dari bawah ke atas (reverse). Integrasikan dengan `AppwriteService.realtime` pada koleksi `messages` mengikuti struktur di `docs/02-features/03-rate-card/04-appwrite-schema.md`. Buatkan juga komponen widget `CustomOfferBubble` untuk merender pesan bertipe 'CustomOffer' di mana payload datanya dibongkar (decode) dari string JSON `offer_data`."

## 2. Prompt Logika Rate Card
> "Di dalam `RateCardController`, buat fungsi `createNewPackage()`. Baca pedoman `docs/02-features/03-rate-card/06-edge-cases.md`. Pastikan fungsi ini mengecek (query) terlebih dahulu ke `AppwriteService.databases` untuk menghitung berapa rate card milik kreator ini. Jika sudah mencapai 3, return pesan gagal lewat GetX Snackbar dan batalkan `createDocument`."

## 3. Prompt Render Direktori Kreator
> "Buatkan UI `KreatorDirectoryPage`. Tarik data dari koleksi `users` di mana atribut `role`='KREATOR'. Tampilkan dalam GridView menggunakan widget `KreatorCard`. Gunakan token warna dan tipografi dari panduan `docs/_global/design-system/`."