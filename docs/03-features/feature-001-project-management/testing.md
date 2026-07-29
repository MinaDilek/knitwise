# Proje Yönetimi — Testing

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/testing.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliği için test stratejisini, test kapsamını, test türlerini, test senaryolarını, kabul kriterlerini ve release öncesi doğrulama checklistini tanımlar.

Project Management, Knitwise içindeki en merkezi feature olduğu için test kapsamı yalnızca ekranların çalışıp çalışmadığını değil, aşağıdaki alanların tamamını kapsamalıdır:

- Proje oluşturma
- Proje listeleme
- Proje detay görüntüleme
- Proje düzenleme
- Status transition
- Archive / restore
- Soft delete / recovery
- Search / filter / sort
- Pattern / yarn / tool relationship
- Cover image
- Progress calculation
- Offline create / edit
- Sync status
- Conflict handling
- Premium active project limit
- Downgrade behavior
- Authorization / ownership
- Analytics privacy
- Accessibility
- Localization
- Migration

Bu doküman Codex veya geliştirici ekip tarafından doğrudan test yazımı için kullanılabilecek şekilde hazırlanmıştır.

---

## 2. Test Prensipleri

Project Management testleri şu prensiplere göre yazılmalıdır:

1. Business rule testleri UI testlerine bırakılmamalıdır.
2. Status transition kuralları unit test ile doğrulanmalıdır.
3. Repository query'leri mutlaka `ownerId` scope kontrolü içermelidir.
4. Offline-first davranışlar ayrı test edilmelidir.
5. Remote sync başarısızlığı veri kaybına yol açmamalıdır.
6. Analytics eventleri PII içermediği için test edilmelidir.
7. Soft delete ve archive davranışları karıştırılmamalıdır.
8. Premium limit mevcut veriyi silmemelidir.
9. Broken relationship durumları crash üretmemelidir.
10. Migration testleri release öncesi çalıştırılmalıdır.

---

## 3. Test Türleri

| Test Türü | Amaç |
|---|---|
| Unit Test | Domain rule, validation, status policy, progress calculation |
| Repository Test | Local/remote data access ve owner scoped query |
| Use Case Test | Create, update, archive, delete, recover gibi iş akışları |
| Widget Test | UI component davranışları |
| Integration Test | Ekranlar ve repository/use case entegrasyonu |
| Offline Test | Bağlantısız create/edit/sync pending |
| Sync Test | Retry, failed, conflict, idempotency |
| Security Test | Unauthorized access, owner isolation, RLS mantığı |
| Analytics Test | Event tetiklenmesi ve privacy |
| Accessibility Test | Screen reader, contrast, touch target |
| Localization Test | Türkçe karakter, tarih formatı, UI metinleri |
| Migration Test | Eski schema'dan yeni schema'ya geçiş |
| Acceptance Test | User story ve flow bazlı uçtan uca doğrulama |

---

## 4. Test Ortamları

### 4.1 Lokal Geliştirme Ortamı

Kapsam:

- Unit testler
- Widget testler
- Repository fake testleri
- Domain service testleri
- Analytics fake tracker testleri

### 4.2 Entegrasyon Ortamı

Kapsam:

- Local database testleri
- Remote data source testleri
- Sync queue testleri
- Auth mock testleri
- Media upload mock testleri

### 4.3 Staging Ortamı

Kapsam:

- Supabase RLS testleri, kullanılıyorsa
- Private storage testleri
- Signed URL testleri
- Premium entitlement testleri
- Migration testleri
- End-to-end smoke testleri

### 4.4 Production Öncesi Smoke Test

Kapsam:

- İlk proje oluşturma
- Proje listeleme
- Proje detay
- Proje düzenleme
- Offline create
- Sync retry
- Limit reached
- Security smoke
- Analytics smoke

---

## 5. Test Data Setleri

### 5.1 Minimum Project Fixture

```json
{
  "projectId": "project_001",
  "ownerId": "user_001",
  "name": "Mavi Kazak",
  "status": "active",
  "createdAt": "2026-07-30T10:00:00Z",
  "updatedAt": "2026-07-30T10:00:00Z",
  "deletedAt": null,
  "syncStatus": "synced"
}
```

### 5.2 Çoklu Status Fixture

Aynı kullanıcı için:

```text
project_active_001     status=active
project_paused_001     status=paused
project_completed_001  status=completed
project_archived_001   status=archived
project_draft_001      status=draft
project_deleted_001    deletedAt != null
```

