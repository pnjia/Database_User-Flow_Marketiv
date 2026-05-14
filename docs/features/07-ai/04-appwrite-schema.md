# Skema Appwrite: Modul AI

## Collection Terkait
Secara harfiah, pemrosesan kecerdasan buatan **tidak berkaitan** pada penyimpanan dan interaksi *database collection* manapun secara mandiri. Hasil komputasinya hanya dijadikan balasan instan (teks draf) yang menumpang diam di lapisan antarmuka. 

Hasil ini baru bermuara di basis data setelah pengguna mengeklik tombol Simpan dan menyetorkan formulir keseluruhan `campaigns`.

## Atribut & API Keys
Tidak ada tabel pendukung spesifik, namun membutuhkan asupan variabel lingkungan (*Environment Variables*) di sisi konfigurasi fungsi Appwrite.
- Key Env Server: `OPENAI_API_KEY` (bersifat tertutup).

## Functions
- `generate-brief-fn`: Berperan memproses *payload* klien `POST` menjadi deskripsi yang ramah dibaca.
- `validate-submission-fn` *(Ekstensi masa depan)*: Dapat memanfaatkan model analitik anomali angka untuk menaksir apakah rasio jumlah *view* TikTok dan jumlah komentar terlalu ekstrem, sehingga otomatis merekomendasikan `Fraud` ke dasbor Admin.