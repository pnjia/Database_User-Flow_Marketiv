# Wireframes: Halaman Chat & Negosiasi

Dokumen ini mendeskripsikan struktur UI (wireframe) untuk fitur perpesanan (Chat) dan negosiasi yang digunakan pada alur **Rate Card Mode** antara UMKM dan Kreator.

## 1. Daftar Negosiasi (Inbox)
**Route**: Diakses melalui navigasi bawah (UMKM & Kreator) atau menu spesifik.

**Tujuan**: Menampilkan riwayat percakapan yang sedang aktif maupun yang sudah selesai.

**Komponen Utama**:
- **Header**: Judul "Pesan" atau "Daftar Negosiasi".
- **Tabs Filter (Opsional)**: "Aktif", "Selesai".
- **Daftar Obrolan (List View)**:
  - Foto Profil Lawan Bicara.
  - Nama Lawan Bicara (UMKM / Kreator).
  - Pesan Terakhir (Snippet teks chat terakhir).
  - Waktu Pesan Terakhir.
  - Badge Status Order (Misal: "Negosiasi", "Menunggu Pembayaran", "Escrow").

---

## 2. Chat Room (Ruang Obrolan)
**Tujuan**: Layar utama untuk berdiskusi *realtime* dan mengirimkan penawaran/bukti penyelesaian pekerjaan.

**Komponen Utama**:
- **Header Bar**:
  - Tombol Kembali.
  - Foto Profil & Nama Lawan Bicara.
  - Status Pesanan Terkini (Contoh label kecil di bawah nama: "Status: Negosiasi").
- **Area Pesan (Message Bubble)**:
  - Bubble Chat Kiri (Lawan bicara) dan Kanan (Pengguna).
  - Bubble Sistem (Notifikasi otomatis di tengah, misal: "Pesanan telah dibayar oleh UMKM").
- **Widget Spesifik di Area Pesan**:
  - **Widget Custom Offer (Dari UMKM)**:
    - Box khusus berisi: Judul Pekerjaan, Scope Pekerjaan, Harga Penawaran.
    - *Jika dilihat oleh Kreator*: Terdapat tombol "Terima Tawaran" dan "Tolak".
  - **Widget Bukti Hasil (Dari Kreator)**:
    - Box khusus berisi URL hasil video.
    - *Jika dilihat oleh UMKM*: Terdapat tombol "Pesanan Selesai / Terima" dan "Minta Revisi".
- **Input Bar (Bottom)**:
  - Tombol Attachment (Klip kertas, untuk kirim gambar/file referensi).
  - Kolom Input Teks ("Ketik pesan...").
  - Tombol Kirim (Icon Send).
  - **Tombol Khusus (Action Menu)**:
    - *Untuk UMKM*: Tombol "Kirim Penawaran" (Memunculkan form Custom Offer).
    - *Untuk Kreator*: Tombol "Kirim Hasil Kerja" (Memunculkan form input URL).