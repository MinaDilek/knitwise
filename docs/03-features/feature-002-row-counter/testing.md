# Row Counter — Testing

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/testing.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliği için test stratejisini, test kapsamını, test senaryolarını, acceptance criteria doğrulamalarını ve release exit criteria maddelerini tanımlar.

Row Counter sık kullanılan, hızlı etkileşim gerektiren ve offline-first çalışması beklenen bir özelliktir. Bu nedenle test kapsamı yalnızca counter oluşturma ve butonların çalışmasıyla sınırlı değildir.

Test kapsamı şu alanları içermelidir:

- Counter oluşturma
- Counter listeleme
- Counter detail görüntüleme
- Increment / decrement
- Hızlı çoklu tap
- Manual value set
- Step value
- Target progress
- Reset
- Undo
- Soft delete
- Project ve part relationship
- Offline increment/decrement
- Sync retry
- Duplicate operation engelleme
- Conflict handling
- Ownership ve authorization
- Analytics privacy
- Accessibility
- Localization
- Migration

Bu dosya Codex veya geliştirici ekip tarafından doğrudan test yazımı için kullanılabilecek seviyede hazırlanmıştır.

---

## 2. Test Prensipleri

Row Counter testleri şu prensiplere göre yazılmalıdır:

1. Counter hesaplama mantığı unit test ile doğrulanmalıdır.
2. Increment/decrement UI testlerine bırakılmamalıdır.
3. Fast tap ve atomic update özel olarak test edilmelidir.
4. Operation log her value change için test edilmelidir.
5. Offline update ve sync retry testleri zorunludur.
6. Duplicate operation duplicate increment üretmemelidir.
7. Counter ownership repository ve backend seviyesinde doğrulanmalıdır.
8. Analytics testleri PII sızıntısını yakalamalıdır.
9. Reset ve delete confirmation test edilmelidir.
10. Broken project/part ilişkileri crash üretmemelidir.
11. Counter name ve notes production log/analytics içinde bulunmamalıdır.
12. Time-dependent testlerde fake clock kullanılmalıdır.

---

## 3. Test Türleri

| Test Türü | Amaç |
|---|---|
| Unit Test | Validation, calculation, progress, undo, enum mapping |
| Use Case Test | Create, increment, decrement, reset, undo, delete |
| Repository Test | Owner scoped query, atomic update, operation insert |
| Data Source Test | Local transaction, index, migration |
| Widget Test | Counter UI componentleri |
| Integration Test | Ekran ve use case entegrasyonu |
| Offline Test | Offline create/update ve pending sync |
| Sync Test | Retry, idempotency, conflict |
| Security Test | Unauthorized access ve ownership isolation |
| Analytics Test | Event ve privacy doğrulaması |
| Accessibility Test | Screen reader, touch target, dynamic text |
| Localization Test | Türkçe karakter, enum label, hata mesajı |
| Acceptance Test | Uçtan uca kullanıcı akışları |
| Regression Test | Her değişiklik sonrası temel akış kontrolü |

---

## 4. Test Ortamları

### 4.1 Local Unit Test Ortamı

Kapsam:

- Domain service
- Value object
- Progress calculator
- Undo service
- Operation compaction
- Failure mapping
- Analytics privacy filter

### 4.2 Local Repository Test Ortamı

Kapsam:

- In-memory DB veya temp DB
- Atomic increment transaction
- Owner scoped query
- Operation insert
- Pending operation query
- Soft delete query
- Migration testleri

### 4.3 Integration Test Ortamı

Kapsam:

- Counter Detail ekranı
- Project Detail counter section
- Create Counter flow
- Offline mode mock
- Sync engine mock
- Analytics fake tracker
- Auth fake user context

### 4.4 Staging Ortamı

Kapsam:

- Supabase RLS, kullanılıyorsa
- Remote idempotency
- Sync retry
- Conflict handling
- Multi-device test
- Signed auth context
- Production-like analytics smoke

---

## 5. Test Data Setleri

### 5.1 Minimum Counter Fixture

```json
{
  "counterId": "counter_001",
  "ownerId": "user_001",
  "projectId": "project_001",
  "partId": null,
  "name": "Gövde",
  "counterType": "row",
  "currentValue": 10,
  "initialValue": 1,
  "stepValue": 1,
  "targetValue": 100,
  "isPrimary": false,
  "createdAt": "2026-08-01T10:00:00Z",
  "updatedAt": "2026-08-01T10:10:00Z",
  "deletedAt": null,
  "syncStatus": "synced",
  "localVersion": 1,
  "remoteVersion": 1,
  "schemaVersion": 1,
  "createdOffline": false
}
```

