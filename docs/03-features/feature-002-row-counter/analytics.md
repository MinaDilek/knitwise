# Row Counter — Analytics

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/analytics.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliği için analytics stratejisini tanımlar.

Row Counter, uygulamanın günlük kullanım alışkanlığı oluşturma potansiyeli en yüksek özelliklerinden biridir. Kullanıcı proje oluşturduktan sonra uygulamaya tekrar tekrar dönmesini sağlayan ana davranışlardan biri counter kullanımıdır.

Analytics ile amaçlanan:

- Kullanıcılar counter oluşturuyor mu?
- Counter gerçekten kullanılıyor mu?
- Increment/decrement akışı hızlı ve sorunsuz mu?
- Kullanıcı target belirliyor mu?
- Kullanıcı bir project içinde birden fazla counter kullanıyor mu?
- Offline counter kullanımı ne kadar yaygın?
- Sync hataları counter deneyimini etkiliyor mu?
- Undo ihtiyacı ne kadar var?
- Counter, project engagement ve retention üzerinde etkili mi?

Bu dokümanda tanımlanan analytics eventleri privacy-safe olmalıdır. Counter adı, notu, project adı, part adı veya pattern içeriği analytics sistemine gönderilmemelidir.

---

## 2. Analytics Prensipleri

Row Counter analytics tasarımı şu prensiplere göre yapılmalıdır:

1. Her increment için event göndermek event spam yaratabilir.
2. Counter kullanım yoğunluğu mümkünse aggregate veya bucket ile ölçülmelidir.
3. Counter name, notes ve project name analytics payload içinde yer almamalıdır.
4. Sayaç değerinin raw hali her zaman gerekli değildir; bucket tercih edilmelidir.
5. Offline eventler kuyruğa alınabilir ama PII içermemelidir.
6. Sync failure kullanıcı verisini kaybettirmediği sürece analytics hata olarak takip edilmelidir.
7. Kullanıcı davranışı eventleri ve teknik güvenilirlik eventleri ayrılmalıdır.
8. Event isimleri stabil ve snake_case olmalıdır.
9. Analytics call'ları increment UI performansını yavaşlatmamalıdır.
10. Debug analytics ve production analytics davranışı ayrılmalıdır.

---

## 3. Ölçülecek Ana Davranışlar

V1 kapsamında ölçülecek ana davranışlar:

- Counter create başlatıldı mı?
- Counter create tamamlandı mı?
- Counter create nereden başlatıldı?
- Kullanıcı quick create mi detailed create mi kullandı?
- Counter increment/decrement ne kadar kullanılıyor?
- Kullanıcı target value belirliyor mu?
- Counter target'a ulaşıyor mu?
- Kullanıcı undo kullanıyor mu?
- Kullanıcı reset kullanıyor mu?
- Kullanıcı manual value edit yapıyor mu?
- Kullanıcı bir project içinde birden fazla counter kullanıyor mu?
- Counter offline kullanılıyor mu?
- Counter sync başarısız oluyor mu?
- Counter conflict oluşuyor mu?
- Broken project/part relationship görülüyor mu?

---

## 4. Ölçülmeyecek Veriler

Aşağıdaki veriler analytics'e gönderilmemelidir:

```text
counterName
name
notes
projectName
partName
patternText
searchQuery
localPath
remotePath
rawUserText
```

Counter value için de mümkünse raw değer yerine bucket kullanılmalıdır.

---

## 5. Event Naming Standardı

Event isimleri snake_case formatında olmalıdır.

Önerilen format:

```text
counter_<object>_<action>
```

Örnekler:

```text
counter_create_started
counter_incremented
counter_reset_confirmed
counter_sync_failed
```

Aggregate eventler için:

```text
counter_usage_summary
counter_session_summary
```

---

## 6. Ortak Payload Alanları

