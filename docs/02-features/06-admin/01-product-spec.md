# Overview: Modul Admin & Pelaporan

## Tujuan Fitur
Menyediakan pusat kendali operasi (Command Center) bagi tim internal Marketiv untuk memantau indikator bisnis, menangani keluhan sengketa (disputes), memoderasi pekerjaan, serta mengekspor data yang relevan dengan keperluan P2MW.

## Deskripsi Singkat
Panel Admin bersifat tertutup dan eksklusif untuk role `ADMIN`. Di dalam platform mobile-nya, Admin difokuskan pada moderasi cepat seperti penanganan *Fraud* bukti video dan sengketa Escrow. Namun, kalkulasi berat atau *export* CSV diarahkan untuk dilakukan melalui fungsi serverless / portal eksternal.

## Daftar Subfitur
- **Dashboard Overview**: Ringkasan taktis mengenai jumlah GMV (Gross Merchandise Value), pengguna aktif, dan *Dispute* yang macet.
- **Validasi Submissions**: Penandaan manual (atau persetujuan atas deteksi AI) apakah *link* pekerjaan kreator berstatus sah (Valid) atau rekayasa (Fraud).
- **Manajemen Sengketa (Disputes)**: Portal untuk memutuskan pergerakan Escrow di Rate Card Mode jika kesepakatan tidak tercapai (Refund ke UMKM atau paksa Transfer ke Kreator).
- **Ekspor Laporan (Web Link)**: Rujukan pengunduhan rekapitulasi operasional proyek.

## Dependensi dengan Fitur Lain
- **Fungsi Keuangan/Escrow**: Intervensi sengketa akan secara mutlak melangkahi aturan *hold* uang dan mengubah log di koleksi `transactions`.
- **Modul Campaign**: Perubahan status validasi submission akan menentukan apakah Campaign selesai atau butuh kreator lain.

## Edge Cases

# Edge Cases: Modul Admin

## Skenario Error & Penanganan

1. **Agregasi Data Macet / Timeout Saat Mengakses Statistik**
   - **Kondisi**: Jika jumlah pengguna mencapai batas tertentu, memuat ratusan ribu dokumen untuk dihitung manual tanpa *caching* menyebabkan `admin-stats-fn` kehabisan napas (*timeout limit 15s*).
   - **Tindakan (Penanganan Skrip Backend)**: Disarankan melakukan penambahan asinkron (menggunakan koleksi khusus *stats* yang memuat angka total yang di-update pertambahan hari) dibanding melakukan fungsi kalkulasi mentah setiap kali *endpoint* dashboard disapa.
   - **Tindakan UI**: Aplikasi admin menampilkan teks "Laporan Statistik Belum Dapat Dimuat" dengan tombol *refresh* terpisah.

2. **Dua Admin Melakukan Validasi Submission yang Sama**
   - **Kondisi**: Admin 1 menekan tombol *Valid*, di detik yang persis sama Admin 2 menekan tombol *Fraud* pada ID Submission tersebut.
   - **Tindakan UI/Logika**: Gunakan kontrol atomik (seperti pengecekan versi stempel *UpdatedAt*) atau terima *request* siapapun yang tiba lebih dulu. Skrip `validate-submission-fn` harus membatalkan permintaan jika mendeteksi field `status_validasi` sudah tidak lagi `Pending`. UI Admin kedua merespons galat: "Dokumen ini telah dieksekusi oleh Admin lain".

3. **Status Pencairan Sengketa Gagal Tertransfer**
   - **Kondisi**: Fungsi `refund-escrow-fn` terkendala (*out of memory*) atau gangguan koneksi pihak ketiga (Midtrans) sehingga modifikasi uang parsial terjadi.
   - **Tindakan Administratif**: Tidak dapat ditangani langsung oleh UI. Wajib ada pencatatan di *Appwrite Console Logs* sehingga tim teknis dapat melacak ketidakcocokan saldo uang di dompet virtual dan segera menyelesaikannya lewat perbaikan *database* seketika.