### 5.2 Multi Counter Fixture

```text
project_001
├── counter_body       currentValue=30 targetValue=100 type=row
├── counter_right_arm  currentValue=12 targetValue=60 type=row
├── counter_left_arm   currentValue=10 targetValue=60 type=row
├── counter_repeat     currentValue=3  targetValue=null type=repeat
└── counter_round      currentValue=8  targetValue=24 type=round
```

### 5.3 Operation Fixture

```json
{
  "operationId": "op_001",
  "counterId": "counter_001",
  "ownerId": "user_001",
  "operationType": "increment",
  "valueBefore": 10,
  "valueAfter": 11,
  "delta": 1,
  "isUndoable": true,
  "createdAt": "2026-08-01T10:20:00Z",
  "syncStatus": "pending",
  "attemptCount": 0,
  "operationOrder": 1
}
```

### 5.4 Multi User Fixture

```text
user_001 → counter_user_001
user_002 → counter_user_002
```

Amaç:

- Owner isolation
- Unauthorized read/write
- Operation ownership
- Project/part relationship authorization

### 5.5 Offline Fixture

```text
counter_offline_001
syncStatus=pending
createdOffline=true
operationCount=25
```

### 5.6 Conflict Fixture

```text
local:
  currentValue=25
  localVersion=5
  pending operations=+5

remote:
  currentValue=18
  remoteVersion=6
  remote changed by another device
```

### 5.7 Broken Relationship Fixture

```text
counter_001
├── projectId=missing_project
└── partId=missing_part
```

---

## 6. Unit Test Kapsamı

### 6.1 CounterName Validation Testleri

#### RC-TEST-UNIT-001 — Geçerli Counter Name Kabul Edilir

**Given** input `Gövde`  
**When** `CounterName.create` çağrılır  
**Then** value `Gövde` olur.

#### RC-TEST-UNIT-002 — Whitespace Trim Edilir

**Given** input `  Sağ Kol  `  
**When** validation çalışır  
**Then** value `Sağ Kol` olur.

#### RC-TEST-UNIT-003 — Boş Counter Name Reddedilir

**Given** input boş string  
**When** validation çalışır  
**Then** `empty_counter_name` hatası döner.

#### RC-TEST-UNIT-004 — Sadece Boşluk Reddedilir

**Given** input `"     "`  
**When** validation çalışır  
**Then** `empty_counter_name` hatası döner.

#### RC-TEST-UNIT-005 — Çok Uzun Counter Name Reddedilir

**Given** 80 karakterden uzun name  
**When** validation çalışır  
**Then** `counter_name_too_long` hatası döner.

---

### 6.2 Numeric Validation Testleri

#### RC-TEST-UNIT-006 — Current Value 0 Geçerlidir

**Given** `currentValue=0`  
**Then** validation başarılıdır.

#### RC-TEST-UNIT-007 — Negative Current Value Reddedilir

**Given** `currentValue=-1`  
**Then** `invalid_counter_value` hatası döner.

#### RC-TEST-UNIT-008 — Initial Value 0 Geçerlidir

**Given** `initialValue=0`  
**Then** validation başarılıdır.

#### RC-TEST-UNIT-009 — Step Value 1 Geçerlidir

**Given** `stepValue=1`  
**Then** validation başarılıdır.

#### RC-TEST-UNIT-010 — Step Value 0 Reddedilir

**Given** `stepValue=0`  
**Then** `invalid_step_value` hatası döner.

#### RC-TEST-UNIT-011 — Step Value Çok Büyükse Reddedilir

**Given** `stepValue=101`  
**Then** `step_value_too_large` hatası döner.

#### RC-TEST-UNIT-012 — Target Null Geçerlidir

**Given** `targetValue=null`  
**Then** validation başarılıdır.

#### RC-TEST-UNIT-013 — Target 0 Reddedilir

**Given** `targetValue=0`  
**Then** `invalid_target_value` hatası döner.

---

### 6.3 CounterType Mapping Testleri

#### RC-TEST-UNIT-014 — Row Type Parse Edilir

**Given** raw value `row`  
**When** enum mapping yapılır  
**Then** `CounterType.row` döner.

#### RC-TEST-UNIT-015 — Unknown Type Safe Yönetilir

**Given** raw value `unknown_type`  
**When** enum mapping yapılır  
**Then** crash olmaz  
**And** safe fallback veya parse failure döner.

---

## 7. Domain Calculation Testleri