Tüm Row Counter eventlerinde mümkünse aşağıdaki ortak alanlar kullanılmalıdır.

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `eventName` | `String` | Evet | Event adı |
| `featureId` | `String` | Evet | `feature-002-row-counter` |
| `screenName` | `String?` | Hayır | Eventin oluştuğu ekran |
| `counterIdHash` | `String?` | Hayır | Hashlenmiş counter ID |
| `projectIdHash` | `String?` | Hayır | Hashlenmiş project ID |
| `counterType` | `String?` | Hayır | `row`, `round`, vb. |
| `hasTarget` | `bool?` | Hayır | Target value var mı |
| `hasProject` | `bool?` | Hayır | Project bağlı mı |
| `hasPart` | `bool?` | Hayır | Part bağlı mı |
| `isPrimary` | `bool?` | Hayır | Primary counter mı |
| `isOffline` | `bool` | Evet | Event anında offline mı |
| `syncStatus` | `String?` | Hayır | `synced`, `pending`, vb. |
| `source` | `String?` | Hayır | Aksiyon kaynağı |
| `result` | `String?` | Hayır | `success`, `failed`, `cancelled` |
| `errorCode` | `String?` | Hayır | Hata kodu |
| `durationMs` | `int?` | Hayır | İşlem süresi |
| `timestamp` | `DateTime` | Evet | Event zamanı |

---

## 7. Screen View Eventleri

### 7.1 `counter_section_viewed`

Project Detail içinde counter section görüntülendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `counterCount` | `int` | Project içindeki counter sayısı |
| `hasPrimaryCounter` | `bool` | Primary counter var mı |
| `hasAnyTarget` | `bool` | Target olan counter var mı |
| `hasSyncIssue` | `bool` | Pending/failed/conflict var mı |

#### Ürün Sorusu

Project detail counter kullanımı için yeterince görünür mü?

---

### 7.2 `counter_list_viewed`

