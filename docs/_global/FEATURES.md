# FEATURES.md — Spesifikasi Modul Fitur Marketiv (Flutter + GetX)

> Kembali ke: [README.md](README.md) | [DATABASE.md](DATABASE.md) | [TECHNICAL_GUIDELINES.md](TECHNICAL_GUIDELINES.md)

---

## Daftar Isi

1. [Sistem Autentikasi & Role](#1-sistem-autentikasi--role)
2. [Modul Campaign Mode](#2-modul-campaign-mode)
3. [Modul Rate Card Mode](#3-modul-rate-card-mode)
4. [Modul Keuangan & Escrow](#4-modul-keuangan--escrow)
5. [Modul AI Features](#5-modul-ai-features)
6. [Modul Admin & Pelaporan](#6-modul-admin--pelaporan)

---

## 1. Sistem Autentikasi & Role

### 1.1 Alur Registrasi

```
1. User pilih role: "Saya Pemilik UMKM" atau "Saya Konten Kreator"
2. User isi form dasar: nama_lengkap, email, password, nomor_whatsapp
3. Jika role = UMKM, user juga isi nama_usaha untuk nama brand/bisnis
4. Jika role = KREATOR, user juga isi username_kreator untuk identitas publik
5. AuthController → RegisterUseCase → AuthRepository → Appwrite Account.create()
6. Appwrite kirim email verifikasi (Account.createVerification())
7. Setelah verifikasi → is_verified = TRUE di collection users
8. Profile document otomatis dibuat di collection `users` setelah registrasi sukses
   (INSERT ke Databases setelah Account.create() berhasil)
```

### 1.2 Alur Login & Session

```
1. User input email + password di LoginPage
2. AuthController → LoginUseCase → AuthRepository → Appwrite Account.createEmailPasswordSession()
3. Session JWT disimpan otomatis oleh Appwrite SDK (via cookie/local)
4. StorageService simpan role + user_id ke GetStorage untuk akses cepat
5. Redirect berdasarkan role:
   - UMKM    → Routes.umkmHome
   - KREATOR → Routes.kreatorHome
   - ADMIN   → Routes.adminHome
6. Session persist setelah app close (Appwrite auto-refresh via Account.getSession())
```

### 1.3 Alur Logout

```dart
// AuthDataSource
Future<void> logout() async {
  await AppwriteService.account.deleteSession(sessionId: 'current');
  StorageService.clearAll(); // hapus role + user_id dari GetStorage
}
```

### 1.4 Cek Session Aktif (Splash Screen)

```dart
// SplashController
Future<void> checkSession() async {
  try {
    final account = await AppwriteService.account.get();
    // Session masih aktif — ambil role dari GetStorage atau query collection users
    final role = StorageService.getRole();
    _redirectByRole(role);
  } on AppwriteException catch (e) {
    if (e.code == 401) {
      // Tidak ada session — ke onboarding/login
      Get.offAllNamed(Routes.onboarding);
    }
  }
}
```

### 1.5 Role-Based Access Control (RBAC)

| Fitur / Screen | UMKM | KREATOR | ADMIN |
|---|:---:|:---:|:---:|
| Buat Campaign | ✅ | ❌ | ❌ |
| Lihat Job Pool | ❌ | ✅ | ✅ |
| Klaim Job | ❌ | ✅ | ❌ |
| Submit URL Bukti Tayang | ❌ | ✅ | ❌ |
| Direktori Kreator (Rate Card) | ✅ | ❌ | ✅ |
| Live Chat (Rate Card) | ✅ | ✅ | ❌ |
| Kirim Custom Offer | ✅ | ❌ | ❌ |
| Buat/Edit Rate Card | ❌ | ✅ | ❌ |
| Top-up / Deposit | ✅ | ❌ | ❌ |
| Withdraw Saldo | ❌ | ✅ | ❌ |
| Dashboard Admin | ❌ | ❌ | ✅ |
| Export Laporan GMV | ❌ | ❌ | ✅ |
| Batalkan/Tahan Escrow | ❌ | ❌ | ✅ |

### 1.6 Entity & Model Auth

```dart
// domain/entities/user_entity.dart
class UserEntity {
  final String id;           // Appwrite document $id
  final String userId;       // Appwrite Auth $id
  final String role;         // 'UMKM' | 'KREATOR' | 'ADMIN'
  final String namaLengkap;
  final String? namaUsaha;   // khusus UMKM
  final String? usernameKreator; // khusus KREATOR
  final String email;        // dibaca dari Appwrite Account, bukan dari collection
  final String? nomorWhatsapp;
  final double dompetSaldo;
  final String? niche;       // untuk kreator
  final String? fotoProfilUrl;
  final String? bio;
  final bool isVerified;
}
```

### 1.7 Skenario Nama per Role

| Role | `nama_lengkap` | `nama_usaha` | Keterangan |
|---|---|---|---|
| UMKM | Nama pribadi pemilik | Nama brand/usaha | `nama_usaha` dipakai di tampilan publik |
| KREATOR | Nama pribadi | null | `username_kreator` dipakai di katalog/chat |
| ADMIN | Nama pribadi staff | null | Untuk audit dan identitas internal |

> Jadi, untuk UMKM, `nama_lengkap` **bukan** otomatis nama usaha. Nama usaha disimpan terpisah di `nama_usaha`.

> Untuk kreator, `username_kreator` adalah identitas publik yang bisa ditampilkan di direktori, chat, dan kartu profil.

> `username_kreator` sebaiknya unik, singkat, dan mudah dikenali.

### 1.8 Controller Pattern Auth

```dart
// Gunakan private Rx dengan public getter
class AuthController extends GetxController {
  final LoginUseCase _loginUseCase;
  final RegisterUseCase _registerUseCase;

  final _isLoading = false.obs;
  final _user = Rxn<UserEntity>();

  bool get isLoading => _isLoading.value;
  UserEntity? get user => _user.value;

  Future<void> login(String email, String password) async {
    _isLoading.value = true;
    final result = await _loginUseCase(LoginParams(email: email, password: password));
    result.fold(
      (failure) => Get.snackbar('Gagal Login', failure.message),
      (user) {
        _user.value = user;
        _redirectByRole(user.role);
      },
    );
    _isLoading.value = false;
  }

  void _redirectByRole(String role) {
    if (role == 'UMKM')    Get.offAllNamed(Routes.umkmHome);
    if (role == 'KREATOR') Get.offAllNamed(Routes.kreatorHome);
    if (role == 'ADMIN')   Get.offAllNamed(Routes.adminHome);
  }
}
```

---

## 2. Modul Campaign Mode

> Campaign Mode adalah sistem **performance-based marketing** di mana UMKM membayar berdasarkan jumlah views yang tervalidasi. **TIDAK ADA komunikasi (chat) sama sekali** di mode ini.

### 2.1 Alur Lengkap End-to-End

```
FASE 1: INISIASI & DEPOSIT (Sisi UMKM)
  1. UMKM buka CreateCampaignPage (wizard multi-step)
  2. Step 1: Isi info produk (judul, deskripsi, niche)
  3. Step 2: Upload aset (foto ≤ 100MB) ATAU input URL Google Drive
  4. Step 3: Set budget & kuota kreator
  5. Step 4: Review + bayar via Midtrans Snap (WebView)
  6. Midtrans kirim notifikasi ke Appwrite Function (midtrans-webhook-fn)
  7. Appwrite Function update status campaign → 'Aktif'
  8. Campaign muncul di Job Pool kreator
      ↓
FASE 2: KLAIM & EKSEKUSI (Sisi Kreator)
  9. Kreator browse JobPoolPage → lihat kartu campaign
  10. Kreator buka JobPoolDetailPage → lihat brief + URL aset
  11. Kreator tap "Klaim Job Ini"
  12. Sistem cek kuota via Appwrite Function (claim-campaign-fn):
      - Ya  → kuota_terpakai+1 → submission dibuat (Pending)
      - Tidak → tombol disabled, tampil "Kuota Penuh"
  13. Kreator akses aset via URL Drive (buka di browser eksternal)
  14. Kreator edit video di luar app (CapCut, dll)
  15. Kreator posting di TikTok/Instagram PRIBADI kreator
      ↓
FASE 3: VALIDASI & PENCAIRAN (Kreator + Sistem)
  16. Kreator buka PekerjaanAktifPage
  17. Kreator paste URL publik TikTok/IG
  18. Kreator tap "Submit Bukti Tayang"
  19. DataSource update submission document di Appwrite
  20. Appwrite Function validate-submission-fn cek anomali views
  21. Jika VALID → Appwrite Function release-escrow-fn rilis dana ke dompet_saldo kreator
  22. UMKM mendapat ringkasan: total views, status, ROI
```

### 2.2 Form Buat Campaign (Wizard Multi-Step)

**Step 1 — Informasi Produk:**
```
Fields:
- judul_campaign       : TextField, required, max 200 char
  Label: "Nama Produk / Judul Campaign"
  Hint: "contoh: Dapur Sehat — Sambal Matah Khas Sukabumi"

- deskripsi_brief      : TextField multiline, required, min 50 char
  Label: "Ceritakan Produk & Instruksi untuk Kreator"
  Tombol: "✨ Bantu Saya dengan AI" → trigger AI Brief Assistant

- niche                : DropdownButton, required
  Options: Kuliner, Fesyen, Pariwisata, Edukasi, Kecantikan, Lainnya
```

**Step 2 — Upload Aset:**
```
- Opsi A: Image picker (foto produk, logo)
  Validasi WAJIB:
    if (file.size > 100 * 1024 * 1024) {
      tampilkan error merah: "File melebihi 100MB. Gunakan link Google Drive."
    }
  Upload ke Appwrite Storage bucket 'campaign_assets'

- Opsi B: TextField URL eksternal
  Label: "Link Google Drive / Dropbox aset video mentah"
  Validasi: harus dimulai https://, tidak boleh kosong jika Opsi A tidak diisi

- Warning Banner (selalu tampil, warna oranye):
  "📌 Untuk video bahan mentah berukuran besar, WAJIB gunakan link Google Drive/Dropbox.
   Marketiv tidak menyimpan file video secara internal."
```

**Step 3 — Budget & Kuota:**
```
- harga_per_1000_views : TextField angka, required
  Label: "Bayaran per 1.000 Views (Rp)"
  Range: Rp 2.000 — Rp 10.000

- kuota_kreator        : TextField angka / Stepper widget, required
  Label: "Berapa kreator yang bisa klaim campaign ini?"
  Min: 1, Max: 100

- total_budget_escrow  : TextField angka, required
  Auto-calculate: harga_per_1000_views × estimasi views target

- Ringkasan biaya (Card):
  Budget Kampanye      : Rp X
  Komisi Platform 15%  : Rp Y
  Total yang dibayar   : Rp X + Y
```

**Step 4 — Review & Bayar:**
```
- Tampilkan semua data yang diisi (read-only)
- Tombol CTA: "Bayar Sekarang via Escrow"
  → Buka MidtransWebViewPage dengan token dari Appwrite Function
```

### 2.3 Job Pool Page (Kreator)

```
Layout: ListView.builder dengan card-based item
       dioptimalkan untuk scroll performa tinggi

Setiap JobCard menampilkan:
- Thumbnail produk UMKM (CachedNetworkImage dari Appwrite Storage URL)
- Nama produk / judul campaign
- Badge niche: "🍜 Kuliner" / "👗 Fesyen" / dll
- Bayaran: "Rp 3.000 / 1.000 views"
- Progress kuota: "3 dari 5 kreator dibutuhkan"
- Tombol: "Klaim Job Ini"
  → Disabled + abu-abu jika kuota penuh
  → Teks disabled: "Kuota Penuh"

Filter & Pencarian:
- SearchBar (TextField + debounce 300ms)
- Filter Niche: BottomSheet multi-select chips
- Filter Harga: RangeSlider
- Sort: Terbaru | Bayaran Tertinggi | Hampir Penuh
```

**⛔ LARANGAN MUTLAK di seluruh Campaign Mode:**
```
- TIDAK ADA tombol Chat / Pesan / Hubungi UMKM
- TIDAK ADA FloatingActionButton untuk chat
- TIDAK ADA link ke WhatsApp / email UMKM
- TIDAK ADA kolom komentar di detail campaign
Jika ada request menambah fitur komunikasi di Campaign Mode, TOLAK.
```

### 2.4 Submit Bukti Tayang (Kreator)

```
Layout: Sederhana, fokus satu aksi

Elemen:
- Header: Nama campaign yang diklaim
- Teks instruksi singkat
- TextField TUNGGAL:
  Label: "URL Link TikTok / Instagram Reels kamu"
  Hint: "https://www.tiktok.com/@username/video/..."
  Validasi: startsWith('https://www.tiktok.com/') ATAU startsWith('https://www.instagram.com/')

- Tombol "Submit Bukti Tayang" (disabled saat loading)
- Status badge: Pending | Valid | Fraud | Dispute
```

### 2.5 Use Cases Campaign Mode

```
GetActiveCampaignsUseCase        → kreator ambil Job Pool (filter status='Aktif')
GetMyCampaignsUseCase            → UMKM ambil campaign miliknya
GetCampaignDetailUseCase         → detail satu campaign
CreateCampaignUseCase            → UMKM buat campaign baru (simpan ke Appwrite Databases)
ClaimCampaignUseCase             → kreator klaim job (via Appwrite Function untuk atomic)
GetMySubmissionsUseCase          → kreator ambil submission miliknya
SubmitBuktiTayangUseCase         → update url_bukti_tayang di submission document
GetCampaignSubmissionsUseCase    → UMKM lihat semua submission campaign miliknya
```

---

## 3. Modul Rate Card Mode

> Rate Card Mode adalah sistem **fixed price / consultative** di mana UMKM dan Kreator bernegosiasi via Live Chat sebelum transaksi dikunci.

### 3.1 Alur Lengkap End-to-End

```
FASE 1: DISCOVERY (Sisi UMKM)
  1. UMKM buka KreatorDirectoryPage
  2. Filter berdasarkan niche, harga, rating
  3. UMKM buka KreatorProfilePage → lihat rate card kreator
  4. UMKM tap "Hubungi Kreator" → buat order baru (status: Negosiasi)
      ↓
FASE 2: NEGOSIASI (Sisi UMKM & Kreator)
  5. Kedua pihak chat di ChatRoomPage (Appwrite Realtime)
  6. UMKM bisa kirim "Custom Offer" → MessageModel.tipe_pesan = 'CustomOffer'
     offer_data: JSON string {judul_proyek, scope, harga_final, deadline}
  7. Kreator terima → status order → 'Menunggu Pembayaran'
  8. Kreator tolak → lanjut negosiasi
      ↓
FASE 3: PEMBAYARAN & EKSEKUSI
  9. UMKM bayar via Midtrans Snap
  10. Midtrans webhook → Appwrite Function → status order → 'Escrow'
  11. Kreator eksekusi konten
  12. Kreator submit URL Collab Post
  13. Status → 'Menunggu Verifikasi'
      ↓
FASE 4: SELESAI / DISPUTE
  14. UMKM konfirmasi pekerjaan selesai → 'Selesai'
      Appwrite Function release-escrow-fn → transfer ke dompet kreator
  15. ATAU UMKM minta revisi → status → 'Revisi'
  16. ATAU salah satu pihak buka dispute → status → 'Dispute'
      Admin intervensi via AdminDisputesPage
```

### 3.2 Live Chat (ChatRoomPage)

```
⚠️ Realtime HANYA aktif di halaman ini menggunakan Appwrite Realtime.

Elemen UI:
- ListView.builder pesan (reverse: true, terbaru di bawah)
- Setiap MessageBubble:
    - Text biasa: bubble biru (sender) / abu (receiver)
    - CustomOffer: Card khusus dengan detail proyek + tombol Terima/Tolak
    - System: teks abu tengah (e.g. "Order dikonfirmasi")
- TextField input + tombol Kirim + EmojiPicker
- Tidak ada attachment file (hanya teks dan Custom Offer)

Aturan:
- Chat HANYA tersedia di Rate Card Mode
- Chat TIDAK ADA di Campaign Mode (lihat § 2 larangan mutlak)
```

### 3.3 Custom Offer Widget

```dart
// Custom Offer dikirim sebagai message dengan tipe_pesan = 'CustomOffer'
// offer_data disimpan sebagai JSON string di Appwrite

// Contoh offer_data:
final offerData = jsonEncode({
  'judul_proyek': 'Video Promosi Sambal Matah',
  'scope_pekerjaan': '1 video TikTok 60 detik + 1 reels IG',
  'harga_final': 350000.0,
  'deadline': '2026-06-01',
});

// Saat display, decode dulu:
final offer = jsonDecode(message.offerData!);
```

### 3.4 Use Cases Rate Card Mode

```
CreateOrderUseCase               → UMKM buat order baru (mulai negosiasi)
GetMyOrdersUseCase               → semua order milik UMKM atau Kreator
GetOrderDetailUseCase            → detail satu order
SendMessageUseCase               → kirim pesan di chat room (Databases.createDocument)
GetMessagesUseCase               → ambil pesan (init load)
SendCustomOfferUseCase           → UMKM kirim custom offer
RespondCustomOfferUseCase        → Kreator terima/tolak offer
SubmitCollabPostUseCase          → Kreator submit URL Collab Post
ConfirmOrderCompleteUseCase      → UMKM konfirmasi pekerjaan selesai
GetRateCardsUseCase              → Kreator ambil list rate card miliknya
CreateRateCardUseCase            → Kreator buat paket baru (cek maks 3)
UpdateRateCardUseCase            → Kreator edit paket
DeleteRateCardUseCase            → Kreator hapus paket
```

---

## 4. Modul Keuangan & Escrow

### 4.1 Prinsip Escrow

```
ATURAN MUTLAK:
- Dana UMKM TIDAK PERNAH langsung dikirim ke kreator tanpa validasi
- Semua dana transit via collection TRANSACTIONS dengan status tracking jelas
- Escrow hanya dilepas setelah:
    Campaign Mode  : URL bukti tayang valid + views tervalidasi
    Rate Card Mode : URL Collab Post valid + tidak ada dispute aktif
- UMKM tidak bisa tarik dana yang sudah di-Escrow
  (kecuali campaign dibatalkan sebelum ada klaim)
- Semua INSERT/UPDATE ke TRANSACTIONS hanya lewat Appwrite Function (server-side)
```

### 4.2 Top-Up / Deposit (UMKM)

```
Metode pembayaran via Midtrans Snap (WebView):
- Virtual Account: BCA, BNI, BRI, Mandiri, Permata
- e-Wallet: GoPay, OVO, Dana, ShopeePay
- QRIS

Alur di Flutter:
1. UMKM tap "Bayar Sekarang"
2. KeuanganController → CreateTransactionUseCase
3. DataSource hit Appwrite Function (midtrans-create-fn) → Function request ke Midtrans API
4. Dapat snap_token → buka MidtransWebViewPage
5. User bayar di WebView Midtrans
6. Midtrans kirim webhook ke Appwrite Function (midtrans-webhook-fn)
7. Function update status transaksi → 'Escrow' di collection TRANSACTIONS
8. App refresh status via Appwrite Realtime (subscribe collection transactions)

PENTING:
- Flutter client TIDAK PERNAH memanggil Midtrans Server Key langsung
- Semua logic Midtrans server-side ada di Appwrite Functions
```

### 4.3 Penarikan Dana / Withdrawal (Kreator)

```
Syarat:
- dompet_saldo > 0
- Tidak ada submission aktif dalam dispute

Form Withdrawal:
- nominal        : TextField angka (min: Rp 10.000)
- nama_bank      : DropdownButton (BCA, BNI, BRI, Mandiri, dll)
- nomor_rekening : TextField angka
- nama_pemilik   : TextField

Alur:
1. Kreator isi form → tap "Tarik Dana"
2. KeuanganController → WithdrawUseCase → validasi saldo cukup
3. DataSource panggil Appwrite Function (withdraw-fn)
4. Function hit Midtrans Iris / Xendit → proses disbursement
5. Status transaksi: Pending → Success
6. Kreator dapat notifikasi in-app
```

### 4.4 Riwayat Transaksi

```
Layout: ListView transaksi dengan filter

Setiap TransactionItem menampilkan:
- Tipe: Deposit / Withdrawal / Pencairan / Fee / Refund
- Nominal (merah untuk keluar, hijau untuk masuk)
- Status badge: Pending | Escrow | Success | Failed | Refunded
- Tanggal & waktu
- Keterangan (nama campaign / order)

Filter: Semua | Deposit | Pencairan | Withdrawal
Date range picker

Query Appwrite:
  databases.listDocuments(
    queries: [
      Query.equal('user_id', currentUserId),
      Query.orderDesc('\$createdAt'),
      Query.limit(20),
    ]
  )
```

### 4.5 Entity & Model Transaksi

```dart
// domain/entities/transaction_entity.dart
class TransactionEntity {
  final String id;
  final String userId;
  final String? idReferensi;        // $id campaign atau order
  final String? tipeReferensi;      // 'Campaign' | 'RateCard'
  final double nominal;
  final String tipe;                // 'Deposit'|'Withdrawal'|'Fee'|'Refund'|'Pencairan'
  final String status;              // 'Pending'|'Escrow'|'Success'|'Failed'|'Refunded'
  final String? midtransOrderId;
  final String? keterangan;
  final DateTime createdAt;
}
```

### 4.6 Use Cases Keuangan

```
CreateDepositTransactionUseCase   → UMKM deposit (panggil Appwrite Function, dapat snap token)
WithdrawUseCase                   → Kreator request withdrawal (panggil Appwrite Function)
GetTransactionHistoryUseCase      → riwayat transaksi user (query Databases)
GetWalletBalanceUseCase           → saldo dompet kreator (query collection users)
ReleaseEscrowUseCase              → rilis escrow ke kreator (dipanggil setelah validasi)
RefundEscrowUseCase               → kembalikan escrow ke UMKM
```

---

## 5. Modul AI Features

### 5.1 AI Brief Assistant

```
Trigger: Tombol "✨ Bantu Saya dengan AI" di Step 1 Form Campaign

Alur di Flutter:
1. UMKM tap tombol "Bantu dengan AI"
2. CampaignController → GenerateBriefUseCase
3. DataSource panggil Appwrite Function (generate-brief-fn) via Functions.createExecution()
4. Appwrite Function panggil OpenAI API (gpt-4o-mini)
   — OPENAI_API_KEY hanya ada di environment variable Appwrite Function, TIDAK di client
5. Response dikirim balik ke Flutter via execution.responseBody
6. Hasil tampil di TextField deskripsi_brief
7. Label tampil: "✨ Draf oleh AI — silakan edit sesuai kebutuhan Anda"
8. UMKM bebas edit hasil AI

Input yang dikirim ke Appwrite Function:
- nama_produk
- deskripsi_singkat (opsional)
- niche

⚠️ TIDAK BOLEH memanggil OpenAI langsung dari Flutter client
   (API key akan ter-expose di APK)
```

```dart
// Contoh pemanggilan di DataSource
Future<String> generateBrief(GenerateBriefParams params) async {
  final execution = await AppwriteService.functions.createExecution(
    functionId: 'generate-brief-fn',
    body: jsonEncode({
      'nama_produk': params.namaProduk,
      'niche': params.niche,
      'deskripsi': params.deskripsiSingkat,
    }),
    method: 'POST',
  );
  final data = jsonDecode(execution.responseBody);
  return data['brief'] as String;
}
```

### 5.2 AI Fraud Detection

```
Dijalankan secara server-side (Appwrite Function validate-submission-fn), bukan di Flutter.

Trigger: Kreator submit URL bukti tayang

Logika heuristik MVP (manual + semi-otomatis):
- Validasi URL format publik
- Flag ke admin jika views growth anomali
- Admin dapat override status via AdminSubmissionsPage

Status submission:
- 'Pending'  → menunggu validasi
- 'Valid'    → lolos, dana dilepas via release-escrow-fn
- 'Fraud'    → ditandai, admin review
- 'Dispute'  → sengketa aktif
```

---

## 6. Modul Admin & Pelaporan

> Hanya dapat diakses oleh user dengan `role = 'ADMIN'`. Route prefix: `/admin`.
> Pada mobile app, admin panel bersifat monitoring & aksi dasar. Export laporan dilakukan via web.

### 6.1 Dashboard Admin

```
AdminHomePage menampilkan:
- Total GMV (hari ini / minggu / bulan) → StatCard
- Total pengguna aktif (UMKM + Kreator) → StatCard
- Total campaign aktif → StatCard
- Total submission pending validasi → StatCard
- Total dispute aktif → StatCard (warna merah jika > 0)
- SimpleLineChart pertumbuhan user (6 bulan)

Data diambil via Appwrite Function (admin-stats-fn)
→ Function memiliki API Key penuh untuk aggregate query lintas collection
```

### 6.2 Manajemen Sengketa / Dispute

```
AdminDisputesPage:
- ListView semua order/submission dengan status 'Dispute'
  Query: databases.listDocuments(queries: [Query.equal('status', 'Dispute')])

Setiap DisputeCard:
- ID dispute (Appwrite $id)
- Nama UMKM + Nama Kreator
- Mode (Campaign / Rate Card)
- Nominal dana dalam dispute
- Tanggal

Aksi admin (BottomSheet detail):
1. "Validasi Manual" → panggil release-escrow-fn
2. "Tahan Dana"      → pertahankan status Dispute
3. "Refund ke UMKM"  → panggil refund-escrow-fn
4. "Blacklist Kreator" → update is_verified=false di collection users

Rule: Admin TIDAK BOLEH menerima bayaran dari salah satu pihak.
```

### 6.3 Manajemen Submission

```
AdminSubmissionsPage:
- Filter: Semua | Pending | Valid | Fraud | Dispute
- ListView SubmissionCard dengan:
  - Nama kreator + nama campaign
  - URL bukti tayang (tap → buka di browser via url_launcher)
  - Views aktual
  - Status badge
  - Tombol aksi: "Tandai Valid" | "Tandai Fraud"
    → Panggil Appwrite Function validate-submission-fn
```

### 6.4 Export Laporan P2MW

```
AdminReportsPage:
(Pada mobile: tampilkan ringkasan, link ke web untuk download CSV)

Data yang bisa diekspor (via web/admin panel):
1. GMV Report        → total transaksi per bulan, breakdown mode, komisi
2. User Activity     → UMKM + Kreator terdaftar vs aktif, retention rate
3. Campaign Report   → total campaign, views rata-rata, fraud rate
4. Transaction Report → semua transaksi detail

Target P2MW:
- Retention rate 65% di bulan ke-6
- 500 UMKM aktif
- GMV Rp 75.000.000/bulan
```

### 6.5 Use Cases Admin

```
GetAdminStatsUseCase              → statistik dashboard admin (via Appwrite Function)
GetDisputesUseCase                → daftar semua dispute (query Databases)
ResolveDisputeUseCase             → aksi admin pada dispute (via Appwrite Function)
GetAllSubmissionsUseCase          → semua submission + filter (query Databases)
ValidateSubmissionUseCase         → tandai valid/fraud (via Appwrite Function)
BlacklistUserUseCase              → blacklist kreator (update document users)
GetReportDataUseCase              → ambil data laporan P2MW (via Appwrite Function)
```

---

*Kembali ke: [README.md](README.md) | [DATABASE.md](DATABASE.md) | [TECHNICAL_GUIDELINES.md](TECHNICAL_GUIDELINES.md)*