### RC-TEST-DOM-001 — Increment Step Kadar Artırır

**Given** currentValue `10`  
**And** stepValue `1`  
**When** increment hesaplanır  
**Then** newValue `11` olur.

### RC-TEST-DOM-002 — Increment Step 5 Uygular

**Given** currentValue `10`  
**And** stepValue `5`  
**When** increment hesaplanır  
**Then** newValue `15` olur.

### RC-TEST-DOM-003 — Decrement Step Kadar Azaltır

**Given** currentValue `10`  
**And** stepValue `1`  
**When** decrement hesaplanır  
**Then** newValue `9` olur.

### RC-TEST-DOM-004 — Decrement Minimum Altına İnmez

**Given** currentValue `0`  
**And** stepValue `1`  
**When** decrement hesaplanır  
**Then** minimum blocked failure döner.

### RC-TEST-DOM-005 — Reset Initial Value'ya Döner

**Given** currentValue `40`  
**And** initialValue `1`  
**When** reset hesaplanır  
**Then** newValue `1` olur.

### RC-TEST-DOM-006 — Manual Set Geçerli Değeri Atar

**Given** currentValue `10`  
**When** manual set `25` yapılır  
**Then** newValue `25` olur.

### RC-TEST-DOM-007 — Undo Önceki Değere Döner

**Given** operation valueBefore `10`, valueAfter `11`  
**When** undo hesaplanır  
**Then** newValue `10` olur.

---

## 8. Progress Calculator Testleri

### RC-TEST-PROG-001 — Target Yoksa None Döner

**Given** targetValue null  
**When** progress hesaplanır  
**Then** state `none` olur.

### RC-TEST-PROG-002 — Target Altında In Progress Döner

**Given** currentValue `30`  
**And** targetValue `100`  
**When** progress hesaplanır  
**Then** state `in_progress`  
**And** percent `30%` olur.

### RC-TEST-PROG-003 — Target'a Ulaşınca Reached Döner

**Given** currentValue `100`  
**And** targetValue `100`  
**When** progress hesaplanır  
**Then** state `reached` olur.

### RC-TEST-PROG-004 — Target Aşılırsa Exceeded Döner

**Given** currentValue `120`  
**And** targetValue `100`  
**When** progress hesaplanır  
**Then** state `exceeded` olur.

### RC-TEST-PROG-005 — Invalid Target Crash Üretmez

**Given** targetValue `0`  
**When** progress hesaplanır  
**Then** state `invalid_target` olur.

---

## 9. Use Case Testleri

### 9.1 CreateCounterUseCase

#### RC-TEST-UC-001 — Project İçinden Counter Oluşturulur

**Given** user project sahibidir  
**When** valid counter create edilir  
**Then** counter lokal kaydedilir  
**And** `projectId` atanır  
**And** `create` operation oluşur.

#### RC-TEST-UC-002 — Empty Name Create'i Engeller

**Given** counter name boş  
**When** create çağrılır  
**Then** validation failure döner  
**And** local insert yapılmaz.

#### RC-TEST-UC-003 — Unauthorized Project'e Counter Oluşturulamaz

**Given** project başka kullanıcıya aittir  
**When** counter create çağrılır  
**Then** unauthorized failure döner.

#### RC-TEST-UC-004 — Part Aynı Project'te Değilse Reddedilir

**Given** part farklı project'e aittir  
**When** counter create sırasında partId verilir  
**Then** relationship validation failure döner.

#### RC-TEST-UC-005 — Offline Create Pending Sync Yapar

**Given** cihaz offline  
**When** valid counter create edilir  
**Then** local counter oluşur  
**And** syncStatus `pending` olur.

### 9.2 IncrementCounterUseCase

#### RC-TEST-UC-006 — Increment Başarılıdır

**Given** currentValue `10`, stepValue `1`  
**When** increment çağrılır  
**Then** currentValue `11` olur  
**And** increment operation insert edilir.

#### RC-TEST-UC-007 — Increment Local Version Artırır

**Given** localVersion `3`  
**When** increment çağrılır  
**Then** localVersion `4` olur.

#### RC-TEST-UC-008 — Increment Deleted Counter'da Engellenir

**Given** `deletedAt != null`  
**When** increment çağrılır  
**Then** `counter_deleted` failure döner.

#### RC-TEST-UC-009 — Increment Unauthorized Counter'da Engellenir

**Given** counter başka kullanıcıya aittir  
**When** increment çağrılır  
**Then** unauthorized failure döner.

### 9.3 DecrementCounterUseCase

