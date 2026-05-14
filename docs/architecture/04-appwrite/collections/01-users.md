# Collection: `USERS`

Menyimpan identitas semua aktor sistem (UMKM, Kreator, Admin). Role tidak ditangani oleh label Appwrite Team, melainkan disimpan sebagai atribut dalam koleksi ini.

**Atribut (Attributes):**
| Attribute Key | Type | Required | Default | Keterangan |
|---|---|---|---|---|
| `user_id` | String | ✅ | — | Sama dengan Appwrite Auth `$id` (link ke account) |
| `role` | Enum | ✅ | — | `UMKM` / `KREATOR` / `ADMIN` |
| `nama_lengkap` | String | ✅ | — | Nama pribadi pemilik akun / staff |
| `nama_usaha` | String | ❌ | null | Nama brand/usaha, wajib jika `role = UMKM` |
| `username_kreator` | String | ❌ | null | Handle/username publik, wajib jika `role = KREATOR` |
| `email` | Email | ✅ | — | Duplikat dari Auth untuk kemudahan query |
| `nomor_whatsapp` | String | ✅ | — | Nomor WhatsApp pengguna |
| `dompet_saldo` | Float | ✅ | 0 | Saldo kreator yang bisa di-withdraw |
| `niche` | String | ❌ | null | Kategori spesifik (kuliner, fesyen, edukasi, dll) |
| `foto_profil_url` | String | ❌ | null | URL gambar dari Appwrite Storage |
| `bio` | String | ❌ | null | Deskripsi singkat pengguna |
| `is_verified` | Boolean | ✅ | false | Status verifikasi email |
| `fcm_token` | String | ❌ | null | Token perangkat untuk notifikasi (Size 255) |
| `unread_count` | Integer | ✅ | 0 | Jumlah notifikasi belum dibaca (Min 0) |

**Pola Izin (Permissions):**
- **CREATE:** `users` (semua yang terautentikasi)
- **READ:** `user:{$id}` (Hanya pemilik dokumen)
- **UPDATE:** `user:{$id}` (Hanya pemilik dokumen)
- **DELETE:** NONE (User tidak bisa menghapus akun sendiri, hanya Admin via Function)