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