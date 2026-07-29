# Proje Yönetimi — Data Model

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/data-model.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliğinin veri modelini tanımlar.

Bu dosya aşağıdaki konuları kapsar:

- Ana project entity yapısı
- Project status enum değerleri
- Project lifecycle alanları
- Pattern, yarn, tool, counter ve part ilişkileri
- Soft delete modeli
- Offline-first local persistence modeli
- Cloud sync için gerekli alanlar
- Premium limit hesaplaması için veri ihtiyaçları
- Index, constraint ve migration notları
- Codex tarafından uygulanabilecek entity ve repository beklentileri

Bu doküman, `requirements.md` ve `flows.md` içindeki davranışların teknik veri karşılığını tanımlar.

---

## 2. Veri Modeli Prensipleri

Project Management veri modeli şu prensiplere göre tasarlanmalıdır:

1. Project entity merkezi ama şişirilmemiş olmalıdır.
2. Pattern, yarn, tool, counter ve part verileri doğrudan project içine gömülmemelidir.
3. Project ilişkileri ayrı relationship tabloları veya koleksiyonları ile yönetilmelidir.
4. Offline-first kullanım desteklenmelidir.
5. Lokal kayıt remote sync işleminden bağımsız olmalıdır.
6. Soft delete `deletedAt` ile temsil edilmelidir.
7. Archived ve deleted kavramları kesinlikle ayrılmalıdır.
8. Tüm user-owned kayıtlar `ownerId` ile scope edilmelidir.
9. Status transition kuralları veri modeli tarafından desteklenmelidir.
10. Migration yapılabilir, genişletilebilir ve test edilebilir yapı kurulmalıdır.

---

## 3. Ana Entity Listesi

V1 Project Management için minimum entity seti:

| Entity | Amaç |
|---|---|
| `Project` | Ana proje kaydı |
| `ProjectPatternLink` | Project ile pattern arasındaki ilişki |
| `ProjectYarnLink` | Project ile yarn arasındaki ilişki |
| `ProjectToolLink` | Project ile hook/needle/tool arasındaki ilişki |
| `ProjectMedia` | Project kapak görseli ve ileride eklenecek medya kayıtları |
| `ProjectSyncState` | Sync durumunu takip eden veri |
| `ProjectStatusHistory` | Status değişiklik geçmişi |
| `ProjectDeletionState` | Soft delete ve recovery bilgileri |
| `ProjectNote` | Notları ayrı entity olarak tutmak istenirse kullanılacak yapı |

V1 için basitleştirilmiş uygulamada `ProjectNote` ve `ProjectDeletionState` alanları doğrudan `Project` içinde tutulabilir. Ancak büyüme potansiyeli nedeniyle entity ayrımı dikkate alınmalıdır.

---

## 4. Project Entity

### 4.1 Amaç

`Project`, kullanıcının örgü veya el işi projesini temsil eden ana entity'dir.

Bir project kaydı:

- Kullanıcıya aittir.
- Yaşam döngüsü status değerine sahiptir.
- Optional ilişkilerle pattern, yarn, tool, counter ve part verilerine bağlanabilir.
- Offline olarak oluşturulabilir.
- Remote sync için takip edilebilir.
- Soft delete ile güvenli şekilde silinebilir.

---

### 4.2 Project Alanları

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `projectId` | `String` / UUID | Evet | Projenin benzersiz kimliği |
| `ownerId` | `String` | Evet | Projenin sahibi kullanıcı |
| `name` | `String` | Evet | Kullanıcı tarafından görülen proje adı |
| `description` | `String?` | Hayır | Proje açıklaması |
| `status` | `ProjectStatus` | Evet | Projenin yaşam döngüsü durumu |
| `technique` | `ProjectTechnique?` | Hayır | Knitting, crochet vb. |
| `category` | `ProjectCategory?` | Hayır | Sweater, scarf, amigurumi vb. |
| `coverMediaId` | `String?` | Hayır | Kapak görseli için medya referansı |
| `startDate` | `DateTime?` | Hayır | Kullanıcının projeye başladığı tarih |
| `targetCompletionDate` | `DateTime?` | Hayır | Hedef bitiş tarihi |
| `completedAt` | `DateTime?` | Hayır | Projenin tamamlandığı tarih |
| `archivedAt` | `DateTime?` | Hayır | Projenin arşivlendiği tarih |
| `deletedAt` | `DateTime?` | Hayır | Soft delete tarihi |
| `manualProgress` | `int?` | Hayır | 0-100 arası manuel ilerleme |
| `notes` | `String?` | Hayır | V1 basit not alanı |
| `createdAt` | `DateTime` | Evet | Oluşturulma tarihi |
| `updatedAt` | `DateTime` | Evet | Son anlamlı güncelleme tarihi |
| `lastOpenedAt` | `DateTime?` | Hayır | Son açılma tarihi, analytics dışı kullanım için |
| `syncStatus` | `SyncStatus` | Evet | Lokal kaydın sync durumu |
| `remoteVersion` | `int?` | Hayır | Remote conflict yönetimi için versiyon |
| `localVersion` | `int` | Evet | Lokal değişiklik versiyonu |
| `schemaVersion` | `int` | Evet | Migration takibi |
| `createdOffline` | `bool` | Evet | Offline oluşturuldu mu |
| `lastSyncedAt` | `DateTime?` | Hayır | Son başarılı sync zamanı |

