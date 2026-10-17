# Pattern Library — Analytics

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Hedef
Kütüphane aktivasyonu, dosya import güvenilirliği, proje bağlama, tekrar ziyaret ve offline erişilebilirlik ölçülür. **Dosya başlığı, notlar, tag metinleri, tam URL, dosya adı, file URI, PDF metni ve içerik hash'i analytics'e gönderilmez.** Analytics devre dışı olsa da ürün çalışır.

## 2. Event sözleşmesi
| Event | Tetikleyici | İzin verilen alanlar |
|---|---|---|
| `pattern_library_viewed` | library ekranı | `countBucket`, `hasFilters` |
| `pattern_import_started` | seçici açılması | `sourceType`, `sourceScreen` |
| `pattern_import_completed` | durable local commit | `sourceType`, `isOffline`, `fileSizeBucket`, `durationBucket` |
| `pattern_import_failed` | input/storage failure | `sourceType`, `errorCode`, `stage` |
| `pattern_preview_opened` | erişilebilir preview | `sourceType`, `accessMode` (`local`,`downloaded`) |
| `pattern_preview_failed` | görüntüleyici hatası | `sourceType`, `errorCode`, `isOffline` |
| `pattern_search_used` | filtre/arama | `filterKinds`, `resultCountBucket`, **query yok** |
| `pattern_favorite_changed` | toggled | `favoriteState` |
| `pattern_metadata_updated` | başarı | `changedFieldKinds` (değerler yok) |
| `pattern_project_linked` | Feature-001 link kaydı | `sourceScreen`, `projectLinkCountBucket` |
| `pattern_project_unlinked` | link kaldırma | `sourceScreen` |
| `pattern_deleted` | soft delete | `sourceType` |
| `pattern_sync_failed` | sync failure | `errorCode`, `operationType` |
| `pattern_asset_offline_unavailable` | uncached asset | `sourceType` |
| `pattern_conflict_detected` | version conflict | `fieldKinds` (değerler yok) |

## 3. Standartlar
- Event ve keyler `snake_case`; şema sabit ve versiyonlu.
- `sourceType` allowlist; `errorCode` allowlist: `invalid_type`, `invalid_size`, `corrupt_file`, `permission_denied`, `no_space`, `network`, `auth`, `storage`, `conflict`, `unknown`.
- `fileSizeBucket`: `0_1mb`, `1_5mb`, `5_20mb`, `20mb_plus`, `unknown`. Bu bucketlar kota kararı değildir.
- `countBucket`: `0`, `1`, `2_5`, `6_20`, `21_100`, `101_plus`.
- IP, cihaz tanımlayıcıları ve user ID analytics sağlayıcısının merkezi privacy ayarlarına tabidir; dosya içinde arbitrary hash'le tanımlayıcı yaratılmamalı.

## 4. Funnel ve KPI
1. `pattern_library_viewed` → `pattern_import_started` → `pattern_import_completed` → `pattern_preview_opened`: first import activation.
2. `pattern_import_completed` → `pattern_project_linked`: library-to-project value.
3. Import success rate = completed / (completed + failed) — kullanıcı iptalleri paydaya alınmaz.
4. Offline open success = local preview opened / eligible offline attempts; uncached remote asset ayrı cohort.
5. Sync failure ve recovery oranları gün ve operation type bazında ölçülür.
6. 7/30-gün library return rate ancak kullanıcı rızası ve merkezi ürün analitiği koşullarında.

## 5. Dedup ve offline analytics
`eventId` ile de-dup; import tamamlandı ancak event delivery retry ederse event iki kez sayılmamalı. Offline telemetry varsa minimum retention, bounded queue ve kullanıcı opt-out uygulanır. App privacy settings Analytics kapalıysa event üretimi durur.

## 6. Testler
- File seçici cancel → `completed` gitmez.
- `pattern_import_completed` yalnız durable local commit sonrası.
- Notes/title/sourceUrl/localUri/remoteObjectKey/sha256 gizlilik testinde reddedilir.
- Metadata search telemetry query içermez.
- Her event allowlist schema ve unknown key fail testine tabi.
- Analytics transport failure import akışını engellemez.

## 7. Açık kararlar
`PL-AN-001` analytics sağlayıcı, `PL-AN-002` opt-in/out ve retention, `PL-AN-003` event rate limiting. Merkezi analytics standardı ile onaylanmalı.