#### RC-TEST-UC-010 — Decrement Başarılıdır

**Given** currentValue `10`, stepValue `1`  
**When** decrement çağrılır  
**Then** currentValue `9` olur  
**And** decrement operation insert edilir.

#### RC-TEST-UC-011 — Minimum Altına Decrement Engellenir

**Given** currentValue `0`, stepValue `1`  
**When** decrement çağrılır  
**Then** minimum blocked failure döner  
**And** operation insert edilmez.

### 9.4 ManualSetCounterUseCase

#### RC-TEST-UC-012 — Manual Set Başarılıdır

**Given** currentValue `10`  
**When** manual set `25` yapılır  
**Then** currentValue `25` olur  
**And** `manual_set` operation oluşur.

#### RC-TEST-UC-013 — Negative Manual Set Reddedilir

**Given** manual value `-5`  
**When** manual set çağrılır  
**Then** validation failure döner.

### 9.5 ResetCounterUseCase

#### RC-TEST-UC-014 — Reset Başarılıdır

**Given** currentValue `40`, initialValue `1`  
**When** reset çağrılır  
**Then** currentValue `1` olur  
**And** `reset` operation oluşur.

#### RC-TEST-UC-015 — Reset Undoable Operation Oluşturur

**Given** reset yapılır  
**Then** operation `isUndoable=true` olmalıdır  
**And** valueBefore/valueAfter saklanmalıdır.

### 9.6 UndoCounterOperationUseCase

#### RC-TEST-UC-016 — Increment Undo Edilir

**Given** son operation increment valueBefore `10`, valueAfter `11`  
**When** undo çağrılır  
**Then** currentValue `10` olur  
**And** undo operation oluşur.

#### RC-TEST-UC-017 — Undoable Operation Yoksa Hata Döner

**Given** counter için undoable operation yok  
**When** undo çağrılır  
**Then** failure döner.

#### RC-TEST-UC-018 — Current Value Operation ile Uyumsuzsa Undo Engellenir

**Given** last operation valueAfter `11`  
**And** currentValue `15`  
**When** undo çağrılır  
**Then** safe failure veya conflict döner.

### 9.7 SoftDeleteCounterUseCase

#### RC-TEST-UC-019 — Counter Soft Delete Edilir

**Given** counter owner'a aittir  
**When** soft delete çağrılır  
**Then** `deletedAt` dolar  
**And** normal listeden çıkar  
**And** `soft_delete` operation oluşur.

---

## 10. Repository Testleri

### RC-TEST-REPO-001 — Counter by ID Owner Scope Kullanır

**Given** iki user counterları vardır  
**When** user_001 kendi counter'ını ister  
**Then** sadece kendi counter'ı döner.

### RC-TEST-REPO-002 — Başka User Counter'ı Dönmez

**Given** user_002 counterId biliniyor  
**When** user_001 aynı counterId ile query yapar  
**Then** null veya unauthorized döner.

### RC-TEST-REPO-003 — Project Counter List Deleted Filtreler

**Given** project içinde active ve deleted counter vardır  
**When** listByProject çağrılır  
**Then** deleted counter dönmez.

### RC-TEST-REPO-004 — Pending Operations Listelenir

**Given** pending operations vardır  
**When** listPendingOperations çağrılır  
**Then** sadece owner'a ait pending operations döner.

### RC-TEST-REPO-005 — Last Undoable Operation Doğru Döner

**Given** counter için birden fazla operation vardır  
**When** last undoable operation istenir  
**Then** en son undoable operation döner.

### RC-TEST-REPO-006 — Soft Deleted Counter IncludeDeleted ile Dönebilir

**Given** deleted counter vardır  
**When** includeDeleted true ile get çağrılır  
**Then** counter dönebilir.

---

## 11. Atomic Update ve Fast Tap Testleri

### RC-TEST-ATOMIC-001 — 10 Hızlı Increment Doğru Sonuç Verir

**Given** currentValue `0`, stepValue `1`  
**When** 10 hızlı increment yapılır  
**Then** final value `10` olur.

### RC-TEST-ATOMIC-002 — 100 Hızlı Increment Tap Kaybetmez

**Given** currentValue `0`, stepValue `1`  
**When** 100 increment çağrısı parallel/sıralı simüle edilir  
**Then** final value `100` olur  
**And** 100 operation veya güvenli compacted operation oluşur.

### RC-TEST-ATOMIC-003 — Increment ve Decrement Karışık Doğru Hesaplanır

**Given** currentValue `10`  
**When** + + - + işlemleri yapılır  
**Then** final value `12` olur.

