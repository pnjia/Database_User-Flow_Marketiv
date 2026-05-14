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