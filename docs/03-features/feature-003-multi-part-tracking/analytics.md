# Multi-Part Tracking — Analytics

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/analytics.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliği için analytics eventlerini, funnel metriklerini, KPI’ları ve privacy kurallarını tanımlar.

Amaç:

- Kullanıcılar project’i partlara bölüyor mu?
- Part oluşturma akışı tamamlanıyor mu?
- Part status/progress aktif kullanılıyor mu?
- Part-counter link kullanılıyor mu?
- Multi-part projeler retention artırıyor mu?
- Broken relationship ve sync hataları var mı?

---

## 2. Privacy Kuralları

Analytics payload içinde bulunmamalıdır:

```text
partName
name
notes
projectName
counterName
patternText
rawUserText
```

Güvenli alanlar:

```text
partType
partStatus
hasProgress
hasLinkedCounter
partCountBucket
progressBucket
syncStatus
errorCode
durationMs
```

---

## 3. Ortak Payload Alanları

| Alan | Tip | Açıklama |
|---|---|---|
| `featureId` | `String` | `feature-003-multi-part-tracking` |
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `partIdHash` | `String?` | Hashlenmiş part ID |
| `partType` | `String?` | Part type |
| `partStatus` | `String?` | Part status |
| `hasProgress` | `bool?` | Progress var mı |
| `hasLinkedCounter` | `bool?` | Counter link var mı |
| `isOffline` | `bool` | Offline mı |
| `syncStatus` | `String?` | Sync durumu |
| `source` | `String?` | Aksiyon kaynağı |
| `result` | `String?` | success/failed/cancelled |
| `errorCode` | `String?` | Hata kodu |
| `durationMs` | `int?` | İşlem süresi |

---

## 4. Event Registry

| Event | Kategori | Öncelik |
|---|---|---|
| `part_section_viewed` | Screen | Must |
| `part_list_viewed` | Screen | Should |
| `part_detail_viewed` | Screen | Must |
| `part_create_started` | Create | Must |
| `part_create_completed` | Create | Must |
| `part_create_failed` | Create | Must |
| `part_create_cancelled` | Create | Should |
| `part_suggested_template_viewed` | Template | Could |
| `part_suggested_template_used` | Template | Should |
| `part_status_changed` | Status | Must |
| `part_completed` | Status | Must |
| `part_reopened` | Status | Should |
| `part_progress_updated` | Progress | Must |
| `part_reordered` | Order | Should |
| `part_edit_started` | Edit | Should |
| `part_edit_saved` | Edit | Must |
| `part_delete_started` | Delete | Should |
| `part_delete_confirmed` | Delete | Must |
| `part_counter_linked` | Relationship | Must |
| `part_counter_unlinked` | Relationship | Should |
| `part_broken_relationship_detected` | Reliability | Must |
| `part_created_offline` | Offline | Must |
| `part_updated_offline` | Offline | Must |
| `part_sync_failed` | Sync | Must |
| `part_conflict_detected` | Sync | Must |
| `part_conflict_resolved` | Sync | Should |

---

## 5. Screen Events

### `part_section_viewed`

Project Detail içinde parts section görüntülenince tetiklenir.

Payload:

- `projectIdHash`
- `partCountBucket`
- `completedPartCountBucket`
- `hasAnyLinkedCounter`
- `hasAnySyncIssue`

### `part_detail_viewed`

Payload:

- `partIdHash`
- `projectIdHash`
- `partType`
- `partStatus`
- `progressBucket`
- `hasLinkedCounter`
- `syncStatus`

---

## 6. Create Events

### `part_create_started`

Payload:

- `source`
- `projectIdHash`
- `existingPartCountBucket`
- `createMode`: `manual`, `suggested_template`, `duplicate`
- `isOffline`

### `part_create_completed`

Payload:

- `partIdHash`
- `projectIdHash`
- `partType`
- `partStatus`
- `hasProgress`
- `hasNotes`
- `createdOffline`
- `durationMs`

### `part_create_failed`

Payload:

- `errorCode`
- `source`
- `createMode`
- `isOffline`

---

## 7. Status ve Progress Events

### `part_status_changed`

Payload:

- `partIdHash`
- `fromStatus`
- `toStatus`
- `source`
- `hasLinkedCounter`

### `part_completed`

Payload:

- `partIdHash`
- `partType`
- `progressBucket`
- `daysSinceCreateBucket`

### `part_progress_updated`

Payload:

- `partIdHash`
- `source`: `manual`, `counter`, `status`
- `progressBucketBefore`
- `progressBucketAfter`

Raw progress yerine bucket kullanılabilir.

---

## 8. Relationship Events

### `part_counter_linked`

Payload:

- `partIdHash`
- `projectIdHash`
- `counterIdHash`
- `partType`
- `source`

Counter name gönderilmez.

### `part_counter_unlinked`

Payload:

- `partIdHash`
- `counterIdHash`
- `source`

---

## 9. Offline ve Sync Events

### `part_created_offline`

Offline part create.

### `part_updated_offline`

Offline part update.

### `part_sync_failed`

Payload:

- `failedPartCount`
- `errorCode`
- `attemptCount`

### `part_conflict_detected`

Payload:

- `conflictType`
- `partIdHash`
- `changedFieldCount`

Payload içinde part name veya notes olmaz.

---

## 10. Funnel Tanımları

### Part Activation Funnel

```text
part_section_viewed
→ part_create_started
→ part_create_completed
→ part_detail_viewed
→ part_status_changed veya part_progress_updated
```

### Suggested Template Funnel

```text
part_suggested_template_viewed
→ part_suggested_template_used
→ part_create_completed
```

### Counter Link Funnel

```text
part_detail_viewed
→ part_counter_linked
→ counter_incremented veya counter_usage_summary
```

---

## 11. KPI’lar

| Metrik | Açıklama |
|---|---|
| Part creation rate | Project kullanıcılarının part oluşturma oranı |
| Average parts per project | Project başına ortalama part sayısı |
| Completed part rate | Completed part oranı |
| Part progress update rate | Progress kullanan kullanıcı oranı |
| Part-counter link rate | Part ile counter bağlama oranı |
| Suggested template usage rate | Önerilen part template kullanımı |
| Multi-part project retention | Part kullanan projelerde geri dönüş oranı |
| Broken relationship rate | Missing counter/part ilişkisi oranı |
| Part sync failure rate | Sync hata oranı |

---

## 12. Bucket Standartları

### Part Count Bucket

```text
0
1
2_3
4_6
7_10
11_20
21_plus
```

### Progress Bucket

```text
0
1_25
26_50
51_75
76_99
100
unknown
```

---

## 13. Codex İçin Beklentiler

- Event isimleri constant olmalı.
- Part name ve notes payload’a eklenmemeli.
- Project/counter ID hashlenmeli.
- Progress raw değer yerine bucket olabilir.
- Analytics failure UI’ı bozmamalı.
- Privacy allowlist test edilmeli.

---

## 14. Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/security.md
```
