# Skema Appwrite: Modul Auth

Bagian ini memetakan layanan infrastruktur Appwrite yang berinteraksi langsung dengan modul Autentikasi.

## Service Appwrite yang Digunakan
- **Appwrite Account**: Menangani logika pembuatan akun, sesi sandi, pemulihan sandi, dan pengelolaan sesi perangkat.
- **Appwrite Databases**: Menangani penyimpanan data profil tambahan pengguna yang tidak ditampung oleh objek *Account* standar.

## Collection Terkait: `users`
Setiap pengguna yang berhasil terdaftar di layanan *Account* diwajibkan memiliki 1 dokumen terkait di dalam database `marketiv_db`, koleksi `users`.

**Atribut Kunci:**
| Attribute Key | Type | Keterangan |
|---|---|---|
| `user_id` | String | Merupakan `$id` dari objek Appwrite Account (PENGHUBUNG UTAMA). |
| `role` | Enum / String | Nilai: `UMKM`, `KREATOR`, atau `ADMIN`. |
| `email` | Email | Duplikasi data email dari akun untuk mempermudah *query*. |
| `is_verified` | Boolean| Status apakah akun sudah tervalidasi. |

*(Lihat `docs/architecture/04-appwrite/collections/01-users.md` untuk atribut lengkap).*

## Auth Provider
Aplikasi Marketiv untuk fase MVP ini hanya menggunakan satu penyedia autentikasi:
- **Email & Password**

## Sesi (Session)
- Tipe Sesi: Sesi berkelanjutan (Persistent Session).
- Refresh: Ditangani secara internal oleh `Appwrite SDK`.
- ID Sesi: Dikelola dengan parameter `current` (contoh: `deleteSession(sessionId: 'current')`).

## Appwrite Functions
- Tidak ada *Appwrite Functions* spesifik yang diwajibkan untuk alur registrasi/login standar klien. Pembuatan akun dan pembuatan dokumen `users` dapat dilakukan secara sekuensial oleh klien karena dokumen diamankan via *Document-Level Security*.
- *(Asumsi)*: Jika pendaftaran membutuhkan validasi pihak ketiga atau logika rumit di masa depan, fungsi `register-fn` dapat ditambahkan.

## Relasi dengan Collection Users
Hubungan bersifat **1-to-1** secara logis.
Objek `Appwrite Account` -> memiliki `$id` unik.
Dokumen `users` -> memiliki field `user_id` yang nilainya sama dengan `$id` tersebut.