### RC-TEST-ATOMIC-004 — Transaction Failure Rollback Yapar

**Given** operation insert sırasında hata oluşur  
**When** increment transaction çalışır  
**Then** counter value yarım güncellenmiş kalmaz.

---

## 12. Widget Testleri

### RC-TEST-WIDGET-001 — Counter Value Büyük Gösterilir

**Given** counter detail render edilir  
**Then** current value ana görünür alanda gösterilir.

### RC-TEST-WIDGET-002 — Increment Button Görünür

**Given** counter detail render edilir  
**Then** `+` aksiyonu görünür ve erişilebilirdir.

### RC-TEST-WIDGET-003 — Decrement Minimumda Disabled veya Feedback Verir

**Given** currentValue `0`  
**When** decrement button render edilir  
**Then** disabled olabilir veya basılınca minimum feedback verir.

### RC-TEST-WIDGET-004 — Target Progress Gösterilir

**Given** targetValue vardır  
**When** counter detail render edilir  
**Then** progress indicator görünür.

### RC-TEST-WIDGET-005 — No Target State Gösterilir

**Given** targetValue null  
**When** counter detail render edilir  
**Then** progress zorunlu gösterilmez veya no-target state görünür.

### RC-TEST-WIDGET-006 — Reset Confirmation Açılır

**Given** reset action seçilir  
**Then** confirmation dialog görünür.

### RC-TEST-WIDGET-007 — Delete Confirmation Açılır

**Given** delete action seçilir  
**Then** confirmation dialog görünür.

### RC-TEST-WIDGET-008 — Sync Indicator Pending Gösterir

**Given** counter syncStatus `pending`  
**When** widget render edilir  
**Then** pending sync indicator görünür.

---

## 13. Integration Testleri

### RC-TEST-INT-001 — Project Detail İçinden Counter Oluşturma

**Given** kullanıcı Project Detail ekranındadır  
**When** Add Counter ile geçerli counter oluşturur  
**Then** counter project detail içinde görünür  
**And** counter detail açılabilir.

### RC-TEST-INT-002 — Counter Increment Project Detail'i Günceller

**Given** project detail içinde counter section vardır  
**When** counter detailde increment yapılır  
**Then** project detail counter summary güncel değeri gösterir.

### RC-TEST-INT-003 — Counter Edit Değerleri Günceller

**Given** counter vardır  
**When** kullanıcı name, target ve step değiştirir  
**Then** counter detail güncel metadata gösterir.

### RC-TEST-INT-004 — Counter Delete Sonrası Liste Güncellenir

**Given** project içinde counter görünür  
**When** counter soft delete edilir  
**Then** counter listeden çıkar.

### RC-TEST-INT-005 — Broken Part Ekranı Crash Üretmez

**Given** counter missing part'a bağlıdır  
**When** counter detail açılır  
**Then** broken part state gösterilir.

---

## 14. Offline Testleri

### RC-TEST-OFF-001 — Offline Increment Local Kaydedilir

**Given** network offline  
**When** increment yapılır  
**Then** local value güncellenir  
**And** syncStatus `pending` olur.

### RC-TEST-OFF-002 — Offline Decrement Local Kaydedilir

**Given** network offline  
**When** decrement yapılır  
**Then** local value güncellenir  
**And** syncStatus `pending` olur.

### RC-TEST-OFF-003 — Offline Manual Set Local Kaydedilir

**Given** network offline  
**When** manual set yapılır  
**Then** local value güncellenir  
**And** operation pending olur.

### RC-TEST-OFF-004 — App Restart Sonrası Offline Value Korunur

**Given** offline increment yapılmıştır  
**When** app restart simüle edilir  
**Then** counter son lokal value ile açılır.

### RC-TEST-OFF-005 — Connectivity Restored Sync Başlatır

**Given** pending operations vardır  
**When** network online olur  
**Then** sync engine operationları işler.

---

## 15. Sync ve Idempotency Testleri

### RC-TEST-SYNC-001 — Pending Operation Sync Başarılıdır

**Given** pending increment operation vardır  
**When** sync çalışır  
**Then** remote update başarılı olur  
**And** operation `synced` olur.

### RC-TEST-SYNC-002 — Sync Failure Local Value Kaybettirmez

**Given** local value increment ile değişmiştir  
**When** sync failed olur  
**Then** local value korunur  
**And** operation failed/pending kalır.

### RC-TEST-SYNC-003 — Aynı Operation İki Kez Gönderilirse Tek Uygulanır

