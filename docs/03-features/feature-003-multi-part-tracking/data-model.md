# Multi-Part Tracking — Data Model

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/data-model.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliğinin veri modelini tanımlar.

---

## 2. Ana Entity Listesi

| Entity | Amaç |
|---|---|
| `ProjectPart` | Project içindeki parça |
| `PartCounterLink` | Part ile Row Counter ilişkisi |
| `PartStatusHistory` | Status geçmişi, opsiyonel |
| `PartSyncState` | Sync state ayrı tutulmak istenirse |
| `PartConflict` | Conflict bilgisi, opsiyonel |

V1 minimum:

```text
ProjectPart + PartCounterLink
```

---

## 3. ProjectPart Entity

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `partId` | `String` / UUID | Evet | Part benzersiz ID |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `projectId` | `String` | Evet | Bağlı project |
| `name` | `String` | Evet | Part adı |
| `partType` | `PartType` | Evet | Parça tipi |
| `partStatus` | `PartStatus` | Evet | Durum |
| `progressPercent` | `int?` | Hayır | Manuel progress |
| `partOrder` | `int` | Evet | Sıralama |
| `notes` | `String?` | Hayır | Kullanıcı notu |
| `createdAt` | `DateTime` | Evet | Oluşturulma |
| `updatedAt` | `DateTime` | Evet | Güncelleme |
| `completedAt` | `DateTime?` | Hayır | Tamamlanma |
| `deletedAt` | `DateTime?` | Hayır | Soft delete |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |
| `localVersion` | `int` | Evet | Lokal versiyon |
| `remoteVersion` | `int?` | Hayır | Remote versiyon |
| `schemaVersion` | `int` | Evet | Migration |
| `createdOffline` | `bool` | Evet | Offline oluşturuldu mu |
| `lastSyncedAt` | `DateTime?` | Hayır | Son sync |

---

## 4. PartType Enum

```text
body
front
back
sleeve
right_sleeve
left_sleeve
collar
cuff
hem
panel
motif
square
head
arm
leg
ear
tail
accessory
border
assembly
finishing
custom
```

---

## 5. PartStatus Enum

```text
not_started
active
paused
completed
blocked
```

Deleted status değildir.

---

## 6. PartCounterLink Entity

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `linkId` | `String` / UUID | Evet | Link ID |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `projectId` | `String` | Evet | Project |
| `partId` | `String` | Evet | Part |
| `counterId` | `String` | Evet | Row counter |
| `relationshipStatus` | `RelationshipStatus` | Evet | active/missing/removed |
| `createdAt` | `DateTime` | Evet | Oluşturulma |
| `updatedAt` | `DateTime` | Evet | Güncelleme |
| `removedAt` | `DateTime?` | Hayır | Kaldırma zamanı |

---

## 7. RelationshipStatus Enum

```text
active
missing
removed
```

---

## 8. Progress Modeli

Part progress kaynakları:

```text
linked_counter
manual
status_based
none
```

Önerilen öncelik:

```text
linked_counter progress
manual progress
completed status = 100
not_started = 0
none
```

Bu karar product review’da netleştirilmelidir.

---

## 9. Project Progress Etkisi

Project progress calculator partları kullanıyorsa:

```text
projectProgress = average(partProgress values)
```

Opsiyonel ağırlıklı model ileride eklenebilir:

```text
partWeight
```

V1 için `partWeight` zorunlu değildir.

---

## 10. Soft Delete Modeli

Part silindiğinde:

```text
deletedAt = now
```

Normal part query:

```text
ownerId == currentUser.id
projectId == selectedProjectId
deletedAt == null
```

Part permanent delete yalnızca cleanup/account deletion ile yapılmalıdır.

---

## 11. Sync Modeli

Part create/update/delete local-first çalışmalıdır.

Sync status:

```text
synced
pending
failed
conflict
```

Her update localVersion artırmalıdır.

---

## 12. Index Önerileri

| Index | Alanlar | Amaç |
|---|---|---|
| `idx_part_owner_project_deleted_order` | `ownerId`, `projectId`, `deletedAt`, `partOrder` | Project part listesi |
| `idx_part_owner_status` | `ownerId`, `partStatus` | Status filtreleri |
| `idx_part_sync` | `ownerId`, `syncStatus` | Sync query |
| `idx_part_deleted` | `ownerId`, `deletedAt` | Cleanup |
| `idx_part_counter_link` | `ownerId`, `partId`, `counterId` | Link query |

---

## 13. Constraint Gereksinimleri

- `partId` unique.
- `ownerId` null olamaz.
- `projectId` null olamaz.
- `name` trim sonrası boş olamaz.
- `name` maksimum 80 karakter.
- `partType` allowlist.
- `partStatus` allowlist.
- `progressPercent` null veya 0-100.
- `partOrder >= 0`.
- Link için part ve counter aynı owner/project içinde olmalıdır.

---

## 14. Supabase Tablo Önerisi

```text
project_parts
part_counter_links
part_status_history
```

### `project_parts`

```text
part_id uuid primary key
owner_id uuid not null
project_id uuid not null
name text not null
part_type text not null
part_status text not null
progress_percent int null
part_order int not null
notes text null
created_at timestamptz not null
updated_at timestamptz not null
completed_at timestamptz null
deleted_at timestamptz null
remote_version int not null default 1
schema_version int not null default 1
```

### `part_counter_links`

```text
link_id uuid primary key
owner_id uuid not null
project_id uuid not null
part_id uuid not null
counter_id uuid not null
relationship_status text not null
created_at timestamptz not null
updated_at timestamptz not null
removed_at timestamptz null
```

RLS:

```text
owner_id = auth.uid()
```

---

## 15. Repository Modeli

### PartRepository

- `createPart`
- `getPartById`
- `listPartsByProject`
- `updatePart`
- `updatePartStatus`
- `updatePartProgress`
- `reorderParts`
- `softDeletePart`

### PartCounterLinkRepository

- `linkCounter`
- `unlinkCounter`
- `listCountersByPart`
- `listPartsByCounter`
- `markMissingRelationship`

---

## 16. Örnek JSON

```json
{
  "partId": "part_001",
  "ownerId": "user_001",
  "projectId": "project_001",
  "name": "Sağ Kol",
  "partType": "right_sleeve",
  "partStatus": "active",
  "progressPercent": 40,
  "partOrder": 3,
  "notes": "Kol evi için 6 sırada bir artır.",
  "createdAt": "2026-08-01T10:00:00Z",
  "updatedAt": "2026-08-01T12:00:00Z",
  "completedAt": null,
  "deletedAt": null,
  "syncStatus": "pending",
  "localVersion": 2,
  "remoteVersion": null,
  "schemaVersion": 1,
  "createdOffline": false,
  "lastSyncedAt": null
}
```

---

## 17. Codex İçin Beklentiler

- Part query’leri `ownerId` ve `projectId` scope içermeli.
- Part reorder transaction içinde yapılmalı.
- Deleted part normal listede görünmemeli.
- Linked counter eksikse crash olmamalı.
- Part name ve notes analytics/log içine gitmemeli.
- Project progress calculator partları desteklemeli.

---

## 18. Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/analytics.md
```
