# Frontend: Modul AI Features

## Halaman Terkait & Route
| Halaman | Route | Keterangan |
|---|---|---|
| **Buat Campaign (Step 1)** | `/umkm/campaign/create` | Tombol dan implementasi fitur asisten penyusunan brief ada di formulir awal pembuatan kampanye. |

## Komponen UI Utama
- **`AI Assistant Button`**: Sebuah tombol gaya sekunder (*Secondary Button*) atau ikon gemerlap (*sparkles*) yang disematkan menempel di sudut kolom isian teks "Deskripsi Brief".
- **`Shimmer Loading Text`**: Efek kelap-kelip animasi pada wilayah kotak isian sewaktu sistem masih berkoordinasi merajut teks dari pihak API Kecerdasan Buatan.

## State Management
- `CreateCampaignController` membawahi tugas pengiriman *trigger* (*Create Execution Request*). 
- Begitu asisten AI selesai berpikir, *Controller* memperbarui *TextEditingController* dari kotak `deskripsi_brief` untuk mengganti *state* di UI.

## Validasi Form
- Fitur AI membutuhkan syarat prasyarat sebelum bisa ditekan, misalnya mengharuskan UMKM sekurang-kurangnya mengetik 3 kata nama produk agar model AI memiliki masukan (konteks) dasar pembuatan kalimat.

## Interaksi Pengguna
- Pengguna (UMKM) mengisi nama produk secara ringkas (contoh: "Keripik Singkong Level 10").
- Pengguna menekan tombol "✨ Bantu Saya dengan AI".
- Kolom "Instruksi Kreator" otomatis terisi *draft* kalimat pemasaran profesional.
- Pengguna dapat merombak dan mengedit lebih lanjut isi draf teks tersebut sesuka hatinya.