**Given** operationId `op_001`  
**When** sync retry aynı operation'ı iki kez gönderir  
**Then** remote value bir kez değişir.

### RC-TEST-SYNC-004 — Retry Attempt Count Artar

**Given** sync operation failed  
**When** retry yapılır  
**Then** attemptCount artar.

### RC-TEST-SYNC-005 — Max Retry Sonrası Failed State Kalır

**Given** max retry aşılmıştır  
**When** sync tekrar otomatik denenmez  
**Then** failed state korunur.

### RC-TEST-SYNC-006 — Operation Compaction Doğru Delta Üretir

**Given** +1 +1 -1 +1 operationları vardır  
**When** safe compaction yapılır  
**Then** delta `+2` olur.

### RC-TEST-SYNC-007 — Reset Boundary Compaction'ı Böler

**Given** +1 +1 reset +1 operationları vardır  
**When** compaction denenir  
**Then** reset boundary olarak ele alınır.

---

## 16. Conflict Testleri

### RC-TEST-CONF-001 — Value Conflict Tespit Edilir

**Given** local ve remote counter value farklı değişmiştir  
**When** sync çalışır  
**Then** conflict detected olur.

### RC-TEST-CONF-002 — Use Local Conflict Çözer

**Given** conflict state vardır  
**When** kullanıcı local value seçer  
**Then** local value korunur  
**And** corrective operation oluşur.

### RC-TEST-CONF-003 — Use Remote Conflict Çözer

**Given** conflict state vardır  
**When** kullanıcı remote value seçer  
**Then** local counter remote value ile güncellenir.

### RC-TEST-CONF-004 — Manual Set Conflict Çözer

**Given** conflict state vardır  
**When** kullanıcı manuel değer girer  
**Then** counter bu değerle güncellenir  
**And** `manual_set` operation oluşur.

### RC-TEST-CONF-005 — Remote Deleted Local Increment Sessiz Silinmez

**Given** remote counter deleted  
**And** local pending increment vardır  
**When** sync çalışır  
**Then** local data silinmez  
**And** conflict veya deleted remote state oluşur.

---

## 17. Security Testleri

### RC-TEST-SEC-001 — Unauthorized Counter Read Engellenir

**Given** user_001 login olmuştur  
**And** counter user_002'ye aittir  
**When** user_001 counter detail açmaya çalışır  
**Then** counter data gösterilmez.

### RC-TEST-SEC-002 — Unauthorized Increment Engellenir

**Given** user_001 başka kullanıcının counterId değerini bilir  
**When** increment çağırır  
**Then** unauthorized failure döner.

### RC-TEST-SEC-003 — Unauthorized Operation Sync Engellenir

**Given** operation başka kullanıcıya aittir  
**When** user_001 sync etmeye çalışır  
**Then** işlem reddedilir.

### RC-TEST-SEC-004 — Project Relationship Ownership Kontrol Edilir

**Given** project başka kullanıcıya aittir  
**When** counter bu project'e bağlanmak istenir  
**Then** unauthorized failure döner.

### RC-TEST-SEC-005 — Part Relationship Ownership Kontrol Edilir

**Given** part başka kullanıcıya aittir  
**When** counter bu part'a bağlanmak istenir  
**Then** unauthorized failure döner.

### RC-TEST-SEC-006 — Deleted Counter Increment Edilemez

**Given** `deletedAt != null`  
**When** increment çağrılır  
**Then** işlem engellenir.

---

## 18. Analytics Testleri

### RC-TEST-AN-001 — Create Started Event Gider

**Given** create flow başlatılır  
**Then** `counter_create_started` event gider.

### RC-TEST-AN-002 — Create Completed Event Gider

**Given** counter başarıyla oluşturulur  
**Then** `counter_create_completed` event gider.

### RC-TEST-AN-003 — Usage Summary Increment Sayar

**Given** session içinde 5 increment yapılır  
**When** usage summary flush edilir  
**Then** increment bucket doğru olur.

### RC-TEST-AN-004 — Counter Name Analytics'e Gitmez

**Given** counter name `Gövde`  
**When** event gönderilir  
**Then** payload içinde `Gövde` bulunmaz.

### RC-TEST-AN-005 — Notes Analytics'e Gitmez

**Given** notes doludur  
**When** event gönderilir  
**Then** notes içeriği payload içinde bulunmaz.

### RC-TEST-AN-006 — Raw Value Yerine Bucket Kullanılır

**Given** currentValue `123`  
**When** usage summary gönderilir  
**Then** raw `123` yerine value bucket gönderilir.

### RC-TEST-AN-007 — Sync Failed Event Gider

