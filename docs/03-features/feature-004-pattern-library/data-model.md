# Pattern Library — Data Model

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Canonical veri sahipliği
`Pattern` ve `PatternAsset` Feature-004'e aittir. `ProjectPatternLink` **Feature-001'in mevcut entity'si** olarak korunur: aynı ilişkiden yeni `PatternProjectLink` tablosu türetilmez. `SyncStatus` değerleri ortak sözleşmeyle aynıdır: `synced`, `pending`, `failed`, `conflict`.

## 2. `Pattern` alanları
| Alan | Tip | Kural |
|---|---|---|
| `patternId` | UUID | Client-generated, immutable PK |
| `ownerId` | UUID | Auth owner; server-side enforced |
| `title` | string | Trim 1–120, öneri; onay bekler |
| `sourceType` | enum | `pdf`, `image`, `url`, opsiyonel `text` |
| `craftType` | enum/string | `knitting`, `crochet`, `other` (genişletilebilir) |
| `category` | string? | Seçili controlled vocabulary veya custom (karar) |
| `tags` | string[] | Normalize; limits open |
| `notes` | string? | User content; analytics dışı |
| `sourceUrl` | string? | Yalnız `https`, privacy-sensitive |
| `sourceName` | string? | Attribution; telif/sahiplik iddiası değildir |
| `sourceLicense` | string? | İsteğe bağlı kullanıcı beyanı |
| `isFavorite` | bool | Default false |
| `primaryAssetId` | UUID? | Tek asset V1 önerisi; metadata gerektiğinde null |
| `createdAt`, `updatedAt` | datetime UTC | Timestamp |
| `deletedAt` | datetime? | Soft delete |
| `localVersion` | int | Monoton local revision |
| `remoteVersion` | int? | Optimistic concurrency |
| `syncStatus` | enum | Ortak enum |
| `lastSyncedAt` | datetime? | Remote sync |
| `schemaVersion` | int | Migration |

## 3. `PatternAsset` alanları
| Alan | Tip | Kural |
|---|---|---|
| `assetId` | UUID | PK |
| `ownerId`, `patternId` | UUID | Owner ve parent eşleşmeli |
| `assetKind` | enum | `pdf`, `image`, `thumbnail` |
| `mimeType` | string | Gerçek içerikten doğrulanır |
| `byteSize` | bigint | Limit politika kontrollü |
| `sha256` | string? | Duplicate detection/integrity; hassas içerik hash'i telemetry'e gitmez |
| `localUri` | string? | App-private storage; remote DB'ye gönderilmez |
| `remoteObjectKey` | string? | Private owner namespace; istemciye public URL olarak açılmaz |
| `assetStatus` | enum | `staged`, `local_available`, `upload_pending`, `remote_available`, `download_pending`, `offline_unavailable`, `failed` |
| `createdAt`, `updatedAt` | datetime UTC | timestamps |
| `deletedAt` | datetime? | Soft-delete/cleanup |

Remote `PatternAsset` kaydında `localUri` bulunmamalı; cihaz içi data layer'da tutulur. `remoteObjectKey` path kullanıcı girdisinden değil server-side/belirlenmiş güvenli kurallarla üretilir.

## 4. İlişkiler ve indeksler
- `ProjectPatternLink(projectId, patternId, ownerId)` Feature-001'e ait; `(ownerId,projectId,patternId)` aktif bağlantıda unique.
- Project soft delete olduğunda kütüphane kaydı kalır; deep link proje durumunu denetler.
- Index: `(ownerId, deletedAt, updatedAt DESC)`, `(ownerId, isFavorite, deletedAt)`, `(ownerId, sourceType)`, owner-scope searchable metadata. `PatternAsset(ownerId,patternId,deletedAt)`.
- Backend composite relationship: asset owner == pattern owner; link owner == project owner == pattern owner.

## 5. Transaction ve outbox
Import için staged asset ile Pattern metadata'nın tutarlılığı: dosya geçici alanda doğrulanır → atomik kayıt/rename mümkünse yapılır → `SyncOutbox` aynı DB transaction içine yazılır. Filesystem/DB cross-resource işlem atomik değilse recovery journal kullanılır; orphan scan ve retry.

Sync operation `operationId` unique; `entityId`, `ownerId`, `operationType`, `baseRemoteVersion`, `attemptCount`, `status` taşır. Asset upload için operationId idempotency ve remote object key dedup; latest-write-wins sessizce veri ezmemelidir.

## 6. Örnek DTO
```json
{
  "patternId": "6c853fed-3a92-4602-a85e-f58619b6481d",
  "ownerId": "db59e298-5abc-4c0d-a839-9b8f2a488b9b",
  "title": "Yaka motifi",
  "sourceType": "pdf",
  "craftType": "knitting",
  "category": "sweater",
  "tags": ["yaka", "motif"],
  "isFavorite": false,
  "primaryAssetId": "e3019b09-7c72-49bb-9860-2678bf59927b",
  "sourceUrl": null,
  "notes": null,
  "deletedAt": null,
  "localVersion": 1,
  "remoteVersion": null,
  "syncStatus": "pending",
  "schemaVersion": 1
}
```

## 7. Database örneği (teknoloji kararı değil)
Supabase/Postgres seçilirse tablo adları `patterns`, `pattern_assets`; `auth.uid() = owner_id` RLS her tabloda, `WITH CHECK` insert/update için; storage private bucket + owner namespace. Dosyanın content-type doğrulaması backend'de de yapılır. Client tarafı ID'ler secret değil.

## 8. Silme ve migration
`deletedAt` soft delete; asset retention ve permanent delete takvimi merkezi veri saklama politikasıyla kararlaştırılır. Migration sırasında eski localURI veya remoteKey erişilemiyorsa kullanıcı metadata'sı korunur, `assetStatus=failed/offline_unavailable`. Yeni alanlarda backward-compatible defaults.

## 9. Açık kararlar
PL-OD-001..008; ayrıca `PL-DM-001` `ProjectPatternLink` schema eşleştirmesi, `PL-DM-002` asset uzantı/format listesi, `PL-DM-003` saklama süresi. Kod başlamadan teyit edilir.
