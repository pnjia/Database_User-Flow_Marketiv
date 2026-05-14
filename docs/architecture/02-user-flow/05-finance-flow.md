# Alur Keuangan & Escrow

1. **Top-Up (UMKM):**
   - UMKM buka `Keuangan` -> Klik `Deposit`.
   - Mengisi jumlah deposit -> Membayar melalui antarmuka Midtrans.
   - Saldo dompet terisi (meskipun secara teknis langsung di-lock menjadi *Escrow* pada transaksi Rate Card/Campaign yang tertunda).

2. **Penarikan / Withdrawal (Kreator):**
   - Kreator buka `Keuangan`.
   - Memastikan `dompet_saldo` > 0.
   - Isi form (nominal, bank, nomor rekening, pemilik).
   - Klik "Tarik Dana" -> Permintaan masuk ke sistem (via Appwrite Function ke Midtrans Iris).
   - Dana masuk ke rekening bank, dompet saldo aplikasi berkurang.