# Row Counter — Implementation Notes

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/implementation-notes.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliğinin teknik implementasyon yaklaşımını tanımlar.

Row Counter, kullanıcı etkileşimi açısından çok sık kullanılan ve hızlı tepki vermesi gereken bir özelliktir. Bu nedenle implementasyonda yalnızca CRUD mantığı yeterli değildir. Aşağıdaki konular bilinçli şekilde tasarlanmalıdır:

- Atomic increment / decrement
- Offline-first local update
- Operation log
- Undo desteği
- Sync retry
- Duplicate operation engelleme
- Conflict handling
- Project ve part ilişki güvenliği
- Analytics event spam önleme
- UI performansı
- Test edilebilir domain logic

Bu dosya Codex veya geliştirici ekip için doğrudan uygulama rehberi olarak hazırlanmıştır.

---

## 2. Teknik Hedefler

Row Counter implementasyonu şu hedefleri karşılamalıdır:

1. Increment ve decrement işlemleri kullanıcıya anlık hissettirilmelidir.
2. Counter değeri remote sync beklemeden lokal güncellenmelidir.
3. Hızlı çoklu tap durumunda hiçbir tap kaybolmamalıdır.
4. Lokal kayıt başarısızsa işlem başarılı sayılmamalıdır.
5. Her değer değişikliği operation log ile izlenebilmelidir.
6. Undo için son operation güvenli şekilde saklanmalıdır.
7. Sync retry duplicate increment üretmemelidir.
8. Counter yalnızca owner tarafından okunup değiştirilebilmelidir.
9. Counter name ve notes analytics/log sistemlerine gönderilmemelidir.
10. Feature, Project Management ve Multi-Part Tracking ile uyumlu olmalıdır.

---

## 3. Önerilen Mimari

Row Counter için önerilen katman yapısı:

```text
Presentation Layer
↓
Application / State Layer
↓
Domain Layer
↓
Repository Layer
↓
Local Data Source
↓
Sync Engine
↓
Remote Data Source
```

### 3.1 Katman Sorumlulukları

| Katman | Sorumluluk |
|---|---|
| Presentation | Counter ekranları, butonlar, form, dialog |
| Application / State | Riverpod controller, state, optimistic UI |
| Domain | Counter hesaplama, validation, progress, undo |
| Repository | Counter ve operation veri erişimi |
| Local Data Source | Atomic local update, transaction, query |
| Sync Engine | Pending operation sync, retry, conflict |
| Remote Data Source | Backend/Supabase işlemleri |
| Analytics | Aggregate usage tracking ve privacy-safe events |

---

## 4. Önerilen Klasör Yapısı

```text
lib/
└── features/
    └── row_counter/
        ├── domain/
        │   ├── entities/
        │   │   ├── row_counter.dart
        │   │   ├── counter_operation.dart
        │   │   └── counter_conflict.dart
        │   ├── enums/
        │   │   ├── counter_type.dart
        │   │   ├── counter_operation_type.dart
        │   │   ├── counter_progress_state.dart
        │   │   └── sync_status.dart
        │   ├── value_objects/
        │   │   ├── counter_name.dart
        │   │   ├── counter_value.dart
        │   │   ├── counter_step.dart
        │   │   └── counter_target.dart
        │   ├── services/
        │   │   ├── counter_domain_service.dart
        │   │   ├── counter_validation_service.dart
        │   │   ├── counter_progress_calculator.dart
        │   │   └── counter_undo_service.dart
        │   └── failures/
        │       └── counter_failure.dart
        ├── application/
        │   ├── controllers/
        │   │   ├── counter_detail_controller.dart
        │   │   ├── counter_list_controller.dart
        │   │   ├── counter_form_controller.dart
        │   │   └── counter_usage_session_controller.dart
        │   ├── state/
        │   │   ├── counter_detail_state.dart
        │   │   ├── counter_list_state.dart
        │   │   └── counter_form_state.dart
        │   └── use_cases/
        │       ├── create_counter_use_case.dart
        │       ├── increment_counter_use_case.dart
        │       ├── decrement_counter_use_case.dart
        │       ├── manual_set_counter_use_case.dart
        │       ├── reset_counter_use_case.dart
        │       ├── undo_counter_operation_use_case.dart
        │       ├── update_counter_metadata_use_case.dart
        │       └── soft_delete_counter_use_case.dart
        ├── data/
        │   ├── repositories/
        │   │   ├── counter_repository_impl.dart
        │   │   ├── counter_operation_repository_impl.dart
        │   │   └── counter_sync_repository_impl.dart
        │   ├── datasources/
        │   │   ├── counter_local_data_source.dart
        │   │   ├── counter_remote_data_source.dart
        │   │   └── counter_sync_data_source.dart
        │   ├── models/
        │   │   ├── row_counter_model.dart
        │   │   ├── counter_operation_model.dart
        │   │   └── counter_conflict_model.dart
        │   └── mappers/
        │       ├── counter_mapper.dart
        │       └── counter_operation_mapper.dart
        ├── presentation/
        │   ├── screens/
        │   │   ├── counter_detail_screen.dart
        │   │   ├── counter_list_screen.dart
        │   │   ├── create_counter_screen.dart
        │   │   └── edit_counter_screen.dart
        │   ├── widgets/
        │   │   ├── counter_value_display.dart
        │   │   ├── counter_increment_button.dart
        │   │   ├── counter_decrement_button.dart
        │   │   ├── counter_progress_indicator.dart
        │   │   ├── counter_card.dart
        │   │   ├── counter_empty_state.dart
        │   │   └── counter_sync_indicator.dart
        │   └── routes/
        │       └── counter_routes.dart
        └── analytics/
            ├── counter_analytics_events.dart
            ├── counter_analytics_tracker.dart
            └── counter_usage_aggregator.dart
```

