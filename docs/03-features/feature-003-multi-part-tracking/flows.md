# Multi-Part Tracking — Flows

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/flows.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliğinin kullanıcı akışlarını tanımlar.

---

## 2. İlgili Ekranlar

| Ekran / Bileşen | Açıklama |
|---|---|
| Project Detail Parts Section | Project içindeki part özeti |
| Part List Screen | Project part listesi |
| Part Detail Screen | Tek part’ın detay ekranı |
| Create Part Sheet | Hızlı part oluşturma |
| Edit Part Screen | Part düzenleme |
| Part Status Menu | Status değişimi |
| Part Reorder UI | Sıralama |
| Link Counter Sheet | Counter bağlama |
| Broken Counter State | Missing counter durumu |

---

## FLOW-MPT-001 — İlk Part Oluşturma

### Ana Akış

1. Kullanıcı Project Detail ekranına girer.
2. Parts section empty state gösterir.
3. Kullanıcı `Add Part` seçer.
4. Create Part Sheet açılır.
5. Sistem `projectId` değerini otomatik alır.
6. Kullanıcı part name girer.
7. Kullanıcı part type seçer.
8. Sistem default status `not_started` atar.
9. Kullanıcı save eder.
10. Sistem validation yapar.
11. Sistem part’ı lokal kaydeder.
12. Sync operation oluşturulur.
13. Part project detail içinde görünür.

### Hatalar

- Name boşsa validasyon.
- Project unauthorized ise kayıt yapılmaz.
- Local save failed ise input korunur.

---

## FLOW-MPT-002 — Suggested Parts ile Oluşturma

### Ana Akış

1. Kullanıcı Add Part ekranında suggested template görür.
2. Project category’ye göre öneriler listelenir.
3. Kullanıcı bir veya birden fazla öneriyi seçer.
4. Sistem partları sıralı şekilde oluşturur.
5. Parts section güncellenir.

### Örnek

```text
Sweater → Front, Back, Right Sleeve, Left Sleeve, Collar
```

---

## FLOW-MPT-003 — Part Detail Açma

1. Kullanıcı Project Detail içinden part seçer.
2. Sistem `partId + ownerId` ile part yükler.
3. Linked counterlar yüklenir.
4. Part detail açılır.
5. Status, progress ve linked counter bilgisi gösterilir.

---

## FLOW-MPT-004 — Part Status Değiştirme

1. Kullanıcı Part Detail içinde status menüsünü açar.
2. Yeni status seçer.
3. Sistem geçişin geçerli olup olmadığını kontrol eder.
4. Local kayıt güncellenir.
5. Sync pending oluşur.
6. Project progress yeniden hesaplanır.

Status değerleri:

```text
not_started
active
paused
completed
blocked
```

---

## FLOW-MPT-005 — Part Completed Yapma

1. Kullanıcı part action menüsünden `Mark Completed` seçer.
2. Sistem confirmation gerekip gerekmediğine karar verir.
3. `partStatus = completed` yapılır.
4. `completedAt` set edilir.
5. Progress 100’e çekilebilir, ürün kararına bağlıdır.
6. Project progress güncellenir.

---

## FLOW-MPT-006 — Completed Part Reopen

1. Kullanıcı completed part detail açar.
2. `Reopen` seçer.
3. Sistem part status değerini `active` yapar.
4. `completedAt` temizlenebilir veya history’de korunabilir.
5. Sync operation oluşur.

---

## FLOW-MPT-007 — Part Progress Güncelleme

1. Kullanıcı part edit veya quick progress action açar.
2. Progress value girer.
3. Sistem 0-100 validasyonu yapar.
4. Local kayıt güncellenir.
5. Project progress yeniden hesaplanır.

---

## FLOW-MPT-008 — Counter Bağlama

1. Kullanıcı Part Detail ekranında `Link Counter` seçer.
2. Sistem aynı project’e ait counterları listeler.
3. Kullanıcı counter seçer.
4. Sistem owner/project doğrulaması yapar.
5. Link oluşturulur.
6. Part detail linked counter gösterir.