### 5.3 Multi User Fixture

```text
user_001 → project_user_001
user_002 → project_user_002
```

Amaç:

- Kullanıcı izolasyonunu test etmek.
- `projectId` manipülasyonu ile başka kullanıcı verisine erişilemediğini doğrulamak.

### 5.4 Relationship Fixture

```text
project_001
├── pattern_link_001
├── yarn_link_001
├── tool_link_001
├── media_001
├── counter_001
└── part_001
```

### 5.5 Broken Relationship Fixture

```text
project_001
├── pattern_link_001 → missing pattern
├── yarn_link_001 → missing yarn
├── tool_link_001 → missing tool
└── media_001 → missing local file
```

### 5.6 Offline Fixture

```text
project_offline_001
syncStatus=pending
createdOffline=true
remoteVersion=null
localVersion=1
```

### 5.7 Conflict Fixture

```text
local project:
  name changed
  localVersion=3
  syncStatus=pending

remote project:
  name changed differently
  remoteVersion=4
```

---

## 6. Unit Test Kapsamı

---

### 6.1 ProjectName Validation Testleri

#### PM-TEST-UNIT-001 — Geçerli Proje Adı Kabul Edilir

**Given** input `Mavi Kazak`  
**When** `ProjectName.create` çağrılır  
**Then** value `Mavi Kazak` olarak oluşturulur.

#### PM-TEST-UNIT-002 — Whitespace Trim Edilir

**Given** input `  Mavi Kazak  `  
**When** validation çalışır  
**Then** value `Mavi Kazak` olur.

#### PM-TEST-UNIT-003 — Boş Proje Adı Reddedilir

**Given** input boş string  
**When** validation çalışır  
**Then** `empty_project_name` hatası döner.

#### PM-TEST-UNIT-004 — Sadece Boşluk Reddedilir

**Given** input `"     "`  
**When** validation çalışır  
**Then** `empty_project_name` hatası döner.

#### PM-TEST-UNIT-005 — Çok Uzun Proje Adı Reddedilir

**Given** 120 karakterden uzun input  
**When** validation çalışır  
**Then** `project_name_too_long` hatası döner.

---

### 6.2 Date Validation Testleri

#### PM-TEST-UNIT-006 — Target Date Start Date'den Sonra Olabilir

**Given** `startDate=2026-07-01`  
**And** `targetCompletionDate=2026-08-01`  
**When** validation çalışır  
**Then** hata dönmez.

#### PM-TEST-UNIT-007 — Target Date Start Date'den Önce Olamaz

**Given** `startDate=2026-08-01`  
**And** `targetCompletionDate=2026-07-01`  
**When** validation çalışır  
**Then** `target_date_before_start_date` hatası döner.

#### PM-TEST-UNIT-008 — Completion Date Start Date'den Önce Olamaz

**Given** `startDate=2026-08-01`  
**And** `completedAt=2026-07-01`  
**When** validation çalışır  
**Then** `completion_date_before_start_date` hatası döner.

---

### 6.3 Manual Progress Testleri

#### PM-TEST-UNIT-009 — 0 Geçerlidir

**Given** `manualProgress=0`  
**Then** validation başarılıdır.

#### PM-TEST-UNIT-010 — 100 Geçerlidir

**Given** `manualProgress=100`  
**Then** validation başarılıdır.

#### PM-TEST-UNIT-011 — Negatif Progress Reddedilir

**Given** `manualProgress=-1`  
**Then** `invalid_manual_progress` hatası döner.

#### PM-TEST-UNIT-012 — 100 Üzeri Progress Reddedilir

**Given** `manualProgress=101`  
**Then** `invalid_manual_progress` hatası döner.

---

## 7. Status Policy Testleri

### PM-TEST-STATUS-001 — Draft Active Olabilir

**Given** status `draft`  
**When** hedef status `active`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-002 — Active Paused Olabilir

**Given** status `active`  
**When** hedef status `paused`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-003 — Paused Active Olabilir

**Given** status `paused`  
**When** hedef status `active`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-004 — Active Completed Olabilir

**Given** status `active`  
**When** hedef status `completed`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-005 — Paused Completed Olabilir

**Given** status `paused`  
**When** hedef status `completed`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-006 — Completed Active Olabilir

**Given** status `completed`  
**When** hedef status `active`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-007 — Active Archived Olabilir

**Given** status `active`  
**When** hedef status `archived`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-008 — Archived Active Olabilir

