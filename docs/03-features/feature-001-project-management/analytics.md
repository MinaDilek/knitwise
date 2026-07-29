# Proje Yönetimi — Analytics

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/analytics.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliği için analytics stratejisini tanımlar.

Analytics ile amaçlanan şey kullanıcıyı izlemek değil; ürünün gerçekten değer üretip üretmediğini, kullanıcıların nerede takıldığını ve hangi proje yönetimi akışlarının iyileştirilmesi gerektiğini anlamaktır.

Bu doküman aşağıdaki konuları kapsar:

- Ölçülmesi gereken kullanıcı davranışları
- Project Management event isimleri
- Event tetikleme kuralları
- Event payload standartları
- Funnel tanımları
- Retention ve engagement metrikleri
- Premium conversion sinyalleri
- Offline ve sync metrikleri
- Privacy ve PII kuralları
- Codex için analytics implementasyon beklentileri

---

## 2. Analytics Prensipleri

Project Management analytics tasarımı şu prensiplere göre yapılmalıdır:

1. Kullanıcının özel proje içeriği analytics sistemine gönderilmemelidir.
2. Project name, notes, description, pattern text ve image path gibi içerikler event payload içinde yer almamalıdır.
3. Analytics eventleri ürün kararlarını destekleyecek kadar anlamlı olmalıdır.
4. Gereksiz event spam oluşturulmamalıdır.
5. Her event açık bir ürün sorusuna cevap vermelidir.
6. Event isimleri tutarlı ve stabil olmalıdır.
7. Teknik hata eventleri ile kullanıcı davranışı eventleri ayrılmalıdır.
8. Offline durumda oluşan eventler güvenli şekilde kuyruğa alınabilir.
9. Event payloadları küçük, anonim ve PII içermeyen yapıta olmalıdır.
10. Premium yönlendirme eventleri kullanıcıyı baskılamak için değil, dönüşüm noktalarını anlamak için kullanılmalıdır.

---

## 3. Analytics Kapsamı

### 3.1 Ölçülecek Davranışlar

V1 kapsamında ölçülecek ana davranışlar:

- Kullanıcı proje oluşturuyor mu?
- Proje oluşturma akışını tamamlıyor mu?
- Hızlı oluşturma mı detaylı oluşturma mı daha çok kullanılıyor?
- Kullanıcı proje detay ekranına dönüyor mu?
- Kullanıcı projeyi düzenliyor mu?
- Kullanıcı status değiştiriyor mu?
- Kullanıcı projeyi tamamlıyor mu?
- Kullanıcı projeyi arşivliyor mu?
- Kullanıcı projeyi siliyor mu?
- Free kullanıcı limit ekranına takılıyor mu?
- Limit ekranından Premium'a geçiş niyeti oluşuyor mu?
- Offline proje oluşturma kullanılıyor mu?
- Sync hataları kullanıcı deneyimini etkiliyor mu?
- Broken relationship state ne kadar görülüyor?
- Search, filter ve sort gerçekten kullanılıyor mu?

### 3.2 Ölçülmeyecek Davranışlar

Aşağıdaki veriler analytics kapsamında toplanmamalıdır:

- Proje adı
- Proje açıklaması
- Proje notu
- Pattern içeriği
- Görsel dosya yolu
- Lokal dosya path'i
- Remote storage path'i
- Kullanıcının kişisel metin girdileri
- Yarn marka/model notları
- Kullanıcı kimliğini doğrudan belirleyen bilgiler
- Exact location
- Cihazdaki dosya isimleri

---

## 4. Event Naming Standardı

Event isimleri snake_case formatında olmalıdır.

Önerilen format:

```text
project_<object>_<action>
```

Örnekler:

```text
project_create_started
project_create_completed
project_detail_viewed
project_status_changed
project_archived
```

Hata eventleri için format:

```text
project_<object>_<action>_failed
```

Örnekler:

```text
project_create_failed
project_sync_failed
project_media_upload_failed
```

---

## 5. Ortak Event Payload Alanları

Tüm Project Management eventlerinde mümkünse aşağıdaki ortak alanlar kullanılmalıdır.

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `eventName` | `String` | Evet | Event adı |
| `featureId` | `String` | Evet | `feature-001-project-management` |
| `screenName` | `String?` | Hayır | Eventin oluştuğu ekran |
| `projectIdHash` | `String?` | Hayır | Hashlenmiş project ID |
| `projectStatus` | `String?` | Hayır | `active`, `paused`, vb. |
| `userPlan` | `String?` | Hayır | `free`, `premium`, `unknown` |
| `isOffline` | `bool` | Evet | Event oluşurken cihaz offline mıydı |
| `syncStatus` | `String?` | Hayır | `synced`, `pending`, vb. |
| `source` | `String?` | Hayır | Aksiyonun kaynağı |
| `result` | `String?` | Hayır | `success`, `failed`, `cancelled` |
| `errorCode` | `String?` | Hayır | Teknik olmayan hata kodu |
| `durationMs` | `int?` | Hayır | İşlem süresi |
| `timestamp` | `DateTime` | Evet | Event zamanı |

