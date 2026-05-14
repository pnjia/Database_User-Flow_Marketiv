# TECHNICAL_GUIDELINES.md — Panduan Teknis, UI/UX, & Batasan Sistem Marketiv (Flutter + GetX)

> Kembali ke: [README.md](README.md) | [FEATURES.md](FEATURES.md) | [DATABASE.md](DATABASE.md)

---

## Daftar Isi

1. [Kebutuhan Non-Fungsional](#1-kebutuhan-non-fungsional)
2. [Navigasi & Struktur Halaman](#2-navigasi--struktur-halaman)
3. [Panduan UI/UX & Design Tokens](#3-panduan-uiux--design-tokens)
4. [Batasan Sistem (Hard Constraints)](#4-batasan-sistem-hard-constraints)
5. [Integrasi Eksternal](#5-integrasi-eksternal)
6. [Model Bisnis & Logika Komisi](#6-model-bisnis--logika-komisi)
7. [Pola Controller & State (GetX)](#7-pola-controller--state-getx)
8. [Catatan untuk AI Coding Assistant](#8-catatan-untuk-ai-coding-assistant)

---

## 1. Kebutuhan Non-Fungsional

### 1.1 Keandalan (Reliability)

```
- Session persist setelah app close (Appwrite Account.getSession() + GetStorage untuk role cache)
- Semua UseCase wajib return Either<Failure, T> — tidak ada unhandled exception
- Controller wajib gunakan try-catch di DataSource, bukan di Controller
- Tangani AppwriteException secara spesifik (lihat §1.3 Error Handling)
- Tampilkan error yang informatif via Get.snackbar(), bukan hanya "Terjadi Kesalahan"
- Implementasikan error boundary: setiap halaman punya state error yang ditampilkan dengan baik
```

### 1.2 Performa (Performance)

```
Target:
- Cold start app: < 3 detik pada perangkat mid-range
- List scroll: 60fps (gunakan ListView.builder, bukan Column + children loop)
- Image load: gunakan CachedNetworkImage untuk semua gambar remote
- API response: tampilkan shimmer loading selama request berlangsung

Cara mencapai:
- ListView.builder untuk semua daftar dinamis (campaign, kreator, transaksi, pesan)
- Lazy loading pagination — gunakan Query.limit() + Query.offset() di Appwrite
- CachedNetworkImage untuk foto profil dan thumbnail campaign
- Debounce SearchBar 300ms sebelum trigger API call
- Compress gambar sebelum upload ke Appwrite Storage (flutter_image_compress)
- Gunakan Query.select([...]) untuk ambil field spesifik saja, hindari ambil seluruh document
- Gunakan const constructor di widget statis untuk minimalkan rebuild
```

### 1.3 Error Handling — AppwriteException

```dart
// Semua DataSource WAJIB handle AppwriteException
// Jangan biarkan AppwriteException naik ke Controller tanpa ditransformasi

try {
  // ... Appwrite SDK call
} on AppwriteException catch (e) {
  switch (e.code) {
    case 401:
      throw UnauthorizedException('Sesi habis. Silakan login kembali.');
    case 404:
      throw NotFoundException('Data tidak ditemukan.');
    case 409:
      throw ConflictException('Data sudah ada atau konflik terjadi.');
    case 429:
      throw RateLimitException('Terlalu banyak permintaan. Coba lagi nanti.');
    default:
      throw ServerException('Terjadi kesalahan server: ${e.message}');
  }
} catch (e) {
  throw ServerException('Kesalahan tidak terduga: $e');
}
```

### 1.4 Keamanan (Security)

```
Wajib diimplementasikan:
1. Session JWT dikelola Appwrite SDK — JANGAN simpan token manual di plaintext GetStorage
   (GetStorage hanya untuk: role, user_id, nama, avatar_url — bukan token)
2. Document-level Permissions aktif di semua collection Appwrite
3. OPENAI_API_KEY TIDAK PERNAH ada di Flutter client / kode Dart
   → Semua panggilan AI lewat Appwrite Function (environment variable server-side)
4. Midtrans Server Key TIDAK PERNAH ada di Flutter client
   → Semua request Midtrans lewat Appwrite Function
5. Appwrite API Key (full access) TIDAK PERNAH ada di Flutter client
   → Hanya ada di Appwrite Function environment variables
6. Flutter hanya punya: APPWRITE_ENDPOINT + APPWRITE_PROJECT_ID (keduanya non-secret, boleh di .env)
7. Validasi input di Flutter (client) DAN di Appwrite Function (server)
8. URL eksternal user (Drive, TikTok, IG) wajib divalidasi format sebelum disimpan
9. File upload wajib dicek ukurannya di sisi Flutter sebelum dikirim ke Storage
10. JANGAN simpan password user dalam bentuk apapun di GetStorage
11. Gunakan HTTPS untuk semua request (Appwrite Cloud default sudah HTTPS)
```

### 1.5 Skalabilitas & Biaya (P2MW Constraint)

```
WAJIB: Aplikasi harus berjalan dalam Free Tier atau biaya minimal

Layanan yang digunakan:
- Appwrite Cloud Free Tier: 75.000 MAU, 2GB storage, 1GB database, 750K executions/bulan
- OpenAI gpt-4o-mini: pay-as-you-go (paling murah)
- Midtrans: hanya bayar per transaksi (tidak ada biaya bulanan)

Yang DILARANG pada fase MVP:
- Server hosting berbayar untuk backend (gunakan Appwrite Functions)
- Penyimpanan video internal skala besar (wajib URL eksternal)
- Fitur video editor built-in
- Push notification berbayar (gunakan Appwrite Realtime untuk in-app notification)
- Dedicated server database

Alokasi budget P2MW untuk pengembangan: MAKSIMAL 30% dari total dana
```

---

## 2. Navigasi & Struktur Halaman

### 2.1 Auth Flow (Sebelum Login)

```
Routes.splash
  → [Cek session Appwrite: Account.getSession('current')]
    → Session ada     → [Baca role dari GetStorage] → Routes.umkmHome / kreatorHome / adminHome
    → AppwriteException(401) → Routes.onboarding (jika first launch) → Routes.login
```

### 2.2 Bottom Navigation — Role UMKM

```
MainNavigationPage (IndexedStack)
  Index 0: UMKMHomePage       (Dashboard: ROI, campaign aktif, total spent)
  Index 1: CampaignListPage   (Daftar kampanye UMKM)
  Index 2: KreatorDirectoryPage (Direktori Kreator — Rate Card Mode)
  Index 3: KeuanganUMKMPage   (Riwayat transaksi, saldo escrow)
  Index 4: ProfilePage        (Edit profil UMKM)

Default index: 0

Nav items:
  🏠 Beranda | 📢 Kampanye | 🔍 Kreator | 💰 Keuangan | 👤 Profil
```

### 2.3 Bottom Navigation — Role KREATOR

```
MainNavigationPage (IndexedStack)
  Index 0: KreatorHomePage       (Dashboard: earnings, job aktif, rating)
  Index 1: JobPoolPage           (Bursa Kerja — Campaign Mode)
  Index 2: PekerjaanAktifPage    (Job yang sudah diklaim, submit URL)
  Index 3: KeuanganKreatorPage   (Saldo, riwayat pendapatan, withdrawal)
  Index 4: ProfilePage           (Edit profil & Rate Card)

Default index: 0

Nav items:
  🏠 Beranda | 💼 Job Pool | ✅ Pekerjaan | 💰 Saldo | 👤 Profil
```

### 2.4 Admin Navigation

```
AdminNavigationPage (DrawerNavigasi atau Bottom Nav):
  - Dashboard     (stats overview via Appwrite Function)
  - Disputes      (manajemen sengketa)
  - Submissions   (validasi bukti tayang)
  - Laporan       (ringkasan, link ke web untuk export)
```

### 2.5 Route Penting & Binding

```dart
// app_pages.dart (contoh sebagian)
GetPage(
  name: Routes.jobPool,
  page: () => const JobPoolPage(),
  binding: JobPoolBinding(),
),
GetPage(
  name: Routes.campaignCreate,
  page: () => const CreateCampaignPage(),
  binding: CampaignBinding(),
),
GetPage(
  name: Routes.negoRoomUMKM,
  page: () => const ChatRoomPage(),
  binding: ChatBinding(),
),
```

---

## 3. Panduan UI/UX & Design Tokens

### 3.1 Prinsip Desain

```
1. Frictionless untuk UMKM (literasi digital menengah ke bawah, usia 30-55)
   - Gunakan wizard (multi-step form), JANGAN form panjang satu layar
   - Icon WAJIB dilabeli dengan teks untuk aksi penting
   - Bahasa Indonesia sederhana — hindari jargon teknis
   - Konfirmasi dialog untuk aksi destruktif (batalkan campaign, tolak penawaran)

2. Mobile-First
   - Semua layout dioptimalkan untuk layar 375px lebar
   - Touch target minimum: 48×48px (Material Design guideline)
   - Jarak antar elemen interaktif minimal 8px
   - Font size minimum: 14sp body, 16sp input field

3. Loading States
   - Shimmer loading untuk setiap list/card yang menunggu data
   - CircularProgressIndicator untuk aksi tombol (disable tombol saat loading)
   - Skeleton placeholder untuk gambar yang belum load

4. Error States
   - Setiap halaman punya EmptyStateWidget dan ErrorStateWidget
   - Pesan error harus spesifik: "Gagal memuat daftar campaign. Periksa koneksi internet."
   - Tombol "Coba Lagi" pada state error
```

### 3.2 Design Tokens (AppColors)

```dart
// lib/core/constants/app_colors.dart
class AppColors {
  // Primary — Orange (warna utama brand Marketiv)
  static const primary50  = Color(0xFFFFF7ED);
  static const primary100 = Color(0xFFFFEDD5);
  static const primary500 = Color(0xFFF97316);  // Main orange
  static const primary600 = Color(0xFFEA580C);  // Hover/press
  static const primary700 = Color(0xFFC2410C);  // Active

  // Secondary — Navy/Dark Blue
  static const secondary500 = Color(0xFF1E3A5F);
  static const secondary600 = Color(0xFF162D4A);
  static const secondary700 = Color(0xFF0F2039);

  // Semantic
  static const success = Color(0xFF16A34A);
  static const warning = Color(0xFFD97706);
  static const danger  = Color(0xFFDC2626);
  static const info    = Color(0xFF2563EB);

  // Neutral
  static const grey50  = Color(0xFFF9FAFB);
  static const grey100 = Color(0xFFF3F4F6);
  static const grey300 = Color(0xFFD1D5DB);
  static const grey500 = Color(0xFF6B7280);
  static const grey700 = Color(0xFF374151);
  static const grey900 = Color(0xFF111827);

  // Background
  static const background = Color(0xFFFAFAFA);
  static const surface    = Color(0xFFFFFFFF);
}
```

### 3.3 Design Tokens (AppTextStyles)

```dart
// lib/core/constants/app_text_styles.dart
class AppTextStyles {
  // Heading — Newsreader
  static const h1 = TextStyle(fontFamily: 'Newsreader', fontSize: 32, fontWeight: FontWeight.w700);
  static const h2 = TextStyle(fontFamily: 'Newsreader', fontSize: 24, fontWeight: FontWeight.w700);
  static const h3 = TextStyle(fontFamily: 'Newsreader', fontSize: 20, fontWeight: FontWeight.w600);

  // Body — Inter
  static const bodyLarge  = TextStyle(fontFamily: 'Inter', fontSize: 16, fontWeight: FontWeight.w400);
  static const bodyMedium = TextStyle(fontFamily: 'Inter', fontSize: 14, fontWeight: FontWeight.w400);
  static const bodySmall  = TextStyle(fontFamily: 'Inter', fontSize: 12, fontWeight: FontWeight.w400);

  // Label
  static const labelMedium = TextStyle(fontFamily: 'Inter', fontSize: 14, fontWeight: FontWeight.w500);
  static const labelSmall  = TextStyle(fontFamily: 'Inter', fontSize: 12, fontWeight: FontWeight.w500);

  // Button
  static const buttonText = TextStyle(fontFamily: 'Inter', fontSize: 16, fontWeight: FontWeight.w600);
}
```

### 3.4 Design Tokens (AppSpacing)

```dart
class AppSpacing {
  static const xs  = 4.0;
  static const sm  = 8.0;
  static const md  = 16.0;
  static const lg  = 24.0;
  static const xl  = 32.0;
  static const xxl = 48.0;

  static const radiusSm = 8.0;
  static const radiusMd = 12.0;
  static const radiusLg = 16.0;

  static const cardElevation = 2.0;
}
```

---

## 4. Batasan Sistem (Hard Constraints)

```
1. ZERO CHAT di Campaign Mode
   → Tidak ada tombol chat, FloatingActionButton chat, link WhatsApp, atau form komentar
   → Jika ada request menambah komunikasi di Campaign Mode, TOLAK

2. File > 100MB wajib URL eksternal
   → Validasi file size SEBELUM upload ke Appwrite Storage
   → Tampilkan warning banner oranye di form upload

3. Rate Card maks 3 paket per kreator
   → Cek count dulu di RateCardController sebelum izinkan tambah
   → Appwrite tidak enforce ini secara native — enforce di aplikasi

4. Dana TRANSACTIONS hanya dari server
   → Flutter client TIDAK PERNAH createDocument ke collection TRANSACTIONS
   → Semua pergerakan dana via Appwrite Function

5. Tidak ada video player built-in
   → Semua URL media dibuka via url_launcher ke browser/app eksternal

6. Tidak ada fitur review/rating MVP
   → Tidak ada bintang, komentar, atau rating kreator di fase MVP

7. Uniqueness submission
   → 1 kreator hanya boleh submit 1x per campaign
   → Enforce di SubmissionController dengan query sebelum createDocument
```

---

## 5. Integrasi Eksternal

### 5.1 Appwrite (Backend Utama)

```
Services yang digunakan:
- Account    → Auth (register, login, logout, session check, email verification)
- Databases  → CRUD collections (campaigns, submissions, messages, dll)
- Storage    → Upload/download foto profil dan aset campaign
- Realtime   → Live updates untuk chat messages
- Functions  → Server-side logic (Midtrans, OpenAI, escrow, dll)

Semua akses via AppwriteService singleton (core/services/appwrite_service.dart)
Import 'package:appwrite/appwrite.dart' HANYA di DataSource layer.
```

### 5.2 Midtrans (Payment Gateway)

```dart
// Alur: Flutter → Appwrite Function → Midtrans API → webhook → Appwrite Function

// Di DataSource (request snap token via Appwrite Function):
Future<String> createPaymentToken({
  required String orderId,
  required double amount,
  required String itemName,
}) async {
  final execution = await AppwriteService.functions.createExecution(
    functionId: 'midtrans-create-fn',
    body: jsonEncode({
      'order_id': orderId,
      'amount': amount,
      'item_name': itemName,
    }),
    method: 'POST',
  );
  final data = jsonDecode(execution.responseBody);
  return data['snap_token'] as String;
}

// Di Controller: buka WebView Midtrans dengan snap_token
// MidtransService.openSnapWebView(snapToken)

PENTING:
- MIDTRANS_CLIENT_KEY boleh ada di Flutter .env (untuk WebView config)
- MIDTRANS_SERVER_KEY TIDAK BOLEH ada di Flutter — hanya di Appwrite Function env vars
```

### 5.3 OpenAI (AI Brief Assistant)

```dart
// Seluruh pemanggilan OpenAI lewat Appwrite Function generate-brief-fn
// Flutter hanya mengirim parameter, tidak pernah menyentuh OpenAI API langsung

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

### 5.4 Google Drive / Dropbox

```dart
// TIDAK ada API integration — hanya simpan URL string
// Validasi URL di Flutter:
bool isValidExternalUrl(String url) {
  if (!url.startsWith('https://')) return false;
  // Opsional: cek domain drive.google.com atau dropbox.com
  return true;
}

// Buka URL di browser eksternal:
await launchUrl(Uri.parse(urlAsetEksternal));
```

### 5.5 TikTok & Instagram (URL Only)

```dart
// TIDAK ada API integration
// Kreator submit URL sebagai string, dibuka via url_launcher
// Validasi di Flutter:
bool isValidSocialMediaUrl(String url) {
  return url.startsWith('https://www.tiktok.com/') ||
         url.startsWith('https://www.instagram.com/');
}

// Buka di browser eksternal:
await launchUrl(Uri.parse(urlBuktiTayang), mode: LaunchMode.externalApplication);
```

### 5.6 Appwrite Realtime (Chat)

```dart
// Aktif HANYA di ChatRoomPage (Rate Card Mode)
// Pattern di ChatController:
@override
void onInit() {
  super.onInit();
  _loadInitialMessages();
  _subscribeToMessages();
}

void _subscribeToMessages() {
  _dataSource.watchMessages(orderId).listen((messages) {
    _messages.value = messages;
  });
}

@override
void onClose() {
  _dataSource.cancelSubscription();
  super.onClose();
}
```

---

## 6. Model Bisnis & Logika Komisi

### 6.1 Campaign Mode (Pay-per-View)

```
UMKM bayar: total_budget + 15% komisi Marketiv

Contoh kalkulasi yang ditampilkan di Step 3 form:
  Budget Kampanye     : Rp 100.000
  Komisi Platform 15%: Rp 15.000
  Total yang dibayar  : Rp 115.000

Pencairan ke kreator (tiered berdasarkan views):
  Dana kreator = (views_aktual / 1000) × harga_per_1000_views
  Sisa budget dikembalikan ke UMKM setelah campaign selesai
```

### 6.2 Rate Card Mode (Fixed Price)

```
UMKM bayar: harga_final + 10% platform fee

Contoh:
  Harga kontrak   : Rp 350.000
  Platform fee 10%: Rp 35.000
  Total dibayar UMKM: Rp 385.000

  Kreator menerima: Rp 350.000 (harga penuh tanpa potongan dari kreator)
  Marketiv: Rp 35.000
```

### 6.3 Target P2MW

```
Target 6 bulan pertama:
- 500 UMKM aktif bertransaksi
- GMV: Rp 75.000.000 / bulan
- Revenue Marketiv: Rp 9.375.000 / bulan (rata-rata take rate 12.5%)
- Retention Rate: 65% di bulan ke-6

Dana P2MW yang diajukan: Rp 11.900.000
BEP: Bulan ke-4 (sekitar 166 UMKM aktif)
```

---

## 7. Pola Controller & State (GetX)

### 7.1 Template Controller

```dart
class FeatureController extends GetxController {
  // Dependencies (via constructor injection dari Binding)
  final GetFeatureUseCase _getFeatureUseCase;
  final CreateFeatureUseCase _createFeatureUseCase;

  FeatureController({
    required GetFeatureUseCase getFeatureUseCase,
    required CreateFeatureUseCase createFeatureUseCase,
  })  : _getFeatureUseCase = getFeatureUseCase,
        _createFeatureUseCase = createFeatureUseCase;

  // State — SELALU private Rx, public getter
  final _items = <FeatureEntity>[].obs;
  final _isLoading = false.obs;
  final _errorMessage = ''.obs;

  List<FeatureEntity> get items => _items;
  bool get isLoading => _isLoading.value;
  String get errorMessage => _errorMessage.value;

  @override
  void onInit() {
    super.onInit();
    loadItems();
  }

  Future<void> loadItems() async {
    _isLoading.value = true;
    _errorMessage.value = '';

    final result = await _getFeatureUseCase(NoParams());
    result.fold(
      (failure) {
        _errorMessage.value = failure.message;
        Get.snackbar('Gagal Memuat', failure.message,
          backgroundColor: AppColors.danger, colorText: Colors.white);
      },
      (data) => _items.value = data,
    );

    _isLoading.value = false;
  }

  @override
  void onClose() {
    // cleanup: cancel subscription, dispose controller
    super.onClose();
  }
}
```

### 7.2 Template UseCase

```dart
// Satu file = satu use case
class GetCampaignsUseCase {
  final CampaignRepository _repository;
  GetCampaignsUseCase(this._repository);

  Future<Either<Failure, List<CampaignEntity>>> call(NoParams params) {
    return _repository.getCampaigns();
  }
}
```

### 7.3 Template Repository

```dart
// domain/repositories/campaign_repository.dart (abstract contract)
abstract class CampaignRepository {
  Future<Either<Failure, List<CampaignEntity>>> getCampaigns();
  Future<Either<Failure, CampaignEntity>> getCampaignById(String id);
  Future<Either<Failure, CampaignEntity>> createCampaign(CreateCampaignParams params);
}

// data/repositories/campaign_repository_impl.dart (implementasi)
class CampaignRepositoryImpl implements CampaignRepository {
  final CampaignRemoteDataSource _dataSource;
  CampaignRepositoryImpl(this._dataSource);

  @override
  Future<Either<Failure, List<CampaignEntity>>> getCampaigns() async {
    try {
      final models = await _dataSource.getCampaigns();
      return Right(models.map((m) => m.toEntity()).toList());
    } on ServerException catch (e) {
      return Left(ServerFailure(message: e.message));
    } on UnauthorizedException catch (e) {
      return Left(AuthFailure(message: e.message));
    } catch (e) {
      return Left(ServerFailure(message: 'Terjadi kesalahan tidak terduga.'));
    }
  }
}
```

### 7.4 Template DataSource (Appwrite)

```dart
// data/datasources/campaign_remote_datasource.dart
abstract class CampaignRemoteDataSource {
  Future<List<CampaignModel>> getCampaigns({String? niche, double? minHarga, double? maxHarga});
  Future<CampaignModel> getCampaignById(String id);
  Future<CampaignModel> createCampaign(CreateCampaignParams params);
}

class CampaignRemoteDataSourceImpl implements CampaignRemoteDataSource {
  final Databases _databases;
  final Functions _functions;

  CampaignRemoteDataSourceImpl({
    required Databases databases,
    required Functions functions,
  })  : _databases = databases,
        _functions = functions;

  @override
  Future<List<CampaignModel>> getCampaigns({
    String? niche,
    double? minHarga,
    double? maxHarga,
  }) async {
    try {
      final queries = [
        Query.equal('status', 'Aktif'),
        Query.orderDesc('\$createdAt'),
        Query.limit(20),
      ];
      if (niche != null) queries.add(Query.equal('niche', niche));
      if (minHarga != null) queries.add(Query.greaterThanEqual('harga_per_1000_views', minHarga));
      if (maxHarga != null) queries.add(Query.lessThanEqual('harga_per_1000_views', maxHarga));

      final result = await _databases.listDocuments(
        databaseId: AppConstants.databaseId,
        collectionId: AppConstants.colCampaigns,
        queries: queries,
      );
      return result.documents.map((d) => CampaignModel.fromDocument(d.data)).toList();
    } on AppwriteException catch (e) {
      throw _mapAppwriteException(e);
    }
  }

  Exception _mapAppwriteException(AppwriteException e) {
    switch (e.code) {
      case 401: return UnauthorizedException('Sesi habis. Silakan login kembali.');
      case 404: return NotFoundException('Data tidak ditemukan.');
      default:  return ServerException('Kesalahan server: ${e.message}');
    }
  }
}
```

---

## 8. Catatan untuk AI Coding Assistant

> **Baca bagian ini sebelum men-generate kode apapun.**

1. **Backend adalah Appwrite, BUKAN Supabase.**
   Jangan pernah generate kode yang mengimport `supabase_flutter`, memanggil `supabase.from()`, atau menggunakan RLS SQL. Semua akses database via `AppwriteService.databases.listDocuments()`, `createDocument()`, `getDocument()`, `updateDocument()`, `deleteDocument()`.

2. **Selalu cek role user** sebelum render widget atau proses logic. Gunakan RBAC dari [FEATURES.md § 1.5](FEATURES.md#15-role-based-access-control-rbac).

3. **Campaign Mode = ZERO CHAT.** Jika ada request menambahkan fitur komunikasi di Campaign Mode, tolak dan acu ke [FEATURES.md § 2](FEATURES.md#2-modul-campaign-mode).

4. **File > 100MB = URL eksternal.** Validasi ukuran file di sisi Flutter sebelum upload ke Appwrite Storage, dan tampilkan pesan error yang jelas.

5. **API Keys sensitif TIDAK di Flutter.** OpenAI key, Midtrans Server Key, dan Appwrite API Key hanya ada di Appwrite Function environment variables. Flutter hanya punya `APPWRITE_ENDPOINT` dan `APPWRITE_PROJECT_ID`.

6. **Rate Card maks 3 paket.** Enforce di RateCardController sebelum izinkan tambah baru, tampilkan snackbar jika sudah penuh.

7. **Escrow adalah inti keamanan.** Jangan pernah langsung mentransfer dana — semua dana transit via collection `TRANSACTIONS` dengan status tracking jelas, dan semua INSERT/UPDATE lewat Appwrite Function.

8. **Binding order wajib:** DataSource → Repository → UseCase → Controller. Jangan pernah melompati urutan ini.

9. **Appwrite hanya di DataSource.** Controller dan UseCase TIDAK PERNAH import `package:appwrite/appwrite.dart`.

10. **Bahasa Indonesia.** Semua teks UI, pesan error, label, snackbar, dan dialog ditulis dalam Bahasa Indonesia sederhana yang mudah dipahami UMKM.

11. **Free tier dulu.** Jangan suggestkan solusi berbayar sebelum batas Free Tier Appwrite Cloud tercapai.

12. **Realtime hanya di Chat.** Appwrite Realtime subscription hanya diaktifkan di `ChatController`. Halaman lain gunakan manual refresh atau pull-to-refresh.

13. **Selalu ada loading & error state.** Setiap halaman yang fetch data wajib punya 3 state: loading (shimmer), error (EmptyStateWidget + tombol retry), dan data tampil.

14. **fromDocument, bukan fromJson.** Model Appwrite di-parse dari `Map<String, dynamic>` hasil `.data` property document. Gunakan `$id` (dengan dollar sign) untuk ID dokumen, dan `$createdAt`/`$updatedAt` untuk timestamp.

15. **Permissions wajib saat createDocument.** Setiap `databases.createDocument()` HARUS menyertakan parameter `permissions` yang sesuai (lihat [DATABASE.md § 10](DATABASE.md#10-permissions-document-level-security)).

16. **Uniqueness submission di-enforce di aplikasi.** Appwrite tidak punya unique constraint antar-field. Lakukan query dulu sebelum createDocument untuk cek duplikasi.

---

*Kembali ke: [README.md](README.md) | [FEATURES.md](FEATURES.md) | [DATABASE.md](DATABASE.md)*

*Dokumen ini dikonversi dari spesifikasi web (Next.js) ke Flutter Mobile App.*
*Referensi: SKPL Marketiv v5.0, Proposal P2MW Marketiv — Universitas Nusa Putra, Sukabumi, 2026.*
