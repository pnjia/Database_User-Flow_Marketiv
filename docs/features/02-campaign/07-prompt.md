# Kumpulan Prompt Reusable: Campaign Mode

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur Campaign Mode.

## 1. Prompt Generate UI Wizard (Frontend)
> "Tolong buatkan UI Flutter untuk Wizard Pembuatan Campaign di Marketiv. Buat 4 langkah menggunakan GetX untuk navigasinya (Info Produk, Upload Aset, Anggaran & Kuota, Review). Referensinya ada di `docs/features/02-campaign/02-frontend.md`. Pastikan form reaktif menghitung estimasi biaya secara otomatis di Langkah 3, di mana total anggaran = (Harga * Target) + 15% fee platform."

## 2. Prompt Integrasi Klaim Job (Backend/Logic)
> "Implementasikan logika klaim campaign di `JobPoolController` dan `CampaignRemoteDataSource`. Jangan gunakan `databases.createDocument` secara langsung. Gunakan `AppwriteService.functions.createExecution()` untuk memanggil function `claim-campaign-fn` dengan parameter `campaignId` dan `kreatorId`. Jika *execution* melempar JSON error, tangkap dan tampilkan ke UI lewat *Snackbar* sesuai `docs/features/02-campaign/06-edge-cases.md`."

## 3. Prompt Refactor / Edge Cases Form
> "Periksa kode `CreateCampaignController`. Tambahkan validasi agar input file image *picker* ditolak jika ukurannya lebih dari 100MB dan aktifkan UI *WarningBanner* untuk meminta pengguna memakai kolom URL Eksternal."
