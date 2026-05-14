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