### 5.1 PII Kuralı

`projectIdHash` kullanılacaksa raw `projectId` gönderilmemelidir.

Hashleme:

- Deterministic olabilir.
- Kullanıcılar arası birleştirilebilir olmamalıdır.
- Salt stratejisi teknik dokümanda netleştirilmelidir.

---

## 6. Screen View Eventleri

### 6.1 `project_list_viewed`

Kullanıcı Project List ekranını görüntülediğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `visibleProjectCount` | `int` | O anda listede görünen proje sayısı |
| `activeProjectCount` | `int` | Active + paused proje sayısı |
| `selectedView` | `String` | `default`, `completed`, `archived`, `all` |
| `hasSearchQuery` | `bool` | Arama aktif mi |
| `hasActiveFilters` | `bool` | Filtre var mı |
| `sortType` | `String` | Aktif sıralama |
| `userPlan` | `String` | Plan bilgisi |

#### Tetikleme Kuralı

- Ekran ilk açıldığında tetiklenmelidir.
- Liste her refresh olduğunda tekrar tekrar event atılmamalıdır.
- Aynı session içinde gereksiz tekrarlar engellenmelidir.

---

### 6.2 `project_empty_state_viewed`

Kullanıcının hiç projesi olmadığında empty state görüldüğünde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `source` | `String` | `project_list` |
| `userPlan` | `String` | Plan bilgisi |

#### Ürün Sorusu

Yeni kullanıcılar proje oluşturma noktasına geliyor mu?

---

### 6.3 `project_detail_viewed`

Kullanıcı proje detay ekranını açtığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `projectStatus` | `String` | Proje status değeri |
| `hasPattern` | `bool` | Pattern bağlı mı |
| `hasYarn` | `bool` | Yarn bağlı mı |
| `hasTool` | `bool` | Tool bağlı mı |
| `hasCounter` | `bool` | Counter var mı |
| `hasParts` | `bool` | Part tracking var mı |
| `hasCoverImage` | `bool` | Cover image var mı |
| `progressSource` | `String` | `parts`, `counter`, `manual`, `none` |
| `syncStatus` | `String` | Sync durumu |

#### Yasak Alanlar

- Proje adı gönderilmemelidir.
- Proje açıklaması gönderilmemelidir.
- Not içeriği gönderilmemelidir.

---

## 7. Project Create Eventleri

### 7.1 `project_create_started`

Kullanıcı proje oluşturma akışını başlattığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `source` | `String` | `empty_state`, `project_list_fab`, `onboarding`, `other` |
| `userPlan` | `String` | `free`, `premium`, `unknown` |
| `activeProjectCount` | `int` | Mevcut active + paused sayısı |
| `activeProjectLimit` | `int?` | Free limit, varsa |
| `isOffline` | `bool` | Offline mı |

---

### 7.2 `project_create_completed`

Proje başarıyla oluşturulduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `createMode` | `String` | `quick`, `detailed` |
| `projectStatus` | `String` | Genellikle `active` veya `draft` |
| `hasDescription` | `bool` | Açıklama girildi mi |
| `hasTechnique` | `bool` | Teknik seçildi mi |
| `hasCategory` | `bool` | Kategori seçildi mi |
| `hasPattern` | `bool` | Pattern bağlandı mı |
| `hasYarn` | `bool` | Yarn bağlandı mı |
| `hasTool` | `bool` | Tool bağlandı mı |
| `hasCoverImage` | `bool` | Kapak görseli eklendi mi |
| `createdOffline` | `bool` | Offline oluşturuldu mu |
| `durationMs` | `int?` | Akış süresi |

#### Ürün Soruları

- Kullanıcılar hızlı oluşturmayı mı detaylı oluşturmayı mı tercih ediyor?
- Detaylı formdaki optional alanlar kullanılıyor mu?
- İlk proje oluşturma süresi makul mü?

---

### 7.3 `project_create_cancelled`

Kullanıcı proje oluşturma akışını tamamlamadan çıktığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `step` | `String?` | Kullanıcının çıktığı adım |
| `hadEnteredName` | `bool` | Proje adı girilmiş miydi |
| `hadOptionalFields` | `bool` | Opsiyonel alanlardan biri girilmiş miydi |
| `durationMs` | `int?` | Akışta geçirilen süre |

