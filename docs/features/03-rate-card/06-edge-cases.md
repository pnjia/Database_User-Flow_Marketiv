# Edge Cases: Modul Rate Card Mode

## Skenario Error & Penanganan

1. **Membuat Paket Lebih dari Batas (Rate Card)**
   - **Kondisi**: Kreator berusaha menambahkan paket ke-4 melalui eksploitasi UI atau glitch.
   - **Tindakan UI/Logika**: Di `RateCardController`, paksa hitung ulang jumlah dokumen yang terasosiasi dengan `kreator_id`. Jika `count >= 3`, cegah pemanggilan `createDocument` dan munculkan *Snackbar* peringatan: "Kamu hanya bisa memiliki maksimal 3 paket Rate Card."

2. **Koneksi Terputus Saat Live Chat**
   - **Kondisi**: Jaringan UMKM/Kreator tidak stabil, koneksi WebSocket Appwrite Realtime terputus.
   - **Tindakan/Fallback**: SDK Appwrite akan mencoba *reconnect* otomatis. UI disarankan menunjukkan indikator kecil di App Bar (misal tulisan `Connecting...`) saat status *stream* *offline*. Pesan yang gagal terkirim harus bisa di-tap untuk *retry*.

3. **Format Data `CustomOffer` Gagal di-Parse**
   - **Kondisi**: String JSON di atribut `offer_data` koleksi `messages` rusak (*corrupt*).
   - **Tindakan UI**: Aplikasi harus membungkus proses dekoding `jsonDecode` menggunakan `try-catch`. Jika gagal, render *fallback widget* yang bertuliskan: "Gagal memuat penawaran." ketimbang membuat layar aplikasi putih/crash.

4. **UMKM Menggantung Pekerjaan Selesai**
   - **Kondisi**: Kreator sudah *submit* URL konten, namun UMKM tidak pernah menekan tombol "Selesai" untuk merilis Escrow berhari-hari.
   - **Tindakan (Asumsi Masa Depan)**: Admin dapat melakukan *force release* setelah batas waktu habis (misal > 7 hari) jika tidak ada komplain yang diajukan.