---

## 5. Domain Entity Tasarımı

### 5.1 RowCounter Entity

```dart
class RowCounter {
  final String counterId;
  final String ownerId;
  final String? projectId;
  final String? partId;
  final String name;
  final CounterType counterType;
  final int currentValue;
  final int initialValue;
  final int stepValue;
  final int? targetValue;
  final bool isPrimary;
  final String? notes;
  final DateTime createdAt;
  final DateTime updatedAt;
  final DateTime? deletedAt;
  final DateTime? lastIncrementedAt;
  final DateTime? lastDecrementedAt;
  final DateTime? lastResetAt;
  final SyncStatus syncStatus;
  final int localVersion;
  final int? remoteVersion;
  final int schemaVersion;
  final bool createdOffline;
  final DateTime? lastSyncedAt;

  const RowCounter({
    required this.counterId,
    required this.ownerId,
    required this.name,
    required this.counterType,
    required this.currentValue,
    required this.initialValue,
    required this.stepValue,
    required this.isPrimary,
    required this.createdAt,
    required this.updatedAt,
    required this.syncStatus,
    required this.localVersion,
    required this.schemaVersion,
    required this.createdOffline,
    this.projectId,
    this.partId,
    this.targetValue,
    this.notes,
    this.deletedAt,
    this.lastIncrementedAt,
    this.lastDecrementedAt,
    this.lastResetAt,
    this.remoteVersion,
    this.lastSyncedAt,
  });

  bool get isDeleted => deletedAt != null;
  bool get hasTarget => targetValue != null;
  bool get hasProject => projectId != null;
  bool get hasPart => partId != null;
}
```

### 5.2 CounterOperation Entity

```dart
class CounterOperation {
  final String operationId;
  final String counterId;
  final String ownerId;
  final String? projectId;
  final CounterOperationType operationType;
  final int? valueBefore;
  final int? valueAfter;
  final int? delta;
  final Map<String, dynamic>? metadataPatch;
  final bool isUndoable;
  final String? undoForOperationId;
  final DateTime createdAt;
  final DateTime? syncedAt;
  final SyncStatus syncStatus;
  final int attemptCount;
  final DateTime? lastAttemptAt;
  final DateTime? nextRetryAt;
  final int operationOrder;

  const CounterOperation({
    required this.operationId,
    required this.counterId,
    required this.ownerId,
    required this.operationType,
    required this.isUndoable,
    required this.createdAt,
    required this.syncStatus,
    required this.attemptCount,
    required this.operationOrder,
    this.projectId,
    this.valueBefore,
    this.valueAfter,
    this.delta,
    this.metadataPatch,
    this.undoForOperationId,
    this.syncedAt,
    this.lastAttemptAt,
    this.nextRetryAt,
  });
}
```

