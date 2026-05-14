# Backend: Modul Keuangan & Escrow

## Integrasi Appwrite & Midtrans
Client Flutter **Dilarang Keras** melakukan modifikasi mandiri ke koleksi `transactions` atau secara manual merubah `dompet_saldo`. Seluruh operasi keuangan dibungkus di belakang server Appwrite Functions dan berinteraksi via API Midtrans.

## Service yang Digunakan
- **Appwrite Functions**: Mengeksekusi permintaan HTTP yang diamankan via variabel env (Client Key dan Server Key Midtrans).
- **Appwrite Databases**: Hanya sebatas izin membaca (READ) historis di sisi pengguna.

## Permission
- Client-side HANYA memiliki hak `READ` pada koleksi `transactions` miliknya (berdasarkan `user_id`).
- Client-side (Kreator) HANYA memiliki hak `READ` pada atribut `dompet_saldo` miliknya di koleksi `users`.
- Otoritas melakukan operasi penulisan (`CREATE`, `UPDATE`) mutlak diberikan kepada Appwrite Functions yang berlapis API Key server-side.

## Process Flow (Logika Backend)
1. **Flow Deposit UMKM**: 
   - Fungsi `midtrans-create-fn` dipanggil. Midtrans Snap URL dikembalikan ke Flutter WebView. 
   - Setelah dibayar, Midtrans mengirim POST ke `midtrans-webhook-fn`. Server Appwrite memverifikasi keaslian webhook, lalu menuliskan rekaman berstatus `Escrow` pada koleksi `transactions`.
2. **Flow Withdrawal Kreator**:
   - Fungsi `withdraw-fn` dipanggil oleh aplikasi klien, membawa data tujuan bank.
   - Skrip server memeriksa apakah sisa `dompet_saldo` mencukupi.
   - Jika cukup, saldo dikurangi (update `users`), rekaman Withdrawal ditulis di `transactions`, dan *Payout API* dari Midtrans dipicu.