**Given** counter sync failed olur  
**Then** `counter_sync_failed` event gider.

### RC-TEST-AN-008 — Conflict Event PII İçermez

**Given** conflict payload içinde notes vardır  
**When** conflict event gönderilir  
**Then** notes analytics'e gitmez.

---

## 19. Accessibility Testleri

### RC-TEST-A11Y-001 — Increment Button Screen Reader Label

**Given** counter detail render edilir  
**Then** increment button anlamlı accessibility label içerir.

### RC-TEST-A11Y-002 — Decrement Button Screen Reader Label

**Given** counter detail render edilir  
**Then** decrement button anlamlı accessibility label içerir.

### RC-TEST-A11Y-003 — Counter Value Okunabilir

**Given** currentValue `10`  
**When** screen reader aktif  
**Then** counter value okunabilir olmalıdır.

### RC-TEST-A11Y-004 — Status Sadece Renkle Anlatılmaz

**Given** sync pending state vardır  
**Then** text veya accessibility label ile ifade edilir.

### RC-TEST-A11Y-005 — Touch Target Uygun

**Given** increment ve decrement butonları render edilir  
**Then** minimum touch target sağlanır.

### RC-TEST-A11Y-006 — Dynamic Text Taşmaz

**Given** büyük font ayarı vardır  
**When** counter detail render edilir  
**Then** ana aksiyonlar kaybolmaz.

---

## 20. Localization Testleri

### RC-TEST-L10N-001 — Counter Type Label Türkçe Görünür

**Given** counterType `row`  
**When** locale Türkçe  
**Then** label Türkçe görünür.

### RC-TEST-L10N-002 — Hata Mesajları Lokalizedir

**Given** empty counter name validation hatası  
**Then** kullanıcıya Türkçe mesaj gösterilir.

### RC-TEST-L10N-003 — Türkçe Counter Name Doğru Saklanır

**Given** counter name `Şiş Örgü Gövde`  
**When** counter oluşturulur  
**Then** Türkçe karakterler bozulmadan saklanır.

### RC-TEST-L10N-004 — Hard-coded User Text Yok

**Given** Row Counter UI taranır  
**Then** user-facing metinler localization dosyalarından gelir.

---

## 21. Migration Testleri

### RC-TEST-MIG-001 — Initial Schema Oluşur

**Given** fresh install  
**When** app başlar  
**Then** `row_counters` ve `counter_operations` yapıları oluşur.

### RC-TEST-MIG-002 — Eski Counter Kaydı Okunur

**Given** eski schema fixture vardır  
**When** migration çalışır  
**Then** counter kaydı okunabilir kalır.

### RC-TEST-MIG-003 — SyncStatus Default Atanır

**Given** eski kayıtta syncStatus yoktur  
**When** migration çalışır  
**Then** güvenli default atanır.

### RC-TEST-MIG-004 — Migration Idempotent

**Given** migration bir kez çalışmıştır  
**When** tekrar çalıştırılır  
**Then** veri bozulmaz.

---

## 22. Acceptance Testleri

### RC-TEST-ACC-001 — Kullanıcı Project İçinden İlk Counter'ını Oluşturur

**Given** kullanıcı project detail ekranındadır  
**When** kullanıcı Add Counter ile geçerli bilgiler girer  
**Then** counter oluşturulur  
**And** project detail içinde görünür.

### RC-TEST-ACC-002 — Kullanıcı Counter'ı Artırır

**Given** counter currentValue `10`  
**When** kullanıcı increment butonuna basar  
**Then** value `11` olur  
**And** işlem lokal saklanır.

### RC-TEST-ACC-003 — Kullanıcı Yanlış Artırmayı Düzeltir

**Given** kullanıcı increment yapmıştır  
**When** undo seçer  
**Then** value önceki değere döner.

### RC-TEST-ACC-004 — Kullanıcı Target ile Progress Görür

**Given** counter targetValue `100` ve currentValue `30`  
**When** counter detail açılır  
**Then** progress `30%` veya eşdeğer gösterilir.

### RC-TEST-ACC-005 — Kullanıcı Offline Counter Kullanır

**Given** cihaz offline  
**When** kullanıcı counter artırır  
**Then** local value güncellenir  
**And** sync pending olur.

### RC-TEST-ACC-006 — Sync Failure Veri Kaybettirmez

**Given** counter offline güncellenmiştir  
**When** sync başarısız olur  
**Then** local value korunur  
**And** kullanıcı çalışmaya devam eder.

