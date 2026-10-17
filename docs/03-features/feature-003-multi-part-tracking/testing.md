# Multi-Part Tracking — Testing

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/testing.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliği için test stratejisini ve test senaryolarını tanımlar.

---

## 2. Test Türleri

| Test Türü | Amaç |
|---|---|
| Unit Test | Validation, status policy, progress calculator |
| Use Case Test | Create/update/status/delete/reorder/link |
| Repository Test | Owner scope, query, soft delete |
| Widget Test | Part card, list, detail |
| Integration Test | Project detail entegrasyonu |
| Offline Test | Pending sync |
| Sync Test | Retry ve conflict |
| Security Test | Unauthorized access |
| Analytics Test | PII kontrolü |
| Accessibility Test | Labels, touch targets |
| Localization Test | Türkçe label ve karakter |

---

## 3. Test Data

### Minimum Part Fixture

```json
{
  "partId": "part_001",
  "ownerId": "user_001",
  "projectId": "project_001",
  "name": "Sağ Kol",
  "partType": "right_sleeve",
  "partStatus": "active",
  "progressPercent": 40,
  "partOrder": 1,
  "deletedAt": null,
  "syncStatus": "synced"
}
```

### Multi Part Fixture

```text
project_001
├── body status=completed progress=100
├── right_sleeve status=active progress=40
├── left_sleeve status=not_started progress=0
└── collar status=not_started progress=null
```

---

## 4. Unit Testleri

### MPT-TEST-UNIT-001 — Part Name Geçerli

Input `Sağ Kol` geçerlidir.

### MPT-TEST-UNIT-002 — Boş Part Name Reddedilir

Trim sonrası boş değer validation failure döndürür.

### MPT-TEST-UNIT-003 — Uzun Name Reddedilir

80 karakter üstü name reddedilir.

### MPT-TEST-UNIT-004 — Part Type Parse Edilir

`right_sleeve` enum olarak parse edilir.

### MPT-TEST-UNIT-005 — Unknown Part Type Crash Üretmez

Unknown value safe failure üretir.

### MPT-TEST-UNIT-006 — Progress 0 Geçerli

### MPT-TEST-UNIT-007 — Progress 100 Geçerli

### MPT-TEST-UNIT-008 — Progress Negatif Reddedilir

### MPT-TEST-UNIT-009 — Progress 100 Üzeri Reddedilir

---

## 5. Use Case Testleri

### MPT-TEST-UC-001 — Part Oluşturulur

Given user project sahibidir, valid input ile part oluşturulur.

Expected:

- Local kayıt oluşur.
- `projectId` atanır.
- Sync pending oluşur.

### MPT-TEST-UC-002 — Unauthorized Project’e Part Oluşturulamaz

Başka user project’i için part create reddedilir.

### MPT-TEST-UC-003 — Part Status Completed Yapılır

Active part completed olur ve `completedAt` set edilir.

### MPT-TEST-UC-004 — Completed Part Reopen

Completed part active yapılabilir.

### MPT-TEST-UC-005 — Progress Update

Progress 40’tan 60’a güncellenir.

### MPT-TEST-UC-006 — Part Soft Delete

`deletedAt` dolar ve normal listeden çıkar.

### MPT-TEST-UC-007 — Reorder Parts

Part order listesi transaction içinde güncellenir.

### MPT-TEST-UC-008 — Counter Link

Aynı project içindeki counter part’a bağlanır.

### MPT-TEST-UC-009 — Counter Unlink

Link kaldırılır, counter silinmez.

---

## 6. Repository Testleri

- Owner scope ile part get.
- Başka user part dönmez.
- Project part list deleted filtreler.
- Reorder sadece aynı project içinde çalışır.
- Linked counter list doğru döner.
- Pending sync query owner scope içerir.

---

## 7. Widget Testleri

- Empty parts state görünür.
- Part card name/status/progress gösterir.
- Status badge text içerir.
- Add Part button görünür.
- Progress indicator görünür.
- Broken counter state crash üretmez.
- Delete confirmation açılır.

---

## 8. Integration Testleri

### MPT-TEST-INT-001 — Project Detail İçinden Part Oluşturma

