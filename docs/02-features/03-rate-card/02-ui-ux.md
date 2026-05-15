# Alur Pengguna (User Flow): Rate Card Mode

## Alur Discovery & Negosiasi
1. UMKM membuka menu **Kreator** (Direktori).
2. Memfilter berdasarkan *niche* atau mencari berdasarkan nama.
3. UMKM menekan profil kreator dan mengecek paket Rate Card-nya.
4. UMKM menekan tombol **Hubungi Kreator**.
5. Sistem menginisialisasi Order Baru (Status: Negosiasi) dan membuka layar Chat.
6. UMKM dan Kreator berdiskusi di **Chat Room**.

## Alur Kesepakatan (Custom Offer)
7. UMKM menekan ikon *Custom Offer* di menu chat.
8. UMKM mengisi *scope* pekerjaan, tenggat waktu, dan nilai kesepakatan final, lalu mengirim.
9. Pesan masuk ke layar Kreator dalam bentuk kartu (Widget Custom Offer).
10. Kreator menekan **Terima**.
11. Status Order berubah menjadi `Menunggu Pembayaran`.

## Alur Eksekusi
12. UMKM menyetorkan dana (*Deposit* via Midtrans). Dana ditahan. Status: `Escrow`.
13. Kreator memproduksi video dan memublikasikannya secara *collab* (fitur platform sosial).
14. Kreator mengirimkan URL hasilnya di aplikasi. Status: `Menunggu Verifikasi`.

## Alur Penyelesaian
15. UMKM memeriksa unggahan tersebut.
16. **Percabangan**:
    - **Selesai**: UMKM menekan setuju. Dana pindah dari Escrow ke Dompet Kreator.
    - **Revisi**: UMKM meminta perubahan, Kreator memperbaiki dan submit ulang.
    - **Sengketa**: Komplain berlarut, pihak membuka *Dispute*, admin akan turun tangan.

## Reference Wireframes & User Flows

# Alur Rate Card Mode (Fixed Price / Custom Offer)

*Karakteristik: Negosiasi langsung melalui Live Chat.*

**Fase Discovery & Negosiasi:**
1. UMKM buka menu `Kreator` -> Cari profil kreator -> Lihat paket Rate Card.
2. UMKM klik "Hubungi Kreator". Sistem membuat order dengan status **Negosiasi**.
3. UMKM & Kreator mengobrol di `Chat Room` (Realtime).
4. UMKM membuat kesepakatan final dengan mengirim **Custom Offer** (widget dalam chat berisi detail: judul, scope, harga final).
5. Kreator menerima/menolak *Custom Offer*.
   - Jika *Terima* -> Status order menjadi **Menunggu Pembayaran**.

**Fase Pembayaran & Eksekusi:**
1. UMKM melakukan pembayaran via deposit (Midtrans Snap).
2. Dana ditahan oleh platform, status order menjadi **Escrow**.
3. Kreator memproduksi dan mengunggah video.
4. Kreator menempelkan link hasil (*Collab Post*) di sistem. Status order menjadi **Menunggu Verifikasi**.

**Fase Penyelesaian:**
1. UMKM memeriksa video.
   - *Percabangan A:* UMKM klik "Selesai" -> Dana cair ke dompet kreator.
   - *Percabangan B:* UMKM minta Revisi.
   - *Percabangan C:* UMKM komplain -> Buka Sengketa (Dispute) -> Admin yang akan mengambil keputusan.