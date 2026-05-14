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