---

## 6. Enum Implementasyonu

### 6.1 CounterType

```dart
enum CounterType {
  row('row'),
  round('round'),
  repeat('repeat'),
  increase('increase'),
  decrease('decrease'),
  custom('custom');

  const CounterType(this.value);
  final String value;
}
```

### 6.2 CounterOperationType

```dart
enum CounterOperationType {
  increment('increment'),
  decrement('decrement'),
  manualSet('manual_set'),
  reset('reset'),
  undo('undo'),
  create('create'),
  updateMetadata('update_metadata'),
  softDelete('soft_delete'),
  recover('recover');

  const CounterOperationType(this.value);
  final String value;
}
```

### 6.3 CounterProgressState

```dart
enum CounterProgressState {
  none('none'),
  inProgress('in_progress'),
  reached('reached'),
  exceeded('exceeded'),
  invalidTarget('invalid_target');

  const CounterProgressState(this.value);
  final String value;
}
```

---

## 7. Value Object Tasarımı

### 7.1 CounterName

```dart
class CounterName {
  static const int maxLength = 80;

  final String value;

  CounterName._(this.value);

  factory CounterName.create(String input) {
    final trimmed = input.trim();

    if (trimmed.isEmpty) {
      throw CounterValidationException.emptyName();
    }

    if (trimmed.length > maxLength) {
      throw CounterValidationException.nameTooLong();
    }

    return CounterName._(trimmed);
  }
}
```

### 7.2 CounterStep

```dart
class CounterStep {
  static const int maxValue = 100;

  final int value;

  CounterStep(this.value) {
    if (value < 1) {
      throw CounterValidationException.invalidStep();
    }

    if (value > maxValue) {
      throw CounterValidationException.stepTooLarge();
    }
  }
}
```

### 7.3 CounterValue

```dart
class CounterValue {
  final int value;

  CounterValue(this.value) {
    if (value < 0) {
      throw CounterValidationException.invalidValue();
    }
  }
}
```

### 7.4 CounterTarget

```dart
class CounterTarget {
  final int value;

  CounterTarget(this.value) {
    if (value < 1) {
      throw CounterValidationException.invalidTarget();
    }
  }
}
```

---

## 8. Use Case Tasarımı

### 8.1 CreateCounterUseCase

Sorumluluklar:

1. Input validate et.
2. Project ownership kontrol et.
3. Part verildiyse relationship doğrula.
4. Counter ID üret.
5. Default değerleri ata.
6. Local counter kaydet.
7. `create` operation oluştur.
8. Sync status ayarla.
9. Analytics event gönder.
10. Oluşan counter'ı döndür.

Pseudo akış:

```text
validate input
authorize project
authorize part if exists
generate counterId
build RowCounter
save local counter
insert create operation
track analytics
return counter
```

---

### 8.2 IncrementCounterUseCase

Sorumluluklar:

1. Owner scope ile counter getir.
2. Deleted state kontrol et.
3. Yeni value hesapla.
4. Local transaction başlat.
5. Counter value güncelle.
6. Increment operation insert et.
7. Sync status pending yap.
8. Transaction commit et.
9. Usage aggregator güncelle.
10. Undo state döndür.

Pseudo akış:

```text
get counter by ownerId + counterId
ensure not deleted
newValue = currentValue + stepValue
begin transaction
update counter currentValue
insert increment operation
commit
return updated counter + undo operation reference
```

---

### 8.3 DecrementCounterUseCase

Increment ile benzerdir, ancak minimum değer kontrolü yapar.

```text
newValue = currentValue - stepValue
if newValue < 0 → return minimumBlocked failure
```

---

### 8.4 ManualSetCounterUseCase

Sorumluluklar:

- Yeni value integer mı kontrol et.
- Minimum değer kontrol et.
- Local transaction içinde update ve operation insert yap.
- Operation type `manual_set` olmalıdır.

---

### 8.5 ResetCounterUseCase

Sorumluluklar:

- Reset confirmation UI tarafından alınmış olmalıdır.
- `currentValue = initialValue`
- Operation type `reset`
- `valueBefore` ve `valueAfter` undo için saklanmalıdır.

---

### 8.6 UndoCounterOperationUseCase

Sorumluluklar:

- Son undoable operation'ı bul.
- Undo süresi geçerli mi kontrol et, UI tarafında da yapılabilir.
- Counter current value ile operation valueAfter uyumlu mu kontrol et.
- `valueBefore` değerine geri dön.
- Undo operation insert et.
- Sync operation oluştur.

---

### 8.7 SoftDeleteCounterUseCase

Sorumluluklar:

- Owner scope ile counter getir.
- `deletedAt` set et.
- Normal listelerden çıkar.
- `soft_delete` operation insert et.
- Analytics delete confirmed event gönder.

---

## 9. Atomic Increment Yaklaşımı

### 9.1 Neden Atomic Olmalı?

Hızlı tap durumunda şu hata oluşabilir:

```text
currentValue = 10
tap1 reads 10
tap2 reads 10
tap1 writes 11
tap2 writes 11
expected 12, actual 11
```

Bu hata kabul edilemez.

### 9.2 Çözüm Seçenekleri

| Yöntem | Açıklama |
|---|---|
| DB transaction | Read/update/operation insert aynı transaction |
| Atomic SQL update | `current_value = current_value + step_value` |
| Local sequential queue | Tap'leri sırayla işler |
| Mutex/lock | Aynı counter için eşzamanlı işlemi engeller |
| Optimistic UI + reconciliation | UI hızlı, DB sonucu ile düzeltme |

### 9.3 V1 Önerisi

V1 için önerilen yaklaşım:

```text
UI optimistic update
+ per-counter sequential operation queue
+ local DB transaction
+ operation log insert
```

Bu sayede hem kullanıcı hızlı feedback alır hem de veri güvenliği korunur.

---

## 10. Repository Tasarımı

### 10.1 CounterRepository Interface

```dart
abstract class CounterRepository {
  Future<RowCounter?> getById({
    required String ownerId,
    required String counterId,
    bool includeDeleted = false,
  });

  Future<List<RowCounter>> listByProject({
    required String ownerId,
    required String projectId,
    bool includeDeleted = false,
  });

  Future<void> create(RowCounter counter);

  Future<RowCounter> updateValue({
    required String ownerId,
    required String counterId,
    required int newValue,
    required SyncStatus syncStatus,
    required DateTime updatedAt,
  });

  Future<void> updateMetadata(RowCounter counter);

  Future<void> softDelete({
    required String ownerId,
    required String counterId,
    required DateTime deletedAt,
  });
}
```

### 10.2 CounterOperationRepository Interface

```dart
abstract class CounterOperationRepository {
  Future<void> insert(CounterOperation operation);

  Future<CounterOperation?> getLastUndoableOperation({
    required String ownerId,
    required String counterId,
  });

  Future<List<CounterOperation>> listPendingOperations({
    required String ownerId,
    int limit = 100,
  });

  Future<void> markSynced({
    required String ownerId,
    required String operationId,
    required DateTime syncedAt,
  });

  Future<void> markFailed({
    required String ownerId,
    required String operationId,
    required String errorCode,
  });
}
```

---

## 11. Local Data Source

### 11.1 Transaction Gereksinimi

Increment/decrement sırasında şu işlemler aynı transaction içinde olmalıdır:

```text
update row_counters currentValue
insert counter_operations
update syncStatus
increase localVersion
```

### 11.2 Local Query Gereksinimleri

Desteklenmesi gereken query'ler:

- Project'e bağlı active counterlar
- Counter by ID
- Primary counter
- Pending operations
- Failed operations
- Last undoable operation
- Deleted counter recovery, ileride
- Part'a bağlı counterlar

### 11.3 Local Persistence Teknolojisi

Project Management ile aynı teknoloji kullanılmalıdır.

Adaylar:

- Drift
- Isar
- SQLite wrapper

Counter için özellikle atomic update ve transaction desteği önemlidir.

---

## 12. Remote Data Source

### 12.1 V1 Remote Stratejisi

V1 için iki strateji vardır:

#### Strateji A — Latest Value Upsert

Remote'a son counter value gönderilir.

Avantaj:

- Uygulaması kolaydır.
- V1 için hızlıdır.

Dezavantaj:

- Multi-device offline conflict riski daha fazladır.

#### Strateji B — Operation-Based Apply

Remote operationları sırayla uygular.

Avantaj:

- Duplicate increment engellemek daha doğrudur.
- Multi-device merge daha güvenlidir.

