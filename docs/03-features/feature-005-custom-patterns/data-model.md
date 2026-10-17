# Feature-005 Custom Patterns — Data Model

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/data-model.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## 1. Model sahipliği ve Feature-004 ilişkisi

Feature-004 Pattern Library'nin `Pattern` varlığı **tek kanonik kayıt** olmalıdır. Buradaki tablolar/alanlar öneri niteliğindedir; mevcut Feature-004 modelinde eşdeğeri varsa yeni tablo/alan tekrar yaratılmaz. `sourceType=custom` ile kullanıcı üretimi tarif işaretlenir.

## 2. Pattern üst varlığı (ortak)

| Alan | Tip | Kural |
|---|---|---|
| `patternId` | UUID | PK; local/offline üretilir |
| `ownerId` | UUID | NOT NULL; auth sahibi |
| `sourceType` | enum | `custom` (diğer Feature-004 türleri korunur) |
| `visibility` | enum | V1 `private` |
| `status` | enum | `draft`, `ready`, `archived` |
| `title` | text | ready için 1–120 |
| `summary` | text? | en fazla 2000 |
| `craftType` | enum | `knitting`, `crochet`, `other` |
| `difficulty` | enum | `beginner`, `intermediate`, `advanced`, `unspecified` |
| `materialsNote` | text? | opsiyonel; limit ürün kararı |
| `gaugeNote` | text? | opsiyonel |
| `sizeNote` | text? | opsiyonel |
| `createdAt`, `updatedAt` | timestamp | UTC |
| `deletedAt` | timestamp? | soft delete |
| `localVersion` | int | local monoton artan |
| `remoteVersion` | int? | optimistic concurrency |
| `syncStatus` | enum | shared `synced/pending/failed/conflict` |
| `lastSyncedAt` | timestamp? | son başarılı eşitleme |
| `schemaVersion` | int | migration |

> `visibility=private` ile `status=ready` birbirinden bağımsızdır; hazır bir tarif otomatik paylaşılmaz.

## 3. PatternInstructionBlock

| Alan | Tip | Kural |
|---|---|---|
| `blockId` | UUID | PK |
| `patternId` | UUID | FK kanonik pattern |
| `ownerId` | UUID | owner denetimi / RLS |
| `blockType` | enum | `heading`, `instruction`, `note` |
| `text` | text | max 20000, draft boş olabilir |
| `sortOrder` | int | pattern bazında benzersiz, sıfırdan başlayan |
| `createdAt`, `updatedAt` | timestamp | UTC |
| `deletedAt` | timestamp? | blok silme/recovery |
| `localVersion` | int | revision |
| `remoteVersion` | int? | conflict |

Index: `(ownerId, patternId, deletedAt, sortOrder)`. Yeniden sıralama tüm etkilenen kayıtları tek transaction'da günceller. Yoğun reorder için fractional key opsiyonu ayrıca değerlendirilebilir; V1 sade integer reindex yeterlidir.

## 4. PatternEditOperation (yerel sync queue)

| Alan | Tip | Açıklama |
|---|---|---|
| `operationId` | UUID | Tekil idempotency anahtarı |
| `ownerId` | UUID | kullanıcı |
| `patternId` | UUID | hedef |
| `blockId` | UUID? | blok özel değişiklik |
| `operationType` | enum | `create`, `update_metadata`, `add_block`, `edit_block`, `remove_block`, `reorder_blocks`, `change_status`, `soft_delete`, `duplicate` |
| `baseRemoteVersion` | int? | beklenen uzak revizyon |
| `payloadRef` | text? | ham içerik yerine yerel güvenli payload referansı |
| `attemptCount` | int | retry |
| `syncStatus` | enum | pending/failed/synced/conflict |
| `createdAt`, `lastAttemptAt` | timestamp | zaman |

### Kritik bütünlük kuralı

**Pattern metadata, blok değişikliği ve operation kaydı tek yerel transaction'da tutulur.** Biri başarısızsa tamamı rollback olur. Queue içeriği silinmeden önce remote ack doğrulanır.

## 5. Conflict / recovery kaydı

`PatternConflict`: `conflictId`, `ownerId`, `patternId`, `blockId?`, `localRevisionRef`, `remoteRevisionRef`, `detectedAt`, `resolvedAt?`, `resolution?`. Her iki metnin ham içeriği analytics ya da server loglarına yazılmaz. Çözüme kadar güvenli lokal geçici kopyaları korunur.

## 6. İlişkiler

`Pattern 1—N PatternInstructionBlock`; `Pattern 1—N PatternEditOperation`; `Pattern N—N Project` Feature-004/001 bağlantı tablosu üzerinden. Projeyi silmek pattern'i silmez. Pattern soft delete sonrası linkler veritabanı tutarlılığını korur fakat UI unavailable state gösterir.

## 7. Örnek veri (şematik; gerçek üretimde UUID)

```json
{
  "patternId": "11111111-1111-4111-8111-111111111111",
  "ownerId": "22222222-2222-4222-8222-222222222222",
  "sourceType": "custom", "visibility": "private",
  "status": "draft", "title": "Bere denemesi",
  "craftType": "knitting", "difficulty": "beginner",
  "syncStatus": "pending",
  "blocks": [
    {"blockId": "33333333-3333-4333-8333-333333333333", "blockType": "heading", "sortOrder": 0, "text": "Lastik"},
    {"blockId": "44444444-4444-4444-8444-444444444444", "blockType": "instruction", "sortOrder": 1, "text": "2 düz, 2 ters tekrar et."}
  ]
}
```

## 8. Backend güvenliği ve migration

- `ownerId=auth.uid()` RLS; blok ve operation insert/update sırasında parent pattern de aynı owner olmalı.
- Versiyon güncellemesi compare-and-swap koşulu taşır: `WHERE remote_version = expected_version`.
- Remote işlenen `operationId`'ler tekil kaydedilmeli; tekrar gönderim iki defa uygulanmaz.
- `sourceType` ekleniyorsa mevcut Feature-004 satırları için güvenli backfill tanımlanmalı; rastgele `custom` varsayma.
- Schema migration işleminde içerik, blok sırası ve ilişki kayıtlarının korunduğu test edilmeli.

## 9. Açık tasarım kararları

Kanonik Pattern tablo adı, Feature-004 mevcut fields mapping; blokları yeni tabloda mı yoksa mevcut içeriğin yapısında mı saklayacağı; remote apply operasyonu; medya ilişkisi; conflict history retention. Bunlar kesinleşmeden üretim SQL migration oluşturulmaz.
