# Row Counter — Data Model

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/data-model.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliğinin veri modelini tanımlar.

Row Counter veri modeli aşağıdaki ihtiyaçları desteklemelidir:

- Project'e bağlı counter oluşturma
- Bir project içinde birden fazla counter tutma
- Counter değerini hızlı ve güvenli güncelleme
- Increment, decrement, reset, manual set ve undo işlemlerini modelleme
- Target value üzerinden progress hesaplama
- Offline-first local persistence
- Operation-based sync
- Conflict detection ve conflict resolution
- Soft delete
- Project ve part relationship yönetimi
- Analytics ve security gereksinimlerine uygun veri ayrımı

Bu doküman `requirements.md` ve `flows.md` içindeki davranışların teknik veri karşılığını tanımlar.

---

## 2. Veri Modeli Prensipleri

Row Counter veri modeli şu prensiplere göre tasarlanmalıdır:

1. Counter güncellemeleri çok hızlı lokal yazılabilmelidir.
2. Her counter kullanıcıya `ownerId` ile scope edilmelidir.
3. Counter, project context ile çalışmalıdır.
4. Counter value tek başına yeterli değildir; sync için operation modeli düşünülmelidir.
5. Offline increment/decrement veri kaybı üretmemelidir.
6. Remote sync başarısızlığı lokal counter değerini geri almamalıdır.
7. Counter soft delete ile kaldırılmalıdır.
8. Counter name ve notes analytics/log sistemlerine gönderilmemelidir.
9. Project ve part ilişkileri bozulursa counter core data korunmalıdır.
10. Data model V1 sonrası advanced counter özelliklerine genişleyebilmelidir.

---

## 3. Ana Entity Listesi

V1 Row Counter için minimum entity seti:

| Entity | Amaç |
|---|---|
| `RowCounter` | Ana counter kaydı |
| `CounterOperation` | Increment/decrement/reset/manual set/undo operation kaydı |
| `CounterSyncState` | Sync state ayrı tutulmak istenirse kullanılabilir |
| `CounterConflict` | Conflict bilgisi ayrı tutulmak istenirse kullanılabilir |
| `CounterSnapshot` | İleri seviye history/snapshot için opsiyonel yapı |

V1 için öneri:

```text
RowCounter + CounterOperation minimum yeterlidir.
CounterConflict ayrı tablo opsiyoneldir.
CounterSnapshot V1 için zorunlu değildir.
```

---

## 4. RowCounter Entity

### 4.1 Amaç

`RowCounter`, kullanıcının project veya project part için kullandığı sayaç kaydıdır.

Bir counter:

- Bir kullanıcıya aittir.
- Genellikle bir project'e bağlıdır.
- Opsiyonel olarak bir part'a bağlı olabilir.
- Güncel numeric value taşır.
- Step ve target bilgisi taşıyabilir.
- Offline güncellenebilir.
- Soft delete edilebilir.

---

### 4.2 RowCounter Alanları

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `counterId` | `String` / UUID | Evet | Counter benzersiz kimliği |
| `ownerId` | `String` | Evet | Counter sahibi kullanıcı |
| `projectId` | `String?` | V1 önerisi Evet | Bağlı project |
| `partId` | `String?` | Hayır | Bağlı project part |
| `name` | `String` | Evet | Kullanıcıya görünen counter adı |
| `counterType` | `CounterType` | Evet | Row, round, repeat vb. |
| `currentValue` | `int` | Evet | Güncel sayaç değeri |
| `initialValue` | `int` | Evet | Reset değeridir |
| `stepValue` | `int` | Evet | Increment/decrement adımı |
| `targetValue` | `int?` | Hayır | Hedef değer |
| `isPrimary` | `bool` | Evet | Project için primary counter mı |
| `notes` | `String?` | Hayır | Kullanıcı notu |
| `createdAt` | `DateTime` | Evet | Oluşturulma zamanı |
| `updatedAt` | `DateTime` | Evet | Son güncelleme zamanı |
| `deletedAt` | `DateTime?` | Hayır | Soft delete zamanı |
| `lastIncrementedAt` | `DateTime?` | Hayır | Son increment zamanı |
| `lastDecrementedAt` | `DateTime?` | Hayır | Son decrement zamanı |
| `lastResetAt` | `DateTime?` | Hayır | Son reset zamanı |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |
| `localVersion` | `int` | Evet | Lokal versiyon |
| `remoteVersion` | `int?` | Hayır | Remote versiyon |
| `schemaVersion` | `int` | Evet | Schema migration takibi |
| `createdOffline` | `bool` | Evet | Offline oluşturuldu mu |
| `lastSyncedAt` | `DateTime?` | Hayır | Son başarılı sync zamanı |

