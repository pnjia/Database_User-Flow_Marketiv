# Overview: Modul AI Features

## Tujuan Fitur
Menyediakan asisten cerdas untuk mempermudah operasional pengguna dengan kemampuan literasi digital terbatas. Di sisi klien, membantu UMKM merangkai kalimat arahan kampanye (Brief). Di sisi pengelola, membantu proses mitigasi penipuan pengesahan hasil penayangan konten.

## Deskripsi Singkat
Modul AI bekerja sepenuhnya di ranah operasional siluman (Background/Serverless). Flutter Client tidak membawa modul khusus kecerdasan buatan, melainkan melemparkan teks mentah/konteks menuju *Appwrite Function*, di mana kunci otentikasi penyedia model (OpenAI / Gemini) disimpan secara proksimal. 

## Daftar Subfitur
- **AI Brief Assistant**: Beroperasi pada Step 1 Wizard Buat Campaign. UMKM mengetikkan gambaran sepintas mengenai produk mereka dan mengeklik asisten; Teks arahan yang profesional terbentuk.
- **AI Fraud Detection (Asumsi Peningkatan)**: Digunakan di ujung server secara parsial untuk menganalisis metrik kejanggalan anomali rasio pada penonton URL video kampanye yang disetorkan kreator sebelum diketok palu *Valid* oleh Admin.

## Dependensi dengan Fitur Lain
- **Modul Campaign (Pembuatan Campaign)**: Terhubung erat dan menjadi bagian alat bantu integral dari langkah pengisian formulir pembuatan pesanan bagi UMKM.
- **Modul Admin (Submission)**: Turut berkontribusi menimbang status *Fraud* pada unggahan bukti tayang.

## Edge Cases

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