---

## FLOW-MPT-009 — Counter Unlink

1. Kullanıcı linked counter alanından unlink seçer.
2. Sistem confirmation isteyebilir.
3. Relationship kaldırılır.
4. Counter silinmez.
5. Part detail güncellenir.

---

## FLOW-MPT-010 — Linked Counter Missing

1. Part detail açılır.
2. Sistem linked counter’ı bulamaz.
3. Part detail açılmaya devam eder.
4. Broken counter state gösterilir.
5. Kullanıcı unlink veya replace seçebilir.

---

## FLOW-MPT-011 — Part Reorder

1. Kullanıcı Part List ekranında reorder mode açar.
2. Part’ları sürükleyerek sıralar.
3. Sistem `partOrder` değerlerini günceller.
4. Local transaction çalışır.
5. Sync pending oluşur.

---

## FLOW-MPT-012 — Part Edit

1. Kullanıcı Part Detail içinden edit seçer.
2. Mevcut bilgiler forma doldurulur.
3. Kullanıcı name/type/status/progress/notes değiştirir.
4. Save eder.
5. Validation çalışır.
6. Local kayıt güncellenir.
7. Sync operation oluşur.

---

## FLOW-MPT-013 — Part Soft Delete

1. Kullanıcı Part Detail action menu açar.
2. Delete seçer.
3. Confirmation dialog gösterilir.
4. Kullanıcı onaylar.
5. `deletedAt` set edilir.
6. Part normal listeden çıkar.
7. Counter linkleri korunur ama project context’te görünmez.
8. Sync operation oluşur.

---

## FLOW-MPT-014 — Project Soft Delete Etkisi

1. Project soft delete edilir.
2. Bağlı partlar normal project context’te görünmez.
3. Partlar permanent delete edilmez.
4. Project recover edilirse partlar tekrar görünür.

---

## FLOW-MPT-015 — Offline Part Create

1. Cihaz offline.
2. Kullanıcı part oluşturur.
3. Part local kaydedilir.
4. `syncStatus = pending`.
5. Online olduğunda sync edilir.

---

## FLOW-MPT-016 — Offline Part Update

1. Cihaz offline.
2. Kullanıcı part status veya progress değiştirir.
3. Local kayıt güncellenir.
4. Sync operation pending olur.

---

## FLOW-MPT-017 — Sync Conflict

1. Aynı part iki cihazda güncellenir.
2. Sync conflict tespit eder.
3. `syncStatus = conflict`.
4. Kullanıcı local/remote/manual seçebilir.
5. Corrective operation oluşturulur.

---

## FLOW-MPT-018 — Project Progress Update

1. Part status/progress değişir.
2. Sistem project progress calculator çalıştırır.
3. Parts progress varsa project progress buna göre güncellenir.
4. Project detail progress display yenilenir.

---

## Flow ve Requirement Eşleştirmesi

| Flow | Requirements |
|---|---|
| FLOW-MPT-001 | MPT-FR-001, MPT-FR-002, MPT-FR-003 |
| FLOW-MPT-002 | MPT-FR-025 |
| FLOW-MPT-003 | MPT-FR-009 |
| FLOW-MPT-004 | MPT-FR-005 |
| FLOW-MPT-005 | MPT-FR-012 |
| FLOW-MPT-006 | MPT-FR-013 |
| FLOW-MPT-007 | MPT-FR-006 |
| FLOW-MPT-008 | MPT-FR-016 |
| FLOW-MPT-009 | MPT-FR-017 |
| FLOW-MPT-010 | MPT-FR-022 |
| FLOW-MPT-011 | MPT-FR-011 |
| FLOW-MPT-012 | MPT-FR-010 |
| FLOW-MPT-013 | MPT-FR-014 |
| FLOW-MPT-015 | MPT-FR-019 |
| FLOW-MPT-016 | MPT-FR-020 |
| FLOW-MPT-018 | MPT-FR-018 |

---

## Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/data-model.md
```