---

### 4.3 Project Alan Açıklamaları

#### `projectId`

- UUID veya eşdeğer global unique ID olmalıdır.
- Kullanıcı tarafından değiştirilemez.
- Route, relationship ve sync işlemlerinde primary identifier olarak kullanılmalıdır.

#### `ownerId`

- Authentication sisteminden gelen kullanıcı ID'si olmalıdır.
- Offline durumda lokal kullanıcı oturumundan türetilmelidir.
- Query, security policy ve sync işlemlerinde zorunlu filtre olarak kullanılmalıdır.

#### `name`

- Kullanıcıya görünen proje adıdır.
- Trim sonrası boş olamaz.
- Önerilen limit: 120 karakter.
- Aynı kullanıcı aynı isimde birden fazla proje oluşturabilir.

#### `status`

Geçerli değerler:

```text
draft
active
paused
completed
archived
```

`deleted` status olarak kullanılmamalıdır. Silinme `deletedAt` ile takip edilmelidir.

#### `manualProgress`

- Kullanıcı manuel ilerleme girmek isterse kullanılır.
- Değer 0 ile 100 arasında olmalıdır.
- Multi-part veya counter progress varsa UI önceliğine göre geride kalabilir.
- Veri yine saklanmalıdır.

#### `syncStatus`

Local-first mimari için gereklidir.

Geçerli değerler:

```text
synced
pending
failed
conflict
```

---

## 5. Enum Tanımları

### 5.1 ProjectStatus

```text
draft
active
paused
completed
archived
```

| Değer | Açıklama |
|---|---|
| `draft` | Henüz aktif proje haline getirilmemiş kayıt |
| `active` | Kullanıcının üzerinde çalıştığı proje |
| `paused` | Geçici olarak durdurulmuş proje |
| `completed` | Tamamlanmış proje |
| `archived` | Aktif listeden kaldırılmış ama saklanan proje |

---

### 5.2 SyncStatus

```text
synced
pending
failed
conflict
```

| Değer | Açıklama |
|---|---|
| `synced` | Lokal ve remote veri uyumlu |
| `pending` | Lokal değişiklik remote'a gönderilmeyi bekliyor |
| `failed` | Sync denemesi başarısız |
| `conflict` | Lokal ve remote veri çakışması var |

---

### 5.3 ProjectTechnique

V1 önerilen değerler:

```text
knitting
crochet
amigurumi
tunisianCrochet
loomKnitting
other
```

| Değer | Açıklama |
|---|---|
| `knitting` | Şiş örgü |
| `crochet` | Tığ işi |
| `amigurumi` | Amigurumi |
| `tunisianCrochet` | Tunus işi |
| `loomKnitting` | Loom knitting |
| `other` | Diğer |

Custom technique desteği ileride eklenebilir. V1'de `other` + açıklama alanı opsiyoneldir.

---

### 5.4 ProjectCategory

V1 önerilen değerler:

```text
sweater
cardigan
scarf
shawl
hat
sock
blanket
bag
toy
amigurumi
homeDecor
babyItem
accessory
other
```

Bu enum kullanıcıya lokalize edilmiş metinlerle gösterilmelidir.

---

### 5.5 MediaType

```text
coverImage
progressImage
referenceImage
other
```

V1 için minimum zorunlu kullanım:

```text
coverImage
```