---

### 4.3 Alan Açıklamaları

#### `counterId`

- UUID olmalıdır.
- Local ve remote tarafında aynı kalmalıdır.
- Operation log içinde referans olarak kullanılır.

#### `ownerId`

- Tüm query, update ve sync işlemlerinde scope için kullanılır.
- Client payload içinden gelse bile server tarafında auth user ile doğrulanmalıdır.

#### `projectId`

- V1 için önerilen modelde counter project'e bağlıdır.
- Bağımsız counter desteklenirse nullable olabilir.
- Project soft delete edilirse counter normal project context'te gösterilmez.

#### `partId`

- Multi-Part Tracking ile ilişkidir.
- Opsiyoneldir.
- Part silinirse counter silinmez; broken relationship state gösterilir.

#### `currentValue`

- Güncel sayaç değeridir.
- Increment/decrement/manual_set/reset işlemleri bu alanı değiştirir.
- V1 önerisi negatif olmamalıdır.

#### `initialValue`

- Reset işlemi bu değere döndürür.
- Default ürün kararına göre `0` veya `1` olabilir.
- Mevcut öneri: `1`.

#### `stepValue`

- Increment ve decrement için kullanılan değer.
- Pozitif integer olmalıdır.
- Default: `1`.

#### `targetValue`

- Opsiyoneldir.
- Progress hesaplama için kullanılır.
- `null` ise progress no-target state gösterilir.

#### `isPrimary`

- Project progress için bir counter'ın öne çıkarılması gerekiyorsa kullanılır.
- V1 için opsiyonel davranıştır.
- Aynı project içinde birden fazla `isPrimary = true` olmamalıdır.

---

## 5. Enum Tanımları

### 5.1 CounterType

```text
row
round
repeat
increase
decrease
custom
```

| Değer | Açıklama |
|---|---|
| `row` | Satır sayacı |
| `round` | Tur sayacı |
| `repeat` | Pattern tekrar sayacı |
| `increase` | Artırma sayacı |
| `decrease` | Eksiltme sayacı |
| `custom` | Özel amaçlı sayaç |

UI label lokalize edilmelidir. Enum value değişmemelidir.

---

### 5.2 CounterOperationType

```text
increment
decrement
manual_set
reset
undo
create
update_metadata
soft_delete
recover
```

| Değer | Açıklama |
|---|---|
| `increment` | Counter değerini step kadar artırır |
| `decrement` | Counter değerini step kadar azaltır |
| `manual_set` | Counter değerini doğrudan değiştirir |
| `reset` | Counter değerini initialValue değerine döndürür |
| `undo` | Önceki operation etkisini geri alır |
| `create` | Counter oluşturma operation'ı |
| `update_metadata` | Name/type/target/step gibi metadata güncelleme |
| `soft_delete` | Counter soft delete |
| `recover` | Counter recovery, V1 opsiyonel |

---

### 5.3 SyncStatus

```text
synced
pending
failed
conflict
```

`Project Management` ile aynı enum kullanılmalıdır.

---

### 5.4 CounterConflictType

```text
value_conflict
metadata_conflict
deleted_remote
project_deleted
part_missing
operation_order_conflict
unknown
```

---

## 6. CounterOperation Entity

### 6.1 Amaç

`CounterOperation`, counter üzerinde yapılan değer veya metadata değişikliklerini temsil eder.

Bu entity özellikle offline sync, retry, undo ve conflict handling için önemlidir.

---

### 6.2 CounterOperation Alanları

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `operationId` | `String` / UUID | Evet | Operation benzersiz ID |
| `counterId` | `String` | Evet | İlgili counter |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `projectId` | `String?` | Hayır | Bağlı project |
| `operationType` | `CounterOperationType` | Evet | Increment, decrement vb. |
| `valueBefore` | `int?` | Hayır | İşlem öncesi değer |
| `valueAfter` | `int?` | Hayır | İşlem sonrası değer |
| `delta` | `int?` | Hayır | Increment/decrement delta |
| `metadataPatch` | `Json?` | Hayır | Metadata update için patch |
| `isUndoable` | `bool` | Evet | Undo destekli mi |
| `undoForOperationId` | `String?` | Hayır | Undo edilen operation ID |
| `createdAt` | `DateTime` | Evet | Operation zamanı |
| `syncedAt` | `DateTime?` | Hayır | Remote sync zamanı |
| `syncStatus` | `SyncStatus` | Evet | Sync durumu |
| `attemptCount` | `int` | Evet | Sync deneme sayısı |
| `lastAttemptAt` | `DateTime?` | Hayır | Son sync denemesi |
| `nextRetryAt` | `DateTime?` | Hayır | Sonraki retry zamanı |
| `operationOrder` | `int` | Evet | Lokal sıralama için monoton sıra |