Part oluşturulur ve Project Detail içinde görünür.

### MPT-TEST-INT-002 — Part Detail Açma

Part card tıklanınca Part Detail açılır.

### MPT-TEST-INT-003 — Status Değişikliği Project Progress’i Günceller

Part completed yapılınca project progress recalculation tetiklenir.

### MPT-TEST-INT-004 — Counter Link Part Detail’de Görünür

Counter bağlandıktan sonra Part Detail linked counter gösterir.

---

## 9. Offline Testleri

- Offline part create local kaydedilir.
- Offline status update pending olur.
- Offline reorder pending olur.
- App restart sonrası local part korunur.
- Online olunca sync tamamlanır.
- Sync failed data kaybettirmez.

---

## 10. Sync ve Conflict Testleri

- Pending part sync başarılı.
- Sync failure local data korur.
- Retry attempt artar.
- Conflict detected.
- Use local conflict çözümü.
- Use remote conflict çözümü.
- Remote deleted local update sessiz silinmez.

---

## 11. Security Testleri

- User kendi part’ını okur.
- Başka user part’ını okuyamaz.
- Başka user part’ını update edemez.
- Başka user project’ine part oluşturamaz.
- Başka user counter’ı link edilemez.
- Deleted part update edilemez.
- Analytics part name ve notes içermez.

---

## 12. Analytics Testleri

- `part_create_started` gider.
- `part_create_completed` gider.
- `part_status_changed` gider.
- `part_progress_updated` gider.
- `part_counter_linked` gider.
- Part name payload’da bulunmaz.
- Notes payload’da bulunmaz.
- Progress bucket kullanılır.

---

## 13. Accessibility Testleri

- Part card screen reader label içerir.
- Status sadece renk ile anlatılmaz.
- Add/Edit/Delete touch target uygun.
- Dynamic text taşma yapmaz.
- Dialog focus doğru.

---

## 14. Localization Testleri

- Part type label Türkçe görünür.
- Status label Türkçe görünür.
- Türkçe karakterli part name bozulmaz.
- Hard-coded user text yoktur.

---

## 15. Acceptance Testleri

### MPT-TEST-ACC-001 — Kullanıcı İlk Part’ı Oluşturur

Project detail içinde Add Part ile part oluşturur ve listede görür.

### MPT-TEST-ACC-002 — Kullanıcı Part’ı Completed Yapar

Part completed olur ve progress etkilenir.

### MPT-TEST-ACC-003 — Kullanıcı Part’a Counter Bağlar

Counter link başarılıdır.

### MPT-TEST-ACC-004 — Offline Part Oluşturur

Offline create local kaydedilir ve sync pending olur.

### MPT-TEST-ACC-005 — Unauthorized Access Engellenir

Başka user part data gösterilmez.

---

## 16. Regression Checklist

- [ ] Part create çalışıyor.
- [ ] Part list görünüyor.
- [ ] Part detail açılıyor.
- [ ] Part edit çalışıyor.
- [ ] Status change çalışıyor.
- [ ] Progress update çalışıyor.
- [ ] Reorder çalışıyor.
- [ ] Counter link/unlink çalışıyor.
- [ ] Soft delete listeden kaldırıyor.
- [ ] Offline create/update çalışıyor.
- [ ] Sync failure data kaybettirmiyor.
- [ ] Unauthorized access engelleniyor.
- [ ] Analytics PII içermiyor.
- [ ] Accessibility smoke geçiyor.
- [ ] Localization smoke geçiyor.

---

## 17. Release Exit Criteria

- Unit testler geçmeli.
- Use case testleri geçmeli.
- Repository owner scope testleri geçmeli.
- Offline/sync testleri geçmeli.
- Security testleri geçmeli.
- Analytics privacy testleri geçmeli.
- Acceptance testleri geçmeli.
- Product Owner kabul vermeli.

---

## 18. Feature-003 Dosya Seti Durumu

```text
03-features/feature-003-multi-part-tracking/
├── overview.md
├── requirements.md
├── flows.md
├── data-model.md
├── analytics.md
├── security.md
├── implementation-notes.md
└── testing.md
```
