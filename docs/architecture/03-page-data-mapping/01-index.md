# Pemetaan Data & Halaman (Page Data Mapping)

Dokumen ini mendeskripsikan secara mendalam bagaimana setiap halaman pada sisi antarmuka Flutter berinteraksi dengan infrastruktur Appwrite, baik itu Database Collections, Storage, maupun eksekusi Appwrite Functions.

## Daftar File
- [02-auth-pages.md](02-auth-pages.md)
- [03-umkm-pages.md](03-umkm-pages.md)
- [04-kreator-pages.md](04-kreator-pages.md)
- [05-shared-pages.md](05-shared-pages.md)
- [06-admin-pages.md](06-admin-pages.md)

## Catatan Asumsi Tambahan
1. **Asumsi Pendelegasian Escrow:** Pindah buku dana dari dompet UMKM ke Escrow Kreator tidak langsung terjadi melalui client. Melainkan aplikasi client "memicu" pembuatan record melalui *Appwrite Functions* demi keamanan, di mana *Function* server-lah yang merubah tabel `transactions` dan tabel `users` (Atribut `dompet_saldo`).
2. **Ketergantungan Upload File:** Data unggahan gambar disimpan di Storage Bucket secara terpisah, lalu URL *string* dari hasil unggahan tersebut disisipkan ke *collection* database utama.
3. **Pembaruan Koleksi Submissions:** Mengacu pada `DATABASE.md`, perubahan field seperti `url_bukti_tayang` idealnya difasilitasi oleh Appwrite Function untuk mencegah manipulasi data *client-side*, meskipun SDK memungkinkan update langsung jika permission diatur.