# Collection: `SUBMISSIONS`

Menyimpan penyerahan bukti hasil pekerjaan kreator terhadap sebuah campaign.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `campaign_id` | String | ✅ | — | Referensi `$id` ke `campaigns` |
| `kreator_id` | String | ✅ | — | Appwrite Auth `$id` dari kreator |
| `url_bukti_tayang` | String | ✅ | — | URL Publik konten (TikTok/Instagram) |
| `jumlah_views_target` | Integer| ❌ | null | Target opsional jika ada |
| `jumlah_views_aktual` | Integer| ✅ | 0 | Jumlah penayangan tercapai |
| `status_validasi` | Enum | ✅ | `Pending`| `Pending`, `Valid`, `Fraud`, `Dispute` |
| `dana_dicairkan` | Float | ✅ | 0 | Total dana yang dikirim ke dompet kreator |

**Pola Izin (Permissions):**
- **CREATE:** `users` (Kreator yang terautentikasi)
- **READ:** `user:{kreator_id}` (UMKM dan Admin membaca ini via eksekusi Appwrite Function)
- **UPDATE:** NONE dari client (Update status dilakukan oleh Appwrite Function)
- **DELETE:** NONE