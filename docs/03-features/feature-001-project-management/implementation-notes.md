
# Proje Yönetimi — Implementation Notes

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/implementation-notes.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliğinin teknik implementasyon yaklaşımını tanımlar.

Bu dosya ürün gereksinimlerinin koda nasıl çevrileceğini açıklar. Codex veya geliştirici ekip bu dosyayı kullanarak feature mimarisini, klasör yapısını, domain servislerini, repository katmanını, state management yaklaşımını, local persistence stratejisini, sync davranışını ve test edilebilir iş kurallarını uygulayabilmelidir.

Bu doküman aşağıdaki dosyalarla birlikte okunmalıdır:

- `overview.md`
- `requirements.md`
- `flows.md`
- `data-model.md`
- `analytics.md`
- `security.md`
- `testing.md`

---

## 2. Teknik Hedefler

Project Management implementasyonu şu hedefleri karşılamalıdır:

1. Kullanıcı lokal olarak hızlı proje oluşturabilmelidir.
2. Proje oluşturma ve düzenleme offline çalışmalıdır.
3. Remote sync başarısızlığı kullanıcı verisini kaybettirmemelidir.
4. İş kuralları UI içine dağılmamalıdır.
5. Proje status transition kuralları merkezi olmalıdır.
6. Project, pattern, yarn, tool, counter ve part ilişkileri gevşek bağlı olmalıdır.
7. Security kontrolleri repository/service seviyesinde uygulanmalıdır.
8. Analytics eventleri typed ve privacy-safe olmalıdır.
9. Kod test edilebilir olmalıdır.
10. Feature gelecekte büyümeye uygun tasarlanmalıdır.

---

## 3. Önerilen Mimari

Knitwise Flutter ile geliştiriliyorsa önerilen mimari:

```text
Presentation Layer
↓
Application / State Layer
↓
Domain Layer
↓
Repository Layer
↓
Local Data Source + Remote Data Source
↓
Sync Engine
```

### 3.1 Katman Sorumlulukları

| Katman | Sorumluluk |
|---|---|
| Presentation | Ekranlar, componentler, formlar, kullanıcı aksiyonları |
| Application / State | Riverpod provider, controller, view model |
| Domain | Entity, value object, business rules, use case |
| Repository | Veri erişim arayüzleri ve koordinasyon |
| Local Data Source | SQLite/Drift/Isar/Hive local persistence |
| Remote Data Source | Supabase/API client |
| Sync Engine | Pending operations, retry, conflict |
| Analytics | Privacy-safe event gönderimi |
| Security | Owner scope, authorization, validation |

---

## 4. Önerilen Klasör Yapısı

Aşağıdaki yapı Codex için önerilen başlangıç yapısıdır.

```text
lib/
└── features/
    └── project_management/
        ├── domain/
        │   ├── entities/
        │   │   ├── project.dart
        │   │   ├── project_pattern_link.dart
        │   │   ├── project_yarn_link.dart
        │   │   ├── project_tool_link.dart
        │   │   ├── project_media.dart
        │   │   └── project_status_history.dart
        │   ├── enums/
        │   │   ├── project_status.dart
        │   │   ├── project_technique.dart
        │   │   ├── project_category.dart
        │   │   ├── sync_status.dart
        │   │   └── relationship_status.dart
        │   ├── value_objects/
        │   │   ├── project_id.dart
        │   │   ├── project_name.dart
        │   │   └── project_progress.dart
        │   ├── services/
        │   │   ├── project_domain_service.dart
        │   │   ├── project_status_policy.dart
        │   │   ├── project_progress_calculator.dart
        │   │   └── project_validation_service.dart
        │   └── failures/
        │       └── project_failure.dart
        ├── application/
        │   ├── controllers/
        │   │   ├── project_list_controller.dart
        │   │   ├── project_detail_controller.dart
        │   │   ├── project_form_controller.dart
        │   │   └── project_status_controller.dart
        │   ├── state/
        │   │   ├── project_list_state.dart
        │   │   ├── project_detail_state.dart
        │   │   └── project_form_state.dart
        │   └── use_cases/
        │       ├── create_project_use_case.dart
        │       ├── update_project_use_case.dart
        │       ├── change_project_status_use_case.dart
        │       ├── archive_project_use_case.dart
        │       ├── restore_project_use_case.dart
        │       ├── soft_delete_project_use_case.dart
        │       └── recover_project_use_case.dart
        ├── data/
        │   ├── repositories/
        │   │   ├── project_repository_impl.dart
        │   │   ├── project_relationship_repository_impl.dart
        │   │   └── project_media_repository_impl.dart
        │   ├── datasources/
        │   │   ├── project_local_data_source.dart
        │   │   ├── project_remote_data_source.dart
        │   │   └── project_sync_data_source.dart
        │   ├── models/
        │   │   ├── project_model.dart
        │   │   ├── project_pattern_link_model.dart
        │   │   ├── project_yarn_link_model.dart
        │   │   ├── project_tool_link_model.dart
        │   │   └── project_media_model.dart
        │   └── mappers/
        │       ├── project_mapper.dart
        │       └── project_relationship_mapper.dart
        ├── presentation/
        │   ├── screens/
        │   │   ├── project_list_screen.dart
        │   │   ├── project_detail_screen.dart
        │   │   ├── create_project_screen.dart
        │   │   └── edit_project_screen.dart
        │   ├── widgets/
        │   │   ├── project_card.dart
        │   │   ├── project_status_badge.dart
        │   │   ├── project_progress_indicator.dart
        │   │   ├── project_empty_state.dart
        │   │   ├── project_limit_reached_view.dart
        │   │   └── broken_relationship_state.dart
        │   └── routes/
        │       └── project_routes.dart
        └── analytics/
            ├── project_analytics_events.dart
            └── project_analytics_tracker.dart
```

