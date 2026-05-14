# Kumpulan Prompt Reusable: Rate Card Mode

Gunakan *prompt* ini untuk memandu AI dalam pengembangan fitur Rate Card Mode.

## 1. Prompt Generate UI Chat & Realtime (Frontend + Backend)
> "Tolong buatkan layar `ChatRoomPage` di Flutter dengan GetX. Gunakan ListView yang dapat di-*scroll* dari bawah ke atas (reverse). Integrasikan dengan `AppwriteService.realtime` pada koleksi `messages` mengikuti struktur di `docs/features/03-rate-card/04-appwrite-schema.md`. Buatkan juga komponen widget `CustomOfferBubble` untuk merender pesan bertipe 'CustomOffer' di mana payload datanya dibongkar (decode) dari string JSON `offer_data`."

## 2. Prompt Logika Rate Card
> "Di dalam `RateCardController`, buat fungsi `createNewPackage()`. Baca pedoman `docs/features/03-rate-card/06-edge-cases.md`. Pastikan fungsi ini mengecek (query) terlebih dahulu ke `AppwriteService.databases` untuk menghitung berapa rate card milik kreator ini. Jika sudah mencapai 3, return pesan gagal lewat GetX Snackbar dan batalkan `createDocument`."

## 3. Prompt Render Direktori Kreator
> "Buatkan UI `KreatorDirectoryPage`. Tarik data dari koleksi `users` di mana atribut `role`='KREATOR'. Tampilkan dalam GridView menggunakan widget `KreatorCard`. Gunakan token warna dan tipografi dari panduan `docs/_global/design-system/`."