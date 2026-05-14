# Backend: Modul Campaign Mode

## Integrasi Appwrite
Fitur Campaign sepenuhnya berinteraksi dengan **Databases**, **Storage**, dan **Functions** di Appwrite. Klien tidak pernah berinteraksi langsung dengan sistem pembayaran pihak ketiga.

## Service yang Digunakan
- **Databases**: Untuk membuat, membaca, dan memperbarui dokumen `campaigns` dan `submissions`.
- **Storage**: `campaign_assets` bucket untuk menyimpan logo atau foto produk UMKM.
- **Functions**: `claim-campaign-fn`, `midtrans-create-fn`, `generate-brief-fn`.

## Permission
- **UMKM**: Diberikan hak `CREATE` dan `UPDATE` (miliknya sendiri) pada dokumen `campaigns`.
- **Kreator**: Diberikan hak `READ` pada `campaigns` yang aktif, dan `CREATE` pada `submissions`.

## Process Flow (Logika Backend)
1. **Penyimpanan Aset**: Gambar diunggah ke Storage, dikembalikan URL-nya.
2. **Pembuatan Campaign**: Disimpan ke Databases dengan status `Draft`.
3. **Pembayaran & Aktivasi**: Appwrite Function `midtrans-create-fn` dipanggil. Saat Webhook konfirmasi sukses dari Midtrans diterima (`midtrans-webhook-fn`), status campaign berubah menjadi `Aktif`.
4. **Pencegahan Race Condition**: Saat kreator klik klaim, klien tidak memodifikasi `kuota_terpakai` secara langsung. Eksekusi dilakukan via `claim-campaign-fn` yang melakukan *atomic update* di server untuk memastikan kuota tidak bocor (jebol).