Bu yapı birebir zorunlu değildir; ancak separation of concerns korunmalıdır.

---

## 5. Domain Entity Tasarımı

### 5.1 Project Entity

Domain entity, UI veya database modeline doğrudan bağımlı olmamalıdır.

Örnek Dart benzeri model:

```dart
class Project {
  final String projectId;
  final String ownerId;
  final String name;
  final String? description;
  final ProjectStatus status;
  final ProjectTechnique? technique;
  final ProjectCategory? category;
  final String? coverMediaId;
  final DateTime? startDate;
  final DateTime? targetCompletionDate;
  final DateTime? completedAt;
  final DateTime? archivedAt;
  final DateTime? deletedAt;
  final int? manualProgress;
  final String? notes;
  final DateTime createdAt;
  final DateTime updatedAt;
  final SyncStatus syncStatus;
  final int localVersion;
  final int? remoteVersion;
  final int schemaVersion;
  final bool createdOffline;
  final DateTime? lastSyncedAt;

  const Project({
    required this.projectId,
    required this.ownerId,
    required this.name,
    required this.status,
    required this.createdAt,
    required this.updatedAt,
    required this.syncStatus,
    required this.localVersion,
    required this.schemaVersion,
    required this.createdOffline,
    this.description,
    this.technique,
    this.category,
    this.coverMediaId,
    this.startDate,
    this.targetCompletionDate,
    this.completedAt,
    this.archivedAt,
    this.deletedAt,
    this.manualProgress,
    this.notes,
    this.remoteVersion,
    this.lastSyncedAt,
  });
}
```

### 5.2 Entity Kuralları

`Project` entity içinde basit computed property'ler bulunabilir:

```dart
bool get isDraft => status == ProjectStatus.draft;
bool get isActive => status == ProjectStatus.active;
bool get isPaused => status == ProjectStatus.paused;
bool get isCompleted => status == ProjectStatus.completed;
bool get isArchived => status == ProjectStatus.archived;
bool get isDeleted => deletedAt != null;
bool get countsTowardActiveLimit =>
    deletedAt == null &&
    (status == ProjectStatus.active || status == ProjectStatus.paused);
```

Ancak karmaşık iş kuralları entity içine aşırı yüklenmemelidir. Status transition, entitlement ve sync kararları service/use case seviyesinde tutulmalıdır.

---

## 6. Enum Implementasyonu

Enum değerleri database ve remote sync için stabil string karşılığına sahip olmalıdır.

### 6.1 ProjectStatus

```dart
enum ProjectStatus {
  draft('draft'),
  active('active'),
  paused('paused'),
  completed('completed'),
  archived('archived');

  const ProjectStatus(this.value);
  final String value;
}
```

### 6.2 SyncStatus

```dart
enum SyncStatus {
  synced('synced'),
  pending('pending'),
  failed('failed'),
  conflict('conflict');

  const SyncStatus(this.value);
  final String value;
}
```

### 6.3 Enum Mapping

- UI label enum value ile aynı olmamalıdır.
- Lokalizasyon UI katmanında yapılmalıdır.
- Unknown enum value geldiğinde uygulama crash olmamalıdır.
- Remote'dan bilinmeyen status gelirse safe fallback veya migration hatası üretilmelidir.