Progress image V1 dışında bırakılabilir ancak model genişlemeye uygun olmalıdır.

---

### 5.6 RelationshipStatus

```text
active
removed
missing
```

İlişki kayıtlarının kendisini silmeden durum takip etmek için kullanılabilir.

---

## 6. ProjectPatternLink Entity

### 6.1 Amaç

`ProjectPatternLink`, bir project ile pattern arasındaki ilişkiyi temsil eder.

V1 önerisi:

- Bir project yalnızca bir primary pattern'e sahip olabilir.
- Pattern opsiyoneldir.
- Pattern kaldırıldığında pattern kaydı silinmez.

---

### 6.2 Alanlar

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `projectPatternLinkId` | `String` / UUID | Evet | İlişki kaydı ID'si |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `patternId` | `String` | Evet | Bağlı pattern |
| `patternSource` | `PatternSource` | Evet | Pattern kaynağı |
| `isPrimary` | `bool` | Evet | Primary pattern mi |
| `relationshipStatus` | `RelationshipStatus` | Evet | İlişki durumu |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Güncelleme zamanı |
| `removedAt` | `DateTime?` | Hayır | İlişki kaldırıldıysa tarih |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |

---

### 6.3 PatternSource Enum

```text
patternLibrary
customPattern
starterPattern
importedPattern
```

---

### 6.4 İş Kuralları

- Aynı project için V1'de en fazla bir `isPrimary = true` ilişki olmalıdır.
- Pattern silinirse project silinmemelidir.
- Broken pattern ilişkisinde UI unavailable state göstermelidir.
- Pattern unlink işlemi pattern ana kaydını silmemelidir.

---

## 7. ProjectYarnLink Entity

### 7.1 Amaç

`ProjectYarnLink`, bir project ile yarn inventory kaydı arasındaki ilişkiyi temsil eder.

Bir project birden fazla yarn kaydıyla ilişkili olabilir.

---

### 7.2 Alanlar

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `projectYarnLinkId` | `String` / UUID | Evet | İlişki ID'si |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `yarnId` | `String` | Evet | Yarn inventory kaydı |
| `usageAmount` | `double?` | Hayır | Kullanım miktarı |
| `usageUnit` | `YarnUsageUnit?` | Hayır | Gram, meter, skein vb. |
| `colorRole` | `String?` | Hayır | Ana renk, kontrast renk vb. |
| `relationshipStatus` | `RelationshipStatus` | Evet | Aktif, removed, missing |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Güncelleme zamanı |
| `removedAt` | `DateTime?` | Hayır | İlişki kaldırıldıysa |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |

---

### 7.3 YarnUsageUnit Enum

```text
gram
meter
yard
skein
ball
piece
unknown
```

---

### 7.4 İş Kuralları

- Yarn ilişkisinin kaldırılması yarn inventory kaydını silmez.
- Aynı yarn bir projeye birden fazla rol ile bağlanabilir; ancak V1'de duplicate kontrolü UI tarafında uyarı verebilir.
- Yarn kaydı yoksa project detail ekranı bozulmamalıdır.
- Yarn usage amount negatif olamaz.

---

## 8. ProjectToolLink Entity

### 8.1 Amaç

`ProjectToolLink`, project ile hook, needle veya başka araç arasındaki ilişkiyi temsil eder.

---

### 8.2 Alanlar

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `projectToolLinkId` | `String` / UUID | Evet | İlişki ID'si |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `toolId` | `String?` | Hayır | Inventory içindeki tool ID |
| `manualToolName` | `String?` | Hayır | Inventory olmadan girilen araç adı |
| `toolType` | `ToolType` | Evet | Hook, needle vb. |
| `sizeMm` | `double?` | Hayır | Milimetre ölçüsü |
| `lengthCm` | `double?` | Hayır | Cable veya needle uzunluğu |
| `relationshipStatus` | `RelationshipStatus` | Evet | İlişki durumu |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Güncelleme zamanı |
| `removedAt` | `DateTime?` | Hayır | İlişki kaldırıldıysa |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |

---

### 8.3 ToolType Enum

```text
crochetHook
knittingNeedle
circularNeedle
doublePointedNeedle
tunisianHook
loom
cable
other
```

---

### 8.4 İş Kuralları

