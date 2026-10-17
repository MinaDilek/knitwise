# Multi-Part Tracking — Implementation Notes

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/implementation-notes.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliğinin teknik implementasyon yaklaşımını tanımlar.

---

## 2. Önerilen Mimari

```text
Presentation
↓
Application / State
↓
Domain
↓
Repository
↓
Local Data Source
↓
Sync Engine
↓
Remote Data Source
```

---

## 3. Klasör Yapısı

```text
lib/
└── features/
    └── multi_part_tracking/
        ├── domain/
        │   ├── entities/
        │   │   ├── project_part.dart
        │   │   └── part_counter_link.dart
        │   ├── enums/
        │   │   ├── part_type.dart
        │   │   ├── part_status.dart
        │   │   └── relationship_status.dart
        │   ├── value_objects/
        │   │   ├── part_name.dart
        │   │   └── part_progress.dart
        │   ├── services/
        │   │   ├── part_validation_service.dart
        │   │   ├── part_status_policy.dart
        │   │   └── part_progress_calculator.dart
        │   └── failures/
        │       └── part_failure.dart
        ├── application/
        │   ├── controllers/
        │   │   ├── part_list_controller.dart
        │   │   ├── part_detail_controller.dart
        │   │   └── part_form_controller.dart
        │   └── use_cases/
        │       ├── create_part_use_case.dart
        │       ├── update_part_use_case.dart
        │       ├── change_part_status_use_case.dart
        │       ├── reorder_parts_use_case.dart
        │       ├── soft_delete_part_use_case.dart
        │       └── link_counter_to_part_use_case.dart
        ├── data/
        │   ├── repositories/
        │   ├── datasources/
        │   ├── models/
        │   └── mappers/
        ├── presentation/
        │   ├── screens/
        │   └── widgets/
        └── analytics/
```

---

## 4. Domain Entity

```dart
class ProjectPart {
  final String partId;
  final String ownerId;
  final String projectId;
  final String name;
  final PartType partType;
  final PartStatus partStatus;
  final int? progressPercent;
  final int partOrder;
  final String? notes;
  final DateTime createdAt;
  final DateTime updatedAt;
  final DateTime? completedAt;
  final DateTime? deletedAt;
  final SyncStatus syncStatus;
  final int localVersion;
  final int? remoteVersion;
  final int schemaVersion;
  final bool createdOffline;
  final DateTime? lastSyncedAt;

  const ProjectPart({
    required this.partId,
    required this.ownerId,
    required this.projectId,
    required this.name,
    required this.partType,
    required this.partStatus,
    required this.partOrder,
    required this.createdAt,
    required this.updatedAt,
    required this.syncStatus,
    required this.localVersion,
    required this.schemaVersion,
    required this.createdOffline,
    this.progressPercent,
    this.notes,
    this.completedAt,
    this.deletedAt,
    this.remoteVersion,
    this.lastSyncedAt,
  });

  bool get isDeleted => deletedAt != null;
  bool get isCompleted => partStatus == PartStatus.completed;
}
```

---

## 5. Enumlar

```dart
enum PartStatus {
  notStarted('not_started'),
  active('active'),
  paused('paused'),
  completed('completed'),
  blocked('blocked');

  const PartStatus(this.value);
  final String value;
}
```

`PartType` enum değerleri `data-model.md` ile uyumlu olmalıdır.

---

## 6. Value Object

### PartName

- Trim edilir.
- Boş olamaz.
- Max 80 karakter.

### PartProgress

- Null olabilir.
- Null değilse 0-100 arasında olmalıdır.

---

## 7. Use Case’ler

### CreatePartUseCase

1. Input validate et.
2. Project ownership kontrol et.
3. `partOrder` belirle.
4. Part entity oluştur.
5. Local save.
6. Sync operation.
7. Analytics event.

### UpdatePartUseCase

- Owner scope ile part getir.
- Deleted kontrolü.
- Alan validasyonları.
- Local save.
- Sync pending.

### ChangePartStatusUseCase

- Status transition policy çalıştır.
- Completed ise `completedAt` set et.
- Reopen ise policy’ye göre `completedAt` yönet.
- Project progress recalculation tetikle.

### ReorderPartsUseCase

- Tüm partlar aynı owner/project içinde mi kontrol et.
- Transaction içinde `partOrder` güncelle.
- Sync operationları oluştur.
- UI state güncelle.

### LinkCounterToPartUseCase

- Part owner/project doğrula.
- Counter owner/project doğrula.
- Link oluştur.
- Broken relationship state engelle.

---

## 8. Repository Interface

```dart
abstract class PartRepository {
  Future<ProjectPart?> getById({
    required String ownerId,
    required String partId,
    bool includeDeleted = false,
  });

  Future<List<ProjectPart>> listByProject({
    required String ownerId,
    required String projectId,
  });

  Future<void> create(ProjectPart part);

  Future<void> update(ProjectPart part);

  Future<void> reorder({
    required String ownerId,
    required String projectId,
    required List<ProjectPartOrderUpdate> updates,
  });

  Future<void> softDelete({
    required String ownerId,
    required String partId,
    required DateTime deletedAt,
  });
}
```

---

## 9. Local Data Source

Transaction gereken işlemler:

- Create part + sync operation
- Status change + progress recalculation trigger
- Reorder multiple parts
- Soft delete
- Link/unlink counter

---

## 10. Project Progress Calculator

Partlar varsa project progress için önerilen yaklaşım:

```text
average(valid part progress)
```

Eğer part progress yok ama status completed ise:

```text
completed = 100
not_started = 0
active/paused = manual veya counter value
```

---

## 11. Riverpod State

Önerilen providerlar:

```dart
partListControllerProvider(projectId)
partDetailControllerProvider(partId)
partFormControllerProvider
```

State modelleri:

- `PartListState`
- `PartDetailState`
- `PartFormState`

---

## 12. UI Notları

Project Detail Parts Section:

- Empty state
- Part cards
- Add Part
- Progress summary
- Completed count

Part Card:

- Name
- Type label
- Status badge
- Progress
- Linked counter indicator

Part Detail:

- Status
- Progress
- Linked counters
- Notes
- Actions

---

## 13. Analytics Implementasyonu

- Eventler constants ile tanımlanmalı.
- Part name ve notes gönderilmemeli.
- Progress bucket kullanılmalı.
- Relationship eventleri counter/project ID hash kullanmalı.

---

## 14. Error Handling

Failure kodları:

```text
part_not_found
unauthorized
empty_part_name
part_name_too_long
invalid_part_type
invalid_part_status
invalid_progress
local_save_failed
sync_failed
conflict_detected
counter_not_found
```

---

## 15. Performance

- Project detail 50 part ile performanslı çalışmalı.
- Reorder debounce edilmeli.
- Part card gereksiz rebuild olmamalı.
- Linked counter data lazy load edilebilir.

---

## 16. Codex Talimatı

1. Enum ve entity oluştur.
2. Validation service yaz.
3. Repository interface oluştur.
4. Local data source ekle.
5. Create/update/status/delete use case yaz.
6. Reorder transaction uygula.
7. Counter link repository oluştur.
8. Project progress calculator entegrasyonu yap.
9. Riverpod controllerları ekle.
10. UI componentleri bağla.
11. Analytics privacy testlerini yaz.
12. Security owner scope testlerini yaz.

---

## 17. Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/testing.md
```
