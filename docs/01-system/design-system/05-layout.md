# Tata Letak (Layout)

Panduan mengatur kontainer, batas lebar maksimal, dan responsivitas.

## Prinsip Mobile-First
Aplikasi ini didesain utama untuk tampilan mobile. Pastikan komponen nyaman digunakan di layar sentuh kecil.

## Ukuran & Breakpoint
- **Mobile Default**: Lebar referensi `375px` (standar viewport mobile).
- **Max-Width (Web/Tablet)**: `480px` (Membatasi lebar maksimal UI meskipun dibuka di web, agar tetap terasa seperti aplikasi mobile).

## Container & SafeArea
- **Screen Container**: Semua halaman utama harus dibungkus `SafeArea` (di Flutter) atau dihindarkan dari notch/status bar.
- **Screen Padding**: Gunakan padding horizontal `md` (16px) untuk konten utama aplikasi, agar tidak menempel ke tepi layar.

## Grid & Flex
- Gunakan tata letak fleksibel (Flexbox/Column/Row) daripada grid statis.
- Sisipkan `SizedBox` atau gap sesuai dengan skala spasi (contoh: gap `16px`).