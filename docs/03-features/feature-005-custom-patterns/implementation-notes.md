# Feature-005 Custom Patterns — Implementation Notes

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/implementation-notes.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## 1. Mimari öneri

Flutter + Riverpod, uygulamanın diğer feature'larıyla aynı local DB ve sync engine üzerinde çalışmalıdır; teknoloji seçimi **kesinleşmiş değildir**. Önce Feature-004 `Pattern` kanonik modeline bakın; custom editor ayrı bir domain servisi olabilir fakat ikinci bağımsız pattern storage oluşturmayın.

```text
Presentation (PatternEditorScreen, BlockEditor, PreviewScreen)
      ↓
Application (PatternEditorController, DraftSaveCoordinator)
      ↓
Domain (ValidateCustomPattern, ReorderBlocks, ResolvePatternConflict)
      ↓
Repository (PatternRepository + PatternBlockRepository)
      ↓
Local transaction + shared sync queue → Feature-004 remote pattern store
```

## 2. Önerilen dosya yerleşimi

```text
lib/features/custom_patterns/
  domain/
    custom_pattern_policy.dart
    pattern_block.dart
    pattern_validation_service.dart
  application/
    pattern_editor_controller.dart
    auto_save_coordinator.dart
    create_custom_pattern_use_case.dart
    save_custom_pattern_use_case.dart
    duplicate_custom_pattern_use_case.dart
    reorder_pattern_blocks_use_case.dart
    resolve_pattern_conflict_use_case.dart
  presentation/
    custom_pattern_editor_screen.dart
    pattern_block_editor.dart
    pattern_preview_screen.dart
  data/
    custom_pattern_mapper.dart
  analytics/
    custom_pattern_analytics.dart
```

## 3. Kaydetme ve concurrency

- **Tek lokal transaction:** Pattern değişiklikleri + blok değişiklikleri + sync operation insert birlikte commit edilir.
- Auto-save: debounce önerilen başlangıç 400–800 ms, cihaz testleriyle ayarlanır; foreground'dan çıkış ve back navigation sırasında flush edilir.
- Editör iki paralel save başlatmaz; local version bazlı sıralama kullanır.
- Save hata verirse ekrandaki düzenleme buffer'ı korunur ve “Cihaza kaydedilemedi” gösterilir; false success yok.
- Tam içerik metnini her tuşta remote göndermekten kaçın; local dirty blocks takibi ve değişiklik gruplama uygula.

```text
onTextChanged(blockId, text)
  → update in-memory draft
  → markDirty(blockId)
  → debounce
  → atomic(local block upsert, pattern revision++, enqueue sync)
  → local-save acknowledgment
```

## 4. Domain API önerisi

```dart
abstract interface class CustomPatternRepository {
  Future<Pattern> createDraft({required String ownerId});
  Future<Pattern?> load({required String ownerId, required String patternId});
  Future<void> saveDraft({required String ownerId, required PatternDraftPatch patch});
  Future<void> moveBlock({required String ownerId, required String patternId, required String blockId, required int toIndex});
  Future<void> markReady({required String ownerId, required String patternId});
  Future<String> duplicate({required String ownerId, required String patternId});
  Future<void> softDelete({required String ownerId, required String patternId});
}
```

`PatternDraftPatch` yalnızca allowlist alanları değiştirebilir (`title`, `summary`, `craftType`, `difficulty`, notlar, blok değişiklikleri). `ownerId`, `sourceType`, `visibility` istemci patch'inden yazılamaz.

## 5. UI bileşenleri

- **Library Add Menu:** `Yeni özel tarif`.
- **Editor Header:** başlık, taslak/ready durumu, lokal/sync durumu, önizleme.
- **Block List:** tür, drag handle, text editor, add/copy/remove.
- **Detail Section:** teknik, zorluk, malzeme/gauge/beden notları.
- **Safe Exit:** kaydetme başarısızsa modal/inline uyarı ve retry.
- **Conflict Screen:** iki sürümün yan yana okunması veya manuel birleştirme; kör overwrite yok.

## 6. Feature-004 entegrasyon kontrol listesi

1. Feature-004 mevcut `Pattern` alanlarıyla mapping tablosu çıkar.
2. Custom kaynak türünü mevcut enum'a ekle; eski kayıtlar için migration yap.
3. Library filtrelerinde yalnızca `ownerId` + `sourceType=custom` + `deletedAt=null` göster.
4. Project link Feature-001/004'ün tek bağlantı tablosunu kullanır.
5. Medya yükleme aynı güvenlik/storage altyapısındadır.
6. Archive/delete durumları Library ile aynı tanımlarda çalışır.
7. Yeni tablo veya duplicate event yaratılmadan entegrasyon testi yaz.

## 7. Remote sync / conflict

`baseRemoteVersion` gönder ve backend CAS / transaction kullan. Aynı `operationId` yeniden uygulanmamalı. Blok bazlı birleştirme yalnızca değişiklikler birbirinden bağımsızsa denenebilir; aynı blok metnindeki çakışma için kullanıcı çözümü zorunlu. Önceki sürümleri çözüm bitene dek sakla.

## 8. Performans ve kalite

- 100 blok benchmark; uzun metinlerde liste virtualization/odak korunumu.
- Otomatik kayıt ve telemetry UI thread'i bloklamaz.
- Çok hızlı edit + reorder + app background testini hedefle.
- Türkçe metin, emoji, satır sonları, boş satırlar eksiksiz korunur.
- Clipboard ve export davranışı daha sonraki feature kararına bağlıdır.

## 9. Codex uygulama sırası

1. Feature-004 şemasını incele, tek kanonik pattern modelini doğrula.
2. Açık kararları ürün sahibine listele; varsayımsal API yazma.
3. Entity/migration + owner scoped repository oluştur.
4. Blok işlemlerini atomik transaction ile implemente et.
5. Auto-save coordinator ve unsaved feedback ekle.
6. Draft/ready validation, duplicate/archive/delete use case'lerini ekle.
7. Editor/preview/Library link UI entegrasyonu kur.
8. Sync CAS/idempotency/conflict path'lerini uygula.
9. Security, offline, recovery ve analytics privacy testlerini çalıştır.
10. Mevcut Feature-004 dosyalarını gereksiz değiştirme; gerekiyorsa kapsamı PR özetinde açıkça belirt.

## 10. Definition of Done

Tüm MUST gereksinimler test edilmiş; offline geri açma ve session kaybı senaryosu geçmiş; ownership sunucu tarafında doğrulanmış; kullanıcı içeriği telemetry'ye gitmiyor; Feature-004'te duplicate veri kaynağı yaratılmamış; açık ürün kararları onaylanmış.
