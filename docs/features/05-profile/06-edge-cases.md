# Edge Cases: Modul Profil

## Skenario Error & Penanganan

1. **Ukuran Unggahan Foto Lebih dari 5MB**
   - **Kondisi**: Pengguna memasukkan berkas RAW foto resolusi 8K (15MB) yang tidak berhasil terkompresi secara optimal.
   - **Respons Sistem**: Penolakan dari Storage Appwrite akan menghasilkan galat 400 Bad Request / ukuran *exceed limit*.
   - **Tindakan UI**: Fungsi *compress* seharusnya mencegah hal ini sebelumnya. Jika masih kelebihan beban, stop loading avatar, kembalikan UI avatar ke gambar semula, tampilkan notifikasi di baris bawah: "Gagal mengganti foto. Pastikan ukuran file di bawah 5MB."

2. **Perubahan Profil Namun Offline (Koneksi Putus)**
   - **Kondisi**: User memperbaiki biografi yang panjang dan menekan simpan namun kehilangan koneksi internet.
   - **Tindakan UI**: Batalkan sinkronisasi data reaktif di klien, pertahankan form tanpa dibersihkan (supaya usaha ketikan user tidak hilang), peringatkan "Koneksi terputus. Gagal memuat perubahan".

3. **Ketidaksesuaian Render Cache (Profile Image Cache)**
   - **Kondisi**: Pengguna sukses mengubah foto, di server sudah berubah, tetapi *widget CachedNetworkImage* masih menampilkan gambar yang lama dari cache device.
   - **Tindakan/Fallback (*Asumsi Front End*)**: Pastikan saat *update* URL, UI men-trigger ulang penarikan kunci URL (dengan membubuhkan penanda waktu unik `?v=12345` di ujung URL agar *widget* mendeteksi referensi yang berbeda dan memuat citra teranyar).