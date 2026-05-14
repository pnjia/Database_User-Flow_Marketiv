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