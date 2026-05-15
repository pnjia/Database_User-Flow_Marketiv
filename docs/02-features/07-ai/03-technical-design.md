# Technical Design

## Data Model Reference
*See `docs/01-system/database/` for authoritative schema.*

## Frontend

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
## Backend

# Backend: Modul AI Features

## Integrasi Appwrite
Semua panggilan koneksi API ke pihak penyedia *Language Model* (OpenAI / Gemini) ditanam ke dalam wadah `Appwrite Functions`. Praktik ini mencegah pembongkaran API Key secara merugikan di aplikasi *Android/iOS* kompilasi.

## Service yang Digunakan
- **Appwrite Functions**: Klien Flutter mengeksekusi `generate-brief-fn` mengirimkan argumen ringkas berwujud tipe *payload JSON*. 
- Skrip peladen tersebut lantas berdiskusi secara sekuensial kepada server *OpenAI API*.

## Permission
- Dapat ditekan (`EXECUTE`) secara terbuka oleh pengguna yang bertindak di dalam batasan koleksi (Tingkat autentikasi reguler UMKM / Kreator berstatus terdaftar).

## Process Flow (Logika Backend)
1. **Flow Generate Brief**:
   - `CreateCampaignController` di-Flutter menyatukan dua *string* ringkas: `nama_produk` & `niche`. Klien men-trigger `createExecution()`.
   - Di dalam mesin Serverless Node.js/Dart `generate-brief-fn`, struktur *Prompt System* statis diinjeksikan (contoh: "Bertindaklah sebagai asisten periklanan digital... Rangkai ringkasan brief kreator untuk produk ini: {{nama_produk}}...").
   - Panggilan diarahkan ke *HTTP POST* OpenAI API (`gpt-4o-mini`).
   - Teks dikerucutkan dari format respons OpenAI, kemudian diformat kembali sebagai string JSON `{"brief": "..."}` dan disorongkan ke klien Flutter.
## AI Prompt

# Kumpulan Prompt Reusable: AI Assistant Mode

Terapkan arahan ini demi kemudahan menyisipkan asisten berbasis Kecerdasan Buatan tanpa mengekspos resiko *coding* rawan.

## 1. Prompt Integrasi Frontend Tombol AI
> "Coba tengok kembali tampilan `CreateCampaignStep1` Anda. Sisipkan tombol *OutlinedButton* di sebelah judul pengisian *Brief*. Buat tombol tersebut memanggil sebuah method `generateAiBrief()` di dalam controller-nya. Tunjukkan status loading reaktif `isLoadingAi.value` agar ketika diakses, UI berubah menjadi *skeleton shimmer* atau tombol meredup (*disabled*) sebelum memantulkan nilai kembaliannya ke dalam `TextEditingController.text` untuk *Brief*."

## 2. Prompt Eksekusi Appwrite Serverless LLM
> "Bantu implementasikan fungsi `generate-brief-fn` Node.js untuk Appwrite yang menghubungkan pengguna ke layanan OpenAI.
> Instruksi Pengerjaan:
> 1. Terima *payload event* Appwrite (parameter masukan berupa *JSON*: `nama_produk` dan `niche`).
> 2. Panggil API `https://api.openai.com/v1/chat/completions`. Sembunyikan *token* di variabel proses lingkungan.
> 3. Gunakan *System Prompt* begini: 'Kamu adalah pakar penyusunan deskripsi pemasaran produk ringkas. Buat paragraf tak lebih dari 4 kalimat. Target audiens adalah Gen-Z'. 
> 4. Sisipkan nama produk yang dilemparkan klien di tengah-tengah pesan percakapan User (*User Prompt*).
> 5. Tangkap data kembalian asisten dari objek JSON LLM, dan tanggapi klien Appwrite dengan objek *Stringify* `{ "brief": ... }`."

## 3. Prompt Refactoring Fallback Timeout
> "Tolong refaktor *method* `generateAiBrief()` di `CampaignRemoteDataSource`. Bungkus panggilan eksekusi `createExecution` ini di dalam utilitas `Timeout` (misalnya batas waktu 10 detik). Apabila koneksi ini memunculkan `TimeoutException`, kirim lemparan *Error Failure* dan tampilkan pesan *Snackbar* bagi UMKM bahwa fitur AI sedang sangat sibuk dan dianjurkan mengetik brief-nya secara manual saja."