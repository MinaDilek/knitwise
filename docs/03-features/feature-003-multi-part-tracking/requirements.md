# Multi-Part Tracking — Requirements

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/requirements.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Multi-Part Tracking** özelliğinin gereksinimlerini tanımlar.

Multi-Part Tracking, bir project’in gövde, kol, motif, panel, amigurumi parçası veya özel bölüm gibi alt parçalara ayrılarak takip edilmesini sağlar.

---

## 2. Requirement ID Standardı

| Prefix | Açıklama |
|---|---|
| `MPT-FR` | Functional Requirement |
| `MPT-NFR` | Non-Functional Requirement |
| `MPT-BR` | Business Rule |
| `MPT-VR` | Validation Rule |
| `MPT-ER` | Error Handling Requirement |
| `MPT-EC` | Edge Case |
| `MPT-AC` | Acceptance Criteria |

---

## 3. Kapsam

V1 kapsamı:

- Project içinde part oluşturma
- Part adı, type, status, order ve progress yönetimi
- Part detail ve edit
- Part soft delete
- Part reorder
- Part ile row counter ilişkilendirme
- Project progress hesaplamasında part kullanımı
- Offline create/update/delete
- Sync status
- Security ve analytics

---

## 4. Fonksiyonel Gereksinimler

### MPT-FR-001 — Part Oluşturma

Sistem, kullanıcının project içinde yeni part oluşturmasına izin vermelidir.

Minimum alanlar:

- `name`
- `partType`
- `partStatus`
- `partOrder`

---

### MPT-FR-002 — Project Context

Project detail ekranından oluşturulan part otomatik olarak ilgili `projectId` ile ilişkilendirilmelidir.

---

### MPT-FR-003 — Part Adı

Kullanıcı part’a anlamlı ad verebilmelidir.

Örnekler:

- Gövde
- Sağ Kol
- Sol Kol
- Baş
- Kulak 1
- Motif 12

---

### MPT-FR-004 — Part Type

Kullanıcı part type seçebilmelidir.

Desteklenen değerler:

```text
body
front
back
sleeve
right_sleeve
left_sleeve
collar
cuff
hem
panel
motif
square
head
arm
leg
ear
tail
accessory
border
assembly
finishing
custom
```

---

### MPT-FR-005 — Part Status

Part status yönetilmelidir.

V1 status değerleri:

```text
not_started
active
paused
completed
blocked
```

Deleted status değildir; `deletedAt` ile yönetilir.

---

### MPT-FR-006 — Part Progress

Kullanıcı part progress değerini manuel girebilmelidir.

Kural:

```text
0 <= progressPercent <= 100
```

---

### MPT-FR-007 — Counter’dan Progress

Part’a bağlı counter target içeriyorsa part progress counter’dan hesaplanabilir.

Öncelik kararı:

```text
linked counter progress > manual part progress
```

ürün kararı olarak değerlendirilebilir.

---

### MPT-FR-008 — Part List

Project detail içinde part listesi gösterilmelidir.

Liste şunları gösterebilir:

- Part adı
- Status
- Progress
- Linked counter sayısı
- Son güncelleme

---

### MPT-FR-009 — Part Detail

Part detail ekranı şunları göstermelidir:

- Name
- Type
- Status
- Progress
- Linked counters
- Notes
- Edit/delete actions

---

### MPT-FR-010 — Part Edit

Kullanıcı part metadata alanlarını düzenleyebilmelidir:

- Name
- Type
- Status
- Progress
- Notes
- Order

---

### MPT-FR-011 — Part Reorder

Kullanıcı project içindeki part sırasını değiştirebilmelidir.

`partOrder` bu amaçla kullanılmalıdır.

---

### MPT-FR-012 — Part Complete

Kullanıcı part’ı completed yapabilmelidir.

Completed olduğunda:

- `partStatus = completed`
- `completedAt` set edilir
- progress 100 olabilir veya olduğu gibi korunabilir, ürün kararı gerektirir

