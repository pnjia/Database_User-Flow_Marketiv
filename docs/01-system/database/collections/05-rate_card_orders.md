# Collection: `RATE_CARD_ORDERS`

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