Dezavantaj:

- Backend daha karmaşık olur.

V1 önerisi:

```text
Local operation log zorunlu.
Remote için başlangıçta latest value upsert kabul edilebilir.
Ancak operationId idempotency altyapısı şimdiden kurulmalıdır.
```

---

## 13. Sync Engine

### 13.1 Pending Operation Sync

Sync engine şu adımları izlemelidir:

1. Pending counter operationları al.
2. Operationları owner ve created/order sırasına göre grupla.
3. Connectivity ve auth kontrol et.
4. Remote'a gönder.
5. Başarılı operationları synced yap.
6. Counter `syncStatus` değerini güncelle.
7. Başarısız operationlar için retry metadata güncelle.
8. Conflict varsa `conflict` status üret.

### 13.2 Retry Policy

Öneri:

```text
attempt 1: immediate or short delay
attempt 2: 30 sec
attempt 3: 2 min
attempt 4: 10 min
attempt 5+: manual or background retry
```

### 13.3 Operation Compaction

Aynı counter için çok sayıda increment/decrement operation varsa sync öncesi delta compaction yapılabilir.

Örnek:

```text
+1 +1 +1 -1 = delta +2
```

Ancak reset veya manual_set varsa compaction dikkatli yapılmalıdır.

Güvenli compaction kuralları:

- Sadece ardışık increment/decrement operationları sıkıştır.
- Reset/manual_set operationları boundary kabul et.
- Undo operation varsa compaction yapmadan önce etkisini hesapla.
- Debug için original operation log lokal saklanabilir.

---

## 14. Conflict Handling

### 14.1 Conflict Tespit

Conflict şu durumlarda tespit edilebilir:

- Remote version local base version'dan farklı.
- Remote deleted, local pending operation var.
- Operation order remote ile uyumsuz.
- Metadata iki yerde değişmiş.
- Manual set ve increment aynı zaman aralığında çakışmış.

### 14.2 V1 Conflict UI

V1 için basit conflict ekranı:

- Local value kullan
- Remote value kullan
- Manuel value gir

### 14.3 Conflict Resolution Use Case

```text
get conflict
user selects resolution
apply selected value
insert corrective operation
mark conflict resolved
sync corrective operation
```

### 14.4 Otomatik Merge

Sadece güvenli olduğunda yapılmalıdır.

Örnek güvenli durum:

- İki cihaz sadece increment operation üretmişse
- Operation ID'leri benzersizse
- Base value biliniyorsa

Riskli durum:

- Bir cihaz reset, diğeri increment
- Bir cihaz manual set, diğeri decrement
- Remote deleted, local increment

---

## 15. Riverpod State Management

### 15.1 Provider Önerileri

```dart
final counterDetailControllerProvider =
    AsyncNotifierProviderFamily<CounterDetailController, CounterDetailState, String>(
  CounterDetailController.new,
);

final projectCountersProvider =
    AsyncNotifierProviderFamily<CounterListController, CounterListState, String>(
  CounterListController.new,
);
```

### 15.2 CounterDetailState

```dart
class CounterDetailState {
  final RowCounter counter;
  final CounterProgressViewData progress;
  final bool isSaving;
  final bool hasPendingSync;
  final bool hasConflict;
  final CounterOperation? lastUndoableOperation;
  final CounterFailure? failure;
}
```

### 15.3 Optimistic UI

Increment için:

1. UI değeri anında artırır.
2. Use case lokal save yapar.
3. Başarılıysa state korunur.
4. Başarısızsa UI rollback veya error state yapar.

Optimistic UI, local transaction sonucu ile reconcile edilmelidir.

---

## 16. UI Implementasyon Notları

### 16.1 Counter Detail Screen

Göstermesi gerekenler:

- Büyük counter value
- Counter name
- Counter type
- Increment button
- Decrement button
- Target progress
- Step value
- Sync indicator
- Edit/reset/delete actions

### 16.2 Increment Button

- Büyük olmalıdır.
- Tek dokunuşla çalışmalıdır.
- Confirmation istememelidir.
- Çoklu tap'e dayanıklı olmalıdır.
- Accessibility label içermelidir.

### 16.3 Decrement Button

- Increment kadar baskın olmak zorunda değildir.
- Minimum değerde disabled veya safe feedback verebilir.
- Yanlış tap düzeltme için erişilebilir olmalıdır.

