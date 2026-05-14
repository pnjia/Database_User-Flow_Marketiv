# Peta Situs (Sitemap) Marketiv

Dokumen ini memetakan seluruh halaman/screen yang ada di dalam aplikasi Marketiv berdasarkan role (UMKM, Kreator, Admin) beserta tujuan dan aksi utama di setiap halamannya.

## 1. Akses Bersama (Shared / Pra-Login)

| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Splash Screen** | Mengecek sesi aktif Appwrite dan mengarahkan pengguna. | Tidak ada (otomatis) |
| **Onboarding** | Penjelasan singkat aplikasi untuk pengguna baru. | Swipe layar, Lanjut, Masuk |
| **Login** | Autentikasi pengguna lama. | Input email & password, Masuk |
| **Register** | Pendaftaran akun baru. | Input data dasar, Daftar |
| **Role Selection** | Memilih role sebelum registrasi selesai (UMKM/Kreator). | Pilih role UMKM atau Kreator |

## 2. Navigasi Role UMKM

Hierarki Utama (Bottom Navigation): Beranda | Kampanye | Kreator | Keuangan | Profil

### 2.1 Beranda & Kampanye (Campaign Mode)
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Home (Dashboard)** | Ringkasan statistik UMKM (ROI, campaign aktif, pengeluaran). | Melihat ringkasan, navigasi cepat |
| **Daftar Campaign** | Menampilkan semua campaign yang pernah/sedang dibuat. | Lihat daftar, klik untuk detail, klik tombol tambah campaign |
| **Buat Campaign (Wizard)** | Formulir multi-step untuk membuat proyek Campaign Mode. | Input produk, upload aset, set budget, trigger AI, Bayar (Escrow) |
| **Detail Campaign** | Informasi lengkap satu campaign spesifik dan bukti tayang dari kreator. | Melihat status kuota, melihat submission URL dari kreator |

### 2.2 Kreator (Rate Card Mode)
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Direktori Kreator** | Mencari kreator untuk proyek fixed-price. | Mencari (search), filter niche/harga, klik profil |
| **Profil Kreator** | Melihat portofolio dan Rate Card kreator. | Melihat detail paket, klik "Hubungi Kreator" |
| **Daftar Negosiasi** | Menampilkan riwayat order Rate Card. | Memilih room chat order yang sedang aktif |
| **Chat Room** | Berkomunikasi realtime dengan kreator (Rate Card Mode). | Kirim pesan teks, kirim **Custom Offer**, setujui pekerjaan selesai |

### 2.3 Keuangan & Profil
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Keuangan** | Manajemen dana dan riwayat transaksi. | Melihat saldo Escrow, deposit (top-up via Midtrans), filter riwayat |
| **Profil UMKM** | Manajemen informasi bisnis. | Edit nama usaha, ubah foto profil, simpan, logout |

---

## 3. Navigasi Role KREATOR

Hierarki Utama (Bottom Navigation): Beranda | Job Pool | Pekerjaan | Saldo | Profil

### 3.1 Beranda & Job Pool (Campaign Mode)
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Home (Dashboard)** | Ringkasan pendapatan, rating, dan job aktif. | Melihat statistik |
| **Job Pool** | Bursa kerja campaign yang tersedia untuk diklaim. | Mencari, filter (niche/harga), klik card campaign |
| **Detail Job Pool** | Melihat brief dan detail harga sebelum klaim. | Baca brief, buka aset (eksternal), klik "Klaim Job Ini" |
| **Pekerjaan Aktif** | Melacak campaign yang sedang dikerjakan kreator. | Pilih pekerjaan yang belum selesai |
| **Submit Bukti Tayang** | Menyerahkan hasil kerja (URL) untuk Campaign Mode. | Input URL TikTok/IG, submit bukti |

### 3.2 Negosiasi & Keuangan (Rate Card Mode)
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Daftar Negosiasi** | Melacak pesanan masuk (Custom Offer). | Klik room nego |
| **Chat Room** | Diskusi realtime dengan UMKM. | Balas chat, terima/tolak *Custom Offer*, kirim link hasil kerja |
| **Keuangan** | Manajemen saldo dompet dan penarikan (withdrawal). | Lihat saldo, isi form bank, klik "Tarik Dana" |

### 3.3 Profil & Rate Card
| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Profil Kreator** | Manajemen biodata publik dan Rate Card. | Edit bio, ganti foto profil, klik menu "Kelola Rate Card", logout |
| **Kelola Rate Card** | Menambah, mengubah, atau menghapus paket jasa (Maks 3). | Tambah paket, isi harga dan deskripsi, simpan/hapus paket |

---

## 4. Navigasi Role ADMIN (Read-Only di Mobile)

Hierarki Utama: Dashboard | Disputes | Submissions | Laporan

| Halaman | Tujuan | Aksi Utama Pengguna |
|---|---|---|
| **Admin Home** | Metrik ringkasan performa platform. | Lihat GMV, pengguna aktif, jumlah sengketa |
| **Disputes** | Daftar order yang masuk sengketa. | Buka detail, klik "Validasi", "Tahan Dana", atau "Refund" |
| **Submissions** | Memantau bukti tayang yang masuk (validasi manual). | Cek URL, tandai "Valid" atau "Fraud" |
| **Laporan** | Ringkasan dan tautan ekspor data. | Lihat ringkasan bulanan |