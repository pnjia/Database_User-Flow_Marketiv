# Collection: `CAMPAIGNS`

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