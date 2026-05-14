# Appwrite Function: `claim-campaign-fn`

- **Pemicu (Trigger)**: HTTP POST (Client)
- **Peran Kunci & Tanggung Jawab**: Mengakses `campaigns`, mengecek `kuota_kreator`, dan atomic increment `kuota_terpakai` lalu insert data `submissions`. Menghindari race-condition kuota jebol.