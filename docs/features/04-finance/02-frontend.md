# Frontend: Modul Keuangan & Escrow

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Keuangan UMKM** | `/umkm/keuangan` | Top-up, status Escrow UMKM. |
| **Keuangan Kreator** | `/kreator/keuangan` | Info Saldo Tersedia, Withdraw. |

## Komponen UI Utama
- **`StatCard` Saldo Utama**: Menampilkan jumlah uang di Escrow (UMKM) atau Dompet Saldo (Kreator).
- **`TransactionCard`**: Representasi daftar riwayat mutasi. Berwarna hijau (masuk) atau merah (keluar).
- **`FilterTabs`**: Untuk menyaring "Semua", "Deposit", "Penarikan", atau "Escrow".
- **`PrimaryButton` Aksi**: Untuk trigger Top-up atau Tarik Dana.

## State Management
- `KeuanganController`: Melakukan pemuatan data (fetching) riwayat transaksi.

## Validasi Form
- Form Penarikan Bank (Kreator) divalidasi dengan batas nominal minimum (contoh Rp 10.000) dan mencukupi sisa `dompet_saldo`.
- Atribut form rekening (Nama Bank, Nomor, Pemilik) tidak boleh kosong.

## Interaksi Pengguna
- **Top-up (UMKM)**: Pengguna menekan bayar, sistem memanggil fungsi untuk meminta Token, UI membuka halaman WebView yang menyajikan antarmuka pembayaran Midtrans Snap.
- **Withdraw (Kreator)**: Pengisi data rekening, menekan tombol Tarik, memunculkan modal *Loading*, dan memberikan peringatan sukses/gagal.