#### Yasak

Girilen proje adı veya açıklaması gönderilmemelidir.

---

### 7.4 `project_create_failed`

Proje oluşturma başarısız olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `createMode` | `String` | `quick`, `detailed` |
| `errorCode` | `String` | `validation_error`, `local_save_failed`, `entitlement_limit`, `unknown` |
| `isOffline` | `bool` | Offline mı |
| `activeProjectCount` | `int?` | Limit için mevcut sayı |
| `activeProjectLimit` | `int?` | Limit değeri |

---

## 8. Project Edit Eventleri

### 8.1 `project_edit_started`

Kullanıcı proje düzenleme ekranını açtığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `projectStatus` | `String` | Mevcut status |
| `source` | `String` | `project_detail`, `quick_action`, `other` |

---

### 8.2 `project_edit_saved`

Proje düzenleme başarıyla kaydedildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `projectStatus` | `String` | Status |
| `changedFields` | `String[]` | Alan isimleri, içerik değil |
| `changedFieldCount` | `int` | Değişen alan sayısı |
| `isOffline` | `bool` | Offline mı |
| `syncStatus` | `String` | Yeni sync durumu |
| `durationMs` | `int?` | Kaydetme süresi |

#### Örnek `changedFields`

```json
["status", "coverImage", "manualProgress"]
```

Alan değerleri gönderilmemelidir.

---

### 8.3 `project_edit_cancelled`

Kullanıcı değişiklikleri kaydetmeden çıkarsa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `hadUnsavedChanges` | `bool` | Kaydedilmemiş değişiklik var mıydı |
| `changedFieldCount` | `int?` | İçerik olmadan değişiklik sayısı |
| `durationMs` | `int?` | Düzenleme süresi |

---

### 8.4 `project_edit_failed`

Proje düzenleme kaydı başarısız olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `errorCode` | `String` | `validation_error`, `local_save_failed`, `sync_failed`, `unauthorized`, `unknown` |
| `changedFieldCount` | `int?` | İçerik olmadan değişiklik sayısı |
| `isOffline` | `bool` | Offline mı |

---

## 9. Status Lifecycle Eventleri

### 9.1 `project_status_changed`

Project status başarıyla değiştiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `fromStatus` | `String` | Önceki status |
| `toStatus` | `String` | Yeni status |
| `source` | `String` | `status_selector`, `action_menu`, `completion_flow`, `restore_flow` |
| `isOffline` | `bool` | Offline mı |
| `syncStatus` | `String` | Sync durumu |

---

### 9.2 `project_completed`

Proje completed durumuna geçtiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `previousStatus` | `String` | `active` veya `paused` |
| `hadPattern` | `bool` | Pattern var mıydı |
| `hadCounter` | `bool` | Counter var mıydı |
| `hadParts` | `bool` | Parts var mıydı |
| `progressSource` | `String` | Completion öncesi progress kaynağı |
| `daysSinceCreatedBucket` | `String` | Bucket değeri |

#### Bucket Önerileri

```text
same_day
1_3_days
4_7_days
8_30_days
31_90_days
90_plus_days
unknown
```

Tam gün sayısı yerine bucket kullanılmalıdır.

---

### 9.3 `project_reopened`

Completed veya archived proje tekrar çalışma durumuna alındığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `fromStatus` | `String` | `completed` veya `archived` |
| `toStatus` | `String` | `active` veya `paused` |
| `wasLimitChecked` | `bool` | Entitlement kontrol edildi mi |
| `limitResult` | `String?` | `allowed`, `blocked`, `not_applicable` |

---

### 9.4 `project_archived`

Proje arşivlendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `previousStatus` | `String` | Önceki status |
| `source` | `String` | `action_menu`, `limit_reached`, `bulk_action` |
| `activeProjectCountAfter` | `int?` | İşlem sonrası aktif sayı |

---

### 9.5 `project_restored_from_archive`

Archived proje geri alındığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `toStatus` | `String` | Restore edilen status |
| `wasLimitChecked` | `bool` | Limit kontrol edildi mi |
| `limitResult` | `String?` | `allowed`, `blocked` |

---

## 10. Delete ve Recovery Eventleri

### 10.1 `project_delete_started`

Kullanıcı delete aksiyonunu başlattığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `projectStatus` | `String` | Mevcut status |
| `source` | `String` | `action_menu`, `detail_screen` |

---

### 10.2 `project_delete_confirmed`

