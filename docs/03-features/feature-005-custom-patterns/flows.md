# Feature-005 Custom Patterns — Flows

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/flows.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## Akış ilkeleri

Her kullanıcı değişikliği önce doğrulanır, **tek yerel transaction** içinde kaydedilir, sonra sync kuyruğuna alınır. Kullanıcıya uzaktaki kayıt tamamlanmış gibi gösterilmez; “cihaza kaydedildi” ile “eşitlendi” ayrıdır.

## CP-FLOW-001 — İlk özel tarifi oluşturma

**Önkoşul:** Oturum açık; kullanıcı Pattern Library'de.

1. “Yeni tarif”e dokunur.
2. Sistem boş taslak için benzersiz `patternId` üretir; `sourceType=custom`, `visibility=private`, `status=draft` atar.
3. Başlık ve açıklama girilir.
4. “Talimat ekle” ile ilk `instruction` bloğu eklenir.
5. Otomatik lokal kayıt ve sync pending oluşur.
6. “Hazır olarak kaydet” seçilirse title/instruction doğrulanır; status `ready` olur.
7. Kütüphane kendi tarifler görünümünde kayıt belirir.

**Alternatif:** Kullanıcı vazgeçerse ve henüz hiçbir lokal yazım yoksa taslak oluşturulmaz; lokal yazım başladıysa “taslağı koru / sil” sunulur.

## CP-FLOW-002 — Taslağa dönme ve otomatik kaydetme

1. Kullanıcı kaydedilmiş taslağı açar.
2. Ekran lokal sürümü gösterir ve varsa sync rozetini sunar.
3. Kullanıcı blok metnini değiştirir; debounce tetiklenir.
4. Yerel transaction, pattern revizyonunu ve değiştirilmiş blokları günceller.
5. Kullanıcı geri giderse flush sonucu beklenir; başarısızsa uyarı ve ekranda kalma seçeneği sağlanır.

**Hata:** Yerel disk doluysa son başarılı sürüm korunur, değişiklik kaydedilmiş gibi sunulmaz.

## CP-FLOW-003 — Talimat bloğu ekleme / değiştirme / taşıma

1. “Blok ekle” seçilir; `heading`, `instruction` veya `note` türü belirlenir.
2. Text yazılır; sıralama `sortOrder` ile tutulur.
3. Kullanıcı blokları sürükleyerek taşır; tek transaction'da sıra güncellenir.
4. Silmede undo veya onay mekanizması sunulur.
5. UI listesi güncel sıralama ve içerikle yenilenir.

**Alternatif:** Çok büyük metin sınırı aşılırsa kayıt engellenir; veri formda kalır.

## CP-FLOW-004 — Malzeme ve varyant notları

1. Detaylar menüsünden teknik, zorluk ve malzeme notları girilir.
2. Metinler validasyonla saklanır.
3. V1'de beden otomatik hesaplanmaz; yazı olarak tutulur.

## CP-FLOW-005 — Önizleme

1. Kullanıcı editörde “Önizle” seçer.
2. Sıralı başlık, talimat ve not blokları okuma görünümünde açılır.
3. Önizleme veriyi değiştirmez; taslak olarak kalabilir.
4. Düzenlemeye dönünce son lokal sürüm korunur.

## CP-FLOW-006 — Kütüphaneye kaydetme ve arama

1. `ready` tarif Library'de “Kendi Tariflerim” filtresine girer.
2. Kullanıcı başlık/etiket bazlı arar.
3. Sonuçlar owner ve `deletedAt=null` kapsamıyla çekilir.
4. Seçilen kayıt açılır veya düzenlenir.

## CP-FLOW-007 — Projeye bağlama

1. Kullanıcı tarif detayından “Projeye bağla” seçer.
2. Kendisine ait aktif/silinmemiş projeler listelenir.
3. Kullanıcı proje seçer; Feature-001/004 ortak link modeli kullanılır.
4. Başarı sonrası project detail'de tarif bağlantısı görünür.
5. Unlink yalnızca bağı kaldırır.

## CP-FLOW-008 — Çoğaltma

1. Kullanıcı “Kopyasını oluştur” seçer.
2. Sistem yeni `patternId`, blok ID'leri ve sıfırlanmış sync state üretir.
3. İçerik lokal transaction içinde kopyalanır.
4. Orijinalin proje bağlantısı otomatik taşınmaz.

## CP-FLOW-009 — Arşivleme, geri açma, silme

1. Kullanıcı “Arşivle” seçerse `status=archived` olur; içerik korunur.
2. “Aktifleştir” `ready` durumuna döndürür; validation tekrar çalışır.
3. “Sil” onayından sonra `deletedAt` doldurulur.
4. Silinen içerik normal Library sorgusundan ve editörden çıkar; projedeki link güvenli unavailable state gösterir.

## CP-FLOW-010 — Offline değişiklik ve sync

1. Cihaz offline iken düzenleme yapılır.
2. Local commit başarılıdır, `syncStatus=pending` kalır.
3. Online dönüşte queue ilgili revizyonu gönderir.
4. Sunucu yetki ve sürüm denetimi yapar.
5. Başarılıysa `synced`; başarısızsa `failed`; revizyon uyuşmazlığında `conflict`.

## CP-FLOW-011 — Çakışma yönetimi

1. Aynı blok iki cihazda değişmiştir.
2. Sync engine base revision uyuşmazlığı tespit eder.
3. İki metin versiyonu lokal olarak korunur; UI “Bu tarif başka cihazda da güncellendi” gösterir.
4. Kullanıcı “Yerel sürümü kullan”, “Bulut sürümünü kullan” veya manuel birleştirme seçer.
5. Yeni revizyon saklanır; sync tekrar denenir.
6. Seçim verilmeden veri silinmez.

## CP-FLOW-012 — Yetkisiz / bozuk bağlantı

Başka kullanıcıya ait `patternId` için içerik sızdırmadan not-found/unauthorized mesajı verilir. Silinmiş pattern projesi linkinde detay açılmaz, projeyi kullanmaya devam etmek mümkündür.

## Akış → gereksinim matrisi

| Flow | Gereksinimler |
|---|---|
| 001 | CP-FR-001..005, 008..009 |
| 002 | CP-FR-008, 016, 020 |
| 003 | CP-FR-004..007, 024 |
| 004 | CP-FR-021..023 |
| 005 | CP-FR-003..005 |
| 006 | CP-FR-010..011 |
| 007 | CP-FR-012, 028 |
| 008 | CP-FR-013 |
| 009 | CP-FR-014..015 |
| 010 | CP-FR-016..019 |
| 011 | CP-FR-018 |
| 012 | CP-FR-011, 027..028 |
