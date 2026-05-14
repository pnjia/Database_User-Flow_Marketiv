# Appwrite Function: `release-escrow-fn`

- **Pemicu (Trigger)**: HTTP POST (Server)
- **Peran Kunci & Tanggung Jawab**: Dipanggil usai validasi. Mencabut status Escrow di koleksi `transactions` dan menyuntikkan (increment) saldo ke atribut `dompet_saldo` milik kreator.