**Given** status `archived`  
**When** hedef status `active`  
**Then** transition allowed olmalıdır.

### PM-TEST-STATUS-009 — Geçersiz Status Reddedilir

**Given** unknown status value  
**When** mapping yapılır  
**Then** safe error veya fallback üretilmelidir.

### PM-TEST-STATUS-010 — Deleted Status Değildir

**Given** `deletedAt != null`  
**Then** proje deleted kabul edilir  
**And** `status=deleted` kullanılmaz.

---

## 8. Progress Calculator Testleri

### PM-TEST-PROGRESS-001 — Parts Progress Önceliklidir

**Given** project manual progress içerir  
**And** valid part progress vardır  
**When** progress hesaplanır  
**Then** source `parts` olur.

### PM-TEST-PROGRESS-002 — Counter Progress İkinci Önceliktedir

**Given** parts progress yoktur  
**And** valid counter target vardır  
**When** progress hesaplanır  
**Then** source `counter` olur.

### PM-TEST-PROGRESS-003 — Manual Progress Fallback Olarak Kullanılır

**Given** parts progress yoktur  
**And** counter progress yoktur  
**And** manual progress vardır  
**When** progress hesaplanır  
**Then** source `manual` olur.

### PM-TEST-PROGRESS-004 — Progress Kaynağı Yoksa None Döner

**Given** hiçbir progress kaynağı yoktur  
**When** progress hesaplanır  
**Then** source `none` olur.

### PM-TEST-PROGRESS-005 — Counter Target Zero İse Hesaplama Yapılmaz

**Given** counter target `0`  
**When** progress hesaplanır  
**Then** counter progress invalid sayılır.

### PM-TEST-PROGRESS-006 — 100 Üzeri Progress Güvenli Yönetilir

**Given** completed part count total part count değerinden büyüktür  
**When** progress hesaplanır  
**Then** uygulama crash olmaz  
**And** inconsistent veya capped display üretilir.

---

## 9. Use Case Testleri

---

### 9.1 CreateProjectUseCase

#### PM-TEST-UC-001 — Quick Create Başarılıdır

**Given** kullanıcı active proje oluşturabilir  
**When** geçerli project name ile create çağrılır  
**Then** project lokal oluşturulur  
**And** status `active` olur  
**And** syncStatus `pending` veya `synced` olur  
**And** analytics success event gider.

#### PM-TEST-UC-002 — Boş Ad ile Create Başarısızdır

**Given** name boş  
**When** create çağrılır  
**Then** validation failure döner  
**And** local insert yapılmaz.

#### PM-TEST-UC-003 — Free Limit Doluyken Active Create Engellenir

**Given** userPlan `free`  
**And** activeProjectCount limit değerine eşit  
**When** active project create çağrılır  
**Then** `activeProjectLimitReached` failure döner  
**And** proje oluşturulmaz.

#### PM-TEST-UC-004 — Draft Create Limitten Etkilenmez

**Given** free limit doludur  
**When** draft project create edilir  
**Then** create başarılı olabilir  
**And** active count artmaz.

#### PM-TEST-UC-005 — Offline Create Lokal Kaydedilir

**Given** cihaz offline  
**When** valid create çağrılır  
**Then** project local database'e yazılır  
**And** `createdOffline=true` olur  
**And** sync operation queue'ya eklenir.

---

### 9.2 UpdateProjectUseCase

#### PM-TEST-UC-006 — Valid Update Başarılıdır

**Given** kullanıcı project sahibidir  
**When** description güncellenir  
**Then** local project update edilir  
**And** `updatedAt` değişir  
**And** `localVersion` artar.

#### PM-TEST-UC-007 — Değişiklik Yoksa updatedAt Değişmez

**Given** project açılmıştır  
**When** hiçbir alan değişmeden save çağrılır  
**Then** `updatedAt` değişmemelidir.

#### PM-TEST-UC-008 — Deleted Project Update Edilemez

**Given** `deletedAt != null`  
**When** update çağrılır  
**Then** failure döner.

#### PM-TEST-UC-009 — Unauthorized Update Engellenir

**Given** project başka kullanıcıya aittir  
**When** update çağrılır  
**Then** unauthorized failure döner.

---

### 9.3 Archive / Restore Use Case

#### PM-TEST-UC-010 — Active Project Archive Edilir

**Given** project status `active`  
**When** archive çağrılır  
**Then** status `archived` olur  
**And** `archivedAt` dolar.