---

## 7. Value Object Tasarımı

### 7.1 ProjectName

Project name validasyonu merkezi tutulmalıdır.

```dart
class ProjectName {
  static const int maxLength = 120;

  final String value;

  ProjectName._(this.value);

  factory ProjectName.create(String input) {
    final trimmed = input.trim();

    if (trimmed.isEmpty) {
      throw ProjectValidationException.emptyName();
    }

    if (trimmed.length > maxLength) {
      throw ProjectValidationException.nameTooLong();
    }

    return ProjectName._(trimmed);
  }
}
```

### 7.2 ManualProgress

```dart
class ManualProgress {
  final int value;

  ManualProgress(this.value) {
    if (value < 0 || value > 100) {
      throw ProjectValidationException.invalidProgress();
    }
  }
}
```

### 7.3 Neden Value Object?

Value object kullanımı:

- Validasyonu merkezi yapar.
- UI, repository ve domain arasında tutarsızlığı azaltır.
- Test yazmayı kolaylaştırır.
- Codex'in kuralları unutmasını zorlaştırır.

---

## 8. Use Case Tasarımı

Use case'ler tek bir kullanıcı niyetini temsil etmelidir.

### 8.1 CreateProjectUseCase

Sorumluluklar:

1. Input validate et.
2. Entitlement kontrolü yap.
3. Project ID üret.
4. Default alanları ata.
5. Local save yap.
6. Sync operation oluştur.
7. Analytics success/failure event gönder.
8. Oluşan project'i döndür.

Pseudo akış:

```text
validate name
check entitlement if status active/paused
generate projectId
build Project
save locally
enqueue sync operation
track analytics
return Project
```

### 8.2 UpdateProjectUseCase

Sorumluluklar:

- Owner scope ile project getir.
- Deleted state kontrolü yap.
- Archived edit policy kontrolü yap.
- Değişen alanları validate et.
- `updatedAt` ve `localVersion` güncelle.
- Local save yap.
- Sync operation enqueue et.
- Analytics event gönder.

### 8.3 ChangeProjectStatusUseCase

Sorumluluklar:

- Project getir.
- Geçiş geçerli mi kontrol et.
- Gerekirse entitlement kontrolü yap.
- Timestamp alanlarını güncelle.
- Status history kaydı oluştur.
- Local save yap.
- Sync operation enqueue et.

### 8.4 SoftDeleteProjectUseCase

Sorumluluklar:

- Owner scope ile project getir.
- Delete confirmation UI'dan gelmiş olmalı.
- `deletedAt` set et.
- Recovery metadata hesapla.
- Local save yap.
- Sync operation enqueue et.
- Project list state güncelle.

### 8.5 RecoverProjectUseCase

Sorumluluklar:

- Deleted project'i recovery scope ile getir.
- Recovery süresi dolmuş mu kontrol et.
- Restore status belirle.
- Eğer active/paused ise entitlement kontrol et.
- `deletedAt` temizle.
- Local save yap.
- Sync operation enqueue et.

---

## 9. ProjectStatusPolicy

Status geçişleri merkezi policy ile kontrol edilmelidir.

Örnek:

```dart
class ProjectStatusPolicy {
  bool canTransition(ProjectStatus from, ProjectStatus to) {
    return switch ((from, to)) {
      (ProjectStatus.draft, ProjectStatus.active) => true,
      (ProjectStatus.active, ProjectStatus.paused) => true,
      (ProjectStatus.paused, ProjectStatus.active) => true,
      (ProjectStatus.active, ProjectStatus.completed) => true,
      (ProjectStatus.paused, ProjectStatus.completed) => true,
      (ProjectStatus.completed, ProjectStatus.active) => true,
      (ProjectStatus.completed, ProjectStatus.paused) => true,
      (ProjectStatus.active, ProjectStatus.archived) => true,
      (ProjectStatus.paused, ProjectStatus.archived) => true,
      (ProjectStatus.completed, ProjectStatus.archived) => true,
      (ProjectStatus.archived, ProjectStatus.active) => true,
      (ProjectStatus.archived, ProjectStatus.paused) => true,
      _ => false,
    };
  }
}
```

### 9.1 Entitlement Gerektiren Geçişler

Aşağıdaki geçişlerde active project limit kontrolü gerekir:

- `draft` → `active`
- `completed` → `active`
- `archived` → `active`
- `paused` → `active`, eğer paused limit dışı yapılırsa
- deleted recovery → `active`