Counter listesi görüntülendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String?` | Project context varsa |
| `counterCount` | `int` | Görünen counter sayısı |
| `selectedView` | `String` | `project`, `all`, `independent` |
| `hasDeletedHidden` | `bool?` | Deleted counterlar gizli mi |

---

### 7.3 `counter_detail_viewed`

Counter Detail ekranı açıldığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `counterType` | `String` | Counter tipi |
| `hasTarget` | `bool` | Target var mı |
| `hasPart` | `bool` | Part bağlı mı |
| `isPrimary` | `bool` | Primary counter mı |
| `valueBucket` | `String?` | Raw value yerine bucket |
| `syncStatus` | `String` | Sync durumu |

---

## 8. Counter Create Eventleri

### 8.1 `counter_create_started`

Kullanıcı counter oluşturma akışını başlattığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `source` | `String` | `project_detail`, `counter_list`, `empty_state`, `other` |
| `projectIdHash` | `String?` | Project context varsa |
| `existingCounterCount` | `int?` | Project içindeki mevcut counter sayısı |
| `isOffline` | `bool` | Offline mı |

---

### 8.2 `counter_create_completed`

Counter başarıyla oluşturulduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `createMode` | `String` | `quick`, `detailed` |
| `counterType` | `String` | Counter tipi |
| `hasTarget` | `bool` | Target girildi mi |
| `hasPart` | `bool` | Part bağlandı mı |
| `hasNotes` | `bool` | Notes var mı, içerik değil |
| `stepBucket` | `String` | Step bucket |
| `initialValueBucket` | `String` | Initial value bucket |
| `createdOffline` | `bool` | Offline oluşturuldu mu |
| `durationMs` | `int?` | Create süresi |

---

### 8.3 `counter_create_cancelled`

Kullanıcı create akışından çıkarsa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `source` | `String` | Kaynak |
| `hadEnteredName` | `bool` | İsim girilmiş miydi |
| `hadTarget` | `bool` | Target girilmiş miydi |
| `durationMs` | `int?` | Akış süresi |

Counter name gönderilmemelidir.

---

### 8.4 `counter_create_failed`

Counter oluşturma başarısız olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `errorCode` | `String` | `validation_error`, `local_save_failed`, `unauthorized`, `unknown` |
| `source` | `String` | Kaynak |
| `isOffline` | `bool` | Offline mı |

---

## 9. Counter Value Eventleri

### 9.1 `counter_incremented`

Counter increment işlemi gerçekleştiğinde tetiklenebilir.

#### Önemli Not

Bu event her tap için gönderilirse event spam oluşabilir. V1 için öneri:

```text
counter_incremented event'i debug veya sampled olabilir.
Production'da counter_usage_summary tercih edilmelidir.
```

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `source` | `String` | `counter_detail`, `project_detail`, `quick_action` |
| `stepBucket` | `String` | Step bucket |
| `valueBucketAfter` | `String?` | İşlem sonrası bucket |
| `hasTarget` | `bool` | Target var mı |
| `isOffline` | `bool` | Offline mı |
| `syncStatus` | `String` | Sync durumu |

---

### 9.2 `counter_decremented`

Counter decrement işlemi gerçekleştiğinde tetiklenebilir.

Payload `counter_incremented` ile benzerdir.

Ek alan:

| Alan | Tip | Açıklama |
|---|---|---|
| `wasMinimumBlocked` | `bool` | Minimum nedeniyle engellendi mi |

---

### 9.3 `counter_manual_value_set`

Kullanıcı counter değerini manuel değiştirdiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `valueBucketBefore` | `String?` | Önceki bucket |
| `valueBucketAfter` | `String?` | Sonraki bucket |
| `hasTarget` | `bool` | Target var mı |
| `source` | `String` | `edit_screen`, `conflict_resolution`, `counter_detail` |

---

### 9.4 `counter_reset_started`

Kullanıcı reset aksiyonunu başlattığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `valueBucketBefore` | `String?` | Reset öncesi bucket |
| `source` | `String` | `action_menu`, `counter_detail` |

---

### 9.5 `counter_reset_confirmed`

Kullanıcı reset onayı verdiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `valueBucketBefore` | `String?` | Reset öncesi bucket |
| `valueBucketAfter` | `String?` | Genellikle initial bucket |
| `undoOffered` | `bool` | Undo gösterildi mi |

---

### 9.6 `counter_reset_cancelled`

Kullanıcı reset dialogundan vazgeçerse tetiklenir.

---

### 9.7 `counter_undo_used`

Kullanıcı undo aksiyonunu kullanırsa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `operationTypeUndone` | `String` | `increment`, `decrement`, `reset`, `manual_set` |
| `timeSinceOperationBucket` | `String` | Undo süresi bucket |
| `source` | `String` | `snackbar`, `history`, `other` |

---

## 10. Target ve Progress Eventleri

### 10.1 `counter_target_added`

Kullanıcı target value eklediğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `targetBucket` | `String` | Raw target yerine bucket |
| `source` | `String` | `create_flow`, `edit_screen` |

---

### 10.2 `counter_target_updated`

Target value değiştirildiğinde tetiklenir.

Payload:

- `counterIdHash`
- `counterType`
- `targetBucketBefore`
- `targetBucketAfter`
- `source`

---

### 10.3 `counter_target_removed`

Target kaldırıldığında tetiklenir.

---

### 10.4 `counter_target_reached`

Counter target değerine ulaştığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `targetBucket` | `String` | Target bucket |
| `incrementsSinceCreateBucket` | `String?` | Kaç işlem sonra ulaşıldı |
| `daysSinceCreateBucket` | `String?` | Kaç gün sonra ulaşıldı |

---

### 10.5 `counter_target_exceeded`

Counter target değerini aştığında tetiklenebilir.

Bu event sampled veya summary içinde tutulabilir.

---

## 11. Metadata Eventleri

### 11.1 `counter_edit_started`

Kullanıcı counter edit ekranını açtığında tetiklenir.

### 11.2 `counter_edit_saved`

Counter metadata kaydedildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `changedFields` | `String[]` | Değişen alan adları, değerler değil |
| `changedFieldCount` | `int` | Değişen alan sayısı |
| `hasTarget` | `bool` | Güncel target var mı |
| `hasPart` | `bool` | Güncel part var mı |

Örnek `changedFields`:

```json
["counterType", "stepValue", "targetValue"]
```

---

### 11.3 `counter_edit_failed`

Edit save başarısız olursa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `errorCode` | `String` | `validation_error`, `local_save_failed`, `unauthorized`, `unknown` |
| `changedFieldCount` | `int?` | Değişen alan sayısı |

---

## 12. Delete Eventleri

### 12.1 `counter_delete_started`

Kullanıcı delete aksiyonunu başlattığında tetiklenir.

### 12.2 `counter_delete_confirmed`

Counter soft delete edildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `hadTarget` | `bool` | Target var mıydı |
| `hadPart` | `bool` | Part var mıydı |
| `source` | `String` | `counter_detail`, `counter_list` |

---

### 12.3 `counter_delete_cancelled`

Delete dialog cancel edilirse tetiklenir.

---

## 13. Relationship Eventleri

### 13.1 `counter_linked_to_project`

Counter project'e bağlandığında tetiklenir.

### 13.2 `counter_unlinked_from_project`

Bağımsız counter desteklenirse kullanılabilir.

### 13.3 `counter_linked_to_part`

Counter part'a bağlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Counter |
| `projectIdHash` | `String` | Project |
| `source` | `String` | `create_flow`, `edit_screen` |

Part adı gönderilmemelidir.

### 13.4 `counter_unlinked_from_part`

Counter part ilişkisinden çıkarıldığında tetiklenir.

### 13.5 `counter_broken_relationship_detected`

Project veya part ilişkisi bozuksa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Counter |
| `relationshipType` | `String` | `project`, `part` |
| `source` | `String` | `counter_detail_load`, `sync`, `project_detail` |

---

## 14. Offline ve Sync Eventleri

### 14.1 `counter_updated_offline`

Offline durumda counter update olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String` | Counter |
| `operationType` | `String` | `increment`, `decrement`, vb. |
| `counterType` | `String` | Counter tipi |
| `syncStatus` | `String` | Genellikle `pending` |