---

### 6.3 Operation Model Neden Gerekli?

Counter value update klasik record update gibi ele alınırsa şu sorunlar oluşur:

- Offline çoklu increment kaybolabilir.
- İki cihazda yapılan değişiklikler ezilebilir.
- Undo zorlaşır.
- Conflict çözümü belirsizleşir.
- Retry duplicate increment üretebilir.

Operation model:

- Değişiklikleri niyet olarak saklar.
- Retry işlemini idempotent yapar.
- Conflict yönetimini iyileştirir.
- Counter history için temel sağlar.

---

### 6.4 Operation Compaction

Offline durumda 50 increment operation oluşabilir.

Sync için sıkıştırma yapılabilir:

```text
increment + increment + increment
→ delta +3
```

Ancak compaction sadece güvenli ise yapılmalıdır.

Compaction riskleri:

- Undo history azalır.
- Operation order detayları kaybolur.
- Conflict çözümü zorlaşabilir.

V1 önerisi:

```text
Local operation log korunsun.
Remote sync için delta compaction opsiyonel uygulansın.
```

---

## 7. Counter Progress Modeli

### 7.1 Hesaplama

Counter target varsa progress hesaplanır:

```text
progress = currentValue / targetValue
```

### 7.2 Computed Alanlar

Database içinde saklanması zorunlu değildir.

Domain/service katmanında hesaplanabilir:

| Alan | Tip | Açıklama |
|---|---|---|
| `progressPercent` | `double?` | Yüzde |
| `progressState` | `CounterProgressState` | none, inProgress, reached, exceeded |
| `progressLabel` | `String` | UI label |

### 7.3 CounterProgressState

```text
none
in_progress
reached
exceeded
invalid_target
```

| Değer | Açıklama |
|---|---|
| `none` | Target yok |
| `in_progress` | Current target altında |
| `reached` | Current target'a eşit |
| `exceeded` | Current target'ı aşmış |
| `invalid_target` | Target geçersiz |

---

## 8. Undo Modeli

### 8.1 V1 Basic Undo

V1 için son operation undo edilebilir.

Bunun için `CounterOperation` içinde şu alanlar yeterlidir:

- `valueBefore`
- `valueAfter`
- `isUndoable`
- `undoForOperationId`

### 8.2 Undo Operation

Undo ayrı operation olarak saklanmalıdır.

Örnek:

```json
{
  "operationType": "undo",
  "undoForOperationId": "op_123",
  "valueBefore": 11,
  "valueAfter": 10
}
```

### 8.3 Undo Süresi

Undo UI süresi product kararıdır.

Öneri:

```text
3-5 saniye
```

Süre dolsa bile operation log kalabilir; ancak UI undo aksiyonu kapanır.

---

## 9. Soft Delete Modeli

### 9.1 Counter Soft Delete

Counter silindiğinde:

- `deletedAt` doldurulur.
- Normal listede görünmez.
- Project detail counter section'dan çıkar.
- Sync operation `soft_delete` oluşturulur.
- Counter verisi hemen permanent delete yapılmaz.

### 9.2 Project Soft Delete Etkisi

Project soft delete edilirse bağlı counterlar:

- Project context içinde görünmez.
- Fiziksel olarak silinmez.
- Project recover edilirse tekrar görünür.

### 9.3 Permanent Delete

Permanent delete:

- Account deletion
- Recovery süresi sonrası cleanup
- Project permanent delete

işlemleriyle yapılmalıdır.

---

## 10. Relationship Modeli

### 10.1 Project Relationship

`RowCounter.projectId` project bağlantısını temsil eder.

Kurallar:

- Project aynı `ownerId` değerine sahip olmalıdır.
- Project `deletedAt == null` olmalıdır, create/update sırasında.
- Project archived ise edit policy uygulanmalıdır.

### 10.2 Part Relationship

`RowCounter.partId` Multi-Part Tracking ile ilişkilidir.

Kurallar:

- Part aynı project'e ait olmalıdır.
- Part aynı owner'a ait olmalıdır.
- Part silinirse counter silinmemelidir.
- UI broken part state göstermelidir.

### 10.3 Primary Counter Constraint

Eğer `isPrimary` desteklenirse:

```text
ownerId + projectId için yalnızca bir active primary counter
```

olmalıdır.

Bu local ve remote seviyede korunmalıdır.

---

## 11. Sync Modeli

### 11.1 Local-First

Counter update sırası:

```text
UI action
→ domain validation
→ local value update
→ operation log insert
→ sync status pending
→ remote sync later
```

Remote sync UI action'ı bloklamamalıdır.

### 11.2 Sync Operation Kaynağı

Counter için sync operation doğrudan `CounterOperation` entity üzerinden yürütülebilir.

Alternatif:

- Genel `sync_operations` tablosu içinde counter operation referansı tutulabilir.
- Feature-specific operation log ayrıca tutulabilir.

V1 önerisi:

```text
CounterOperation operation log olarak tutulur.
Genel sync queue bu operation'ları işleyebilir.
```

### 11.3 Idempotency

Her operation remote'a gönderilirken `operationId` kullanılmalıdır.

Remote aynı `operationId` ile gelen isteği ikinci kez işlerse duplicate increment oluşmamalıdır.

### 11.4 Remote Apply Stratejisi

Remote tarafı operation apply edebiliyorsa en güvenli model:

```text
counter current value + ordered operations
```

Basit V1 remote update modeli:

```text
counter latest currentValue upsert
```

Daha kolaydır ancak conflict riskini artırır.

Öneri:

```text
Local operation log mutlaka tutulsun.
Remote V1 latest value upsert olabilir.
V2'de operation-based remote apply'e geçilebilir.
```

---

## 12. Conflict Modeli

### 12.1 Conflict Nedenleri

Counter conflict şu durumlarda oluşabilir:

- Aynı counter iki cihazda offline artırılmıştır.
- Bir cihaz reset yaparken diğer cihaz increment yapmıştır.
- Bir cihaz manual set yaparken diğer cihaz decrement yapmıştır.
- Remote counter deleted iken local update vardır.
- Metadata iki cihazda farklı güncellenmiştir.
- Operation order uyuşmamıştır.

### 12.2 Conflict Alanları

Opsiyonel `CounterConflict` entity:

| Alan | Tip | Zorunlu | Açıklama |
|---|---|---:|---|
| `conflictId` | `String` | Evet | Conflict ID |
| `counterId` | `String` | Evet | İlgili counter |
| `ownerId` | `String` | Evet | Kullanıcı scope |
| `conflictType` | `CounterConflictType` | Evet | Conflict tipi |
| `localValue` | `int?` | Hayır | Lokal value |
| `remoteValue` | `int?` | Hayır | Remote value |
| `localPayload` | `Json?` | Hayır | Lokal payload |
| `remotePayload` | `Json?` | Hayır | Remote payload |
| `detectedAt` | `DateTime` | Evet | Tespit zamanı |
| `resolvedAt` | `DateTime?` | Hayır | Çözüm zamanı |
| `resolutionStrategy` | `String?` | Hayır | Kullanılan strateji |

### 12.3 V1 Conflict Resolution

V1 seçenekleri:

```text
use_local
use_remote
manual_set
```

Counter için `higher_value_wins` otomatik stratejisi önerilmez; çünkü kullanıcı reset yapmış olabilir.

---

## 13. Index ve Query Gereksinimleri

### 13.1 RowCounter Indexleri

| Index | Alanlar | Amaç |
|---|---|---|
| `idx_counter_owner_project_deleted` | `ownerId`, `projectId`, `deletedAt` | Project counter listesi |
| `idx_counter_owner_updated` | `ownerId`, `updatedAt` | Genel liste/sıralama |
| `idx_counter_sync_status` | `ownerId`, `syncStatus` | Pending sync query |
| `idx_counter_project_primary` | `ownerId`, `projectId`, `isPrimary` | Primary counter query |
| `idx_counter_part` | `ownerId`, `partId` | Part bağlı counterlar |
| `idx_counter_deleted` | `ownerId`, `deletedAt` | Cleanup/recovery |

### 13.2 CounterOperation Indexleri

| Index | Alanlar | Amaç |
|---|---|---|
| `idx_counter_operation_counter_order` | `counterId`, `operationOrder` | Operation history |
| `idx_counter_operation_sync` | `ownerId`, `syncStatus`, `nextRetryAt` | Sync retry |
| `idx_counter_operation_idempotency` | `operationId` | Duplicate engelleme |
| `idx_counter_operation_created` | `ownerId`, `createdAt` | Debug/history |