#### PM-TEST-UC-011 — Archived Project Restore Edilir

**Given** project status `archived`  
**When** restore to active çağrılır  
**And** limit uygundur  
**Then** status `active` olur  
**And** `archivedAt` temizlenir.

#### PM-TEST-UC-012 — Restore Active Limit Doluysa Engellenir

**Given** free limit doludur  
**When** archived project active olarak restore edilir  
**Then** limit failure döner  
**And** project archived kalır.

---

### 9.4 Complete / Reopen Use Case

#### PM-TEST-UC-013 — Project Complete Edilir

**Given** project status `active`  
**When** complete çağrılır  
**Then** status `completed` olur  
**And** `completedAt` dolar.

#### PM-TEST-UC-014 — Completed Project Reopen Edilir

**Given** project status `completed`  
**And** active limit uygundur  
**When** reopen çağrılır  
**Then** status `active` olur.

#### PM-TEST-UC-015 — Reopen Limit Doluysa Engellenir

**Given** free limit doludur  
**When** completed project reopen çağrılır  
**Then** limit failure döner  
**And** project completed kalır.

---

### 9.5 Soft Delete / Recovery Use Case

#### PM-TEST-UC-016 — Soft Delete Başarılıdır

**Given** user project sahibidir  
**When** soft delete çağrılır  
**Then** `deletedAt` dolar  
**And** normal listeden çıkar.

#### PM-TEST-UC-017 — Recovery Süresi İçinde Recover Başarılıdır

**Given** project soft deleted  
**And** recovery window devam ediyor  
**When** recover çağrılır  
**Then** `deletedAt` temizlenir.

#### PM-TEST-UC-018 — Recovery Süresi Geçmişse Recover Engellenir

**Given** project soft deleted  
**And** recovery window expired  
**When** recover çağrılır  
**Then** recovery failure döner.

---

## 10. Repository Testleri

### PM-TEST-REPO-001 — Liste Sadece Owner Projectlerini Döndürür

**Given** local database içinde iki farklı user projectleri vardır  
**When** `listProjects(ownerId=user_001)` çağrılır  
**Then** yalnızca `user_001` projectleri döner.

### PM-TEST-REPO-002 — Deleted Project Normal Listede Dönmez

**Given** `deletedAt != null` project vardır  
**When** normal list query çalışır  
**Then** deleted project dönmez.

### PM-TEST-REPO-003 — Active Count Doğru Hesaplanır

**Given** active, paused, completed, archived, draft projectler vardır  
**When** active count hesaplanır  
**Then** yalnızca active ve paused sayılır.

### PM-TEST-REPO-004 — Search Owner Scope İçinde Çalışır

**Given** iki kullanıcıda benzer project name vardır  
**When** user_001 search yapar  
**Then** user_002 projectleri dönmez.

### PM-TEST-REPO-005 — Sort updatedAt Desc Çalışır

**Given** farklı updatedAt değerleri olan projectler vardır  
**When** default list query çalışır  
**Then** en güncel project ilk sırada olur.

### PM-TEST-REPO-006 — Archived Query Sadece Archived Döndürür

**Given** farklı status değerleri vardır  
**When** archived view query çalışır  
**Then** yalnızca archived projectler döner.

### PM-TEST-REPO-007 — Recovery Query Deleted Projectleri Döndürür

**Given** deleted projectler vardır  
**When** recovery query çalışır  
**Then** yalnızca owner'a ait recoverable deleted projectler döner.

---

## 11. Relationship Testleri

### PM-TEST-REL-001 — Pattern Link Başarılıdır

**Given** project ve pattern aynı kullanıcıya aittir  
**When** pattern link çağrılır  
**Then** relationship oluşturulur.

### PM-TEST-REL-002 — Başka Kullanıcının Pattern'i Bağlanamaz

**Given** pattern başka kullanıcıya aittir  
**When** link çağrılır  
**Then** unauthorized failure döner.

### PM-TEST-REL-003 — Starter Pattern Bağlanabilir

**Given** starter pattern public erişilebilir yapıdadır  
**When** link çağrılır  
**Then** relationship oluşturulur.

### PM-TEST-REL-004 — Yarn Link Başarılıdır

**Given** yarn user'a aittir  
**When** yarn project'e bağlanır  
**Then** relationship oluşturulur.

### PM-TEST-REL-005 — Yarn Unlink Ana Kaydı Silmez

**Given** project yarn link vardır  
**When** unlink çağrılır  
**Then** relationship kaldırılır  
**And** yarn inventory kaydı kalır.

