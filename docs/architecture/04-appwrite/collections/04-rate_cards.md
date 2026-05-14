# Collection: `RATE_CARDS`

Menyimpan paket layanan harga pasti (Rate Card Mode) milik Kreator. Maksimal 3 paket per Kreator.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `kreator_id` | String | ✅ | — | Appwrite Auth `$id` dari kreator |
| `nama_paket` | String | ✅ | — | Nama/Judul layanan (max 100 char) |
| `deskripsi_paket` | String | ❌ | null | Rincian layanan |
| `harga_paket` | Float | ✅ | — | Nilai harga jasa tetap |
| `deliverable` | String | ❌ | null | Hasil yang diberikan |
| `estimasi_hari` | Integer| ❌ | null | Estimasi lama pengerjaan |
| `is_active` | Boolean| ✅ | true | Status ketersediaan paket |

**Pola Izin (Permissions):**
- **CREATE:** `user:{kreator_id}`
- **READ:** `any` (Terbuka untuk dibaca semua pengguna/publik)
- **UPDATE:** `user:{kreator_id}`
- **DELETE:** `user:{kreator_id}`