Mevcut kararımıza göre `paused` da active limit sayımına dahil olduğu için `paused` → `active` geçişi limit sayısını artırmaz. Yine de service seviyesinde policy net tutulmalıdır.

---

## 10. Repository Tasarımı

### 10.1 ProjectRepository Interface

```dart
abstract class ProjectRepository {
  Future<Project?> getById({
    required String ownerId,
    required String projectId,
    bool includeDeleted = false,
  });

  Future<List<Project>> listProjects({
    required String ownerId,
    required ProjectListQuery query,
  });

  Future<void> create(Project project);

  Future<void> update(Project project);

  Future<void> softDelete({
    required String ownerId,
    required String projectId,
    required DateTime deletedAt,
  });

  Future<int> countActiveProjects({
    required String ownerId,
  });
}
```

### 10.2 Scope Kuralı

Repository methodları sadece `projectId` almamalıdır.

Yanlış:

```dart
getProjectById(projectId)
```

Doğru:

```dart
getProjectById(ownerId: ownerId, projectId: projectId)
```

### 10.3 Relationship Repository

```dart
abstract class ProjectRelationshipRepository {
  Future<void> linkPattern(ProjectPatternLink link);
  Future<void> unlinkPattern({
    required String ownerId,
    required String projectId,
    required String patternId,
  });

  Future<void> linkYarn(ProjectYarnLink link);
  Future<void> unlinkYarn({
    required String ownerId,
    required String projectYarnLinkId,
  });

  Future<void> linkTool(ProjectToolLink link);
  Future<void> unlinkTool({
    required String ownerId,
    required String projectToolLinkId,
  });
}
```

### 10.4 Media Repository

```dart
abstract class ProjectMediaRepository {
  Future<ProjectMedia> createCoverImage({
    required String ownerId,
    required String projectId,
    required LocalImageFile image,
  });

  Future<void> removeCoverImage({
    required String ownerId,
    required String projectId,
  });

  Future<void> markUploadFailed({
    required String ownerId,
    required String mediaId,
    required String errorCode,
  });
}
```

---

## 11. Local Data Source

### 11.1 Sorumluluklar

Local data source:

- Project create
- Project update
- Project list query
- Project detail query
- Relationship query
- Soft delete update
- Sync status update
- Pending operation query
- Migration

işlemlerini desteklemelidir.

### 11.2 Local Transaction

Project create sırasında core project ve ilişkiler birlikte oluşturuluyorsa transaction kullanılmalıdır.

Örnek:

```text
begin transaction
insert project
insert pattern link if exists
insert yarn links if exists
insert tool links if exists
insert media metadata if exists
insert sync operations
commit
```

Core project insert başarısız olursa ilişki kayıtları oluşturulmamalıdır.

Optional ilişki insert başarısız olursa davranış product kararına göre:

- Tüm işlem rollback, veya
- Core project korunur ve ilişki hatası gösterilir

V1 önerisi:

```text
Core project kaydı korunur, optional ilişki hatası kullanıcıya non-blocking gösterilir.
```

Ancak transaction tasarımı buna göre bilinçli yapılmalıdır.

---

## 12. Remote Data Source

### 12.1 Sorumluluklar

Remote data source:

- Project create/update
- Project soft delete sync
- Relationship sync
- Media metadata sync
- Status history sync
- Remote fetch
- Conflict response parse

işlemlerini yürütür.

### 12.2 Supabase Kullanımı

Supabase kullanılacaksa:

- `projects` tablosunda RLS aktif olmalıdır.
- `owner_id = auth.uid()` policy uygulanmalıdır.
- Client payload içindeki `ownerId` tek başına güvenilir kabul edilmemelidir.
- Storage bucket private olmalıdır.
- Signed URL üretimi owner check ile yapılmalıdır.

### 12.3 Remote DTO

Remote model domain entity'den ayrı tutulmalıdır.

Neden:

- Remote field isimleri snake_case olabilir.
- Domain model camelCase olabilir.
- Migration ve backward compatibility kolaylaşır.
- API response değişiklikleri domain'i bozmaz.

---

## 13. Sync Engine Yaklaşımı

### 13.1 Local-First Sync

Her create/update/delete işlemi önce lokal kayıt üretir.

Sonra sync queue'ya operation eklenir.

Örnek operation:

```json
{
  "operationId": "op_001",
  "entityType": "project",
  "entityId": "project_001",
  "operationType": "update",
  "attemptCount": 0,
  "status": "pending"
}
```

