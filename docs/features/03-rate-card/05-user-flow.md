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