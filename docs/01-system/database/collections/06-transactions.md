# Collection: `TRANSACTIONS`

Koleksi sentral untuk riwayat seluruh arus dana.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `user_id` | String | ✅ | — | Appwrite Auth `$id` pemilik transaksi |
| `id_referensi` | String | ❌ | null | Mengacu pada ID dokumen `campaigns` atau `orders` |
| `tipe_referensi` | Enum | ❌ | null | `Campaign` atau `RateCard` |
| `nominal` | Float | ✅ | — | Nilai transaksi |
| `tipe` | Enum | ✅ | — | `Deposit`, `Withdrawal`, `Fee`, `Refund`, `Pencairan` |
| `status` | Enum | ✅ | `Pending` | `Pending`, `Escrow`, `Success`, `Failed`, `Refunded` |
| `midtrans_order_id`| String | ❌ | null | ID spesifik dari API Midtrans |
| `keterangan` | String | ❌ | null | Keterangan transaksi |

**Pola Izin (Permissions):**
- **CREATE:** NONE dari Client (HANYA ditulis via Appwrite Function backend)
- **READ:** `user:{user_id}` (Hanya pemilik yang dapat melihat riwayat transaksinya)
- **UPDATE:** NONE dari Client
- **DELETE:** NONE