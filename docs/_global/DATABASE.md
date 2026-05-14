# DATABASE.md — Skema Database Marketiv (Flutter + GetX)

> Kembali ke: [README.md](README.md) | [FEATURES.md](FEATURES.md) | [TECHNICAL_GUIDELINES.md](TECHNICAL_GUIDELINES.md)

---
  
## Prinsip Utama

> Database **TIDAK BOLEH** menyimpan tipe data file binary. Semua aset besar disimpan sebagai URL string. Database murni relasional (teks/angka/boolean).

> Flutter client **TIDAK PERNAH** membaca database langsung. Semua akses database dilakukan via `DataSource` layer menggunakan `appwrite` Dart SDK, dengan **Document-Level Permissions** aktif di semua collection.

> Appwrite menggunakan **NoSQL Document** model — setiap "tabel" disebut **Collection**, setiap "baris" disebut **Document**. ID dokumen di-generate oleh Appwrite (`ID.unique()`).

---

## Daftar Isi

1. [Setup Appwrite Project](#1-setup-appwrite-project)
2. [Collection: USERS](#2-collection-users)
3. [Collection: CAMPAIGNS](#3-collection-campaigns)
4. [Collection: SUBMISSIONS](#4-collection-submissions)
5. [Collection: RATE_CARDS](#5-collection-rate_cards)
6. [Collection: RATE_CARD_ORDERS](#6-collection-rate_card_orders)
7. [Collection: TRANSACTIONS](#7-collection-transactions)
8. [Collection: MESSAGES](#8-collection-messages)
9. [Relasi Antar Collection](#9-relasi-antar-collection)
10. [Permissions (Document-Level Security)](#10-permissions-document-level-security)
11. [Pola Query di DataSource Flutter](#11-pola-query-di-datasource-flutter)
12. [Storage Buckets](#12-storage-buckets)
13. [Appwrite Functions (Pengganti Edge Functions)](#13-appwrite-functions-pengganti-edge-functions)

---

## 1. Setup Appwrite Project

### 1.1 Inisialisasi SDK di Flutter

```dart
// core/services/appwrite_service.dart
import 'package:appwrite/appwrite.dart';

class AppwriteService {
  static final Client _client = Client();
  static late final Account account;
  static late final Databases databases;
  static late final Storage storage;
  static late final Realtime realtime;
  static late final Functions functions;

  static Future<void> initialize() async {
    _client
      .setEndpoint(AppConstants.appwriteEndpoint) // e.g. https://cloud.appwrite.io/v1
      .setProject(AppConstants.appwriteProjectId);

    account   = Account(_client);
    databases = Databases(_client);
    storage   = Storage(_client);
    realtime  = Realtime(_client);
    functions = Functions(_client);
  }

  static Client get client => _client;
}
```

### 1.2 Konstanta ID

```dart
// core/constants/app_constants.dart
class AppConstants {
  static const appwriteEndpoint  = 'https://cloud.appwrite.io/v1';
  static const appwriteProjectId = 'YOUR_PROJECT_ID';

  // Database
  static const databaseId       = 'marketiv_db';

  // Collection IDs
  static const colUsers         = 'users';
  static const colCampaigns     = 'campaigns';
  static const colSubmissions   = 'submissions';
  static const colRateCards     = 'rate_cards';
  static const colRateCardOrders = 'rate_card_orders';
  static const colTransactions  = 'transactions';
  static const colMessages      = 'messages';

  // Storage Bucket IDs
  static const bucketProfileImage  = 'profile_images';
  static const bucketCampaignAssets = 'campaign_assets';

  // Komisi
  static const campaignFeePercent  = 0.15; // 15%
  static const rateCardFeePercent  = 0.10; // 10%
}
```

---

## 2. Collection: `USERS`

Menyimpan identitas semua aktor sistem (UMKM, Kreator, Admin).

### 2.1 Skema Attributes

| Attribute Key     | Type     | Required | Default | Keterangan |
|-------------------|----------|----------|---------|-----------|
| `user_id`         | String   | ✅       | —       | Sama dengan Appwrite Auth `$id` (link ke account) |
| `role`            | Enum     | ✅       | —       | `UMKM` / `KREATOR` / `ADMIN` |
| `nama_lengkap`    | String   | ✅       | —       | Nama pribadi pemilik akun / staff |
| `nama_usaha`      | String   | ❌       | null    | Nama brand/usaha, wajib jika `role = UMKM` |
| `username_kreator`| String   | ❌       | null    | Handle/username publik, wajib jika `role = KREATOR` |
| `email`           | Email    | ✅       | —       | Duplikat dari Auth untuk kemudahan query |
| `nomor_whatsapp`  | String   | ✅       | —       | |
| `dompet_saldo`    | Float    | ✅       | 0       | Saldo kreator yang bisa di-withdraw |
| `niche`           | String   | ❌       | null    | kuliner, fesyen, edukasi, dll |
| `foto_profil_url` | String   | ❌       | null    | URL dari Appwrite Storage |
| `bio`             | String   | ❌       | null    | |
| `is_verified`     | Boolean  | ✅       | false   | |
| `fcm_token`       | String   | ❌       | null    | Alamat unik perangkat untuk notifikasi (Size 255) |
| `unread_count`    | Integer  | ✅       | 0       | Jumlah notifikasi belum dibaca (Min 0) |

> **Catatan:** `email` kini diduplikasi ke collection `users` untuk mendukung query list/search yang lebih efisien tanpa harus memanggil Account API berulang kali.

> **Skenario 3 (disarankan):** `nama_lengkap` selalu dipakai untuk nama pribadi, `nama_usaha` dipakai khusus untuk UMKM agar nama brand tidak tercampur dengan identitas pemilik, dan `username_kreator` dipakai untuk identitas publik kreator.

> **Aturan tambahan:** `username_kreator` sebaiknya dibuat unik agar tidak bentrok antar kreator.

### 2.2 Index

```
Index: role (key)            → untuk filter berdasarkan role
Index: user_id (unique)      → lookup user by auth ID
Index: username_kreator (unique) → lookup kreator by handle publik
Index: fcm_token_idx (key)   → index attributes: fcm_token
```

### 2.3 Permissions

```
Collection-level: NONE (semua akses lewat document-level)

Per Document:
  CREATE: users  (role: any authenticated)
  READ:   user:{$id}  (hanya pemilik dokumen)
  UPDATE: user:{$id}
  DELETE: NONE (user tidak bisa hapus akun sendiri)

Admin override via Appwrite Functions (server-side dengan API key penuh)
```

### 2.4 Model Flutter

```dart
// data/models/user_model.dart
import 'package:appwrite/models.dart' as appwrite_models;

class UserModel {
  final String id;           // Appwrite document $id
  final String userId;       // = Appwrite Auth $id
  final String role;
  final String namaLengkap;
  final String? namaUsaha;
  final String? usernameKreator;
  final String? nomorWhatsapp;
  final double dompetSaldo;
  final String? niche;
  final String? fotoProfilUrl;
  final String? bio;
  final bool isVerified;
  final String? fcmToken;
  final int unreadCount;
  final DateTime createdAt;

  factory UserModel.fromDocument(Map<String, dynamic> data) => UserModel(
    id: data['\$id'],
    userId: data['user_id'],
    role: data['role'],
    namaLengkap: data['nama_lengkap'],
    namaUsaha: data['nama_usaha'],
    usernameKreator: data['username_kreator'],
    nomorWhatsapp: data['nomor_whatsapp'],
    dompetSaldo: (data['dompet_saldo'] as num).toDouble(),
    niche: data['niche'],
    fotoProfilUrl: data['foto_profil_url'],
    bio: data['bio'],
    isVerified: data['is_verified'] ?? false,
    fcmToken: data['fcm_token'],
    unreadCount: data['unread_count'] ?? 0,
    createdAt: DateTime.parse(data['\$createdAt']),
  );

  Map<String, dynamic> toJson() => {
    'user_id': userId,
    'role': role,
    'nama_lengkap': namaLengkap,
    'nama_usaha': namaUsaha,
    'username_kreator': usernameKreator,
    'nomor_whatsapp': nomorWhatsapp,
    'dompet_saldo': dompetSaldo,
    'niche': niche,
    'foto_profil_url': fotoProfilUrl,
    'bio': bio,
    'is_verified': isVerified,
    'fcm_token': fcmToken,
    'unread_count': unreadCount,
  };

  UserEntity toEntity() => UserEntity(
    id: id,
    userId: userId,
    role: role,
    namaLengkap: namaLengkap,
    namaUsaha: namaUsaha,
    usernameKreator: usernameKreator,
    nomorWhatsapp: nomorWhatsapp,
    dompetSaldo: dompetSaldo,
    niche: niche,
    fotoProfilUrl: fotoProfilUrl,
    bio: bio,
    isVerified: isVerified,
    fcmToken: fcmToken,
    unreadCount: unreadCount,
  );
}
```

---

## 3. Collection: `CAMPAIGNS`

Menyimpan semua proyek pemasaran yang dibuat oleh UMKM.

### 3.1 Skema Attributes

| Attribute Key         | Type    | Required | Default   | Keterangan |
|-----------------------|---------|----------|-----------|-----------|
| `umkm_id`             | String  | ✅       | —         | Appwrite Auth `$id` dari UMKM |
| `judul_campaign`      | String  | ✅       | —         | max 200 char |
| `deskripsi_brief`     | String  | ✅       | —         | Instruksi editing video |
| `url_aset_eksternal`  | String  | ✅       | —         | Link Google Drive/Dropbox |
| `kuota_kreator`       | Integer | ✅       | —         | Jumlah kreator yang bisa klaim |
| `kuota_terpakai`      | Integer | ✅       | 0         | Sudah diklaim berapa |
| `harga_per_1000_views`| Float   | ✅       | —         | Range: 2000 - 10000 |
| `total_budget_escrow` | Float   | ✅       | —         | Dana yang di-lock |
| `niche`               | String  | ❌       | null      | |
| `status`              | Enum    | ✅       | `Draft`   | Lihat logika status di bawah |

**Status Enum Values:** `Draft` / `Aktif` / `Penuh` / `Selesai` / `Dibatalkan`

### 3.2 Logika Status Campaign

| Status       | Kondisi |
|--------------|---------|
| `Draft`      | UMKM sedang isi form, belum bayar |
| `Aktif`      | Dana masuk Escrow, tampil di Job Pool |
| `Penuh`      | `kuota_terpakai >= kuota_kreator` |
| `Selesai`    | Semua submission valid, semua dana dicairkan |
| `Dibatalkan` | Campaign dibatalkan, dana dikembalikan ke UMKM |

### 3.3 Index

```
Index: umkm_id (key)         → ambil campaign milik UMKM tertentu
Index: status (key)          → filter campaign aktif untuk Job Pool
Index: niche (key)           → filter berdasarkan niche
```

### 3.4 Permissions

```
CREATE: user:{umkm_id}    → hanya UMKM pemilik
READ:   any               → semua user terautentikasi bisa lihat campaign Aktif
                            (filter status di query layer)
UPDATE: user:{umkm_id}    → hanya pemilik (lewat Appwrite Function untuk update kuota)
DELETE: NONE
```

### 3.5 Model Flutter

```dart
// data/models/campaign_model.dart
class CampaignModel {
  final String id;
  final String umkmId;
  final String judulCampaign;
  final String deskripsiBrief;
  final String urlAsetEksternal;
  final int kuotaKreator;
  final int kuotaTerpakai;
  final double hargaPer1000Views;
  final double totalBudgetEscrow;
  final String? niche;
  final String status;
  final DateTime createdAt;

  factory CampaignModel.fromDocument(Map<String, dynamic> data) { ... }
  CampaignEntity toEntity() { ... }
}
```

---

## 4. Collection: `SUBMISSIONS`

Menyimpan bukti pekerjaan kreator untuk setiap campaign.

### 4.1 Skema Attributes

| Attribute Key       | Type    | Required | Default   | Keterangan |
|---------------------|---------|----------|-----------|-----------|
| `campaign_id`       | String  | ✅       | —         | Referensi ke collection campaigns |
| `kreator_id`        | String  | ✅       | —         | Appwrite Auth `$id` kreator |
| `url_bukti_tayang`  | String  | ✅       | —         | URL publik TikTok/Instagram Reels |
| `jumlah_views_target` | Integer | ❌     | null      | |
| `jumlah_views_aktual` | Integer | ✅     | 0         | |
| `status_validasi`   | Enum    | ✅       | `Pending` | Pending / Valid / Fraud / Dispute |
| `dana_dicairkan`    | Float   | ✅       | 0         | |

> **Uniqueness constraint** (1 kreator, 1 campaign): Enforce di `SubmissionController` sebelum `createDocument` — query dulu apakah sudah ada submission dengan `campaign_id` + `kreator_id` yang sama.

### 4.2 Index

```
Index: campaign_id (key)
Index: kreator_id (key)
Index: [campaign_id, kreator_id] (key) → cek duplikasi
Index: status_validasi (key)           → filter admin
```

### 4.3 Permissions

```
CREATE: users (any authenticated kreator)
READ:   user:{kreator_id}          → kreator baca submission sendiri
        (UMKM baca lewat Appwrite Function, bukan langsung)
UPDATE: NONE dari client (semua update lewat Appwrite Function)
DELETE: NONE
```

---

## 5. Collection: `RATE_CARDS`

Menyimpan paket harga jasa kreator (maks 3 per kreator).

### 5.1 Skema Attributes

| Attribute Key    | Type    | Required | Default | Keterangan |
|------------------|---------|----------|---------|-----------|
| `kreator_id`     | String  | ✅       | —       | Appwrite Auth `$id` kreator |
| `nama_paket`     | String  | ✅       | —       | max 100 char |
| `deskripsi_paket`| String  | ❌       | null    | |
| `harga_paket`    | Float   | ✅       | —       | |
| `deliverable`    | String  | ❌       | null    | |
| `estimasi_hari`  | Integer | ❌       | null    | |
| `is_active`      | Boolean | ✅       | true    | |

> **Constraint maks 3 paket:** Enforce di `RateCardController` — query count dulu sebelum izinkan tambah baru. Jika sudah 3, tampilkan snackbar: *"Kamu sudah memiliki 3 paket. Hapus salah satu untuk menambah paket baru."*

### 5.2 Index

```
Index: kreator_id (key)
Index: is_active (key)
```

### 5.3 Permissions

```
CREATE: user:{kreator_id}
READ:   any (direktori publik untuk UMKM)
UPDATE: user:{kreator_id}
DELETE: user:{kreator_id}
```

---

## 6. Collection: `RATE_CARD_ORDERS`

Menyimpan transaksi/booking pada Rate Card Mode.

### 6.1 Skema Attributes

| Attribute Key    | Type    | Required | Default       | Keterangan |
|------------------|---------|----------|---------------|-----------|
| `umkm_id`        | String  | ✅       | —             | Appwrite Auth `$id` UMKM |
| `kreator_id`     | String  | ✅       | —             | Appwrite Auth `$id` kreator |
| `ratecard_id`    | String  | ❌       | null          | Referensi rate card yang dipilih |
| `judul_proyek`   | String  | ❌       | null          | |
| `scope_pekerjaan`| String  | ❌       | null          | |
| `harga_final`    | Float   | ✅       | —             | |
| `deadline`       | String  | ❌       | null          | Format: `YYYY-MM-DD` |
| `url_collab_post`| String  | ❌       | null          | URL hasil konten kreator |
| `status`         | Enum    | ✅       | `Negosiasi`   | Lihat status flow di bawah |

**Status Enum Values:** `Negosiasi` / `Menunggu Pembayaran` / `Escrow` / `Revisi` / `Menunggu Verifikasi` / `Selesai` / `Dibatalkan` / `Dispute`

### 6.2 Status Flow

```
Negosiasi → Menunggu Pembayaran → Escrow → [Revisi] → Menunggu Verifikasi → Selesai
                                          ↘ Dibatalkan
                                          ↘ Dispute (→ Admin intervensi)
```

### 6.3 Index

```
Index: umkm_id (key)
Index: kreator_id (key)
Index: status (key)
```

### 6.4 Permissions

```
CREATE: users (any authenticated UMKM)
READ:   user:{umkm_id}, user:{kreator_id}   → kedua pihak bisa baca
UPDATE: user:{umkm_id}, user:{kreator_id}   → kedua pihak (terbatas per field via Function)
DELETE: NONE
```

---

## 7. Collection: `TRANSACTIONS`

Menyimpan semua pergerakan dana di platform.

### 7.1 Skema Attributes

| Attribute Key       | Type    | Required | Default   | Keterangan |
|---------------------|---------|----------|-----------|-----------|
| `user_id`           | String  | ✅       | —         | Appwrite Auth `$id` user |
| `id_referensi`      | String  | ❌       | null      | `$id` campaign atau order |
| `tipe_referensi`    | Enum    | ❌       | null      | `Campaign` / `RateCard` |
| `nominal`           | Float   | ✅       | —         | |
| `tipe`              | Enum    | ✅       | —         | `Deposit` / `Withdrawal` / `Fee` / `Refund` / `Pencairan` |
| `status`            | Enum    | ✅       | `Pending` | `Pending` / `Escrow` / `Success` / `Failed` / `Refunded` |
| `midtrans_order_id` | String  | ❌       | null      | |
| `keterangan`        | String  | ❌       | null      | |

> **PENTING:** Flutter client hanya boleh **READ** collection ini. Semua **CREATE / UPDATE** dilakukan via **Appwrite Function** menggunakan API Key server-side — setelah validasi webhook Midtrans.

### 7.2 Index

```
Index: user_id (key)
Index: status (key)
Index: tipe (key)
Index: $createdAt (key)    → sort riwayat terbaru
```

### 7.3 Permissions

```
CREATE: NONE dari client (hanya Appwrite Function dengan API Key)
READ:   user:{user_id}     → hanya pemilik transaksi
UPDATE: NONE dari client
DELETE: NONE
```

---

## 8. Collection: `MESSAGES`

Digunakan untuk Live Chat di Rate Card Mode saja.

### 8.1 Skema Attributes

| Attribute Key | Type    | Required | Default | Keterangan |
|---------------|---------|----------|---------|-----------|
| `order_id`    | String  | ✅       | —       | Referensi ke `rate_card_orders` |
| `sender_id`   | String  | ✅       | —       | Appwrite Auth `$id` pengirim |
| `tipe_pesan`  | Enum    | ✅       | `Text`  | `Text` / `CustomOffer` / `System` |
| `konten`      | String  | ✅       | —       | Isi pesan |
| `offer_data`  | String  | ❌       | null    | JSON string untuk CustomOffer |
| `is_read`     | Boolean | ✅       | false   | |

> **Catatan:** Appwrite tidak mendukung tipe JSONB native. `offer_data` disimpan sebagai String JSON, di-decode di sisi Flutter saat diperlukan.

### 8.2 Index

```
Index: order_id (key)         → ambil semua pesan per order
Index: [order_id, $createdAt] → sort pesan terbaru
```

### 8.3 Permissions

```
CREATE: user:{umkm_id dari order}, user:{kreator_id dari order}
READ:   user:{umkm_id dari order}, user:{kreator_id dari order}
UPDATE: user:{sender_id}  (untuk update is_read)
DELETE: NONE
```

### 8.4 Realtime Flutter (Appwrite)

```dart
// ChatDataSource — subscribe realtime messages via Appwrite Realtime
class ChatRemoteDataSource {
  final Realtime _realtime = AppwriteService.realtime;
  RealtimeSubscription? _subscription;

  Stream<List<MessageModel>> watchMessages(String orderId) {
    final controller = StreamController<List<MessageModel>>.broadcast();

    _subscription = _realtime.subscribe([
      'databases.${AppConstants.databaseId}'
      '.collections.${AppConstants.colMessages}'
      '.documents',
    ]);

    _subscription!.stream.listen((event) {
      // Filter hanya event untuk orderId ini
      final data = event.payload;
      if (data['order_id'] == orderId) {
        // Trigger reload penuh atau append pesan baru
        _fetchMessages(orderId).then((messages) {
          controller.add(messages);
        });
      }
    });

    return controller.stream;
  }

  // Cleanup di ChatController.onClose()
  void cancelSubscription() {
    _subscription?.close();
  }

  Future<List<MessageModel>> _fetchMessages(String orderId) async {
    final result = await AppwriteService.databases.listDocuments(
      databaseId: AppConstants.databaseId,
      collectionId: AppConstants.colMessages,
      queries: [
        Query.equal('order_id', orderId),
        Query.orderAsc('\$createdAt'),
        Query.limit(100),
      ],
    );
    return result.documents
        .map((d) => MessageModel.fromDocument(d.data))
        .toList();
  }
}
```

```dart
// ChatController — cleanup subscription
@override
void onClose() {
  _dataSource.cancelSubscription();
  super.onClose();
}
```

---

## 9. Relasi Antar Collection

```
USERS ||--o{ CAMPAIGNS          : "Membuat (role=UMKM)"         via umkm_id
USERS ||--o{ SUBMISSIONS        : "Mengerjakan (role=KREATOR)"  via kreator_id
USERS ||--o{ RATE_CARDS         : "Memiliki (maks 3)"           via kreator_id
USERS ||--o{ RATE_CARD_ORDERS   : "Terlibat sebagai UMKM"       via umkm_id
USERS ||--o{ RATE_CARD_ORDERS   : "Terlibat sebagai KREATOR"    via kreator_id
USERS ||--o{ TRANSACTIONS       : "Melakukan"                   via user_id
CAMPAIGNS ||--o{ SUBMISSIONS    : "Menerima banyak bukti"       via campaign_id
RATE_CARD_ORDERS ||--o{ MESSAGES : "Memiliki percakapan"        via order_id
```

> **Appwrite tidak mendukung foreign key constraint** secara native. Referensi integrity di-enforce di level **Controller/UseCase** Flutter dan **Appwrite Function**.

---

## 10. Permissions (Document-Level Security)

Appwrite menggunakan sistem **Permission** berbasis role, tidak ada RLS SQL. Pola yang dipakai:

```dart
// Contoh: membuat Campaign document dengan permission yang benar
Future<CampaignModel> createCampaign(CreateCampaignParams params) async {
  final userId = AppwriteService.account.get(); // current user ID

  final doc = await AppwriteService.databases.createDocument(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colCampaigns,
    documentId: ID.unique(),
    data: params.toJson(),
    permissions: [
      Permission.read(Role.user(currentUserId)),    // UMKM baca miliknya
      Permission.update(Role.user(currentUserId)),  // UMKM update miliknya
      Permission.read(Role.users()),                // Semua user login bisa baca (untuk Job Pool)
    ],
  );

  return CampaignModel.fromDocument(doc.data);
}
```

### Ringkasan Permission per Collection

| Collection        | Read Client         | Write Client            | Write Server (Function) |
|-------------------|---------------------|-------------------------|-------------------------|
| `users`           | Pemilik saja        | Pemilik saja            | ✅ Admin override       |
| `campaigns`       | Semua (authenticated)| Pemilik UMKM            | ✅ Update kuota/status  |
| `submissions`     | Pemilik kreator     | Pemilik kreator (CREATE)| ✅ Update status/validasi|
| `rate_cards`      | Semua (publik)      | Pemilik kreator         | —                       |
| `rate_card_orders`| Kedua pihak         | Kedua pihak (terbatas)  | ✅ Update status        |
| `transactions`    | Pemilik saja        | ❌ TIDAK ADA            | ✅ Semua INSERT/UPDATE  |
| `messages`        | Kedua pihak order   | Kedua pihak order       | ✅ Pesan System         |

---

## 11. Pola Query di DataSource Flutter

### SELECT dengan filter

```dart
// Ambil campaign aktif untuk Job Pool
Future<List<CampaignModel>> getActiveCampaigns({
  String? niche,
  double? minHarga,
  double? maxHarga,
  int limit = 20,
  int offset = 0,
}) async {
  final queries = [
    Query.equal('status', 'Aktif'),
    Query.orderDesc('\$createdAt'),
    Query.limit(limit),
    Query.offset(offset),
  ];

  if (niche != null)    queries.add(Query.equal('niche', niche));
  if (minHarga != null) queries.add(Query.greaterThanEqual('harga_per_1000_views', minHarga));
  if (maxHarga != null) queries.add(Query.lessThanEqual('harga_per_1000_views', maxHarga));

  final result = await AppwriteService.databases.listDocuments(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colCampaigns,
    queries: queries,
  );

  return result.documents
      .map((d) => CampaignModel.fromDocument(d.data))
      .toList();
}
```

### INSERT (Create Document)

```dart
// Kreator klaim campaign → buat submission
Future<SubmissionModel> claimCampaign(String campaignId, String kreatorId) async {
  // 1. Cek sudah klaim sebelumnya
  final existing = await AppwriteService.databases.listDocuments(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colSubmissions,
    queries: [
      Query.equal('campaign_id', campaignId),
      Query.equal('kreator_id', kreatorId),
    ],
  );
  if (existing.total > 0) throw Exception('Kamu sudah mengklaim campaign ini.');

  // 2. Cek kuota via Appwrite Function (atomic, anti-race condition)
  final response = await AppwriteService.functions.createExecution(
    functionId: 'claim-campaign-fn',
    body: jsonEncode({'campaign_id': campaignId, 'kreator_id': kreatorId}),
  );

  final data = jsonDecode(response.responseBody);
  if (data['error'] != null) throw Exception(data['error']);

  return SubmissionModel.fromDocument(data['submission']);
}
```

### UPDATE (Update Document)

```dart
// Submit URL bukti tayang
Future<void> submitBuktiTayang(String submissionId, String url) async {
  await AppwriteService.databases.updateDocument(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colSubmissions,
    documentId: submissionId,
    data: {'url_bukti_tayang': url},
  );
}
```

### GET Single Document

```dart
Future<CampaignModel> getCampaignById(String campaignId) async {
  final doc = await AppwriteService.databases.getDocument(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colCampaigns,
    documentId: campaignId,
  );
  return CampaignModel.fromDocument(doc.data);
}
```

### DELETE Document

```dart
Future<void> deleteRateCard(String rateCardId) async {
  await AppwriteService.databases.deleteDocument(
    databaseId: AppConstants.databaseId,
    collectionId: AppConstants.colRateCards,
    documentId: rateCardId,
  );
}
```

---

## 12. Storage Buckets

| Bucket ID          | Konten                        | Maks Ukuran | Akses      |
|--------------------|-------------------------------|-------------|------------|
| `profile_images`   | Foto profil UMKM & Kreator    | 5 MB        | Public read |
| `campaign_assets`  | Logo / foto produk UMKM       | 100 MB      | Public read |

### Upload File di Flutter

```dart
// Upload foto profil
Future<String> uploadProfileImage(File file, String userId) async {
  final result = await AppwriteService.storage.createFile(
    bucketId: AppConstants.bucketProfileImage,
    fileId: ID.unique(),
    file: InputFile.fromPath(
      path: file.path,
      filename: 'profile_$userId.jpg',
    ),
    permissions: [
      Permission.read(Role.any()),        // Public read
      Permission.update(Role.user(userId)),
      Permission.delete(Role.user(userId)),
    ],
  );

  // Construct public URL
  final url = '${AppConstants.appwriteEndpoint}'
    '/storage/buckets/${AppConstants.bucketProfileImage}'
    '/files/${result.\$id}/view'
    '?project=${AppConstants.appwriteProjectId}';

  return url;
}
```

**Catatan:**
- Video mentah UMKM **TIDAK disimpan** di Appwrite Storage. UMKM wajib gunakan Google Drive/Dropbox dan paste URL-nya.
- Compress gambar di sisi client sebelum upload menggunakan `flutter_image_compress`.

---

## 13. Appwrite Functions (Pengganti Edge Functions)

Semua logika server-side yang sebelumnya ada di Supabase Edge Functions kini dipindahkan ke **Appwrite Functions** (runtime: Node.js atau Dart).

| Function ID             | Trigger       | Deskripsi |
|-------------------------|---------------|-----------|
| `claim-campaign-fn`     | HTTP          | Atomic claim + increment kuota, anti-race condition |
| `midtrans-webhook-fn`   | HTTP (webhook)| Terima notifikasi Midtrans, update TRANSACTIONS |
| `release-escrow-fn`     | HTTP          | Rilis dana escrow ke dompet kreator |
| `refund-escrow-fn`      | HTTP          | Refund escrow ke UMKM |
| `validate-submission-fn`| HTTP          | Admin validasi bukti tayang (tandai Valid/Fraud) |
| `generate-brief-fn`     | HTTP          | Panggil OpenAI API untuk AI Brief Assistant |
| `withdraw-fn`           | HTTP          | Proses withdrawal kreator via Midtrans Iris |
| `admin-stats-fn`        | HTTP          | Hitung statistik dashboard admin |

### Contoh: Memanggil Appwrite Function dari Flutter

```dart
// Panggil function untuk generate brief AI
Future<String> generateBrief({
  required String namaProduk,
  required String niche,
  String? deskripsiSingkat,
}) async {
  final execution = await AppwriteService.functions.createExecution(
    functionId: 'generate-brief-fn',
    body: jsonEncode({
      'nama_produk': namaProduk,
      'niche': niche,
      'deskripsi': deskripsiSingkat,
    }),
    method: 'POST',
  );

  if (execution.responseStatusCode != 200) {
    throw ServerException('Gagal generate brief: ${execution.responseBody}');
  }

  final data = jsonDecode(execution.responseBody);
  return data['brief'] as String;
}
```

> **API Key Appwrite** (yang memiliki akses penuh ke database) **TIDAK PERNAH** ada di Flutter client. Hanya ada di environment variable Appwrite Function di server.

---

*Kembali ke: [README.md](README.md) | [FEATURES.md](FEATURES.md) | [TECHNICAL_GUIDELINES.md](TECHNICAL_GUIDELINES.md)*
