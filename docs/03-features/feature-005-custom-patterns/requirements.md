# Feature-005 Custom Patterns — Requirements

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/requirements.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## Gereksinim sözlüğü

`CP-FR` fonksiyonel, `CP-BR` iş kuralı, `CP-VR` doğrulama, `CP-NFR` kalite, `CP-AC` kabul kriteri.

## Fonksiyonel gereksinimler

| ID | Öncelik | Gereksinim |
|---|---|---|
| CP-FR-001 | MUST | Giriş yapmış kullanıcı kendi hesabında özel tarif taslağı açabilir. |
| CP-FR-002 | MUST | Yeni tarif `sourceType=custom`, `visibility=private` olarak başlar. |
| CP-FR-003 | MUST | Başlık ve opsiyonel açıklama yazılabilir. |
| CP-FR-004 | MUST | Bir veya birden çok talimat bloğu eklenebilir. |
| CP-FR-005 | MUST | Blok için tür (`heading`, `instruction`, `note`), metin ve sıra tutulur. |
| CP-FR-006 | MUST | Blokların sırası değiştirilebilir; sonuç yerel olarak atomik kaydedilir. |
| CP-FR-007 | MUST | Blok düzenlenebilir ve silinebilir; silme işlemi kullanıcı tarafından geri alınabilir veya onaylanır. |
| CP-FR-008 | MUST | Kapat/aç senaryosunda son başarılı lokal kaydedilmiş taslak geri yüklenir. |
| CP-FR-009 | MUST | Kullanıcı taslağı adlandırıp `ready` durumuna getirebilir; bu yayınlama anlamına gelmez. |
| CP-FR-010 | MUST | Tarifi Pattern Library'nin aynı listesinde kendi tarif filtresiyle görüntüleyebilir. |
| CP-FR-011 | MUST | Tarif düzenleme `ownerId` yetkilendirmesine tabidir. |
| CP-FR-012 | MUST | Tarif proje ile bağlantılandırılabilir; kaldırılması tarifi silmez. |
| CP-FR-013 | MUST | Tarif çoğaltılınca yeni `patternId`, yeni revizyon ve kendi ilişki kayıtları oluşur. |
| CP-FR-014 | MUST | Tarif arşivlenebilir ve tekrar aktif hale getirilebilir. |
| CP-FR-015 | MUST | Tarif silindiğinde `deletedAt` set edilir; varsayılan sorgudan gizlenir. |
| CP-FR-016 | MUST | Offline create/edit/delete desteklenir; lokal kaydetme cloud sync'i beklemez. |
| CP-FR-017 | MUST | Sync durumları `synced`, `pending`, `failed`, `conflict` ile gösterilebilir. |
| CP-FR-018 | MUST | İki cihazda aynı blok düzenlenirse sessiz veri ezilmez; çakışma çözümü gerekir. |
| CP-FR-019 | MUST | Tarih/saat ve sürüm metaverisi saklanır; içerik analytics'e taşınmaz. |
| CP-FR-020 | MUST | Formdan çıkışta henüz lokal kaydedilmemiş değişiklik varsa güvenli davranılır. |
| CP-FR-021 | SHOULD | Opsiyonel malzeme/şiş/tığ/iplik ve gauge notu eklenebilir. |
| CP-FR-022 | SHOULD | Opsiyonel beden ve teknik etiketleri eklenebilir. |
| CP-FR-023 | SHOULD | Feature-004 medya altyapısından fotoğraf bağlanabilir. |
| CP-FR-024 | SHOULD | Talimat bloğu kopyalanabilir. |
| CP-FR-025 | SHOULD | Boş durum, hata, sync ve çakışma mesajları erişilebilir gösterilir. |
| CP-FR-026 | COULD | İçerik üzerinde yalnızca yerel arama yapılabilir; V1 temel liste araması yeterli. |
| CP-FR-027 | MUST | Harici/içe aktarılan tarif kullanıcı düzenleme yetkisine otomatik dönüşmez; kaynak türü kontrol edilir. |
| CP-FR-028 | MUST | Tarif silindiğinde projedeki eski bağlantılar güvenli “tarif kullanılamıyor” durumuna geçer. |

## İş kuralları