### PM-TEST-REL-006 — Tool Manual Entry Başarılıdır

**Given** kullanıcı manual tool bilgisi girer  
**When** project tool link oluşturulur  
**Then** `manualToolName` veya size bilgisi ile kayıt oluşur.

### PM-TEST-REL-007 — Broken Pattern Project Detail'i Bozmaz

**Given** pattern link missing pattern'e işaret eder  
**When** project detail yüklenir  
**Then** core project gösterilir  
**And** broken relationship state gösterilir.

---

## 12. Widget Testleri

### PM-TEST-WIDGET-001 — Empty State Görünür

**Given** project list boş  
**When** Project List render edilir  
**Then** empty state görünür  
**And** create button görünür.

### PM-TEST-WIDGET-002 — Project Card Minimum Bilgi Gösterir

**Given** valid project card data  
**When** ProjectCard render edilir  
**Then** name, status, placeholder/cover ve updated info görünür.

### PM-TEST-WIDGET-003 — Status Badge Text İçerir

**Given** status `active`  
**When** badge render edilir  
**Then** sadece renk değil text label da bulunur.

### PM-TEST-WIDGET-004 — Limit Reached Screen Seçenekleri Gösterir

**Given** limit failure state  
**When** screen render edilir  
**Then** archive, complete, upgrade ve cancel seçenekleri görünür.

### PM-TEST-WIDGET-005 — Broken Relationship State Görünür

**Given** missing pattern relationship  
**When** detail section render edilir  
**Then** unavailable state ve remove/replace action görünür.

### PM-TEST-WIDGET-006 — Delete Dialog Archive ile Delete Farkını Anlatır

**Given** delete action seçilir  
**When** dialog açılır  
**Then** archive alternatifi veya açıklaması görünür.

---

## 13. Integration Testleri

### PM-TEST-INT-001 — İlk Proje Oluşturma Akışı

**Given** yeni kullanıcı Project List ekranındadır  
**When** create project yapar  
**Then** Project Detail ekranına yönlenir  
**And** proje listede görünür.

### PM-TEST-INT-002 — Detaylı Create Akışı

**Given** kullanıcı detailed create açar  
**When** name, category, yarn ve cover seçer  
**Then** project ve ilişkiler oluşturulur.

### PM-TEST-INT-003 — Edit ve Detail Güncelleme

**Given** existing project vardır  
**When** kullanıcı name değiştirip save eder  
**Then** Project Detail yeni adı gösterir  
**And** Project List de güncellenir.

### PM-TEST-INT-004 — Archive Sonrası Liste Güncellenir

**Given** active project listede görünür  
**When** kullanıcı archive eder  
**Then** project default listeden çıkar  
**And** archived view içinde görünür.

### PM-TEST-INT-005 — Soft Delete Sonrası Liste Güncellenir

**Given** project listede görünür  
**When** kullanıcı siler  
**Then** project normal listeden çıkar.

### PM-TEST-INT-006 — Search No Result State

**Given** projectler vardır  
**When** eşleşmeyen search query girilir  
**Then** no result state gösterilir.

---

## 14. Offline Testleri

### PM-TEST-OFF-001 — Offline Create

**Given** network unavailable  
**When** valid project create edilir  
**Then** local save yapılır  
**And** syncStatus `pending` olur.

### PM-TEST-OFF-002 — Offline Edit

**Given** existing local project vardır  
**And** network unavailable  
**When** kullanıcı edit save yapar  
**Then** local data güncellenir  
**And** syncStatus `pending` olur.

### PM-TEST-OFF-003 — Connectivity Restored Sync

**Given** pending project vardır  
**When** network gelir  
**Then** sync operation çalışır  
**And** success ise syncStatus `synced` olur.

### PM-TEST-OFF-004 — Sync Failure Veri Kaybettirmez

**Given** pending local update vardır  
**When** sync failed olur  
**Then** local update korunur  
**And** syncStatus `failed` olur.

### PM-TEST-OFF-005 — Offline Limit Cache

**Given** user free plandadır  
**And** entitlement cache vardır  
**When** offline create yapılır  
**Then** cache rule uygulanır  
**And** veri silinmez.

---

## 15. Sync ve Conflict Testleri

### PM-TEST-SYNC-001 — Pending Operation Oluşturulur

**Given** project create edilir  
**When** local save başarılıdır  
**Then** sync operation queue'ya eklenir.

