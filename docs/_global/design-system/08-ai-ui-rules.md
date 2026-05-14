# Aturan UI untuk AI (AI UI Rules)

Saat AI (kamu) men-generate atau memodifikasi kode antarmuka (frontend), patuhi secara ketat aturan-aturan berikut agar hasil kode konsisten, profesional, dan mudah dikelola.

## 1. Prefer Tailwind CSS (atau setara Utility-First)
- Jika menggunakan web stack, prioritaskan penulisan gaya dengan *utility classes* (Tailwind).
- Jika menggunakan Flutter, prioritaskan penggunaan konstanta tema yang sudah ada (`AppColors`, `AppSpacing`). Jangan gunakan hardcoded hex/angka.

## 2. Gunakan Komponen Reusable
- Jangan buat elemen dari nol jika komponennya sudah ada (seperti `PrimaryButton`, `StatusBadge`, `AuthTextField`).
- Pisahkan UI kompleks menjadi fungsi atau *custom widget* mandiri jika dipakai lebih dari satu kali.

## 3. Mobile-First
- Pastikan desain kompatibel dengan lebar layar kecil.
- Gunakan `Expanded` atau `Flexible` untuk elemen yang memenuhi ruang sisa, jangan memberikan ukuran statis lebar yang dapat memotong layar (overflow).
- Pastikan *touch target* cukup besar (minimal 48x48 px).

## 4. Smooth Transition & Feedback
- Berikan efek loading (seperti Shimmer atau CircularProgressIndicator) pada setiap pengambilan data (*fetching*).
- Jangan ubah state mendadak tanpa indikator.

## 5. Rounded Corners & Consistent Spacing
- Terapkan lengkungan sudut pada card, input, dan tombol sesuai dokumen Spacing. Jangan campur sudut tajam 0px jika bukan niat spesifik desain.
- Pakai jarak spasi yang terstandar (contoh: 8, 16, 24). Jangan pakai angka ganjil tidak standar (contoh: 11, 17).

## 6. Semantic HTML / Proper Semantics
- Pada Flutter, gunakan Widget semantik yang sesuai. Gunakan `ListView.builder` untuk daftar dinamis panjang, bukan `SingleChildScrollView` + `Column`.
- Manfaatkan `SafeArea` agar UI tidak tertutup notch HP.