---

### 14.2 `counter_sync_started`

Counter sync işlemi başladığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `pendingCounterCount` | `int` | Pending counter sayısı |
| `pendingOperationCount` | `int` | Pending operation sayısı |
| `trigger` | `String` | `connectivity_restored`, `app_start`, `manual_retry`, `background` |

---

### 14.3 `counter_sync_completed`

Sync başarıyla tamamlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `syncedCounterCount` | `int` | Sync olan counter sayısı |
| `syncedOperationCount` | `int` | Sync olan operation sayısı |
| `durationMs` | `int?` | Sync süresi |

---

### 14.4 `counter_sync_failed`

Sync başarısız olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `failedCounterCount` | `int` | Başarısız counter sayısı |
| `failedOperationCount` | `int` | Başarısız operation sayısı |
| `errorCode` | `String` | `network_error`, `auth_error`, `server_error`, `conflict`, `unknown` |
| `attemptCount` | `int?` | Deneme sayısı |

---

### 14.5 `counter_conflict_detected`

Counter conflict tespit edildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String?` | Counter |
| `conflictType` | `String` | `value_conflict`, `metadata_conflict`, vb. |
| `operationCountBucket` | `String?` | İlgili operation sayısı bucket |

---

### 14.6 `counter_conflict_resolved`

Conflict çözüldüğünde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String?` | Counter |
| `resolutionStrategy` | `String` | `use_local`, `use_remote`, `manual_set`, `operation_merge` |
| `durationMs` | `int?` | Çözüm süresi |

---

## 15. Usage Summary Eventleri

### 15.1 `counter_usage_summary`

Counter kullanımını event spam oluşturmadan ölçmek için önerilen ana eventtir.

#### Ne Zaman Gönderilir?

Olası tetikleme noktaları:

- Counter Detail ekranı kapanırken
- App background'a giderken
- Session sonunda
- Belirli aralıklarla
- Günlük aggregate olarak

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `counterIdHash` | `String?` | Hashlenmiş counter ID |
| `counterType` | `String` | Counter tipi |
| `incrementCountBucket` | `String` | Increment sayısı bucket |
| `decrementCountBucket` | `String` | Decrement sayısı bucket |
| `manualSetCountBucket` | `String` | Manual set bucket |
| `undoCountBucket` | `String` | Undo bucket |
| `resetCountBucket` | `String` | Reset bucket |
| `sessionDurationBucket` | `String` | Session süresi bucket |
| `startedValueBucket` | `String?` | Başlangıç bucket |
| `endedValueBucket` | `String?` | Bitiş bucket |
| `hasTarget` | `bool` | Target var mı |
| `targetReachedDuringSession` | `bool` | Session içinde hedefe ulaşıldı mı |
| `wasOfflineAnytime` | `bool` | Session içinde offline oldu mu |

---

## 16. Bucket Standartları

