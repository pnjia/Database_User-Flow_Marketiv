# Autentikasi (Account)

- **Metode:** Email & Password menggunakan Appwrite Account SDK.
- **Manajemen Sesi:** Sesi dikelola murni oleh SDK Appwrite (JWT tersimpan secara native).
- **Pengelompokan Role:** Role (UMKM / Kreator / Admin) tidak ditangani oleh label Appwrite Team, melainkan disimpan sebagai atribut `role` dalam koleksi `users`.
