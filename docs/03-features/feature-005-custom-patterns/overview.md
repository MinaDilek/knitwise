# Feature-005 Custom Patterns — Overview

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/overview.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## Kullanıcı problemi

Örgü yapan kişiler kişisel tariflerini deftere, nota veya dağınık ekran görüntülerine kaydediyor. Tur/satır talimatlarını, beden varyantlarını, ip ve şiş bilgilerini değiştirdiklerinde güncel sürümün hangisi olduğunu anlamaları zorlaşıyor. Knitwise, **yapılandırılmış ama serbest yazıma izin veren** bir kişisel tarif editörü sunmalıdır.

## V1 değer önerisi

- Başlık, açıklama, zorluk ve örgü tekniği ile **özel tarif oluşturma**.
- Başlıklandırılmış, sıralı talimat blokları; metin ağırlıklı düzenleme.
- Taslağı kaydetme, yeniden açma, düzenleme, çoğaltma, arşivleme ve soft delete.
- Opsiyonel malzeme / gauge / beden açıklamaları ve kullanıcıya ait fotoğraf bağlantısı.
- Pattern Library içinde kendi tariflerini bulma, filtreleme ve projeye bağlama.
- Offline-first düzenleme, senkronizasyon durumu ve çakışma yönetimi.
- Not/komut metni kaybına karşı otomatik lokal kaydetme ve değişiklik uyarısı.

## V1 kapsam dışı

İşaret tablosu (chart) editörü, otomatik beden derecelendirme, AI tarif yazımı, tarif pazaryeri/yayınlama, ortak eşzamanlı editör, ölçülü PDF dizgi aracı, karmaşık telif yönetimi, otomatik video transkripsiyonu, yorum sistemi. Bunlar ayrı karar gerektirir.

## Temel kullanıcı yolculuğu

`Pattern Library → Yeni Tarif → Başlık/temel bilgiler → Talimat blokları → Yerel taslak → Kaydet → Kütüphanede görüntüle → Projeye bağla`

## Çekirdek tasarım ilkeleri

1. Serbest metin kaybolmaz; kullanıcı biçimlendirmesi korunur.
2. Kaydetme internet gerektirmez; arka plan sync başarısız olursa lokal veri tutulur.
3. Ham kullanıcı metni hiçbir analytics olayına yazılmaz.
4. Oluşturulan tarif **private** başlar; V1 public paylaşım yoktur.
5. Project link kaldırmak tarifin kendisini silmez.
6. Silme bir `deletedAt` alanıdır; `PatternStatus` değildir.

## Başlıca bağımlılıklar

Feature-001 Project Management, Feature-004 Pattern Library, Feature-017 Local Persistence, Feature-018 Cloud Sync; görsel depolama politikası Feature-004 ile ortaklaştırılır.

## Açık kararlar

| ID | Konu | V1 önerisi |
|---|---|---|
| CP-OD-001 | Ortak pattern entity | Feature-004 `Pattern` ile aynı kayıt; `sourceType=custom` |
| CP-OD-002 | Blok biçimi | Sıralı başlık/metin blokları; rich-text yerine güvenli düz metin |
| CP-OD-003 | Otomatik kaydetme | Lokal debounce + ekran kapanışında flush |
| CP-OD-004 | Varyant/beden | Açıklama alanı; otomatik hesaplama yok |
| CP-OD-005 | Sürüm geçmişi UI | V1 dışı; çakışma kurtarmaya yetecek revizyon bilgisi |
| CP-OD-006 | Duplicate | Yeni ID ile kullanıcı sahipliğinde kopya |

## Başarı göstergeleri

İlk özel tarif oluşturma, taslaktan kayda dönüşüm, tarife tekrar dönüş, projeye bağlama, lokal kayıt başarısızlığı, senkronizasyon çakışması. Ölçümlerin paydaları `analytics.md` içinde tariflenir.