---

## 14. Constraint Gereksinimleri

### 14.1 RowCounter Constraints

- `counterId` unique olmalıdır.
- `ownerId` null olamaz.
- `name` trim sonrası boş olamaz.
- `name` maksimum 80 karakter olmalıdır.
- `counterType` geçerli enum olmalıdır.
- `currentValue >= 0` olmalıdır.
- `initialValue >= 0` olmalıdır.
- `stepValue >= 1` olmalıdır.
- `stepValue <= 100` önerilir.
- `targetValue == null OR targetValue >= 1` olmalıdır.
- `deletedAt != null` kayıtlar normal listede dönmemelidir.

### 14.2 CounterOperation Constraints

- `operationId` unique olmalıdır.
- `counterId` null olamaz.
- `ownerId` null olamaz.
- `operationType` geçerli enum olmalıdır.
- Increment için `delta > 0` olmalıdır.
- Decrement için `delta < 0` veya operation type'a göre negatif etkili olmalıdır.
- `operationOrder` counter bazında monoton artmalıdır.
- `syncStatus` geçerli enum olmalıdır.

---

## 15. Local Database Önerisi

Counter çok sık güncellendiği için local database performansı kritiktir.

Gereksinimler:

- Hızlı atomic update
- Transaction desteği
- Index desteği
- Offline query
- Migration
- Operation log saklama
- Batch write
- Conflict/sync metadata

Flutter için adaylar:

- Drift
- Isar
- SQLite wrapper
- Hive, ancak relational query zayıf olabilir

V1 için öneri:

```text
Project Management ile aynı local persistence teknolojisi kullanılmalıdır.
```

---

## 16. Remote Database / Supabase Model Önerisi

Supabase kullanılırsa tablolar:

```text
row_counters
counter_operations
counter_conflicts
```

`counter_conflicts` V1 için opsiyoneldir.

### 16.1 `row_counters` Tablosu

Önerilen kolonlar:

```text
counter_id uuid primary key
owner_id uuid not null
project_id uuid null
part_id uuid null
name text not null
counter_type text not null
current_value int not null
initial_value int not null
step_value int not null
target_value int null
is_primary boolean not null default false
notes text null
created_at timestamptz not null
updated_at timestamptz not null
deleted_at timestamptz null
last_incremented_at timestamptz null
last_decremented_at timestamptz null
last_reset_at timestamptz null
remote_version int not null default 1
schema_version int not null default 1
```

### 16.2 `counter_operations` Tablosu

Önerilen kolonlar:

```text
operation_id uuid primary key
counter_id uuid not null
owner_id uuid not null
project_id uuid null
operation_type text not null
value_before int null
value_after int null
delta int null
metadata_patch jsonb null
is_undoable boolean not null default false
undo_for_operation_id uuid null
created_at timestamptz not null
synced_at timestamptz null
operation_order bigint not null
```

### 16.3 RLS

Tüm tablolarda:

```text
owner_id = auth.uid()
```

policy zorunludur.

---

## 17. Repository Modeli

### 17.1 CounterRepository

Sorumluluklar:

- Counter create
- Counter get by id
- Project counter list
- Counter update metadata
- Counter value update
- Counter soft delete
- Counter recovery, varsa
- Primary counter query
- Active counter count

### 17.2 CounterOperationRepository

Sorumluluklar:

- Operation insert
- Pending operation query
- Operation mark synced
- Operation mark failed
- Retry metadata update
- Last undoable operation query
- Operation compaction, varsa

### 17.3 CounterSyncRepository

Sorumluluklar:

- Pending counter operations sync
- Conflict detect
- Conflict resolve
- Remote version update
- Sync status update

---

## 18. Domain Service Modeli

### 18.1 CounterDomainService

Sorumluluklar:

- Increment hesaplama
- Decrement hesaplama
- Minimum değer kontrolü
- Manual set validasyonu
- Reset hesaplama
- Undo hesaplama
- Target progress hesaplama

### 18.2 CounterValidationService

Sorumluluklar:

- Name validation
- Type validation
- Current value validation
- Step validation
- Target validation
- Notes length validation
- Project/part relationship validation

### 18.3 CounterProgressCalculator

Sorumluluklar:

- Progress percent hesaplama
- Progress state belirleme
- Target exceeded/reached durumlarını üretme

---

