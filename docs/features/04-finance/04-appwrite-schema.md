# Skema Appwrite: Modul Keuangan

## Collection Terkait
- **`transactions`**: Buku besar riwayat keuangan universal platform.
- **`users`** (sebagian): Atribut `dompet_saldo` yang menentukan ketersediaan kas.

## Attributes & Relationships (`transactions`)
- `user_id` (Relasi logis ke pemilik catatan ini di koleksi `users`).
- `id_referensi` (String opsional yang merujuk ke ID dari pesanan terkait / kampanye).
- `tipe_referensi` (Enum: Campaign, RateCard).
- `nominal` (Float).
- `tipe` (Enum: Deposit, Withdrawal, Fee, Refund, Pencairan).
- `status` (Enum: Pending, Escrow, Success, Failed, Refunded).
- `midtrans_order_id` (String - ID acuan ke dashboard Midtrans).

## Storage
Tidak ada keterkaitan dengan layanan Storage dalam alur keuangan.

## Functions Utama
Sistem ini menggunakan sekumpulan *Function* untuk menjaga integritas transaksi uang.
- `midtrans-create-fn`
- `midtrans-webhook-fn`
- `release-escrow-fn` (Mencairkan dana)
- `refund-escrow-fn` (Mengembalikan dana)
- `withdraw-fn` (Pencairan ke bank)