### 16.4 Undo Snackbar

- Increment/decrement/reset sonrası gösterilebilir.
- Çok uzun süre kalmamalıdır.
- Son operation için geçerli olmalıdır.

### 16.5 Reset Dialog

- Confirmation zorunlu olmalıdır.
- Reset'in initial value'ya döneceğini açıkça söylemelidir.
- Undo varsa bunu belirtebilir.

---

## 17. Validation Implementasyonu

### 17.1 CounterValidationService

```dart
class CounterValidationService {
  void validateName(String name) {}
  void validateType(CounterType type) {}
  void validateCurrentValue(int value) {}
  void validateInitialValue(int value) {}
  void validateStepValue(int value) {}
  void validateTargetValue(int? value) {}
  void validateNotes(String? notes) {}
}
```

### 17.2 Error Codes

```text
empty_counter_name
counter_name_too_long
invalid_counter_type
invalid_counter_value
invalid_initial_value
invalid_step_value
step_value_too_large
invalid_target_value
notes_too_long
counter_not_found
unauthorized
counter_deleted
local_save_failed
sync_failed
conflict_detected
```

UI bu kodları lokalize mesajlara çevirmelidir.

---

## 18. Analytics Implementasyonu

### 18.1 CounterAnalyticsTracker

```dart
class CounterAnalyticsTracker {
  void trackCreateStarted(CounterCreateStartedEvent event) {}
  void trackCreateCompleted(CounterCreateCompletedEvent event) {}
  void trackUsageSummary(CounterUsageSummaryEvent event) {}
  void trackSyncFailed(CounterSyncFailedEvent event) {}
}
```

### 18.2 Usage Aggregator

Her increment için event göndermek yerine session aggregate tutulmalıdır.

```dart
class CounterUsageAggregator {
  void recordIncrement(String counterId) {}
  void recordDecrement(String counterId) {}
  void recordUndo(String counterId) {}
  void recordReset(String counterId) {}
  CounterUsageSummary buildSummary(String counterId) {}
  void flush(String counterId) {}
}
```

### 18.3 Privacy Filter

Payload içinde şunlar olmamalıdır:

- Counter name
- Notes
- Project name
- Part name
- Pattern text
- Raw user input

---

## 19. Error Handling

### 19.1 Failure Model

```dart
sealed class CounterFailure {
  const CounterFailure();

  factory CounterFailure.notFound() = CounterNotFoundFailure;
  factory CounterFailure.unauthorized() = CounterUnauthorizedFailure;
  factory CounterFailure.validation(String code) = CounterValidationFailure;
  factory CounterFailure.minimumValueBlocked() = CounterMinimumValueFailure;
  factory CounterFailure.localSaveFailed() = CounterLocalSaveFailure;
  factory CounterFailure.syncFailed() = CounterSyncFailure;
  factory CounterFailure.conflictDetected() = CounterConflictFailure;
}
```

### 19.2 UI Mapping

- Minimum value blocked → hafif feedback
- Local save failed → snackbar veya inline error
- Sync failed → non-blocking sync indicator
- Unauthorized → generic not-found/access denied
- Conflict → conflict resolution screen

---

## 20. Routing

Önerilen route yapısı:

```text
/projects/:projectId/counters
/projects/:projectId/counters/new
/projects/:projectId/counters/:counterId
/projects/:projectId/counters/:counterId/edit
```

Bağımsız counter desteklenirse:

```text
/counters
/counters/:counterId
```

### 20.1 Route Guard

Counter detail açılmadan önce:

- Auth var mı?
- Counter var mı?
- Owner doğru mu?
- Deleted değil mi?
- Project deleted mi?
- Part relationship güvenli mi?

kontrol edilmelidir.

---

## 21. Performance Notları

### 21.1 Increment Performansı

Increment action path içinde ağır işler yapılmamalıdır.

Kaçınılması gerekenler:

- Remote request bekleme
- Büyük analytics payload oluşturma
- Full project reload
- Full counter list reload
- Gereksiz image veya pattern load

### 21.2 State Update

Counter detail state minimal güncellenmelidir.

Project detail counter section için sadece ilgili counter summary güncellenebilir.

### 21.3 Batch Sync

Çok sayıda operation için sync batching uygulanmalıdır.