- Tool inventory kaydı opsiyoneldir.
- Kullanıcı manual tool girebilir.
- `toolId` boşsa `manualToolName` veya ölçü bilgisi bulunmalıdır.
- Tool ilişkisinin kaldırılması tool inventory kaydını silmez.

---

## 9. ProjectMedia Entity

### 9.1 Amaç

`ProjectMedia`, project'e bağlı medya kayıtlarını temsil eder.

V1'de ana kullanım:

- Kapak görseli

İleride desteklenebilecek kullanımlar:

- Progress photo
- Reference image
- Finished project image

---

### 9.2 Alanlar

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `mediaId` | `String` / UUID | Evet | Medya ID'si |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `mediaType` | `MediaType` | Evet | Cover, progress vb. |
| `localPath` | `String?` | Hayır | Lokal dosya yolu |
| `remotePath` | `String?` | Hayır | Remote storage path |
| `thumbnailLocalPath` | `String?` | Hayır | Lokal thumbnail |
| `thumbnailRemotePath` | `String?` | Hayır | Remote thumbnail |
| `mimeType` | `String?` | Hayır | Image MIME type |
| `fileSizeBytes` | `int?` | Hayır | Dosya boyutu |
| `width` | `int?` | Hayır | Görsel genişliği |
| `height` | `int?` | Hayır | Görsel yüksekliği |
| `isCover` | `bool` | Evet | Kapak görseli mi |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Güncelleme zamanı |
| `deletedAt` | `DateTime?` | Hayır | Medya soft delete |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |
| `uploadStatus` | `UploadStatus` | Evet | Upload durumu |

---

### 9.3 UploadStatus Enum

```text
localOnly
uploadPending
uploading
uploaded
uploadFailed
cleanupPending
```

---

### 9.4 İş Kuralları

- Project silindiğinde medya hemen fiziksel olarak silinmeyebilir.
- Soft deleted project medya cleanup için işaretlenebilir.
- Kapak görseli kaldırılırsa project kaydı silinmez.
- Bir project için V1'de tek aktif cover image olmalıdır.
- Görsel private storage üzerinde tutulmalıdır.

---

## 10. ProjectStatusHistory Entity

### 10.1 Amaç

Project status değişikliklerinin geçmişini tutar.

V1 için zorunlu olmayabilir; ancak audit, analytics ve bug çözümü için önerilir.

---

### 10.2 Alanlar

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `statusHistoryId` | `String` / UUID | Evet | History kaydı ID |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `fromStatus` | `ProjectStatus?` | Hayır | Önceki status |
| `toStatus` | `ProjectStatus` | Evet | Yeni status |
| `reason` | `String?` | Hayır | Sistem veya kullanıcı sebebi |
| `changedAt` | `DateTime` | Evet | Değişiklik zamanı |
| `changedBy` | `String?` | Hayır | Kullanıcı veya sistem |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |

---

## 11. Project Note Modeli

### 11.1 Basit V1 Yaklaşımı

V1'de proje notları doğrudan `Project.notes` alanında tutulabilir.

Avantajları:

- Uygulaması kolaydır.
- Tek not alanı yeterlidir.
- UI daha basittir.

Dezavantajları:

- Çok uzun notlarda project kaydı büyür.
- Not history tutulmaz.
- Conflict resolution zorlaşabilir.

---

### 11.2 Genişletilebilir Yaklaşım

İleride `ProjectNote` ayrı entity yapılabilir.

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `noteId` | `String` / UUID | Evet | Not ID |
| `projectId` | `String` | Evet | Bağlı proje |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `content` | `String` | Evet | Not içeriği |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Güncelleme zamanı |
| `deletedAt` | `DateTime?` | Hayır | Soft delete |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |

V1 önerisi:

```text
Project.notes alanı kullanılabilir.
```

Ancak implementation bu alanı ileride ayrı entity'ye taşımaya uygun yazılmalıdır.

---

## 12. Soft Delete Modeli

### 12.1 Project Soft Delete

Project silindiğinde:

- `deletedAt` doldurulur.
- `status` değiştirilmek zorunda değildir.
- Normal listelerde görünmez.
- Recovery süresi içinde geri alınabilir.
- Remote sync için delete operation kuyruğa alınır.

### 12.2 Soft Delete Alanları

Minimum alanlar:

