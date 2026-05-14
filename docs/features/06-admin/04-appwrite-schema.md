# Skema Appwrite: Modul Admin

## Collection Terkait
Semua fitur Admin bersifat menyerap (membaca atau menulis paksa) isi dari koleksi:
- **`campaigns`**, **`submissions`** (Monitoring Bukti)
- **`rate_card_orders`**, **`transactions`** (Memonitor Disput & Refund)

## Attributes & Relationships
(Sama dengan pemetaan yang tercantum di dokumentasi setiap modul/fitur utamanya).

## Auth & Role
Hak akses menu Dasbor (*Route Guarding*) dicegat (*intercepted*) jika nilai atribut `role` != `ADMIN`.

## Storage
Tidak memerlukan akses modifikasi, tetapi memiliki izin lintas publik untuk membaca berkas bukti/portofolio.

## Functions (Spesifik Admin)
- `admin-stats-fn`: Membuat dan membalas kalkulasi data statistik.
- `validate-submission-fn`: Menyematkan status `Valid` atau `Fraud` dari klien admin.
- `refund-escrow-fn`: Melakukan serah terima (*release*) saldo secara terbalik kembali kepada pemesan UMKM yang kalah/setuju dibatalkan dalam *Dispute*.