### PM-TEST-SYNC-002 — Retry Attempt Artar

**Given** sync operation failed  
**When** retry yapılır  
**Then** `attemptCount` artar.

### PM-TEST-SYNC-003 — Max Retry Sonrası Failed Kalır

**Given** max retry aşılmıştır  
**When** sync tekrar denenmez  
**Then** operation failed state'te kalır  
**And** kullanıcı manuel retry görebilir.

### PM-TEST-SYNC-004 — Duplicate Operation Duplicate Project Oluşturmaz

**Given** aynı create operation iki kez gönderilir  
**When** remote idempotent çalışır  
**Then** tek project oluşur.

### PM-TEST-SYNC-005 — Conflict Detected

**Given** local ve remote version farklı değişmiştir  
**When** sync çalışır  
**Then** syncStatus `conflict` olur.

### PM-TEST-SYNC-006 — Conflict Use Local

**Given** conflict state vardır  
**When** kullanıcı local version seçer  
**Then** local payload remote'a gönderilir  
**And** conflict çözülür.

### PM-TEST-SYNC-007 — Remote Deleted Local Edit Conflict

**Given** remote project deleted  
**And** local project updated  
**When** sync çalışır  
**Then** local data sessizce silinmez  
**And** conflict veya recovery state oluşur.

---

## 16. Security Testleri

### PM-TEST-SEC-001 — Unauthorized Detail Block

**Given** user_001 login olmuştur  
**And** project user_002'ye aittir  
**When** user_001 project detail açmaya çalışır  
**Then** project data gösterilmez.

### PM-TEST-SEC-002 — Unauthorized Update Block

**Given** user_001 başka kullanıcının projectId değerini bilir  
**When** update request gönderir  
**Then** update reddedilir.

### PM-TEST-SEC-003 — Unauthorized Delete Block

**Given** user_001 başka kullanıcının projectId değerini kullanır  
**When** soft delete çağırır  
**Then** delete reddedilir.

### PM-TEST-SEC-004 — Relationship Ownership Block

**Given** user_001 kendi project'ine user_002 yarn kaydını bağlamaya çalışır  
**When** link çağrılır  
**Then** unauthorized failure döner.

### PM-TEST-SEC-005 — Media Private Access

**Given** project media private storage içindedir  
**When** başka kullanıcı erişmeye çalışır  
**Then** erişim reddedilir.

### PM-TEST-SEC-006 — Signed URL Loglanmaz

**Given** signed URL üretilir  
**When** logging yapılır  
**Then** full signed URL loglarda bulunmaz.

### PM-TEST-SEC-007 — Deep Link Unauthorized

**Given** user_001 başka kullanıcının project deep link'ini açar  
**Then** generic not-found veya access denied gösterilir  
**And** project data sızmaz.

### PM-TEST-SEC-008 — Deleted Project Normal Detail Açılmaz

**Given** `deletedAt != null`  
**When** detail açılır  
**Then** normal project detail gösterilmez.

---

## 17. Analytics Testleri

### PM-TEST-AN-001 — Create Started Event

**Given** kullanıcı create flow başlatır  
**Then** `project_create_started` event gider.

### PM-TEST-AN-002 — Create Completed Event

**Given** project başarıyla oluşturulur  
**Then** `project_create_completed` event gider.

### PM-TEST-AN-003 — Create Failed Event

**Given** validation error oluşur  
**Then** `project_create_failed` event gider  
**And** `errorCode=validation_error` olur.

### PM-TEST-AN-004 — Project Name Analytics'e Gitmez

**Given** project adı `Mavi Kazak`  
**When** analytics event gönderilir  
**Then** payload içinde `Mavi Kazak` bulunmaz.

### PM-TEST-AN-005 — Notes Analytics'e Gitmez

**Given** project notes doludur  
**When** edit event gönderilir  
**Then** notes içeriği payload içinde bulunmaz.

### PM-TEST-AN-006 — Search Query Analytics'e Gitmez

**Given** kullanıcı search query girer  
**When** search event gönderilir  
**Then** raw query payload içinde bulunmaz  
**And** yalnızca `queryLengthBucket` bulunur.

### PM-TEST-AN-007 — Limit Eventleri Gönderilir

**Given** free limit doludur  
**When** kullanıcı create dener  
**Then** `project_limit_reached_viewed` event gider.

---

## 18. Accessibility Testleri

### PM-TEST-A11Y-001 — Project Card Screen Reader Label

**Given** project card render edilir  
**Then** screen reader için anlamlı label vardır.

