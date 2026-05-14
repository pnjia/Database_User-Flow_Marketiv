# Skema Appwrite: Modul Campaign Mode

## Collection Terkait
- **`campaigns`**: Data detail proyek dari UMKM.
- **`submissions`**: Data hasil pekerjaan/tautan bukti tayang dari Kreator.

## Attributes & Relationships
### `campaigns`
- `umkm_id` (Relasi logis ke `users`)
- `judul_campaign`, `deskripsi_brief`, `niche` (String)
- `url_aset_eksternal` (String)
- `harga_per_1000_views`, `total_budget_escrow` (Float)
- `kuota_kreator`, `kuota_terpakai` (Integer)
- `status` (Enum: Draft, Aktif, Penuh, Selesai, Dibatalkan)

### `submissions`
- `campaign_id` (Relasi logis ke `campaigns`)
- `kreator_id` (Relasi logis ke `users`)
- `url_bukti_tayang` (String)
- `jumlah_views_aktual` (Integer)
- `status_validasi` (Enum: Pending, Valid, Fraud, Dispute)

## Storage
- **`campaign_assets`**: Digunakan menyimpan aset foto kampanye (Maks 100MB). Publik *read*, UMKM *write*.

## Functions
- `claim-campaign-fn`: Mengatur inkrementasi `kuota_terpakai` dan membuat dokumen `submissions`.
- `midtrans-create-fn`: Men-generate token Snap saat pembuatan campaign.