| Alan | Tip | Açıklama |
|---|---|---|
| `deletedAt` | `DateTime?` | Silinme zamanı |
| `deleteReason` | `String?` | Opsiyonel silme sebebi |
| `deletedBy` | `String?` | Kullanıcı veya sistem |
| `recoverableUntil` | `DateTime?` | Kurtarılabilir son tarih |

V1 için `recoverableUntil` hesaplanabilir alan da olabilir.

---

### 12.3 Recovery Rule

Önerilen recovery süresi:

```text
30 gün
```

Recovery süresi geçmeden:

- Project restore edilebilir.
- `deletedAt` temizlenir.
- İlişkiler tekrar normal akışta kullanılabilir hale gelir.

Recovery süresi geçtikten sonra:

- Project permanent delete için işaretlenebilir.
- Media cleanup başlatılabilir.
- Relationship kayıtları kalıcı silinebilir veya anonymize edilebilir.

---

## 13. Status ve Timestamp Kuralları

| İşlem | `status` | `completedAt` | `archivedAt` | `deletedAt` |
|---|---|---|---|---|
| Draft oluşturma | `draft` | null | null | null |
| Active oluşturma | `active` | null | null | null |
| Pause | `paused` | değişmez | null | null |
| Resume | `active` | değişmez | null | null |
| Complete | `completed` | set | null | null |
| Reopen | `active` veya `paused` | clear veya history | null | null |
| Archive | `archived` | değişmez | set | null |
| Restore Archive | seçilen status | değişmez | clear | null |
| Soft Delete | değişmeyebilir | değişmez | değişmez | set |
| Recover Delete | önceki/uygun status | değişmez | duruma göre | clear |

---

## 14. Relationship Modeli

### 14.1 Genel Kurallar

Tüm relationship kayıtlarında bulunması önerilen ortak alanlar:

- Relationship ID
- `projectId`
- `ownerId`
- Related entity ID
- `relationshipStatus`
- `createdAt`
- `updatedAt`
- `removedAt`
- `syncStatus`

### 14.2 Neden Ayrı Relationship Entity?

Ayrı ilişki modeli şu avantajları sağlar:

- Project kaydı şişmez.
- Offline sync daha kontrollü olur.
- Broken relationship safe state gösterilebilir.
- Birden fazla yarn veya tool ilişkisi desteklenir.
- İleride usage tracking eklenebilir.
- Inventory kayıtları project'ten bağımsız kalır.

---

## 15. Progress Veri Modeli

Project progress doğrudan tek bir alandan hesaplanmamalıdır.

### 15.1 Progress Kaynakları

| Kaynak | Veri Nerede? | Öncelik |
|---|---|---:|
| Multi-part completion | `feature-003` part records | 1 |
| Target-based counter | `feature-002` counter records | 2 |
| Manual progress | `Project.manualProgress` | 3 |
| Yok | null | 4 |

### 15.2 Manual Progress

`Project.manualProgress`:

- Nullable olmalıdır.
- 0-100 aralığında olmalıdır.
- Diğer kaynaklar yoksa gösterilmelidir.

### 15.3 Hesaplanan Progress

Computed alan olarak üretilebilir:

```text
computedProgressPercent
computedProgressSource
computedProgressLabel
```

Bu alanlar database içinde zorunlu saklanmak yerine domain/service katmanında hesaplanabilir.

---

## 16. Premium Limit İçin Veri Modeli

### 16.1 Active Project Count Query

Free plan aktif proje sayımı şu koşullarla yapılmalıdır:

```text
ownerId = currentUserId
deletedAt IS NULL
status IN ('active', 'paused')
```

### 16.2 Limit Dışı Status Değerleri

Şunlar sayılmamalıdır:

```text
draft
completed
archived
```

### 16.3 Downgrade Durumu

Premium downgrade sonrası limit üstü project kayıtları değişmeden kalmalıdır.

Data model:

- Projeleri otomatik status değiştirmemelidir.
- Entitlement service sadece yeni activation/create işlemlerini engellemelidir.
- Existing records preserved kalmalıdır.

---

## 17. Offline ve Sync Modeli

### 17.1 Local-First Yaklaşım

Project create ve update işlemleri önce lokal veritabanına yazılmalıdır.

Remote sync ayrı bir işlem olarak ele alınmalıdır.

### 17.2 Sync Alanları

Minimum sync alanları:

| Alan | Açıklama |
|---|---|
| `syncStatus` | synced, pending, failed, conflict |
| `localVersion` | Lokal değişiklik sayacı |
| `remoteVersion` | Remote versiyon |
| `lastSyncedAt` | Son başarılı sync |
| `createdOffline` | Offline oluşturuldu mu |