### PM-TEST-A11Y-002 — Status Renk Dışı Anlatılır

**Given** status badge render edilir  
**Then** status text veya accessible label ile ifade edilir.

### PM-TEST-A11Y-003 — Touch Target

**Given** create, edit, archive, delete butonları vardır  
**Then** minimum touch target ölçülerine uygundur.

### PM-TEST-A11Y-004 — Dynamic Text

**Given** kullanıcı büyük font kullanır  
**When** project list ve detail açılır  
**Then** kritik aksiyonlar taşmaz veya kaybolmaz.

### PM-TEST-A11Y-005 — Dialog Focus

**Given** delete confirmation dialog açılır  
**Then** focus dialog içinde yönetilir  
**And** screen reader dialog içeriğini okuyabilir.

---

## 19. Localization Testleri

### PM-TEST-L10N-001 — Status Label Lokalize Edilir

**Given** status value `active`  
**When** Türkçe locale kullanılır  
**Then** label `Aktif` veya onaylı Türkçe karşılık olur.

### PM-TEST-L10N-002 — Türkçe Karakter Search

**Given** project name `Şiş Örgü Hırka`  
**When** kullanıcı `şiş` arar  
**Then** project bulunur.

### PM-TEST-L10N-003 — Tarih Formatı Lokalize Edilir

**Given** project updated date vardır  
**When** Türkçe locale kullanılır  
**Then** tarih Türkçe kullanıcı beklentisine uygun gösterilir.

### PM-TEST-L10N-004 — Hard-coded Text Yok

**Given** Project Management UI taranır  
**Then** user-facing text localization dosyalarından gelir.

---

## 20. Migration Testleri

### PM-TEST-MIG-001 — Initial Schema Oluşur

**Given** fresh install  
**When** app başlar  
**Then** project tabloları/collections oluşur.

### PM-TEST-MIG-002 — Eski Project Kaydı Okunur

**Given** eski schema fixture vardır  
**When** migration çalışır  
**Then** project erişilebilir kalır.

### PM-TEST-MIG-003 — Migration Idempotent

**Given** migration bir kez çalışmıştır  
**When** tekrar çalıştırılır  
**Then** veri bozulmaz.

### PM-TEST-MIG-004 — Yeni Alan Default Değer Alır

**Given** eski kayıtta `syncStatus` yoktur  
**When** migration çalışır  
**Then** güvenli default atanır.

---

## 21. Acceptance Testleri

### PM-TEST-ACC-001 — Kullanıcı İlk Projesini Oluşturur

**Given** yeni kullanıcı hiç projeye sahip değildir  
**When** kullanıcı empty state üzerinden proje oluşturur  
**Then** proje oluşur  
**And** Project Detail açılır  
**And** proje default listede görünür.

### PM-TEST-ACC-002 — Kullanıcı Projeyi Düzenler

**Given** kullanıcı active project sahibidir  
**When** kullanıcı açıklama ve kategori değiştirir  
**Then** değişiklikler kaydedilir  
**And** detail ekranında görünür.

### PM-TEST-ACC-003 — Kullanıcı Projeyi Tamamlar

**Given** kullanıcı active project sahibidir  
**When** kullanıcı complete action onaylar  
**Then** project completed olur  
**And** default listeden çıkar  
**And** completed view içinde görünür.

### PM-TEST-ACC-004 — Kullanıcı Projeyi Arşivler ve Geri Alır

**Given** active project vardır  
**When** kullanıcı archive eder  
**Then** archived view içinde görünür  
**When** kullanıcı restore eder  
**Then** uygun status ile geri döner.

### PM-TEST-ACC-005 — Kullanıcı Projeyi Siler ve Kurtarır

**Given** project vardır  
**When** kullanıcı soft delete yapar  
**Then** normal listeden çıkar  
**When** kullanıcı recovery süresi içinde restore eder  
**Then** proje geri gelir.

### PM-TEST-ACC-006 — Free Limit Kullanıcıyı Engeller Ama Veri Silmez

**Given** free kullanıcı limit kadar active project sahibidir  
**When** yeni active project oluşturmak ister  
**Then** limit reached ekranı görünür  
**And** mevcut projeler korunur.

### PM-TEST-ACC-007 — Offline Kullanıcı Proje Oluşturur

**Given** internet bağlantısı yoktur  
**When** kullanıcı proje oluşturur  
**Then** proje lokal kaydedilir  
**And** bağlantı gelince sync edilir.

