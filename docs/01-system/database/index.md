# Appwrite Mapping & Arsitektur Backend

Dokumen ini merinci peta teknis integrasi Flutter dengan BaaS Appwrite yang diterapkan dalam proyek Marketiv.

## Daftar File
- [02-auth.md](02-auth.md)
- [03-storage.md](03-storage.md)
- [04-functions.md](04-functions.md)

## Collections
- [01-users.md](collections/01-users.md)
- [02-campaigns.md](collections/02-campaigns.md)
- [03-submissions.md](collections/03-submissions.md)
- [04-rate_cards.md](collections/04-rate_cards.md)
- [05-rate_card_orders.md](collections/05-rate_card_orders.md)
- [06-transactions.md](collections/06-transactions.md)
- [07-messages.md](collections/07-messages.md)

## Relasi Antar Collection (Konsep Logis)
Appwrite tidak men-support *Foreign Key Constraint*. Relasi bergantung pada logika client dan integritas di Appwrite Function:
- **`users` -> `campaigns`**: 1 to N (`umkm_id`)
- **`users` -> `submissions`**: 1 to N (`kreator_id`)
- **`campaigns` -> `submissions`**: 1 to N (`campaign_id`)
- **`users` -> `rate_cards`**: 1 to Max(3) (`kreator_id`)
- **`users` -> `rate_card_orders`**: Terlibat di UMKM (`umkm_id`) & Kreator (`kreator_id`)
- **`rate_card_orders` -> `messages`**: 1 to N (`order_id`)
- **`users` -> `transactions`**: 1 to N (`user_id`)
