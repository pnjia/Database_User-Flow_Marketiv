# Alur Keuangan
*(Alur spesifik deposit escrow dan penarikan dana)*

### 1. Deposit Escrow (UMKM)
- Saat campaign diaktifkan, UMKM wajib melakukan deposit dana ke rekening escrow sistem.
- Integrasi pembayaran menggunakan pihak ketiga (Midtrans).
- Verifikasi pembayaran via `midtrans-webhook-fn` yang memicu perubahan status campaign menjadi `Aktif`.

### 2. Penahanan Dana (Escrow)
- Dana deposit ditahan oleh sistem selama proses pengerjaan campaign berlangsung.

### 3. Penarikan Dana & Validasi (Kreator)
- Setelah Admin memvalidasi bukti tayang melalui `validate-submission-fn`, sistem secara otomatis atau manual memicu penarikan dana escrow untuk kreator.

### 4. Penanganan Dispute (Refund)
- Jika terjadi sengketa atau campaign dibatalkan secara sah, Admin memicu `refund-escrow-fn` untuk mengembalikan dana kepada UMKM sesuai aturan yang berlaku.