### 17.3 Sync Operation Queue

Uygulama içinde ayrı bir sync queue tutulabilir.

Önerilen queue alanları:

| Alan | Tip | Açıklama |
|---|---|---|
| `operationId` | String | Queue operation ID |
| `entityType` | String | Project, ProjectYarnLink vb. |
| `entityId` | String | İlgili kayıt |
| `operationType` | String | create, update, delete, upload |
| `payload` | JSON | Gönderilecek veri |
| `attemptCount` | int | Deneme sayısı |
| `lastAttemptAt` | DateTime? | Son deneme |
| `nextRetryAt` | DateTime? | Sonraki deneme |
| `status` | String | pending, running, failed, done |
| `createdAt` | DateTime | Oluşturma |

---

## 18. Conflict Modeli

### 18.1 Conflict Nedenleri

Conflict oluşabilecek durumlar:

- Aynı project iki cihazda offline düzenlenir.
- Remote project silinmişken lokal edit yapılır.
- Pattern relationship remote kaldırılmışken lokal değiştirilir.
- Notes iki cihazda farklı güncellenir.
- Cover image iki cihazda değiştirilir.

### 18.2 Conflict Resolution V1 Önerisi

| Alan Tipi | Önerilen Strateji |
|---|---|
| Basit metadata | Last write wins |
| Project name | Last write wins veya kullanıcı seçimi |
| Notes | Kullanıcı seçimi / manuel merge |
| Status | Daha güvenli status korunur |
| Relationships | Append-safe / explicit remove wins |
| Media | Son başarılı upload veya kullanıcı seçimi |

### 18.3 Conflict Alanları

Conflict durumunda şu bilgiler tutulabilir:

| Alan | Açıklama |
|---|---|
| `conflictId` | Conflict ID |
| `entityType` | Hangi entity |
| `entityId` | Hangi kayıt |
| `localPayload` | Lokal versiyon |
| `remotePayload` | Remote versiyon |
| `detectedAt` | Tespit zamanı |
| `resolvedAt` | Çözüm zamanı |
| `resolutionStrategy` | Kullanılan strateji |

V1'de ayrı conflict table zorunlu değildir, ancak sync architecture buna uygun tasarlanmalıdır.

---

## 19. Index ve Query Gereksinimleri

### 19.1 Project Indexleri

Önerilen indexler:

| Index | Alanlar | Amaç |
|---|---|---|
| `idx_project_owner_status_deleted` | `ownerId`, `status`, `deletedAt` | Listeleme ve aktif proje sayımı |
| `idx_project_owner_updated` | `ownerId`, `updatedAt` | Varsayılan sıralama |
| `idx_project_owner_created` | `ownerId`, `createdAt` | Oluşturulma sıralaması |
| `idx_project_sync_status` | `syncStatus` | Sync queue taraması |
| `idx_project_deleted` | `deletedAt` | Recovery / cleanup |
| `idx_project_archived` | `archivedAt` | Archived view |
| `idx_project_completed` | `completedAt` | Completed view |

### 19.2 Relationship Indexleri

| Entity | Index | Amaç |
|---|---|---|
| `ProjectPatternLink` | `projectId`, `relationshipStatus` | Project detail pattern load |
| `ProjectYarnLink` | `projectId`, `relationshipStatus` | Project materials load |
| `ProjectToolLink` | `projectId`, `relationshipStatus` | Project tools load |
| `ProjectMedia` | `projectId`, `mediaType`, `deletedAt` | Cover image load |
| `ProjectStatusHistory` | `projectId`, `changedAt` | History display |

---

## 20. Constraint Gereksinimleri

### 20.1 Project Constraints

- `projectId` unique olmalıdır.
- `ownerId` null olamaz.
- `name` non-draft projelerde trim sonrası boş olamaz.
- `status` geçerli enum değerlerinden biri olmalıdır.
- `manualProgress` 0-100 aralığında olmalıdır.
- `targetCompletionDate`, `startDate` değerinden önce olmamalıdır.
- `completedAt`, `startDate` değerinden önce olmamalıdır.

### 20.2 Relationship Constraints

