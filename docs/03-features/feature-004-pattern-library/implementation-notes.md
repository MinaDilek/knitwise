# Pattern Library — Implementation Notes

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Mimari
**Öneri (kesin teknoloji kararı değil):** Flutter UI + Riverpod application layer + ortak local DB (Feature-017) + ortak outbox/sync engine (Feature-018) + private object storage; cloud backend merkezi mimari kararına uymalı. Feature-001 `ProjectPatternLink` tekrar yaratılmaz.

```text
presentation (library/list/detail/import/preview/picker)
    ↓
application (use-cases + controller + state)
    ↓
domain (Pattern, PatternAsset, validation/policies)
    ↓
repositories (owner-scoped)
    ↓
local DB + private asset store + transactional outbox
    ↓
sync adapter + authenticated remote DB/object storage
```

## 2. Önerilen modül yapısı
```text
lib/features/pattern_library/
  domain/entities/{pattern,pattern_asset}.dart
  domain/services/{pattern_validation,asset_policy}.dart
  application/use_cases/{import_pattern,save_link_pattern,unlink_pattern,delete_pattern,open_pattern_asset}.dart
  application/controllers/{pattern_list,pattern_detail,pattern_import}.dart
  data/repositories/{pattern_repository_impl,asset_repository_impl}.dart
  data/datasources/{pattern_local,pattern_remote,private_asset_store}.dart
  presentation/screens/{pattern_library,pattern_detail,pattern_import}.dart
  presentation/widgets/{pattern_card,asset_status,preview_state,pattern_picker}.dart
  analytics/pattern_analytics.dart
```

## 3. Contract / API
```dart
abstract interface class PatternRepository {
  Future<Pattern?> getById({required String ownerId, required String patternId});
  Future<List<Pattern>> search({required String ownerId, required PatternFilter filter});
  Future<Pattern> createLocal({required String ownerId, required PatternDraft draft});
  Future<Pattern> updateMetadata({required String ownerId, required String patternId, required PatternPatch patch});
  Future<void> softDelete({required String ownerId, required String patternId});
}
abstract interface class PatternAssetStore {
  Future<StagedAsset> stageAndValidate(SelectedFile selected);
  Future<PrivateAssetRef> promote(StagedAsset staged, String assetId);
  Future<AssetAvailability> availability(String ownerId, String assetId);
  Future<void> purgeOrphanedStages();
}
```
Bu imzalar referans niteliğinde olup Flutter sürümü, error type ve storage platformuyla uyarlanır. `ownerId` parametresi güvenli server auth yerine geçmez.

## 4. Import state machine
`idle → selecting → validating → staging → saving_local → available_local → pending_sync → synced`; hata `failed` ve açık retry yoluna geçer. Kullanıcı seçiciden dönerken form kapatılırsa geçici asset temizlenir.

**Kritik işlem sırası:** (1) picker, (2) safe validation, (3) app-private staged copy, (4) local metadata+asset+outbox atomic commit veya journal, (5) promote file, (6) emit UI success, (7) background sync. Cross-resource rollback mümkün değilse restart recovery orphan cleanup sağlar.

## 5. Sync stratejisi
- Metadata versioned upsert; remote `version` conflict dönerse `conflict` state ve resolution UI.
- Asset upload idempotency with stable asset ID / object key, checksum verification, retry backoff, resumable option supported ise.
- `pending` → `synced` yalnız ilgili metadata/asset remote durumuyla tutarlıysa; kısmi başarıda detail hem local available hem upload pending gösterebilir.
- File upload başarısız olursa locally available asset çalışır.
- Offline başka cihazda hiç cache yoksa asset unavailable state kabul edilir; download başarı gibi gösterilmez.

## 6. Search / indexing
V1 metadata search: normalized title, tags, category/craft. Raw PDF OCR/indexing yok. Arama kullanıcı içeriğini analytics veya üçüncü taraf arama servisine göndermez. Search debounce ve indexed local query kullanılır.

## 7. Preview
- PDF progressive/page renderer; external action links otomatik çalışmaz.
- Image preview bounds/thumbnail ile; çok büyük görsel memory-safe decode.
- URL kaydı platform browser'ında explicit user action ile açılır.
- Viewer'dan çıkınca memory resources dispose edilir.

## 8. Link entegrasyonu
Feature-001 `ProjectPatternLink` repository üzerinden `link(projectId,patternId)`; her iki record owner/project status policy doğrulanır. UI PatternPicker sadece uygun ve aktif kayıtları gösterir. Duplicate link engellenir. Unlink pattern'i silmez.

## 9. State ve UI
`PatternListController`: search/filter/sort/pagination; `PatternImportController`: staged progress + cancel; `PatternDetailController`: metadata + preview availability + sync status. Hata/success state ayrık; optimistic metadata update local persistence başarısızsa geri alınır.

## 10. Temizleme ve migration
Orphaned staged dosyalar startup/background janitor ile cleanup. `deletedAt` retention merkezi policy; cloud object silme yetkisi owner doğrulanarak ve referans bütünlüğüne bakılarak uygulanır. Metadata DB migration ve asset dir migration ayrı test edilir.

## 11. Error code sözleşmesi
`pattern_not_found`, `unauthorized`, `invalid_title`, `unsupported_asset_type`, `asset_too_large`, `corrupt_asset`, `permission_denied`, `insufficient_storage`, `asset_offline_unavailable`, `asset_missing`, `local_save_failed`, `sync_failed`, `sync_conflict`.

## 12. Kodlama sırası
1. Açık ürün kararlarını kapat; kaynak tipleri ve storage mimarisini teyit et.
2. Typed entity/enums + migrations + owner-scoped repositories.
3. Asset stage/validate/promote + recovery cleanup.
4. Create/edit/delete/search use-case testleri.
5. PDF/image viewer, offline availability states.
6. Feature-001 link picker integration.
7. Outbox metadata+asset sync, idempotency/conflict.
8. Privacy-safe analytics, A11Y/localization, E2E/security tests.

## 13. Codex uyarıları
- Dosyaları doğrudan Downloads path'inden açmaya devam etme.
- Public storage bucket veya permanent public URL kullanma.
- Data modelde `ProjectPatternLink` duplicasyonu yapma.
- Hata durumunda user data'yı silme.
- Bilinmeyen limit veya teknoloji kararını uydurma.
- Kullanıcı metinlerini analytics veya error logs'a ekleme.
- Cloud-only file offline erişilir iddiasında bulunma.