Kullanıcı delete confirmation onayı verdiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `projectStatus` | `String` | Silme öncesi status |
| `recoveryAvailable` | `bool` | Recovery mümkün mü |
| `recoveryWindowDays` | `int?` | Örn. 30 |

---

### 10.3 `project_delete_cancelled`

Kullanıcı delete dialogundan vazgeçerse tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `selectedAlternative` | `String?` | `archive`, `cancel`, `none` |

---

### 10.4 `project_recovered`

Soft deleted project geri alındığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `restoreStatus` | `String` | Restore sonrası status |
| `daysSinceDeletedBucket` | `String` | Bucket |
| `wasLimitChecked` | `bool` | Limit kontrolü yapıldı mı |

---

## 11. Search, Filter ve Sort Eventleri

### 11.1 `project_search_performed`

Kullanıcı proje araması yaptığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `queryLengthBucket` | `String` | Arama metni uzunluk bucket'ı |
| `resultCount` | `int` | Sonuç sayısı |
| `selectedView` | `String` | Hangi liste görünümünde arama yapıldı |
| `hadResults` | `bool` | Sonuç var mı |

#### Query İçeriği Gönderilmez

Arama metninin kendisi analytics'e gönderilmemelidir.

#### Bucket Önerileri

```text
1_2
3_5
6_10
11_20
20_plus
```

---

### 11.2 `project_filter_applied`

Filtre uygulandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `filterTypes` | `String[]` | `status`, `technique`, `category`, vb. |
| `filterCount` | `int` | Aktif filtre sayısı |
| `resultCount` | `int` | Sonuç sayısı |
| `selectedView` | `String` | Liste görünümü |

Filtre değerleri kullanıcı özel veri içeriyorsa gönderilmemelidir.

---

### 11.3 `project_filters_cleared`

Filtreler temizlendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `clearedFilterCount` | `int` | Temizlenen filtre sayısı |
| `source` | `String` | `clear_all`, `chip_remove`, `empty_state_action` |

---

### 11.4 `project_sort_changed`

Sıralama değiştirildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `fromSortType` | `String?` | Önceki sıralama |
| `toSortType` | `String` | Yeni sıralama |
| `selectedView` | `String` | Hangi listede değiştirildi |

---

## 12. Relationship Eventleri

### 12.1 `project_pattern_linked`

Projeye pattern bağlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `patternSource` | `String` | `patternLibrary`, `customPattern`, `starterPattern`, `importedPattern` |
| `source` | `String` | `create_flow`, `edit_screen`, `detail_empty_state` |

Pattern adı veya pattern içeriği gönderilmemelidir.

---

### 12.2 `project_pattern_unlinked`

Pattern ilişkisi kaldırıldığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `patternSource` | `String?` | Kaynak |
| `source` | `String` | `edit_screen`, `broken_relationship_state`, `replace_flow` |

---

### 12.3 `project_yarn_linked`

Yarn projeye bağlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `create_flow`, `edit_screen`, `materials_section` |
| `usageUnit` | `String?` | `gram`, `meter`, `skein`, vb. |
| `hasUsageAmount` | `bool` | Miktar girildi mi |

Yarn marka, renk adı veya kullanıcı notu gönderilmemelidir.

---

### 12.4 `project_yarn_unlinked`

Yarn ilişkisi kaldırıldığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `edit_screen`, `broken_relationship_state` |

---

### 12.5 `project_tool_linked`

Tool projeye bağlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `toolType` | `String` | `crochetHook`, `knittingNeedle`, vb. |
| `source` | `String` | `create_flow`, `edit_screen`, `tools_section` |
| `isManualEntry` | `bool` | Inventory dışı manuel giriş mi |

---

### 12.6 `project_tool_unlinked`

Tool ilişkisi kaldırıldığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `toolType` | `String?` | Tool tipi |
| `source` | `String` | Kaynak |

---

## 13. Cover Image Eventleri

### 13.1 `project_cover_image_added`

Kapak görseli eklendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `camera`, `photo_library`, `existing_image` |
| `imageSizeBucket` | `String?` | Boyut bucket |
| `uploadStatus` | `String` | `localOnly`, `uploadPending`, `uploaded` |

Dosya adı, path veya görsel içeriği gönderilmemelidir.

---

### 13.2 `project_cover_image_removed`

Kapak görseli kaldırıldığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `edit_screen`, `detail_screen` |

---

### 13.3 `project_media_upload_failed`

Medya upload başarısız olursa tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `mediaType` | `String` | `coverImage`, vb. |
| `errorCode` | `String` | `network_error`, `file_too_large`, `unsupported_type`, `storage_error`, `unknown` |
| `attemptCount` | `int?` | Deneme sayısı |

