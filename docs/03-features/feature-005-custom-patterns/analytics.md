# Feature-005 Custom Patterns — Analytics

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/analytics.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## 1. Ölçüm hedefi

Kullanıcı kendi tarifini oluşturabiliyor, düzenlemeye geri geliyor, projeye bağlayabiliyor mu? Analitikler **içerik toplamadan** bu soruları yanıtlamalıdır.

## 2. Olay sözlüğü

| Event | Tetikleyici | İzin verilen event-specific alanlar |
|---|---|---|
| `custom_pattern_create_started` | Yeni tarif açıldı | `sourceScreen` |
| `custom_pattern_draft_saved` | İlk lokal taslak kaydı başarılı | `isOffline`, `blockCountBucket` |
| `custom_pattern_ready_saved` | Status `ready` oldu | `blockCountBucket`, `hasMaterials`, `hasMedia` |
| `custom_pattern_create_failed` | Kayıt başarısız | `errorCode`, `stage` |
| `custom_pattern_editor_opened` | Editör açıldı | `status`, `blockCountBucket` |
| `custom_pattern_block_added` | Blok ekleme | `blockType` |
| `custom_pattern_blocks_reordered` | Sıra değişti | `blockCountBucket` |
| `custom_pattern_preview_opened` | Önizleme | `status` |
| `custom_pattern_project_linked` | Proje bağı | `sourceScreen` |
| `custom_pattern_duplicated` | Kopya oluşturuldu | `blockCountBucket` |
| `custom_pattern_archived` | Arşivlendi | `previousStatus` |
| `custom_pattern_deleted` | Soft delete | `previousStatus` |
| `custom_pattern_sync_failed` | Sync hatası | `errorCode`, `attemptBucket` |
| `custom_pattern_conflict_detected` | Revizyon çakışması | `conflictScope` (`block/metadata/delete`) |
| `custom_pattern_conflict_resolved` | Seçim tamamlandı | `resolution` (`local/remote/manual`) |

`custom_pattern_block_added` yüksek frekanslı olabilir; V1'de session aggregate veya sample önerilir, zorunlu ham event değildir.

## 3. Genel payload allowlist

`featureId`, `eventName`, `sourceScreen`, `status`, `isOffline`, `syncStatus`, `blockType`, `blockCountBucket`, `hasMaterials`, `hasMedia`, `errorCode`, `durationBucket`, `timestamp`.

YASAK: `title`, `summary`, `block.text`, `materialsNote`, `gaugeNote`, `sizeNote`, dosya yolu, fotoğraf URI, proje/pattern adı, auth token, rastgele ek metadata. Kimlik korelasyonu gerekiyorsa privacy onaylı ve dönüştürülmüş opaque ID kullanılmalı; varsayılan eventlerde ID yoktur.

## 4. Funnel ve tanımlar

**Oluşturma:** `create_started → draft_saved → ready_saved`. Aynı kullanıcı ve başlatılan create instance için eşleştirilir; taslak sayısı yerine kullanıcı oranı da raporlanır.

**Kullanım:** `ready_saved → editor_opened (sonraki gün) → project_linked`.

**Güvenilirlik:** `draft_saved (isOffline=true) → sync ack`; yalnızca güvenli sistem metriği olarak, ham içerik yok.

### KPI

| KPI | Pay | Payda | Segment |
|---|---|---|---|
| İlk custom tarif aktivasyonu | İlk başarılı `draft_saved` yapan kullanıcı | Feature ekranını açan tekil kullanıcı | yeni/mevcut |
| Hazır tarife dönüşüm | `ready_saved` olan oluşturma akışları | `create_started` akışları | online/offline |
| Editöre geri dönüş | 7 günde tekrar açılan custom tarif kullanıcıları | İlk hafta tarif kaydedenler | craft type |
| Projeye bağlama | En az 1 bağı olan özel tarif | Toplam `ready` özel tarif | — |
| Lokal kayıt hata oranı | Başarısız lokal kayıt | Tüm lokal save denemeleri | platform |
| Conflict oranı | Conflict sonuçlanan sync | Tüm sync denemeleri | platform |

## 5. Bucket ve operasyonel kurallar

Blok sayısı: `0`, `1`, `2_5`, `6_20`, `21_100`, `101_plus`. Süre: `lt_1s`, `1_5s`, `5_30s`, `30s_plus`. Analytics gönderimi save işlemini bekletmez. Offline kuyruğu üzerinde consent ve saklama süresi politikası Feature-017/018 ve uygulama gizlilik politikasına tabidir.

## 6. Testler

- Event registry yalnızca allowlist alanlarını kabul eder.
- `title` veya Türkçe kişisel talimat string'i serializer üzerinden asla çıkmaz.
- Offline event flush tekrar denemelerinde duplicate sayım önlenir.
- Draft save başarısızsa başarılı event atılmaz.
- Feature-004 pattern analytics olaylarıyla aynı aksiyon iki kez sayılmaz; event ownership tanımlıdır.
