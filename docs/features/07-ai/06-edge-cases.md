# Edge Cases: Modul AI Features

## Skenario Error & Penanganan

1. **Model AI Down / Waktu Respon Terlampaui (Timeout)**
   - **Kondisi**: Peladen API LLM (OpenAI, Anthropic, Gemini) kelebihan muatan (*overload*) sehingga menolak merespon pertanyaan lebih dari 15 detik, memicu putus eksekusi dari Appwrite Server.
   - **Respons Sistem**: Function memuntahkan `HTTP 500` / `Function Timeout`.
   - **Tindakan UI**: Antarmuka wajib membuang logo *Loading* segera. Tampilkan *Snackbar* bernada damai: "Asisten AI saat ini sedang sibuk. Harap coba lagi beberapa saat, atau susun instruksi brief Anda secara manual."

2. **Aset Prompt Terlalu Kurang Informasi**
   - **Kondisi**: UMKM sama sekali tidak menulis Nama Produk (atau menulis string 1 karakter `a`), tetapi mengeklik asisten AI.
   - **Tindakan UI**: Cegah pemanggilan server sebelum divalidasi lokal. Tampilkan notifikasi "Harap cantumkan Nama Produk sebelum menjalankan asisten, agar instruksi kami tepat sasaran."

3. **Halusinasi Output (Inappropriate Content / Halusinasi)**
   - **Kondisi**: Asisten melontarkan kata-kata kotor, halusinasi nama merk kompetitor, atau skenario di luar kendali.
   - **Tindakan (Sisi Function)**: Pertegas perintah pada skrip parameter `system_prompt` untuk membatasi AI bersikap formal dan netral.
   - **Tindakan UI**: Sediakan opsi pengguna UMKM untuk merevisi atau mengeklik ulang asisten guna me-*regenerate* jawaban. Biarkan kotak form pada akhirnya memiliki kuasa untuk dimodifikasi secara manual.