### 16.1 Value Bucket

```text
0
1_10
11_25
26_50
51_100
101_250
251_500
501_plus
unknown
```

### 16.2 Count Bucket

```text
0
1
2_5
6_10
11_25
26_50
51_plus
```

### 16.3 Duration Bucket

```text
0_10_sec
11_30_sec
31_60_sec
1_5_min
5_15_min
15_plus_min
unknown
```

### 16.4 Days Bucket

```text
same_day
1_3_days
4_7_days
8_30_days
31_90_days
90_plus_days
unknown
```

---

## 17. Funnel Tanımları

### 17.1 Counter Activation Funnel

```text
counter_section_viewed
→ counter_create_started
→ counter_create_completed
→ counter_detail_viewed
→ counter_usage_summary(incrementCountBucket != 0)
```

#### Ürün Sorusu

Kullanıcı counter oluşturduktan sonra gerçekten kullanıyor mu?

---

### 17.2 Target Usage Funnel

```text
counter_create_completed
→ counter_target_added
→ counter_usage_summary
→ counter_target_reached
```

#### Ürün Sorusu

Target eklemek completion motivasyonunu artırıyor mu?

---

### 17.3 Offline Reliability Funnel

```text
counter_updated_offline
→ counter_sync_started
→ counter_sync_completed
```

#### Ürün Sorusu

Offline counter güncellemeleri güvenilir şekilde sync oluyor mu?

---

### 17.4 Undo Need Funnel

```text
counter_incremented veya counter_decremented
→ counter_undo_used
```

#### Ürün Sorusu

Yanlış tap veya düzeltme ihtiyacı ne kadar sık?

---

## 18. KPI ve Metrikler

### 18.1 Activation Metrikleri

| Metrik | Tanım |
|---|---|
| Counter creation rate | Project kullanıcılarının counter oluşturma oranı |
| First counter creation rate | Yeni kullanıcıların ilk counter oluşturma oranı |
| Create completion rate | Create başlayanların tamamlaması |
| Quick create usage rate | Quick create kullanım oranı |
| Detailed create usage rate | Detailed create kullanım oranı |

### 18.2 Engagement Metrikleri

| Metrik | Tanım |
|---|---|
| Incrementing active users | Counter increment yapan aktif kullanıcı sayısı |
| Average increments per counter session | Session başına increment |
| Multiple counter usage rate | Project başına 2+ counter kullanım oranı |
| Counter detail revisit rate | Counter detail tekrar ziyaret |
| Counter per project average | Project başına ortalama counter sayısı |

### 18.3 Reliability Metrikleri

| Metrik | Tanım |
|---|---|
| Offline counter update rate | Offline counter update oranı |
| Counter sync failure rate | Counter sync hata oranı |
| Counter conflict rate | Conflict oranı |
| Local save failure rate | Lokal kayıt hata oranı |
| Broken relationship rate | Broken project/part ilişki oranı |

### 18.4 UX Metrikleri

| Metrik | Tanım |
|---|---|
| Undo usage rate | Undo kullanım oranı |
| Reset rate | Reset kullanım oranı |
| Manual set rate | Manuel value düzeltme oranı |
| Decrement after increment rate | Yanlış tap/düzeltme sinyali |
| Target reached rate | Target'a ulaşan counter oranı |

---

## 19. Event Registry

