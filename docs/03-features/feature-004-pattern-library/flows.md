# Pattern Library — Flows

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Ekranlar
`PatternLibraryScreen` → `PatternImportSheet` → `PatternMetadataForm` → `PatternDetailScreen` → `PatternPreviewScreen`; ayrıca `PatternPickerSheet` (Project Detail), `LinkProjectSheet`, `DeleteConfirmDialog`, `AssetUnavailableState`, `SyncConflictState`.

## FLOW-PL-001 — PDF import
**Önkoşul:** kullanıcı oturum sahibi. **Ana yol:**
1. Library > Add > Import PDF; sistem seçicisi açılır.
2. Kullanıcı dosya seçer; app geçici kopya alır ve dosyanın signature/MIME/limit kurallarını doğrular.
3. Form varsayılan başlık önerir; kullanıcı düzenler, etiket ve kaynak bilgisi ekler.
4. Create sonrası tek yerel transaction: `Pattern`, `PatternAsset`, `SyncOutbox` metadata kaydedilir; kalıcı dosya staging'den taşınır.
5. Liste güncellenir, `assetStatus=local_available`; cloud varsa upload arka plandadır.
**Alternatif:** seçim iptal → kayıt yok; unsupported/corrupt → hata + farklı dosya; disk full → durumu açıkla, partial cleanup; app kapanırsa recovery job geçici kopyaları temizler.

## FLOW-PL-002 — Görsel import
1. Add > Image > permission-mediated photo/file picker.
2. Dosya tip/size kontrol; gerekirse thumbnail üretimi iş parçacığında.
3. Başlık eklenir; kayıt oluşturulur, offline açılabilir.
**Hatalar:** revoked permission, malformed image, unsupported HEIC (platform desteği yoksa); kullanıcıya desteklenen tipler gösterilir.

## FLOW-PL-003 — URL referansı
1. Add > Link, kullanıcı `https://` bağlantısı ve başlık yazar.
2. App şema/sözdizimi doğrular; URL'yi sunucu tarafında fetch etmez.
3. Pattern `sourceType=url` kaydedilir, detail'de dış bağlantı açma onayı verilir.
4. Bağlantı artık çalışmıyorsa kullanıcı linki düzenler; metadata korunur.

## FLOW-PL-004 — Liste, arama, filtre
1. Library açılır; yalnızca owner ve aktif kayıtlar listelenir.
2. Kullanıcı başlığa veya etikete göre arar; craft/category/favorite filtresi uygular.
3. Arama boşsa empty results; henüz kayıt yoksa empty library; yükleme hatasında retry.
4. Son seçilen filtreler ürün kararıyla bellekte tutulabilir; sensitive query analytics'e gitmez.

## FLOW-PL-005 — Detail ve preview
1. Pattern kartı seçilir; metadata gösterilir.
2. `sourceType=pdf/image` ve local asset varsa viewer açılır.
3. Yalnızca cloud asset varsa ve online ise authenticated download → private cache → preview.
4. Offline ve uncached ise `offline_unavailable`; broken file ise `asset_error` ve retry.
5. `sourceType=url` ise güvenli dış açma akışı. Web sayfası embed edilmez.

## FLOW-PL-006 — Metadata düzenleme
1. Edit → başlık, türden bağımsız metadata, tag/favorite/notes güncellenir.
2. Validate → local transaction → outbox pending → detay güncellenir.
3. Sync hatasında düzenleme lokal kalır; conflict varsa kullanıcıya seçim sunulur.

## FLOW-PL-007 — Project’e link
1. Project Detail > Link Pattern → owner'a ait aktif pattern picker.
2. Pattern seçilir → Feature-001 `ProjectPatternLink` idempotent ilişki yazılır.
3. Aynı link ikinci kez eklenmez; pattern başka projelerde de seçilebilir.
4. Unlink yalnızca bağlantıyı kaldırır, `Pattern` ve asset silinmez.
**Edge:** proje archived/deleted ise ProjectStatusPolicy'ye uyulmalı.

## FLOW-PL-008 — Soft delete
1. Pattern Detail > Delete > onay.
2. `deletedAt` işaretlenir, listeden ve picker'dan çıkar; ilişkiler güvenli şekilde gizlenir.
3. Lokal asset temizliği ancak retention/sync stratejisine göre ve geri alınamaz silme kararıyla yapılır.
4. İptalde hiçbir veri değişmez.

## FLOW-PL-009 — Offline çalışma / sync
1. Offline import metadata ve asset yerelde tutulur; `pending` outbox oluşturulur.
2. Bağlantı gelince auth check → metadata sync ve gerekirse asset upload → remote ref.
3. Retry aynı operation ID ile yapılır; hata local asset'i silmez.
4. İki cihaz edit çakışırsa sessiz LWW yerine version conflict veya güvenli field merge uygulanır.

## FLOW-PL-010 — İzin / depolama hatası
- Seçici izni reddedilirse neyin eksik olduğu açıklanır; ayarlara yönlendirme opsiyonel.
- Kaynak dosya okunamazsa temp kayıt temizlenir.
- Private cache bozulursa asset `failed` olur; varsa cloud'dan tekrar indirilir.
- Kullanıcı başka hesaba geçince önceki hesabın private cache'i yeni hesapta görünmez.

## 2. Requirement izleme
| Akış | Gereksinimler |
|---|---|
| 001-003 | PL-FR-001..005, 012..014, 026, 030 |
| 004 | PL-FR-007..009 |
| 005 | PL-FR-010, 011, 024 |
| 006 | PL-FR-006, 020, 021 |
| 007 | PL-FR-015..017 |
| 008 | PL-FR-018, 019 |
| 009 | PL-FR-020..023 |
| 010 | PL-FR-013, 024, 029, 030 |

## 3. Açık UX kararları
Multi-asset preview, pattern text source, PDF sayfa hatırlama, harici bağlantı uyarısının sıklığı ve bulk import ürün onayı bekler.