### 13.2 Operation Types

```text
create
update
soft_delete
recover
archive
restore
media_upload
relationship_create
relationship_remove
```

### 13.3 Retry Policy

Önerilen retry policy:

- İlk hata sonrası kısa bekleme
- Sonraki hatalarda exponential backoff
- Max attempt sayısı
- Kullanıcı manuel retry yapabilir
- Auth hatasında otomatik tekrar durdurulabilir

### 13.4 Idempotency

Remote operation tekrar gönderildiğinde duplicate kayıt oluşmamalıdır.

Bunun için:

- Stable `operationId`
- Stable `projectId`
- Upsert stratejisi
- `localVersion` / `remoteVersion`
- Unique constraint

kullanılmalıdır.

---

## 14. Conflict Handling

### 14.1 Conflict Durumları

Conflict şu durumlarda oluşabilir:

- Aynı proje iki cihazda offline düzenlenmiştir.
- Remote project deleted iken local update vardır.
- Status iki cihazda farklı değiştirilmiştir.
- Notes iki cihazda farklı güncellenmiştir.
- Cover image iki cihazda farklı değiştirilmiştir.

### 14.2 V1 Conflict Stratejisi

V1 için basit strateji:

| Alan | Strateji |
|---|---|
| Metadata | Last write wins |
| Status | Güvenli olan state tercih edilir veya kullanıcı seçimi |
| Notes | Kullanıcı seçimi |
| Relationships | Explicit remove wins veya append-safe |
| Media | Son başarılı upload veya kullanıcı seçimi |

### 14.3 Conflict UI

Conflict UI V1'de basit olabilir:

- Local version kullan
- Remote version kullan
- Daha sonra karar ver

Notes gibi alanlarda manuel merge ileri sürüme bırakılabilir.

### 14.4 Conflict Durumunda Veri Kaybı

Sistem conflict durumunda lokal veriyi sessizce silmemelidir.

`syncStatus = conflict` yapılmalı ve kullanıcıya güvenli seçenek sunulmalıdır.

---

## 15. State Management Yaklaşımı

### 15.1 Riverpod Önerisi

Flutter projesinde Riverpod kullanılıyorsa:

- Project list için async notifier
- Project detail için family provider
- Project form için state notifier
- Entitlement için ayrı provider
- Sync status için stream/provider

kullanılabilir.

### 15.2 Provider Örnekleri

```dart
final projectListControllerProvider =
    AsyncNotifierProvider<ProjectListController, ProjectListState>(
  ProjectListController.new,
);

final projectDetailControllerProvider =
    AsyncNotifierProviderFamily<ProjectDetailController, ProjectDetailState, String>(
  ProjectDetailController.new,
);
```

### 15.3 State Sınıfları

ProjectListState:

```dart
class ProjectListState {
  final List<Project> projects;
  final ProjectListFilter filter;
  final ProjectSortType sortType;
  final bool isLoading;
  final ProjectFailure? failure;
}
```

ProjectDetailState:

```dart
class ProjectDetailState {
  final Project project;
  final ProjectPatternLink? patternLink;
  final List<ProjectYarnLink> yarnLinks;
  final List<ProjectToolLink> toolLinks;
  final ProjectProgressViewData progress;
  final bool hasBrokenRelationships;
  final ProjectFailure? failure;
}
```

ProjectFormState:

```dart
class ProjectFormState {
  final String name;
  final String description;
  final ProjectStatus selectedStatus;
  final bool isSaving;
  final bool hasUnsavedChanges;
  final ProjectFailure? failure;
}
```

---

## 16. UI Implementasyon Notları

### 16.1 Project List Screen

Sorumluluklar:

- Project list gösterir.
- Empty state gösterir.
- Search/filter/sort kontrollerini içerir.
- Create project aksiyonunu sunar.
- Sync indicator gösterebilir.

Yapmaması gerekenler:

- Business rule hesaplaması yapmamalı.
- Entitlement kararını tek başına vermemeli.
- Raw database sorgusu çalıştırmamalı.

### 16.2 Project Card

Göstermesi gerekenler:

- Project name
- Status badge
- Cover image placeholder
- Updated info
- Progress summary, varsa
- Sync indicator, gerekiyorsa

### 16.3 Project Detail Screen

Göstermesi gerekenler:

- Core project bilgileri
- Status
- Progress
- Pattern section
- Yarn section
- Tool section
- Counter section
- Parts section
- Notes section
- Action menu

Optional relationship hatalarında ekran crash olmamalıdır.

