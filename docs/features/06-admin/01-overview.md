# Overview: Modul Admin & Pelaporan

## Tujuan Fitur
Menyediakan pusat kendali operasi (Command Center) bagi tim internal Marketiv untuk memantau indikator bisnis, menangani keluhan sengketa (disputes), memoderasi pekerjaan, serta mengekspor data yang relevan dengan keperluan P2MW.

## Deskripsi Singkat
Panel Admin bersifat tertutup dan eksklusif untuk role `ADMIN`. Di dalam platform mobile-nya, Admin difokuskan pada moderasi cepat seperti penanganan *Fraud* bukti video dan sengketa Escrow. Namun, kalkulasi berat atau *export* CSV diarahkan untuk dilakukan melalui fungsi serverless / portal eksternal.

## Daftar Subfitur
- **Dashboard Overview**: Ringkasan taktis mengenai jumlah GMV (Gross Merchandise Value), pengguna aktif, dan *Dispute* yang macet.
- **Validasi Submissions**: Penandaan manual (atau persetujuan atas deteksi AI) apakah *link* pekerjaan kreator berstatus sah (Valid) atau rekayasa (Fraud).
- **Manajemen Sengketa (Disputes)**: Portal untuk memutuskan pergerakan Escrow di Rate Card Mode jika kesepakatan tidak tercapai (Refund ke UMKM atau paksa Transfer ke Kreator).
- **Ekspor Laporan (Web Link)**: Rujukan pengunduhan rekapitulasi operasional proyek.

## Dependensi dengan Fitur Lain
- **Fungsi Keuangan/Escrow**: Intervensi sengketa akan secara mutlak melangkahi aturan *hold* uang dan mengubah log di koleksi `transactions`.
- **Modul Campaign**: Perubahan status validasi submission akan menentukan apakah Campaign selesai atau butuh kreator lain.