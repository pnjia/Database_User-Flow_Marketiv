# Alur Autentikasi & Sesi

1. **Aplikasi Dibuka** -> `Splash Screen`
   - *Sistem mengecek session via Appwrite SDK.*
   - **Percabangan (Login):**
     - Jika ADA sesi aktif -> Pindah ke `Home` (berdasarkan Role UMKM / Kreator / Admin).
     - Jika TIDAK ADA sesi -> Pindah ke `Onboarding` (jika baru pertama kali) atau `Login`.
2. **Pendaftaran (Register):**
   - Pengguna memilih tipe peran (UMKM atau Kreator).
   - Mengisi form dasar (nama, email, password, WA) + field khusus (nama_usaha / username_kreator).
   - Klik Daftar -> Appwrite membuat akun dan mengirim verifikasi email.
   - Sistem menyimpan dokumen ke Appwrite Database (`users`).