### 16.4 Create/Edit Form

Form:

- Local validation göstermeli
- Save sırasında duplicate submit engellemeli
- Unsaved changes durumunda confirm göstermeli
- Offline durumda kullanıcıyı engellememeli
- Optional alanları zorunlu yapmamalı

---

## 17. Validation Implementasyonu

Validation iki seviyede yapılmalıdır:

1. UI / form validation
2. Domain validation

UI validation kullanıcı deneyimi içindir. Domain validation güvenilirlik içindir.

### 17.1 Project Validation Service

```dart
class ProjectValidationService {
  void validateForCreate(CreateProjectInput input) {}
  void validateForUpdate(UpdateProjectInput input) {}
  void validateStatus(ProjectStatus status) {}
  void validateDates(DateTime? start, DateTime? target, DateTime? completed) {}
  void validateManualProgress(int? progress) {}
}
```

### 17.2 Validation Error Kodları

```text
empty_project_name
project_name_too_long
description_too_long
notes_too_long
invalid_status
invalid_status_transition
target_date_before_start_date
completion_date_before_start_date
invalid_manual_progress
unauthorized
project_not_found
```

Bu kodlar UI'da lokalize mesajlara dönüştürülmelidir.

---

## 18. Entitlement Implementasyonu

### 18.1 EntitlementService

```dart
abstract class EntitlementService {
  Future<ProjectActivationDecision> canCreateActiveProject({
    required String ownerId,
  });

  Future<ProjectActivationDecision> canActivateProject({
    required String ownerId,
    required String projectId,
  });
}
```

### 18.2 Active Count

Active count query:

```text
ownerId = currentUser
deletedAt IS NULL
status IN ('active', 'paused')
```

### 18.3 Limit Reached

Limit dolduğunda use case failure döndürmelidir:

```dart
ProjectFailure.activeProjectLimitReached(
  activeProjectCount: count,
  activeProjectLimit: limit,
)
```

UI bu failure'ı Limit Reached ekranına dönüştürmelidir.

---

## 19. Progress Calculator

Progress hesaplama UI içinde yapılmamalıdır.

```dart
class ProjectProgressCalculator {
  ProjectProgressViewData calculate({
    required Project project,
    required List<ProjectPartSummary> parts,
    required List<ProjectCounterSummary> counters,
  }) {
    // 1. parts
    // 2. counter
    // 3. manual
    // 4. none
  }
}
```

### 19.1 Progress Source Priority

```text
parts
counter
manual
none
```

### 19.2 100 Üzeri Değerler

Progress 100'ü aşarsa:

- UI crash olmamalıdır.
- Raw data korunmalıdır.
- Display value cap edilebilir.
- Inconsistent state göstermek mümkündür.

---

## 20. Analytics Implementasyonu

Analytics eventleri merkezi tracker ile gönderilmelidir.

```dart
class ProjectAnalyticsTracker {
  void trackCreateStarted(ProjectCreateStartedEvent event) {}
  void trackCreateCompleted(ProjectCreateCompletedEvent event) {}
  void trackCreateFailed(ProjectCreateFailedEvent event) {}
  void trackDetailViewed(ProjectDetailViewedEvent event) {}
}
```

### 20.1 Privacy Filter

Analytics tracker payload göndermeden önce privacy allowlist uygulamalıdır.

Yasak alanlar:

```text
name
description
notes
searchQuery
imagePath
localPath
remotePath
patternText
```

### 20.2 Event Constants

Event isimleri string literal olarak dağınık yazılmamalıdır.

Öneri:

```dart
class ProjectAnalyticsEventNames {
  static const createStarted = 'project_create_started';
  static const createCompleted = 'project_create_completed';
  static const detailViewed = 'project_detail_viewed';
}
```

---

## 21. Error Handling

### 21.1 Failure Model

```dart
sealed class ProjectFailure {
  const ProjectFailure();

  factory ProjectFailure.notFound() = ProjectNotFoundFailure;
  factory ProjectFailure.unauthorized() = ProjectUnauthorizedFailure;
  factory ProjectFailure.validation(String code) = ProjectValidationFailure;
  factory ProjectFailure.localSaveFailed() = ProjectLocalSaveFailure;
  factory ProjectFailure.syncFailed() = ProjectSyncFailure;
  factory ProjectFailure.activeProjectLimitReached({
    required int activeProjectCount,
    required int activeProjectLimit,
  }) = ProjectActiveLimitFailure;
}
```

### 21.2 UI Mapping