- **CP-BR-001** Özel tarife yalnızca sahibi erişebilir; kimlik bilgisi istemciden gelen `ownerId` ile kabul edilmez.
- **CP-BR-002** `draft`, `ready`, `archived` içerik durumlarıdır; `deletedAt` ayrıdır.
- **CP-BR-003** Taslak boş talimatla saklanabilir; `ready` için en az bir dolu `instruction` zorunludur.
- **CP-BR-004** `ready` bir gizlilik/publication izni değildir.
- **CP-BR-005** Feature-004 linkleri kanonik `patternId` kullanır; bağımsız ikinci `CustomPattern` ana kaydı üretilmez.
- **CP-BR-006** Kullanıcının başlığını, metnini ve notunu API hatası/analytics/logger yazmaz.
- **CP-BR-007** Silinen tarife düzenleme, yeni proje bağlantısı ve varsayılan arama kapalıdır.
- **CP-BR-008** Çoklu cihaz eşzamanlı blok değişikliklerinde kazananı rastgele zaman damgasına göre seçme; revizyon kontrolü zorunlu.

## Validasyon

| ID | Alan | Kural |
|---|---|---|
| CP-VR-001 | `title` | Taslakta boş olabilir; `ready` öncesi trim sonrası 1–120 karakter |
| CP-VR-002 | `summary` | En fazla 2.000 karakter |
| CP-VR-003 | `block.text` | Blok başına en fazla 20.000 karakter; en az bir dolu talimat `ready` için gerekli |
| CP-VR-004 | `blocks` | V1'de en fazla 500 aktif blok; limit ürün onayına açık |
| CP-VR-005 | `sortOrder` | Aynı tarifte deterministik ve benzersiz sıralama |
| CP-VR-006 | `difficulty` | `beginner`, `intermediate`, `advanced`, `unspecified` |
| CP-VR-007 | `craftType` | `knitting`, `crochet`, `other` |
| CP-VR-008 | `projectId` | Link edilen proje aynı kullanıcıya ait ve silinmemiş olmalı |
| CP-VR-009 | `media` | Format, boyut ve güvenlik Feature-004 medya kurallarına uymalı |

## Fonksiyonel olmayan gereksinimler

- **CP-NFR-001:** Metin düzenleme ve lokal kaydetme internet olmadan devam eder.
- **CP-NFR-002:** Save debounce gecikmesi kullanıcıya veri kaybı hissettirmez; ekran kapanışı ve arka plana geçişte flush zorunlu.
- **CP-NFR-003:** 100 blokluk tarifte düzenleme akıcı olmalı; ölçümler gerçek cihazda toplanır.
- **CP-NFR-004:** Tüm kontrol ve durum mesajları screen reader, büyük font ve klavye ile erişilebilir olmalı.
- **CP-NFR-005:** Metadata ve talimat metni Türkçe Unicode karakterlerini korumalı.
- **CP-NFR-006:** Analytics isteği, lokalde kaydetme işlemini bloke etmemeli.
- **CP-NFR-007:** Yetki backend/RLS'de zorunlu, yalnızca UI'da olmamalı.

## Kabul kriterleri

1. **CP-AC-001:** Kullanıcı offline iken yeni taslak ve iki talimat bloğu açar; uygulamayı kapatıp açınca son başarılı lokal değişiklikler bulunur.
2. **CP-AC-002:** Başlıksız taslak saklanır fakat `ready` denemesi validasyonla engellenir.
3. **CP-AC-003:** Geçerli başlık ve instruction ile `ready` yapılan tarif Library'de kendi tarifleri arasında görünür.
4. **CP-AC-004:** Blok sıralaması değiştirildiğinde aç/kapat sonrası aynı sıradadır.
5. **CP-AC-005:** Tarif proje A'ya bağlanır; bağlantı kaldırıldığında tarif Library'de kalır.
6. **CP-AC-006:** Duplicate yeni ID alır; orijinalin değişimi kopyayı etkilemez.
7. **CP-AC-007:** Başka kullanıcının `patternId` değerini bilen kişi içeriğini okuyamaz/değiştiremez.
8. **CP-AC-008:** Synced tarif iki cihazda çakışan şekilde değiştirilirse biri sessizce kaybolmaz.
9. **CP-AC-009:** Silinen tarif varsayılan listeden çıkar; eski proje linkinde kırık bağlantı güvenli gösterilir.
10. **CP-AC-010:** Telemetri payload'larında başlık, tarif metni, notlar, dosya yolu yoktur.

## Uygulama öncesi açık kararlar

`CP-OD-001`–`CP-OD-006` (`overview.md`) ürün sahibi tarafından netleştirilecek. Özellikle Feature-004 kanonik modelinin alan adlarıyla eşleme gerçekleştirilmeden yeni tablo yazılmamalı.