---

## 22. Migration Notları

### 22.1 V1 Initial Schema

Oluşturulacak yapılar:

- `row_counters`
- `counter_operations`
- indexler
- sync metadata
- soft delete alanları

### 22.2 Gelecek Genişlemeler

Mimari şu özelliklere hazır olmalıdır:

- Watch/widget increment
- Smart pattern-linked counter
- Counter templates
- Counter groups
- Counter history UI
- Advanced repeat logic
- Voice increment

---

## 23. Testing Notları

Detaylı test kapsamı `testing.md` içinde tanımlanacaktır.

Bu dosya açısından minimum test hedefleri:

- CounterName validation
- Step validation
- Increment calculation
- Decrement minimum block
- Atomic fast tap test
- Operation log insert test
- Undo test
- Reset test
- Soft delete test
- Offline increment test
- Sync retry duplicate prevention test
- Conflict test
- Analytics privacy test
- Unauthorized access test

---

## 24. Release Checklist

- [ ] RowCounter entity hazır.
- [ ] CounterOperation entity hazır.
- [ ] Enum mapping test edildi.
- [ ] Increment atomic çalışıyor.
- [ ] Decrement minimum altına düşmüyor.
- [ ] Manual set validation çalışıyor.
- [ ] Reset confirmation çalışıyor.
- [ ] Undo çalışıyor veya bilinçli ertelenmiş.
- [ ] Operation log her value change için oluşuyor.
- [ ] Offline increment/decrement çalışıyor.
- [ ] Sync retry duplicate increment üretmiyor.
- [ ] Counter project detail içinde görünüyor.
- [ ] Multiple counter destekleniyor.
- [ ] Target progress hesaplanıyor.
- [ ] Deleted counter normal listede görünmüyor.
- [ ] Owner isolation test edildi.
- [ ] Analytics PII içermiyor.
- [ ] Accessibility minimum kontrolleri geçti.
- [ ] Localization tamamlandı.

---

## 25. Codex İçin Net Uygulama Talimatı

Codex bu feature'ı geliştirirken:

1. Önce enumları ve domain entity'leri oluştur.
2. Value object validation sınıflarını yaz.
3. CounterDomainService ve CounterProgressCalculator oluştur.
4. Repository interface'lerini tanımla.
5. Local data source transaction mantığını uygula.
6. Create/increment/decrement/manual set/reset/undo use case'lerini yaz.
7. Operation log'u her değer değişikliğine bağla.
8. Riverpod controller ve state modellerini yaz.
9. Counter Detail UI'ı bağla.
10. Project Detail counter section entegrasyonunu yap.
11. Sync queue ve retry mantığını bağla.
12. Analytics usage summary aggregator ekle.
13. Security ve privacy testlerini yaz.
14. Fast tap ve offline testlerini çalıştır.

Kaçınılacak hatalar:

- Her tap için remote request beklemek.
- Operation log olmadan value update yapmak.
- Retry sırasında yeni operationId üretmek.
- Scope'suz `counterId` query yapmak.
- Deleted counter'ı listede göstermek.
- Counter name veya notes'u analytics'e göndermek.
- Reset için confirmation göstermemek.
- Increment için confirmation istemek.
- Fast tap durumunda tap kaybetmek.

---

## 26. Açık Teknik Kararlar

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-IMPL-OD-001 | Local DB teknolojisi | Project Management ile aynı | Open |
| RC-IMPL-OD-002 | Remote sync stratejisi | V1 latest value + operationId idempotency | Open |
| RC-IMPL-OD-003 | Operation compaction | Increment/decrement boundary bazlı | Open |
| RC-IMPL-OD-004 | Undo süresi | 3-5 saniye | Open |
| RC-IMPL-OD-005 | Primary counter | Opsiyonel | Open |
| RC-IMPL-OD-006 | Haptic feedback | Mobilde açık olabilir | Open |
| RC-IMPL-OD-007 | Conflict UI | Basit seçim ekranı | Open |
| RC-IMPL-OD-008 | Independent counter | V1 sonrası değerlendirilebilir | Open |

---

## 27. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/testing.md
```

`testing.md` içinde Row Counter için unit test, use case test, repository test, widget test, offline test, sync test, security test, analytics test ve acceptance test senaryoları tanımlanacaktır.