## 19. Migration Stratejisi

### 19.1 V1 Initial Migration

V1 migration:

- `row_counters` oluşturur.
- `counter_operations` oluşturur.
- Indexleri oluşturur.
- Sync metadata alanlarını ekler.
- Soft delete alanlarını ekler.

### 19.2 Gelecek Migration Örnekleri

İleride eklenebilecekler:

- Counter groups
- Counter templates
- Smart pattern-linked counters
- Counter history UI
- Device-specific operation metadata
- Watch/widget integration fields
- Advanced repeat counters
- Counter reminders

---

## 20. Data Privacy

Analytics/log sistemlerine gönderilmemesi gereken alanlar:

- `name`
- `notes`
- Project name
- Part name
- Pattern text

Güvenli alanlar:

- `counterType`
- `hasTarget`
- `syncStatus`
- `operationType`
- `deltaBucket`
- `errorCode`
- `durationMs`

Raw `currentValue` bazı analytics senaryolarında gerekli olmayabilir; bucket kullanılması tercih edilir.

---

## 21. Örnek JSON Modelleri

### 21.1 RowCounter JSON

```json
{
  "counterId": "counter_001",
  "ownerId": "user_001",
  "projectId": "project_001",
  "partId": null,
  "name": "Gövde",
  "counterType": "row",
  "currentValue": 30,
  "initialValue": 1,
  "stepValue": 1,
  "targetValue": 100,
  "isPrimary": true,
  "notes": "Her 4 satırda artır.",
  "createdAt": "2026-08-01T10:00:00Z",
  "updatedAt": "2026-08-01T10:20:00Z",
  "deletedAt": null,
  "lastIncrementedAt": "2026-08-01T10:20:00Z",
  "lastDecrementedAt": null,
  "lastResetAt": null,
  "syncStatus": "pending",
  "localVersion": 5,
  "remoteVersion": null,
  "schemaVersion": 1,
  "createdOffline": false,
  "lastSyncedAt": null
}
```

### 21.2 CounterOperation JSON

```json
{
  "operationId": "op_001",
  "counterId": "counter_001",
  "ownerId": "user_001",
  "projectId": "project_001",
  "operationType": "increment",
  "valueBefore": 29,
  "valueAfter": 30,
  "delta": 1,
  "metadataPatch": null,
  "isUndoable": true,
  "undoForOperationId": null,
  "createdAt": "2026-08-01T10:20:00Z",
  "syncedAt": null,
  "syncStatus": "pending",
  "attemptCount": 0,
  "lastAttemptAt": null,
  "nextRetryAt": null,
  "operationOrder": 12
}
```

---

## 22. Codex İçin Uygulama Beklentileri

Codex bu veri modelini uygularken:

- Counter value update işlemlerini atomic yapmalıdır.
- Operation log oluşturmadan increment/decrement kaydetmemelidir.
- Local save remote sync'ten önce gelmelidir.
- `ownerId` tüm query'lerde bulunmalıdır.
- `projectId` tek başına güvenlik için yeterli değildir.
- Counter name ve notes analytics/log sistemine gönderilmemelidir.
- Soft deleted counter normal listelerde görünmemelidir.
- Project soft delete counterları project context'ten gizlemelidir.
- Undo için `valueBefore` ve `valueAfter` güvenli saklanmalıdır.
- Duplicate operation idempotency ile engellenmelidir.
- Conflict durumunda lokal veri sessizce silinmemelidir.

---

## 23. Açık Teknik Kararlar

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-DM-OD-001 | Default initialValue | 1 önerilir | Open |
| RC-DM-OD-002 | Counter projectId nullable mı? | V1'de non-null önerilir | Open |
| RC-DM-OD-003 | Operation log zorunlu mu? | Evet, minimum düzeyde | Open |
| RC-DM-OD-004 | Remote operation apply olacak mı? | V1'de latest value upsert olabilir | Open |
| RC-DM-OD-005 | Primary counter desteklenecek mi? | Opsiyonel | Open |
| RC-DM-OD-006 | Counter conflict table olacak mı? | V1'de opsiyonel | Open |
| RC-DM-OD-007 | Notes tutulacak mı? | Opsiyonel, max 1000 karakter | Open |
| RC-DM-OD-008 | Counter recovery UI olacak mı? | V1'de şart değil | Open |

---

## 24. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/analytics.md
```

`analytics.md` içinde Row Counter için event isimleri, payload kuralları, funnel metrikleri, usage aggregation ve privacy kuralları tanımlanacaktır.