---

## 22. Regression Test Checklist

Her Project Management değişikliğinden sonra kontrol edilmesi gerekenler:

- [ ] Quick create çalışıyor.
- [ ] Detailed create çalışıyor.
- [ ] Project list default filtre doğru.
- [ ] Project detail açılıyor.
- [ ] Project edit çalışıyor.
- [ ] Pause/resume çalışıyor.
- [ ] Complete/reopen çalışıyor.
- [ ] Archive/restore çalışıyor.
- [ ] Soft delete normal listeden kaldırıyor.
- [ ] Search çalışıyor.
- [ ] Filter çalışıyor.
- [ ] Sort çalışıyor.
- [ ] Pattern link/unlink çalışıyor.
- [ ] Yarn link/unlink çalışıyor.
- [ ] Tool link/unlink çalışıyor.
- [ ] Cover image add/remove çalışıyor.
- [ ] Offline create çalışıyor.
- [ ] Sync failure veri kaybettirmiyor.
- [ ] Free limit çalışıyor.
- [ ] Unauthorized access engelleniyor.
- [ ] Analytics PII içermiyor.

---

## 23. Release Exit Criteria

Feature release'e hazır sayılırsa:

- Tüm Must acceptance testleri geçmeli.
- Unit testlerde status policy, validation ve progress calculator tam geçmeli.
- Repository owner scope testleri geçmeli.
- Offline create/edit testleri geçmeli.
- Sync failure veri kaybı üretmemeli.
- Security testleri geçmeli.
- Analytics privacy testleri geçmeli.
- Critical accessibility sorunları çözülmüş olmalı.
- Localization smoke test geçmeli.
- Migration testleri geçmeli.
- Release blocker bug kalmamalı.
- Product Owner kabul vermeli.

---

## 24. Codex İçin Test Yazım Talimatı

Codex bu feature için test üretirken şu sırayı izlemelidir:

1. Domain validation testlerini yaz.
2. Status transition policy testlerini yaz.
3. Progress calculator testlerini yaz.
4. Project repository fake/local testlerini yaz.
5. Create/update/status/delete use case testlerini yaz.
6. Entitlement limit testlerini yaz.
7. Offline create/edit testlerini yaz.
8. Sync pending/failed/conflict testlerini yaz.
9. Security owner isolation testlerini yaz.
10. Analytics privacy testlerini yaz.
11. Widget testlerini yaz.
12. Integration acceptance flow testlerini yaz.

Testlerde dikkat edilmesi gerekenler:

- Test isimleri davranışı açık anlatmalıdır.
- Raw kullanıcı içeriği analytics assertion içinde bulunmamalıdır.
- Fake repository owner scope hatalarını yakalayacak şekilde tasarlanmalıdır.
- Time-dependent testlerde fake clock kullanılmalıdır.
- Network-dependent testlerde mock connection kullanılmalıdır.
- Premium entitlement için fake entitlement service kullanılmalıdır.
- Local DB testleri temiz fixture ile başlamalıdır.

---

## 25. Açık Test Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-TEST-OD-001 | Test framework | Flutter test + mocktail / riverpod test utilities | Open |
| PM-TEST-OD-002 | Integration test cihazları | iOS Simulator + Android Emulator | Open |
| PM-TEST-OD-003 | Local DB test yaklaşımı | In-memory DB veya temp DB | Open |
| PM-TEST-OD-004 | Supabase RLS testleri | Staging environment | Open |
| PM-TEST-OD-005 | E2E test aracı | Patrol veya Flutter integration_test | Open |
| PM-TEST-OD-006 | Accessibility automation | Flutter semantics test + manuel QA | Open |
| PM-TEST-OD-007 | Visual regression gerekli mi? | Project card/detail için değerlendirilebilir | Open |
| PM-TEST-OD-008 | Conflict UI V1 test kapsamı | Basit seçim ekranı | Open |

---

## 26. Feature-001 Dosya Seti Durumu

Project Management için ana doküman seti:

```text
03-features/feature-001-project-management/
├── overview.md
├── requirements.md
├── flows.md
├── data-model.md
├── analytics.md
├── security.md
├── implementation-notes.md
└── testing.md
```

Bu dosya ile feature-001 için temel dokümantasyon seti tamamlanır.

Sonraki adım:

- Bu dosyaların hızlı tutarlılık review'ı yapılabilir.
- Ardından `feature-002-row-counter` dokümantasyonuna geçilebilir.