| Event | Kategori | Öncelik |
|---|---|---|
| `counter_section_viewed` | Screen | Must |
| `counter_list_viewed` | Screen | Should |
| `counter_detail_viewed` | Screen | Must |
| `counter_create_started` | Create | Must |
| `counter_create_completed` | Create | Must |
| `counter_create_cancelled` | Create | Should |
| `counter_create_failed` | Create | Must |
| `counter_incremented` | Value | Could / sampled |
| `counter_decremented` | Value | Could / sampled |
| `counter_manual_value_set` | Value | Should |
| `counter_reset_started` | Reset | Should |
| `counter_reset_confirmed` | Reset | Must |
| `counter_reset_cancelled` | Reset | Could |
| `counter_undo_used` | Undo | Must |
| `counter_target_added` | Target | Must |
| `counter_target_updated` | Target | Should |
| `counter_target_removed` | Target | Should |
| `counter_target_reached` | Target | Must |
| `counter_target_exceeded` | Target | Could |
| `counter_edit_started` | Edit | Should |
| `counter_edit_saved` | Edit | Must |
| `counter_edit_failed` | Edit | Must |
| `counter_delete_started` | Delete | Should |
| `counter_delete_confirmed` | Delete | Must |
| `counter_delete_cancelled` | Delete | Could |
| `counter_linked_to_project` | Relationship | Should |
| `counter_linked_to_part` | Relationship | Should |
| `counter_unlinked_from_part` | Relationship | Could |
| `counter_broken_relationship_detected` | Reliability | Must |
| `counter_updated_offline` | Offline | Must |
| `counter_sync_started` | Sync | Should |
| `counter_sync_completed` | Sync | Should |
| `counter_sync_failed` | Sync | Must |
| `counter_conflict_detected` | Sync | Must |
| `counter_conflict_resolved` | Sync | Should |
| `counter_usage_summary` | Usage | Must |

---

## 20. Privacy Kuralları

Analytics payload içinde yasak alanlar:

```text
counterName
name
notes
projectName
partName
patternText
rawSearchQuery
localPath
remotePath
```

Güvenli alanlar:

```text
counterType
hasTarget
hasPart
isPrimary
syncStatus
operationType
errorCode
durationMs
valueBucket
countBucket
```

Raw `currentValue` ve `targetValue` yerine bucket kullanılmalıdır.

---

## 21. Codex İçin Implementasyon Beklentileri

Codex analytics implementasyonunda:

- Increment UI thread'ini analytics çağrısıyla yavaşlatmamalıdır.
- Event isimleri constant olarak tanımlanmalıdır.
- Payload keyleri typed model veya constants ile yönetilmelidir.
- Counter name ve notes payload'a eklenmemelidir.
- `counter_usage_summary` aggregate mantığı desteklenmelidir.
- Offline event queue PII içermemelidir.
- Analytics failure kullanıcı deneyimini bozmamalıdır.
- Debug build içinde daha detaylı event açılabilir ama production privacy korunmalıdır.
- Unit testlerle privacy allowlist doğrulanmalıdır.

---

## 22. Analytics Test Senaryoları

### 22.1 Create Event Testleri

- Counter create başladığında `counter_create_started` gider.
- Counter create başarılıysa `counter_create_completed` gider.
- Counter create validation hatasında `counter_create_failed` gider.
- Counter create cancel edilirse `counter_create_cancelled` gider.

### 22.2 Usage Event Testleri

- Counter increment session sonunda `counter_usage_summary` içinde sayılır.
- Decrement count summary içinde doğru bucket'a girer.
- Undo kullanılırsa `counter_undo_used` gider.
- Reset onaylanırsa `counter_reset_confirmed` gider.

### 22.3 Privacy Testleri

Şu içerikler hiçbir payload içinde bulunmamalıdır:

- Counter name
- Notes
- Project name
- Part name
- Raw path
- Pattern text

### 22.4 Sync Event Testleri

- Offline update için `counter_updated_offline` gider.
- Sync fail olursa `counter_sync_failed` gider.
- Conflict oluşursa `counter_conflict_detected` gider.
- Conflict çözülürse `counter_conflict_resolved` gider.

---

## 23. Açık Analytics Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-AN-OD-001 | Increment event production'da gönderilecek mi? | Summary tercih edilmeli | Open |
| RC-AN-OD-002 | Usage summary ne zaman flush edilecek? | Detail close + app background | Open |
| RC-AN-OD-003 | Raw value yerine bucket zorunlu mu? | Evet | Open |
| RC-AN-OD-004 | Analytics aracı | Firebase veya PostHog | Open |
| RC-AN-OD-005 | Offline analytics queue olacak mı? | Evet, PII'siz | Open |
| RC-AN-OD-006 | Target reached event tek sefer mi? | Counter target per threshold tek sefer | Open |
| RC-AN-OD-007 | Debug mode increment eventleri açık mı? | Evet olabilir | Open |

---

## 24. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/security.md
```

`security.md` içinde Row Counter için ownership, authorization, sync güvenliği, operation idempotency, offline local data security, analytics privacy ve abuse prevention kuralları tanımlanacaktır.