---

## 14. Counter, Part ve Progress Eventleri

### 14.1 `project_counter_created_from_project`

Project Detail üzerinden counter oluşturulduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `project_detail`, `empty_counter_section` |

---

### 14.2 `project_part_created_from_project`

Project Detail üzerinden part oluşturulduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `source` | `String` | `project_detail`, `empty_parts_section` |

---

### 14.3 `project_progress_source_displayed`

Project progress gösterildiğinde tetiklenebilir.

Bu event dikkatli kullanılmalıdır; çok sık tetiklenirse event spam oluşabilir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `progressSource` | `String` | `parts`, `counter`, `manual`, `none` |
| `progressBucket` | `String?` | 0_25, 26_50, vb. |

#### Progress Bucket Önerileri

```text
0
1_25
26_50
51_75
76_99
100
over_100
unknown
```

Raw progress değeri gönderilmesi zorunlu değildir.

---

## 15. Offline ve Sync Eventleri

### 15.1 `project_created_offline`

Offline durumda proje oluşturulduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `createMode` | `String` | `quick`, `detailed` |
| `syncStatus` | `String` | Genellikle `pending` |

---

### 15.2 `project_updated_offline`

Offline durumda proje düzenlendiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `changedFieldCount` | `int` | Değişen alan sayısı |
| `syncStatus` | `String` | `pending` |

---

### 15.3 `project_sync_started`

Project sync işlemi başladığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `pendingProjectCount` | `int` | Bekleyen project sayısı |
| `pendingOperationCount` | `int` | Bekleyen operation sayısı |
| `trigger` | `String` | `connectivity_restored`, `manual_retry`, `app_start`, `background` |

---

### 15.4 `project_sync_completed`

Project sync işlemi başarıyla tamamlandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `syncedProjectCount` | `int` | Başarıyla sync olan proje sayısı |
| `syncedOperationCount` | `int` | Başarılı operation sayısı |
| `durationMs` | `int?` | Sync süresi |

---

### 15.5 `project_sync_failed`

Project sync başarısız olduğunda tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `failedProjectCount` | `int` | Başarısız project sayısı |
| `failedOperationCount` | `int` | Başarısız operation sayısı |
| `errorCode` | `String` | `network_error`, `auth_error`, `server_error`, `conflict`, `unknown` |
| `attemptCount` | `int?` | Deneme sayısı |

---

### 15.6 `project_sync_conflict_detected`

Conflict tespit edildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `entityType` | `String` | `project`, `projectNote`, `projectMedia`, vb. |
| `conflictType` | `String` | `field_conflict`, `deleted_remote`, `status_conflict`, `media_conflict` |

---

### 15.7 `project_sync_conflict_resolved`