- Relationship kayıtları aynı `ownerId` scope içinde olmalıdır.
- `projectId` mevcut veya sync pending project'e referans vermelidir.
- Removed relationship normal active query'de görünmemelidir.
- ProjectYarnLink usage amount negatif olmamalıdır.
- ProjectToolLink için `toolId` veya manual tool bilgisi bulunmalıdır.

---

## 21. Lokal Database Önerisi

Uygulama Flutter ile geliştirilecekse lokal persistence için adaylar:

- Drift
- Isar
- Hive
- SQLite wrapper
- Supabase local cache yaklaşımı

Bu doküman teknoloji seçimini zorunlu kılmaz, ancak aşağıdaki yetenekler gereklidir:

- Offline query
- Index desteği
- Migration desteği
- Transaction desteği
- Relationship query desteği
- Large text field desteği
- Sync metadata saklama
- Soft delete filtering

Teknoloji kararı `implementation-notes.md` içinde kesinleştirilmelidir.

---

## 22. Remote Database / Supabase Model Önerisi

Eğer Supabase kullanılacaksa önerilen tablolar:

```text
projects
project_pattern_links
project_yarn_links
project_tool_links
project_media
project_status_history
sync_operations
```

### 22.1 `projects` Tablosu

Önerilen kolonlar:

```text
project_id uuid primary key
owner_id uuid not null
name text not null
description text null
status text not null
technique text null
category text null
cover_media_id uuid null
start_date timestamptz null
target_completion_date timestamptz null
completed_at timestamptz null
archived_at timestamptz null
deleted_at timestamptz null
manual_progress int null
notes text null
created_at timestamptz not null
updated_at timestamptz not null
remote_version int not null default 1
schema_version int not null default 1
```

### 22.2 Row Level Security

RLS mantığı:

```text
owner_id = auth.uid()
```

Tüm project ve relationship tablolarında uygulanmalıdır.

---

## 23. Repository Modeli

Codex implementasyonunda Project Management için önerilen repository sorumlulukları:

### 23.1 ProjectRepository

Sorumluluklar:

- Project create
- Project update
- Project get by id
- Project list query
- Search
- Filter
- Sort
- Soft delete
- Restore
- Archive
- Status update
- Active count calculation

### 23.2 ProjectRelationshipRepository

Sorumluluklar:

- Pattern link/unlink
- Yarn link/unlink
- Tool link/unlink
- Broken relationship query
- Relationship cleanup

### 23.3 ProjectMediaRepository

Sorumluluklar:

- Cover image create
- Cover image replace
- Cover image remove
- Thumbnail metadata
- Upload state update

### 23.4 ProjectSyncRepository

Sorumluluklar:

- Pending records query
- Sync status update
- Conflict status update
- Last synced timestamp update
- Operation queue management

---

## 24. Domain Service Modeli

### 24.1 ProjectService

İş kuralları UI içinde değil, domain/service katmanında uygulanmalıdır.

ProjectService sorumlulukları:

- Create project use case
- Update project use case
- Validate project
- Change status
- Archive
- Restore
- Complete
- Reopen
- Soft delete
- Recover
- Calculate progress
- Check entitlement before activation

### 24.2 EntitlementService ile İlişki

ProjectService, active status oluşturmadan önce EntitlementService çağırmalıdır.

Kontrol gereken işlemler:

- Active project creation
- Draft to active
- Paused to active
- Completed to active
- Archived to active
- Deleted recovery to active

---

## 25. Migration Stratejisi

### 25.1 Schema Version

Her project kaydı `schemaVersion` alanı taşımalıdır.

Uygulama yeni sürüme geçtiğinde:

- Eski project kayıtları okunabilir kalmalıdır.
- Migration tamamlanmadan veri kaybı olmamalıdır.
- Migration idempotent olmalıdır.

### 25.2 İlk Migration

V1 başlangıç migration:

- `projects` tablosu oluşturulur.
- Relationship tabloları oluşturulur.
- Indexler oluşturulur.
- Soft delete alanları eklenir.
- Sync alanları eklenir.

### 25.3 Gelecek Migration Örnekleri

İleride yapılabilecek migrationlar:

- `Project.notes` alanını `ProjectNote` tablosuna taşıma
- Multiple cover media desteği
- Progress photo desteği
- Collaboration için shared access tablosu
- Public project profile alanları
- AI summary alanları

---

## 26. Veri Gizliliği

Aşağıdaki alanlar analytics veya log sistemlerine gönderilmemelidir:

