# Collection: `MESSAGES`

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