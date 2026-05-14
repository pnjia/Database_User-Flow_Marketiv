# Overview: Modul Keuangan & Escrow

## Tujuan Fitur
Memastikan keamanan transaksi antara UMKM dan Kreator Mikro melalui sistem penahanan dana sementara (Escrow), serta memfasilitasi pengisian saldo (Top-Up) dan pencairan penghasilan (Withdrawal).

## Deskripsi Singkat
Modul ini bertindak sebagai jembatan *payment gateway* (via Midtrans) dan pengelola buku besar internal (Ledger) untuk mencatat semua aliran masuk, tertahan, maupun keluar uang, guna menumbuhkan kepercayaan karena meminimalisir risiko penipuan (buzzer bodong).

## Daftar Subfitur
- **Deposit / Top-up**: Pemilik UMKM menyetorkan dana via Virtual Account/QRIS Midtrans.
- **Sistem Escrow**: Dana transaksi tidak langsung dikirim ke kreator, melainkan ditahan otomatis oleh sistem pada `transactions`.
- **Withdrawal**: Kreator menarik uang hasil kerjanya ke rekening bank pribadi.
- **Riwayat Transaksi**: Laporan catatan aliran uang (*ledger*).

## Dependensi dengan Fitur Lain
- **Campaign Mode & Rate Card Mode**: Keuangan adalah tulang punggung dari kedua mode eksekusi tersebut. Tanpa status transaksi "Escrow", proyek tidak dapat aktif.
- **Validasi Admin**: Uang dari Escrow menuju dompet Kreator hanya bisa dipicu oleh keputusan validasi (Selesai/Valid).