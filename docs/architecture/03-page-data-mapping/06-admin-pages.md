# Halaman Role Admin

### 1. Halaman: **Admin Home / Submissions / Disputes**
- **Tujuan Halaman**: Panel dasbor sentral dan penengah perselisihan (Read-Only/Basic actions di mobile app).
- **Koleksi Terkait**: `campaigns`, `submissions`, `users`, `transactions`, `rate_card_orders`.
- **Operasi CRUD**: `READ`, `UPDATE` (via API Server)
- **Data yang Dibaca**: Agregat statistik (melalui `admin-stats-fn`) untuk membaca laporan *fraud*, GMV, dan *active disputes*.
- **Data yang Diubah**: Eksekusi penentuan *dispute* (`refund-escrow-fn`) dan penandaan kualitas video (`validate-submission-fn`).
- **Kebutuhan Autentikasi**: Ya (Role: ADMIN).