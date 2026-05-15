# Alur Rate Card Flow
*(Alur spesifik negosiasi dan pemesanan jasa kreator)*

### 1. Manajemen Rate Card (Kreator)
- Kreator membuat atau memperbarui dokumen dalam koleksi `rate_cards`.
- Kreator menentukan harga, deskripsi jasa, dan ketersediaan slot.

### 2. Penelusuran & Pemesanan (UMKM)
- UMKM menelusuri profil kreator.
- Sistem melakukan *nested query* logis ke dokumen `rate_cards` yang terkait dengan kreator yang berstatus `is_active = true`.
- UMKM melakukan inisiasi pemesanan jasa berdasarkan detail `rate_cards` yang dipilih.

### 3. Negosiasi & Order
- Sistem mencatat order dalam koleksi `rate_card_orders` (terkait dengan status pesanan).
- Proses negosiasi dilakukan sebelum kesepakatan final dan pembayaran dilakukan.
