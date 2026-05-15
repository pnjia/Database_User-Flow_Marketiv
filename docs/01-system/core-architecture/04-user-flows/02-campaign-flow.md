# Alur Campaign Flow
*(Alur spesifik untuk pembuatan dan pengerjaan campaign)*

### 1. Alur UMKM (Pembuatan Campaign)
1. **Buat Campaign**: UMKM membuat dokumen di koleksi `campaigns` dengan status awal: `Draft`.
2. **Unggah Asset**: Mengunggah file produk ke Storage Bucket `campaign_assets`.
3. **Pembayaran Deposit**: UMKM melakukan pembayaran deposit escrow.
4. **Aktivasi**: Sistem mengubah status menjadi `Aktif` HANYA JIKA Webhook Midtrans (`midtrans-webhook-fn`) mengonfirmasi deposit sukses.

### 2. Alur Kreator (Pengerjaan Campaign)
1. **Pencarian**: Kreator melihat campaign dengan status `Aktif` dan `kuota_terpakai` < `kuota_kreator`.
2. **Klaim**: Kreator mengklaim campaign (Function: `claim-campaign-fn`) yang akan mengupdate `kuota_terpakai` +1.
3. **Pengerjaan**: Kreator mengerjakan campaign sesuai brief.
4. **Submission**: Kreator mengisi atribut `url_bukti_tayang` pada koleksi `submissions`.

### 3. Validasi & Penyelesaian
- **Admin**: Melakukan validasi bukti tayang menggunakan `validate-submission-fn`.
- **Status**: Status pengerjaan diperbarui berdasarkan hasil validasi admin.