### RC-TEST-ACC-007 — Kullanıcı Counter'ı Siler

**Given** counter vardır  
**When** kullanıcı delete onaylar  
**Then** counter normal listeden çıkar.

### RC-TEST-ACC-008 — Başka Kullanıcının Counter'ına Erişilemez

**Given** user_001 login olmuştur  
**When** user_002 counterId ile detail açmaya çalışır  
**Then** counter data gösterilmez.

---

## 23. Regression Checklist

Her Row Counter değişikliğinden sonra kontrol edilmesi gerekenler:

- [ ] Project içinden counter oluşturma çalışıyor.
- [ ] Counter detail açılıyor.
- [ ] Increment çalışıyor.
- [ ] Decrement çalışıyor.
- [ ] Minimum altına düşmüyor.
- [ ] Step value uygulanıyor.
- [ ] Manual set çalışıyor.
- [ ] Target progress doğru.
- [ ] Reset confirmation çalışıyor.
- [ ] Undo çalışıyor.
- [ ] Soft delete listeden kaldırıyor.
- [ ] Multiple counter listeleniyor.
- [ ] Project detail counter summary güncelleniyor.
- [ ] Offline increment çalışıyor.
- [ ] Sync failure local value kaybettirmiyor.
- [ ] Duplicate operation duplicate increment üretmiyor.
- [ ] Unauthorized access engelleniyor.
- [ ] Analytics PII içermiyor.
- [ ] Accessibility smoke geçiyor.
- [ ] Localization smoke geçiyor.

---

## 24. Release Exit Criteria

Row Counter release'e hazır sayılırsa:

- Tüm Must acceptance testleri geçmeli.
- Increment/decrement unit testleri geçmeli.
- Fast tap atomic testleri geçmeli.
- Operation log testleri geçmeli.
- Offline update testleri geçmeli.
- Sync retry ve idempotency testleri geçmeli.
- Security owner isolation testleri geçmeli.
- Analytics privacy testleri geçmeli.
- Widget ve integration smoke testleri geçmeli.
- Accessibility minimum gereksinimleri karşılanmalı.
- Localization smoke testleri geçmeli.
- Migration testleri geçmeli.
- Release blocker bug kalmamalı.
- Product Owner kabul vermeli.

---

## 25. Codex İçin Test Yazım Talimatı

Codex testleri şu sırayla yazmalıdır:

1. Value object validation testleri.
2. Counter domain calculation testleri.
3. Progress calculator testleri.
4. Use case testleri.
5. Repository owner scope testleri.
6. Atomic fast tap testleri.
7. Operation log testleri.
8. Offline update testleri.
9. Sync retry ve idempotency testleri.
10. Conflict testleri.
11. Security testleri.
12. Analytics privacy testleri.
13. Widget testleri.
14. Integration acceptance testleri.
15. Regression checklist automation.

Testlerde dikkat edilmesi gerekenler:

- Fake clock kullan.
- Fake auth context kullan.
- Fake analytics tracker kullan.
- In-memory local DB veya temp DB kullan.
- Network mock kullan.
- Counter name ve notes assertion içinde analytics payload'da bulunmadığını doğrula.
- Parallel increment testlerinde race condition yakalayacak yapı kur.
- Operation ID duplicate testini mutlaka yaz.
- Owner scope testlerini sadece UI ile değil repository seviyesinde yaz.

---

## 26. Açık Test Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-TEST-OD-001 | Fast tap test yöntemi | Parallel futures + local queue test | Open |
| RC-TEST-OD-002 | Local DB test yaklaşımı | In-memory/temp DB | Open |
| RC-TEST-OD-003 | Remote idempotency testi nerede? | Staging veya mock server | Open |
| RC-TEST-OD-004 | Conflict UI V1 test kapsamı | Basit seçim ekranı | Open |
| RC-TEST-OD-005 | Accessibility automation | Semantics test + manuel QA | Open |
| RC-TEST-OD-006 | Analytics framework | Firebase/PostHog kararına göre | Open |
| RC-TEST-OD-007 | E2E test aracı | Flutter integration_test veya Patrol | Open |

---

## 27. Feature-002 Dosya Seti Durumu

Row Counter için ana doküman seti:

```text
03-features/feature-002-row-counter/
├── overview.md
├── requirements.md
├── flows.md
├── data-model.md
├── analytics.md
├── security.md
├── implementation-notes.md
└── testing.md
```

Bu dosya ile feature-002 için temel dokümantasyon seti tamamlanır.

Sonraki adım:

```text
03-features/feature-003-multi-part-tracking/overview.md
```