Failure'lar UI mesajlarına merkezi mapper ile çevrilmelidir.

Örnek:

```dart
String mapProjectFailureToMessage(ProjectFailure failure, Locale locale) {}
```

Hata mesajları teknik detay sızdırmamalıdır.

---

## 22. Routing ve Deep Link

### 22.1 Route Yapısı

Önerilen route:

```text
/projects
/projects/:projectId
/projects/:projectId/edit
/projects/deleted
/projects/archived
```

### 22.2 Deep Link Guard

Project detail açılmadan önce:

- Auth var mı?
- Project var mı?
- Owner doğru mu?
- `deletedAt` null mı?
- Archived state nasıl gösterilecek?

kontrol edilmelidir.

### 22.3 Unauthorized Result

Unauthorized veya not found durumları güvenli şekilde aynı generic state'e yönlendirilebilir.

---

## 23. Media Implementasyonu

### 23.1 Cover Image Flow

1. Kullanıcı görsel seçer.
2. Local validation yapılır.
3. Görsel optimize edilir.
4. Local metadata kaydedilir.
5. Project `coverMediaId` güncellenir.
6. Upload operation queue'ya alınır.
7. Upload başarılı olursa remote path kaydedilir.
8. Upload başarısız olursa uploadStatus `uploadFailed` olur.

### 23.2 Image Optimization

Kesin değerler tasarım ve kalite testleriyle belirlenmelidir.

Öneri:

- Thumbnail oluştur.
- Büyük görseli sıkıştır.
- Max dimension limiti uygula.
- HEIC gerekiyorsa dönüştür.
- EXIF metadata temizlemeyi değerlendir.

### 23.3 Private Storage

Remote upload private bucket'a yapılmalıdır.

Signed URL üretimi media gösterim katmanında yapılmalıdır.

---

## 24. Search, Filter, Sort Implementasyonu

### 24.1 Search

Search lokal olarak yapılabilir.

Gereksinimler:

- Case-insensitive
- Turkish character support
- Trim
- Debounce
- Raw query analytics'e gönderilmez

### 24.2 Filter

Filter query object kullanılmalıdır.

```dart
class ProjectListFilter {
  final Set<ProjectStatus> statuses;
  final ProjectTechnique? technique;
  final ProjectCategory? category;
  final bool? hasPattern;
  final bool? hasCounter;
}
```

### 24.3 Sort

Sort allowlist ile uygulanmalıdır.

```dart
enum ProjectSortType {
  updatedDesc,
  updatedAsc,
  createdDesc,
  createdAsc,
  nameAsc,
  nameDesc,
}
```

Arbitrary field sorting desteklenmemelidir.

---

## 25. Localization

Tüm kullanıcıya görünen metinler localization dosyalarından gelmelidir.

Hard-coded olmaması gereken metinler:

- Status label
- Error message
- Empty state
- Confirmation dialog
- Limit reached message
- Sync indicator
- Broken relationship state
- Button label

Enum value ile UI label ayrılmalıdır.

Örnek:

```text
status value: active
tr label: Aktif
en label: Active
```

---

## 26. Accessibility

Project Management UI:

- Screen reader label sağlamalıdır.
- Status sadece renk ile anlatılmamalıdır.
- Touch target minimum ölçüye uygun olmalıdır.
- Dynamic text desteklemelidir.
- Confirmation dialog erişilebilir olmalıdır.
- Error state screen reader tarafından okunabilir olmalıdır.

Örnek label:

```text
Mavi Kazak, aktif proje, son güncelleme bugün
```

Bu label içinde kullanıcı içeriği screen reader için kullanılabilir; analytics/log için kullanılmamalıdır.

---

## 27. Testing Stratejisi

Detaylı test kapsamı `testing.md` içinde tanımlanacaktır.

Bu dosya açısından minimum test hedefleri:

- ProjectName validation unit test
- Status transition policy unit test
- Active project count test
- Create project use case test
- Update project use case test
- Soft delete test
- Recover test
- Archive/restore test
- Offline create test
- Sync failed local preservation test
- Unauthorized repository query test
- Progress calculator test
- Analytics privacy test

---

## 28. Performance Notları

### 28.1 Project List

Project list:

- Lazy load desteklemeli
- Thumbnail kullanmalı
- Full image load etmemeli
- Query indexlerinden yararlanmalı
- Gereksiz rebuild azaltılmalı

### 28.2 Project Detail

Detail ekranında core project data önce gösterilebilir.

Relationship dataları kademeli yüklenebilir.