- `name`
- `description`
- `notes`
- `localPath`
- `remotePath`
- Pattern metni
- Kullanıcı tarafından girilen custom alanlar

Loglarda kullanılabilecek güvenli alanlar:

- `projectId`, gerekiyorsa hashlenmiş
- `status`
- `syncStatus`
- `errorCode`
- `operationType`
- `durationMs`
- `result`

---

## 27. Data Export ve Account Deletion

### 27.1 Data Export

Kullanıcının veri export talebinde project verileri dahil edilmelidir:

- Project core fields
- Project relationships
- Project notes
- Project media metadata
- Status history
- Sync state, gerekiyorsa hariç tutulabilir

### 27.2 Account Deletion

Account deletion sırasında:

- User-owned project kayıtları silinmeli veya anonymize edilmelidir.
- Project media cleanup yapılmalıdır.
- Relationship kayıtları silinmelidir.
- Backup retention politikaları uygulanmalıdır.

---

## 28. Örnek JSON Modeli

### 28.1 Project JSON

```json
{
  "projectId": "7d8b1b30-4b4a-4f4e-9b20-8c80c3a9f001",
  "ownerId": "user_123",
  "name": "Mavi Kazak",
  "description": "Kış için basic kazak projesi",
  "status": "active",
  "technique": "knitting",
  "category": "sweater",
  "coverMediaId": "media_456",
  "startDate": "2026-07-30T00:00:00Z",
  "targetCompletionDate": null,
  "completedAt": null,
  "archivedAt": null,
  "deletedAt": null,
  "manualProgress": 20,
  "notes": "Kol ölçüsünü biraz uzun tut.",
  "createdAt": "2026-07-30T10:00:00Z",
  "updatedAt": "2026-07-30T10:00:00Z",
  "syncStatus": "pending",
  "remoteVersion": null,
  "localVersion": 1,
  "schemaVersion": 1,
  "createdOffline": true,
  "lastSyncedAt": null
}
```

### 28.2 ProjectYarnLink JSON

```json
{
  "projectYarnLinkId": "link_001",
  "projectId": "7d8b1b30-4b4a-4f4e-9b20-8c80c3a9f001",
  "ownerId": "user_123",
  "yarnId": "yarn_789",
  "usageAmount": 250,
  "usageUnit": "gram",
  "colorRole": "main",
  "relationshipStatus": "active",
  "createdAt": "2026-07-30T10:05:00Z",
  "updatedAt": "2026-07-30T10:05:00Z",
  "removedAt": null,
  "syncStatus": "pending"
}
```

---

## 29. Codex İçin Uygulama Beklentileri

Codex bu veri modelini uygularken:

- Entity modellerini net ayırmalıdır.
- Project içine tüm relationship datalarını gömmemelidir.
- Repository pattern kullanmalıdır.
- Domain validation UI'dan bağımsız olmalıdır.
- Soft delete query filtreleri unutulmamalıdır.
- `ownerId` tüm sorgularda kullanılmalıdır.
- Offline create ve update local-first çalışmalıdır.
- Sync metadata her değişiklikte güncellenmelidir.
- Migration dosyaları geriye uyumlu olmalıdır.
- Enum değerleri string olarak stabil tutulmalıdır.
- UI label ile enum value birbirine karıştırılmamalıdır.

---

## 30. Açık Teknik Kararlar

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-DM-OD-001 | Local DB teknolojisi | Drift veya Isar | Open |
| PM-DM-OD-002 | Remote DB | Supabase uygun | Open |
| PM-DM-OD-003 | Notes ayrı tablo mu? | V1'de Project.notes | Open |
| PM-DM-OD-004 | Status history V1 zorunlu mu? | Önerilir ama opsiyonel | Open |
| PM-DM-OD-005 | Conflict table olacak mı? | V1'de basit syncStatus yeterli olabilir | Open |
| PM-DM-OD-006 | Media cleanup nasıl yapılacak? | Background cleanup queue | Open |
| PM-DM-OD-007 | Project ID formatı | UUID | Open |
| PM-DM-OD-008 | Manual tool entry desteklenecek mi? | Evet | Open |

---

## 31. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-001-project-management/analytics.md
```

`analytics.md` içinde Project Management için event isimleri, event payloadları, funnel ölçümleri, conversion metrikleri ve privacy kuralları tanımlanacaktır.
