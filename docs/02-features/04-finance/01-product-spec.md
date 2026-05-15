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

## Edge Cases

# Edge Cases: Modul Keuangan & Escrow

## Skenario Error & Penanganan

1. **Pembayaran Midtrans Terputus/Ditutup Manual**
   - **Kondisi**: UMKM berada di dalam Midtrans WebView namun menekan ikon "X" tanpa menyelesaikan transfer uang.
   - **Tindakan/Fallback**: Status dokumen di Campaign atau pesanan tetap akan macet di status "Menunggu Pembayaran" / "Draft" karena Server *Webhook* tidak menerima notifikasi pelunasan `Settlement`. UI harus menyajikan tombol untuk "Lanjutkan Pembayaran" yang akan me-request token baru atau menggunakan token yang sama jika masa berlakunya belum kedaluwarsa.

2. **Kreator Menarik Dana Melebihi Saldo Dompet**
   - **Kondisi**: Saldo ada Rp 50.000, kreator menginput Rp 100.000 untuk di-withdraw.
   - **Tindakan UI**: Form divalidasi murni secara *client-side*. Jika input > `_user.value.dompetSaldo`, nonaktifkan tombol submit dan munculkan pesan merah "Saldo tidak mencukupi".

3. **Inkonsistensi Saldo Jika Server Crash Saat Release Escrow**
   - **Kondisi**: Eksekusi `release-escrow-fn` berhasil menambahkan nilai di database, tapi putus (*crash*) sebelum mengubah status rekaman Transaksi menjadi Selesai.
   - **Tindakan Ideal (*Asumsi/Penanganan Backend*)**: Pastikan *Appwrite Function* membungkus rangkaian penambahan saldo dan modifikasi status dalam satu tahapan (transaksional) menggunakan *try-catch* agresif di sisi *script backend*, dan membatalkan semuanya bila gagal separuh jalan, untuk mencegah pembengkakan (uang bocor) di dompet pengguna.

4. **Nomor Rekening Tujuan Withdrawal Invalid**
   - **Kondisi**: Permintaan pendaftaran pencairan ke bank via API Disbursement (Iris) ditolak sistem bank karena nomor tidak valid.
   - **Respons Sistem**: Fungsi `withdraw-fn` mengembalikan JSON Error dari pihak ketiga. 
   - **Tindakan UI**: Tangkap respons ini dan pop-up Snackbar: "Transaksi ditolak. Periksa kembali kebenaran nomor rekening bank." Uang jangan di-deduct (atau kembalikan) dari/ke `dompet_saldo`.