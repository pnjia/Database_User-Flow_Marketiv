# Alur Pengguna (User Flow): Keuangan & Escrow

## 1. Alur Deposit via Webhook (UMKM)
1. Dari proses checkout Kampanye/Rate Card, UMKM diarahkan menekan **Bayar Sekarang**.
2. *Loading*. Klien memanggil server untuk memproduksi *token*.
3. Halaman membuka WebView *Midtrans Snap*.
4. UMKM memilih metode transfer bank atau e-Wallet.
5. Pembayaran selesai dilakukan UMKM.
6. UMKM menekan tutup di WebView, diarahkan kembali ke aplikasi.
7. Di belakang layar, Webhook menyelaraskan status.
8. Status uang di daftar transaksi UMKM tercatat sebagai `Escrow`.

## 2. Alur Pencairan Escrow (Otomatis)
1. Kreator menyerahkan pekerjaan (Submit URL).
2. Pihak berwenang (UMKM di Rate Card Mode, atau Admin di Campaign Mode) melakukan persetujuan ("Selesai" atau "Valid").
3. Proses tersebut memicu eksekusi fungsi serverless.
4. Nominal transaksi yang sebelumnya di-lock pindah dicatat ke dalam `dompet_saldo` kreator.

## 3. Alur Tarik Tunai / Withdrawal (Kreator)
1. Kreator membuka menu **Keuangan**.
2. Memeriksa label `Saldo Tersedia`.
3. Kreator memilih bank tujuan dari menu *Dropdown* dan melengkapi nama serta nomor rekening.
4. Kreator memasukkan nominal uang.
5. Kreator menekan **Tarik Dana**.
6. Sistem memproses, memunculkan notifikasi "Permintaan Penarikan Diproses".
7. Di server, saldo terpotong dan mutasi berstatus `Pending` dicatat, menunggu rekonsiliasi dari *Payment Gateway*.

## Reference Wireframes & User Flows

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