---

### MPT-FR-013 — Completed Part Reopen

Kullanıcı completed part’ı tekrar active yapabilmelidir.

---

### MPT-FR-014 — Part Soft Delete

Kullanıcı part’ı silebilmelidir.

Silme:

```text
deletedAt = now
```

şeklinde soft delete olmalıdır.

---

### MPT-FR-015 — Part Duplicate

V1 için opsiyonel olarak part duplicate desteklenebilir.

Amigurumi ve motif projelerinde faydalıdır.

---

### MPT-FR-016 — Counter Link

Kullanıcı part’a row counter bağlayabilmelidir.

Bir part birden fazla counter’a sahip olabilir.

---

### MPT-FR-017 — Counter Unlink

Part-counter ilişkisi kaldırılabilmelidir.

Counter kaydı silinmemelidir.

---

### MPT-FR-018 — Project Progress

Project progress hesaplamasında part bilgileri kullanılabilmelidir.

Önerilen öncelik:

```text
parts progress
counter progress
manual project progress
none
```

---

### MPT-FR-019 — Offline Part Create

Kullanıcı offline durumdayken part oluşturabilmelidir.

---

### MPT-FR-020 — Offline Part Update

Kullanıcı offline durumda part düzenleyebilmelidir.

---

### MPT-FR-021 — Sync Status

Part için sync status takip edilmelidir:

```text
synced
pending
failed
conflict
```

---

### MPT-FR-022 — Broken Counter Relationship

Part’a bağlı counter bulunamazsa part detail crash olmamalıdır.

---

### MPT-FR-023 — Notes

Part için opsiyonel notes alanı desteklenebilir.

Notes analytics/log içine gönderilmemelidir.

---

### MPT-FR-024 — Empty State

Project içinde hiç part yoksa kullanıcıya empty state gösterilmelidir.

---

### MPT-FR-025 — Suggested Parts

V1’de opsiyonel olarak project type’a göre suggested parts sunulabilir.

Örnek:

```text
Sweater → Front, Back, Right Sleeve, Left Sleeve, Collar
Amigurumi → Head, Body, Arms, Legs, Ears
```

---

## 5. Fonksiyonel Olmayan Gereksinimler

### MPT-NFR-001 — Performans

Project detail içinde 50 part listelenebilmelidir.

### MPT-NFR-002 — Offline-First

Part create/update/delete internet bağlantısına bağlı olmamalıdır.

### MPT-NFR-003 — Veri Kaybı Önleme

Sync failure part verisini silmemelidir.

### MPT-NFR-004 — Accessibility

Part card status sadece renk ile anlatılmamalıdır.

### MPT-NFR-005 — Localization

Part type ve status label değerleri lokalize edilmelidir.

### MPT-NFR-006 — Security

Her part `ownerId` ile scope edilmelidir.

### MPT-NFR-007 — Analytics Privacy

Part name ve notes analytics’e gönderilmemelidir.

---

## 6. İş Kuralları

### MPT-BR-001 — Ownership

Her part bir owner’a ait olmalıdır.

### MPT-BR-002 — Project Zorunluluğu

V1’de part project’e bağlı olmalıdır.

### MPT-BR-003 — Part Name

Part name trim sonrası boş olamaz.

### MPT-BR-004 — Part Type

Part type allowlist değerlerinden biri olmalıdır.

### MPT-BR-005 — Part Status

Part status allowlist değerlerinden biri olmalıdır.

### MPT-BR-006 — Deleted Status Değildir

Silme `deletedAt` ile yönetilir.

### MPT-BR-007 — Normal Listeler

`deletedAt != null` olan partlar normal listelerde görünmez.

### MPT-BR-008 — Part Order

Aynı project içinde part order tutarlı olmalıdır.

### MPT-BR-009 — Completed Timestamp

Part completed olduğunda `completedAt` set edilebilir.

### MPT-BR-010 — Reopen

Completed part tekrar active yapılabilir.

### MPT-BR-011 — Counter Unlink