Kullanıcı veya sistem conflict çözdüğünde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String?` | Hashlenmiş project ID |
| `resolutionStrategy` | `String` | `use_local`, `use_remote`, `manual_merge`, `last_write_wins` |
| `durationMs` | `int?` | Conflict çözüm süresi |

---

## 16. Premium ve Limit Eventleri

### 16.1 `project_limit_reached_viewed`

Free kullanıcı limit ekranını gördüğünde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `activeProjectCount` | `int` | Mevcut active + paused sayısı |
| `activeProjectLimit` | `int` | Limit |
| `attemptedAction` | `String` | `create_active`, `restore_active`, `reopen_completed`, `resume_project` |
| `source` | `String` | Ekran veya akış kaynağı |

---

### 16.2 `project_limit_action_selected`

Limit ekranında kullanıcı aksiyon seçtiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `selectedAction` | `String` | `archive_existing`, `complete_existing`, `upgrade`, `cancel` |
| `attemptedAction` | `String` | Hangi işlem engellenmişti |
| `activeProjectCount` | `int` | Aktif proje sayısı |
| `activeProjectLimit` | `int` | Limit |

---

### 16.3 `project_upgrade_cta_clicked`

Project Management bağlamında Premium CTA tıklandığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `source` | `String` | `limit_reached`, `project_list`, `project_detail`, `archive_restore` |
| `activeProjectCount` | `int?` | Aktif proje sayısı |
| `activeProjectLimit` | `int?` | Limit |
| `ctaVariant` | `String?` | A/B test varsa varyant |

---

### 16.4 `project_downgrade_over_limit_viewed`

Downgrade sonrası limit üstü kullanıcı bilgilendirme ekranı gördüğünde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `activeProjectCount` | `int` | Mevcut aktif proje sayısı |
| `activeProjectLimit` | `int` | Free limit |
| `projectsOverLimit` | `int` | Limit üstü proje sayısı |

---

## 17. Broken Relationship Eventleri

### 17.1 `project_broken_relationship_detected`

Proje detayında eksik ilişki tespit edildiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `relationshipType` | `String` | `pattern`, `yarn`, `tool`, `counter`, `part`, `media` |
| `source` | `String` | `project_detail_load`, `sync`, `manual_action` |

---

### 17.2 `project_broken_relationship_removed`

Kullanıcı bozuk ilişkiyi kaldırdığında tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `relationshipType` | `String` | İlişki tipi |
| `source` | `String` | `broken_relationship_state` |

---

### 17.3 `project_broken_relationship_replaced`

Kullanıcı bozuk ilişkiyi yeni ilişkiyle değiştirdiğinde tetiklenir.

#### Payload

| Alan | Tip | Açıklama |
|---|---|---|
| `projectIdHash` | `String` | Hashlenmiş project ID |
| `relationshipType` | `String` | İlişki tipi |
| `source` | `String` | `broken_relationship_state` |

---

## 18. Funnel Tanımları

### 18.1 İlk Proje Oluşturma Funnel'ı

Amaç: Yeni kullanıcının ilk projesini oluşturup oluşturmadığını ölçmek.

```text
project_empty_state_viewed
→ project_create_started
→ project_create_completed
→ project_detail_viewed
```

#### Ana Metrikler

| Metrik | Açıklama |
|---|---|
| Empty state to create start rate | Empty state görenlerin kaçı create başlatıyor |
| Create completion rate | Create başlatanların kaçı tamamlıyor |
| First project success rate | İlk proje oluşturma başarı oranı |
| Time to first project | Kullanıcının ilk projeyi oluşturma süresi |

---

### 18.2 Detaylı Proje Oluşturma Funnel'ı

```text
project_create_started(createMode=detailed)
→ project_pattern_linked / project_yarn_linked / project_tool_linked
→ project_create_completed
```

#### Ürün Soruları

- Detaylı form çok mu uzun?
- Kullanıcılar optional alanları kullanıyor mu?
- Optional alanlar completion oranını düşürüyor mu?

---

### 18.3 Active Project Engagement Funnel'ı

```text
project_list_viewed
→ project_detail_viewed
→ project_counter_created_from_project veya project_edit_saved
→ project_status_changed
```

#### Ürün Soruları

- Kullanıcı projeyi sadece oluşturuyor mu, gerçekten takip ediyor mu?
- Project Detail ekranı başka modüllere geçiş sağlıyor mu?

---

### 18.4 Completion Funnel'ı

```text
project_create_completed
→ project_detail_viewed
→ project_status_changed(toStatus=completed)
→ project_completed
```

#### Ürün Soruları

- Kullanıcılar projelerini tamamlandı olarak işaretliyor mu?
- Tamamlanan projelerde counter veya parts kullanımı daha yüksek mi?

---

### 18.5 Free Limit Conversion Funnel'ı

```text
project_limit_reached_viewed
→ project_limit_action_selected(selectedAction=upgrade)
→ project_upgrade_cta_clicked
→ premium_purchase_started
→ premium_purchase_completed
```

Not:

`premium_purchase_started` ve `premium_purchase_completed` Premium feature analytics içinde tanımlanacaktır.

---

## 19. KPI ve Metrikler

### 19.1 Activation Metrikleri

| Metrik | Tanım |
|---|---|
| First project creation rate | Yeni kullanıcıların ilk proje oluşturma oranı |
| Time to first project | İlk proje oluşturana kadar geçen süre |
| Quick create usage rate | Quick create kullanım oranı |
| Detailed create usage rate | Detailed create kullanım oranı |

### 19.2 Engagement Metrikleri

| Metrik | Tanım |
|---|---|
| Projects per active user | Aktif kullanıcı başına proje sayısı |
| Active projects per user | Kullanıcı başına active + paused proje sayısı |
| Project detail revisit rate | Project detail ekranına tekrar dönme oranı |
| Project edit rate | Projelerin düzenlenme oranı |
| Counter attach rate | Projeye counter eklenme oranı |
| Pattern attach rate | Projeye pattern bağlanma oranı |
| Yarn attach rate | Projeye yarn bağlanma oranı |

### 19.3 Lifecycle Metrikleri

| Metrik | Tanım |
|---|---|
| Pause rate | Active projelerin paused olma oranı |
| Resume rate | Paused projelerin active olma oranı |
| Completion rate | Projelerin completed olma oranı |
| Archive rate | Projelerin archived olma oranı |
| Delete rate | Projelerin soft delete olma oranı |
| Recovery rate | Silinen projelerin geri alınma oranı |

### 19.4 Reliability Metrikleri

| Metrik | Tanım |
|---|---|
| Project create failure rate | Proje oluşturma hata oranı |
| Local save failure rate | Lokal kayıt hata oranı |
| Sync failure rate | Sync hata oranı |
| Conflict rate | Sync conflict oranı |
| Media upload failure rate | Görsel upload hata oranı |
| Broken relationship rate | Eksik ilişki görülme oranı |

### 19.5 Premium Metrikleri

| Metrik | Tanım |
|---|---|
| Limit reached rate | Free kullanıcıların limite ulaşma oranı |
| Upgrade CTA click rate | Limit sonrası upgrade CTA tıklama oranı |
| Archive instead of upgrade rate | Kullanıcının upgrade yerine arşivleme seçme oranı |
| Downgrade over-limit rate | Downgrade sonrası limit üstünde kalan kullanıcı oranı |

---

## 20. Event Trigger Kuralları

### 20.1 Tekrarlı Event Önleme

Aşağıdaki eventler session içinde gereksiz tekrar atılmamalıdır:

- `project_list_viewed`
- `project_empty_state_viewed`
- `project_detail_viewed`
- `project_progress_source_displayed`

Öneri:

- Aynı project detail için kısa süre içinde tekrar view event atılmayabilir.
- Refresh veya state rebuild event üretmemelidir.
- Widget rebuild analytics event tetiklememelidir.

### 20.2 Başarı ve Hata Eventleri

Bir işlem için:

- Başladı event'i
- Tamamlandı event'i
- Hata event'i

ayrı tutulmalıdır.

Örnek:

```text
project_create_started
project_create_completed
project_create_failed
```

### 20.3 Offline Event Kuyruğu

Offline durumda analytics eventleri:

- Lokal kuyruğa alınabilir.
- PII içermemelidir.
- Connectivity geldiğinde gönderilebilir.
- Zaman damgası event oluştuğu ana ait olmalıdır.
- Çok eski eventler policy'e göre düşürülebilir.

---

## 21. Privacy ve PII Kuralları

### 21.1 Kesinlikle Gönderilmeyecek Alanlar

Analytics payload içinde aşağıdaki alanlar bulunmamalıdır:

```text
projectName
name
description
notes
patternText
imagePath
localPath
remotePath
fileName
searchQuery
customYarnName
customToolName
```

### 21.2 Güvenli Alanlar

Aşağıdaki alanlar kullanılabilir:

```text
projectStatus
hasPattern
hasYarn
hasTool
hasCounter
hasParts
hasCoverImage
progressSource
syncStatus
errorCode
durationMs
activeProjectCount
activeProjectLimit
```

### 21.3 Hashlenmiş ID Kullanımı

`projectIdHash` kullanılabilir ancak:

- Raw `projectId` gönderilmemelidir.
- Hash stratejisi platform güvenlik yaklaşımına uygun olmalıdır.
- User tracking için kötüye kullanılmamalıdır.

---

## 22. Analytics Event Registry

Aşağıdaki tablo V1 için önerilen Project Management event listesidir.

| Event | Kategori | Öncelik |
|---|---|---|
| `project_list_viewed` | Screen | Must |
| `project_empty_state_viewed` | Screen | Must |
| `project_detail_viewed` | Screen | Must |
| `project_create_started` | Create | Must |
| `project_create_completed` | Create | Must |
| `project_create_cancelled` | Create | Should |
| `project_create_failed` | Create | Must |
| `project_edit_started` | Edit | Should |
| `project_edit_saved` | Edit | Must |
| `project_edit_cancelled` | Edit | Should |
| `project_edit_failed` | Edit | Must |
| `project_status_changed` | Lifecycle | Must |
| `project_completed` | Lifecycle | Must |
| `project_reopened` | Lifecycle | Should |
| `project_archived` | Lifecycle | Must |
| `project_restored_from_archive` | Lifecycle | Should |
| `project_delete_started` | Delete | Should |
| `project_delete_confirmed` | Delete | Must |
| `project_delete_cancelled` | Delete | Should |
| `project_recovered` | Recovery | Should |
| `project_search_performed` | Discovery | Should |
| `project_filter_applied` | Discovery | Should |
| `project_filters_cleared` | Discovery | Could |
| `project_sort_changed` | Discovery | Could |
| `project_pattern_linked` | Relationship | Must |
| `project_pattern_unlinked` | Relationship | Should |
| `project_yarn_linked` | Relationship | Must |
| `project_yarn_unlinked` | Relationship | Should |
| `project_tool_linked` | Relationship | Should |
| `project_tool_unlinked` | Relationship | Could |
| `project_cover_image_added` | Media | Should |
| `project_cover_image_removed` | Media | Could |
| `project_media_upload_failed` | Reliability | Must |
| `project_counter_created_from_project` | Cross-feature | Must |
| `project_part_created_from_project` | Cross-feature | Should |
| `project_progress_source_displayed` | Progress | Could |
| `project_created_offline` | Offline | Must |
| `project_updated_offline` | Offline | Should |
| `project_sync_started` | Sync | Should |
| `project_sync_completed` | Sync | Should |
| `project_sync_failed` | Sync | Must |
| `project_sync_conflict_detected` | Sync | Must |
| `project_sync_conflict_resolved` | Sync | Should |
| `project_limit_reached_viewed` | Premium | Must |
| `project_limit_action_selected` | Premium | Must |
| `project_upgrade_cta_clicked` | Premium | Must |
| `project_downgrade_over_limit_viewed` | Premium | Should |
| `project_broken_relationship_detected` | Reliability | Must |
| `project_broken_relationship_removed` | Reliability | Should |
| `project_broken_relationship_replaced` | Reliability | Should |

---

## 23. Codex İçin Implementasyon Beklentileri

Codex analytics implementasyonunda şu kurallara uymalıdır:

- Analytics çağrıları doğrudan UI içine dağınık yazılmamalıdır.
- Merkezi bir `AnalyticsService` veya benzeri abstraction kullanılmalıdır.
- Event isimleri sabit constant olarak tanımlanmalıdır.
- Payload keyleri typo riskini azaltmak için constant veya typed model ile yönetilmelidir.
- Event gönderimi iş akışını bloklamamalıdır.
- Analytics hataları kullanıcı deneyimini bozmamalıdır.
- Offline event queue PII içermemelidir.
- Debug build ile production analytics davranışı ayrılmalıdır.
- Eventler unit/integration test ile doğrulanabilir olmalıdır.
- Analytics consent gerekiyorsa event gönderimi consent state'e bağlı olmalıdır.

---

## 24. Test Edilmesi Gereken Analytics Senaryoları

### 24.1 Create Event Testleri

- Quick create başladığında `project_create_started` gider.
- Quick create başarılı olduğunda `project_create_completed` gider.
- Boş ad validasyonunda `project_create_failed` gider.
- Kullanıcı vazgeçerse `project_create_cancelled` gider.
- Offline create için `project_created_offline` gider.

### 24.2 Lifecycle Event Testleri

- Pause / resume işleminde `project_status_changed` gider.
- Complete işleminde `project_completed` gider.
- Archive işleminde `project_archived` gider.
- Restore işleminde `project_restored_from_archive` gider.

### 24.3 Privacy Testleri

Aşağıdaki alanların hiçbir event payload içinde olmadığını test et:

- Project name
- Description
- Notes
- Search query
- Image path
- Pattern text

### 24.4 Premium Event Testleri

- Limit dolduğunda `project_limit_reached_viewed` gider.
- Kullanıcı upgrade seçerse `project_limit_action_selected` ve `project_upgrade_cta_clicked` gider.
- Downgrade over-limit mesajı gösterilirse ilgili event gider.

### 24.5 Sync Event Testleri

- Sync başlar.
- Sync tamamlanır.
- Sync fail olur.
- Conflict tespit edilir.
- Conflict çözülür.

---

## 25. Açık Analytics Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-AN-OD-001 | Analytics aracı ne olacak? | Firebase Analytics veya PostHog değerlendirilebilir | Open |
| PM-AN-OD-002 | `projectIdHash` kullanılacak mı? | Evet, gerekirse | Open |
| PM-AN-OD-003 | Offline analytics queue tutulacak mı? | Evet, PII'siz | Open |
| PM-AN-OD-004 | Consent gerekli mi? | Hukuki değerlendirmeye bağlı | Open |
| PM-AN-OD-005 | Progress displayed event V1'e dahil mi? | Could, event spam riski var | Open |
| PM-AN-OD-006 | A/B test altyapısı olacak mı? | V1'de şart değil | Open |
| PM-AN-OD-007 | Search query length bucket yeterli mi? | Evet, raw query gönderilmemeli | Open |
| PM-AN-OD-008 | Error code standardı nerede tutulacak? | Shared analytics constants | Open |

---

## 26. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-001-project-management/security.md
```

`security.md` içinde Project Management için authorization, ownership, RLS, media privacy, soft delete güvenliği, data retention, PII koruması ve abuse prevention kuralları tanımlanacaktır.