### 28.3 Local Query

Sık kullanılan query'ler:

- Active/paused list
- Updated desc sort
- Search by name
- Archived list
- Completed list
- Deleted recovery list
- Pending sync list

için index tasarlanmalıdır.

---

## 29. Migration Notları

### 29.1 V1 Initial Schema

V1 initial migration:

- `projects`
- `project_pattern_links`
- `project_yarn_links`
- `project_tool_links`
- `project_media`
- `project_status_history`, opsiyonel
- `sync_operations`

tablolarını veya eşdeğer local collections yapılarını oluşturmalıdır.

### 29.2 Future-Proof Alanlar

Aşağıdaki alanlar ileride faydalı olacaktır:

- `schemaVersion`
- `localVersion`
- `remoteVersion`
- `lastSyncedAt`
- `createdOffline`
- `deletedAt`
- `archivedAt`

### 29.3 Migration Test

Migration testleri eski local database fixture'ları ile yapılmalıdır.

---

## 30. Release Checklist

Project Management implementasyonu release'e hazır olmadan önce:

- [ ] Entity modelleri oluşturuldu.
- [ ] Enum mapping test edildi.
- [ ] ProjectRepository owner scoped çalışıyor.
- [ ] Create project local-first çalışıyor.
- [ ] Update project local-first çalışıyor.
- [ ] Status policy merkezi.
- [ ] Entitlement limit uygulanıyor.
- [ ] Soft delete normal listelerden hariç tutuyor.
- [ ] Archived ve deleted ayrılmış durumda.
- [ ] Relationship link/unlink çalışıyor.
- [ ] Broken relationship safe state var.
- [ ] Cover image local metadata ve upload state çalışıyor.
- [ ] Sync pending/failed/conflict state var.
- [ ] Analytics PII göndermiyor.
- [ ] Error handling kullanıcı dostu.
- [ ] Accessibility temel kontrolleri yapıldı.
- [ ] Localization tamamlandı.
- [ ] Unit ve integration testler yazıldı.

---

## 31. Codex İçin Net Uygulama Talimatı

Codex bu feature'ı geliştirirken:

1. Önce domain enum ve entity modellerini oluştur.
2. Sonra validation ve status policy servislerini yaz.
3. Ardından repository interface'lerini tanımla.
4. Local data source implementasyonunu yap.
5. Create/update/status/delete use case'lerini uygula.
6. Riverpod controller/state katmanını ekle.
7. Project list ve detail ekranlarını bağla.
8. Relationship ve media işlemlerini ekle.
9. Sync queue ve sync status yönetimini bağla.
10. Analytics tracker'ı privacy-safe şekilde entegre et.
11. Security scope testlerini yaz.
12. UI polish ve accessibility kontrollerini tamamla.

Kod üretirken şu hatalardan kaçın:

- Business logic'i widget içine yazma.
- `projectId` ile scope'suz query yapma.
- Deleted projectleri normal listede gösterme.
- Archive ve delete'i aynı şey gibi modelleme.
- Analytics'e user content gönderme.
- Remote sync başarısızlığında lokal veriyi silme.
- Pattern/yarn/tool kayıtlarını unlink sırasında silme.
- Enum label ve enum value'yu karıştırma.

---

## 32. Açık Teknik Kararlar

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-IMPL-OD-001 | Local DB teknolojisi | Drift veya Isar | Open |
| PM-IMPL-OD-002 | State management | Riverpod | Open |
| PM-IMPL-OD-003 | Remote backend | Supabase uygun | Open |
| PM-IMPL-OD-004 | Sync engine custom mı olacak? | V1 için lightweight custom queue | Open |
| PM-IMPL-OD-005 | Conflict UI V1 kapsamı | Basit seçim ekranı | Open |
| PM-IMPL-OD-006 | Notes ayrı entity mi? | V1'de `Project.notes` | Open |
| PM-IMPL-OD-007 | Media compression kütüphanesi | Platform testine göre seçilecek | Open |
| PM-IMPL-OD-008 | Analytics aracı | Firebase veya PostHog | Open |
| PM-IMPL-OD-009 | Status history zorunlu mu? | Önerilir, scope'a göre | Open |
| PM-IMPL-OD-010 | Draft recovery V1 tam mı? | Minimum local draft | Open |

---

## 33. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-001-project-management/testing.md
```

`testing.md` içinde Project Management için unit test, integration test, widget test, offline test, sync test, security test, analytics test ve acceptance test senaryoları tanımlanacaktır.