Counter unlink part’ı veya counter’ı silmez.

### MPT-BR-012 — Project Delete

Project soft delete edilirse partlar project context’te görünmez.

---

## 7. Validasyon Kuralları

| ID | Kural |
|---|---|
| MPT-VR-001 | `name` trim sonrası 1-80 karakter |
| MPT-VR-002 | `partType` allowlist içinde |
| MPT-VR-003 | `partStatus` allowlist içinde |
| MPT-VR-004 | `progressPercent` null veya 0-100 |
| MPT-VR-005 | `projectId` owner’a ait olmalı |
| MPT-VR-006 | Linked counter aynı owner ve project içinde olmalı |
| MPT-VR-007 | `notes` max 1000 karakter |
| MPT-VR-008 | `partOrder` negatif olmamalı |

---

## 8. Hata Yönetimi

| ID | Durum | Beklenen |
|---|---|---|
| MPT-ER-001 | Part create failure | Kullanıcı girdisi korunur |
| MPT-ER-002 | Unauthorized project | Part oluşturulmaz |
| MPT-ER-003 | Invalid status | Validasyon hatası |
| MPT-ER-004 | Local save failed | Kullanıcı uyarılır |
| MPT-ER-005 | Sync failed | Local data korunur |
| MPT-ER-006 | Counter missing | Broken relationship state |
| MPT-ER-007 | Part deleted | Normal detail gösterilmez |

---

## 9. Edge Case’ler

- Project içinde 0 part var.
- Project içinde 50+ part var.
- Kullanıcı hızlıca reorder yapar.
- Part linked counter silinir.
- Part offline oluşturulur, sonra project remote’da deleted olur.
- Part completed iken counter hala aktif kalır.
- Part progress 100 ama status completed değildir.
- Status completed ama progress 0’dır.
- Duplicate part isimleri vardır.
- Unknown part type remote’dan gelir.

---

## 10. Acceptance Criteria

### MPT-AC-001 — Part Oluşturma

**Given** kullanıcı project detail ekranındadır
**When** geçerli bilgilerle part oluşturur
**Then** part project altında görünür.

### MPT-AC-002 — Part Name Boş Olamaz

**Given** name boş
**When** save edilir
**Then** validation hatası gösterilir.

### MPT-AC-003 — Part Status Değişir

**Given** part active durumdadır
**When** kullanıcı completed seçer
**Then** part completed olur.

### MPT-AC-004 — Part Progress Görünür

**Given** part progress 40
**When** project detail açılır
**Then** part progress görünür.

### MPT-AC-005 — Part Counter Link

**Given** part ve counter aynı project içindedir
**When** kullanıcı counter bağlar
**Then** part detail içinde linked counter görünür.

### MPT-AC-006 — Offline Create

**Given** cihaz offline
**When** kullanıcı part oluşturur
**Then** part local kaydedilir ve sync pending olur.

### MPT-AC-007 — Soft Delete

**Given** part vardır
**When** kullanıcı siler
**Then** part normal listeden çıkar.

### MPT-AC-008 — Unauthorized Erişim Engellenir

**Given** part başka kullanıcıya aittir
**When** kullanıcı erişmeye çalışır
**Then** data gösterilmez.

---

## 11. Definition of Ready

- Part type enumları net.
- Part status enumları net.
- Project relationship modeli hazır.
- Counter relationship modeli hazır.
- Progress öncelik kararı verilmiş veya açık karar olarak işaretlenmiş.
- Offline sync stratejisi belli.
- Security ve analytics kuralları hazır.

---

## 12. Definition of Done

- Part create/edit/delete çalışıyor.
- Part status ve progress çalışıyor.
- Part list project detail içinde görünüyor.
- Part-counter link çalışıyor.
- Offline create/update çalışıyor.
- Sync failure veri kaybettirmiyor.
- Owner isolation test edildi.
- Analytics PII içermiyor.
- Accessibility ve localization tamamlandı.
- Testler geçiyor.
