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