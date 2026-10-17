# Feature-005 Custom Patterns — Security

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/security.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## 1. Tehdit modeli

Riskler: ID tahminiyle özel tarif okuma, başka kullanıcının tarifine blok enjekte etme, offline queue'nun yanlış hesapla gönderimi, XSS/HTML enjeksiyonu, dosya URI sızması, aynı tarifi iki cihazda düzenlerken veri kaybı, paylaşıma yanlışlıkla açılma.

## 2. Kimlik ve izin matrisi

| İşlem | Sahip | Başka kullanıcı | Oturum dışı |
|---|---|---|---|
| Listele / oku | Evet | Hayır | Hayır |
| Oluştur | Evet | Hayır | Hayır (V1 öneri) |
| Blok ekle/sil/düzenle | Evet | Hayır | Hayır |
| Projeye bağla | Evet, proje de kendisine aitse | Hayır | Hayır |
| Duplicate | Evet | Hayır | Hayır |
| Archive/delete | Evet | Hayır | Hayır |
| Offline sync | Token doğrulamasıyla | Hayır | Beklemeye alınır |

## 3. Sunucu kuralları

- Her sorgu `auth.uid()` üzerinden sahibi doğrular; client `ownerId` kabul edilmez.
- Child `PatternInstructionBlock.ownerId` ile parent `Pattern.ownerId` eşleşir; foreign key tek başına yeterli değildir.
- Feature-004 içine eklenen custom kayıtlarla diğer `sourceType` kayıtlarının düzenleme policy'si ayrılır.
- `deletedAt != null` için normal edit/read kapalı; ayrı restore özel yetki akışı.
- `visibility=private` varsayılan; V1'de public endpoint/ACL eklenmez.
- Soft delete bağlantıları bozsa bile erişim kontrolünü gevşetmez.

## 4. İçerik güvenliği

- Metin kullanıcı kontrolündedir. UI'da HTML olarak yorumlama; default düz metin render.
- Markdown desteği ileride eklenirse HTML, script, external image ve unsafe linkler sanitize edilir.
- Teknik notlar ve blok metni structured logging, crash report ve analytics'ten çıkarılır.
- Medya kullanımı Feature-004 storage bucket ownership, signed URL ve upload MIME/size kontrollerine uyar.
- Dosya yolu ve obje URL'sini telemetry'ye taşımama.

## 5. Offline ve sync güvenliği

- Lokal veri per-account namespace içinde tutulur; logout'ta cache erişimi kesilir. Silme/koruma stratejisi genel hesap politikasına bağlıdır.
- Pending operation token yenilenemediğinde gönderilmez; başka kullanıcı oturumunda çalışmaz.
- Her `operationId` idempotent; remote version compare-and-swap ile lost update önlenir.
- Çakışma çıktısı her iki sürümü korur; otomatik last-write-wins yasaktır.
- Retry backoff ve queue üst sınırı önlemleri Feature-018 ile ortak.

## 6. Kritik testler

| ID | Senaryo | Beklenen |
|---|---|---|
| CP-SEC-001 | A, B'nin patternId ile oku | 403/404; içerik yok |
| CP-SEC-002 | A, B'nin pattern'ine block ekle | İşlem reddedilir |
| CP-SEC-003 | A, B'nin project'ine pattern bağla | Reddedilir |
| CP-SEC-004 | Offline A işlemini B oturumunda gönder | İşlem gönderilmez |
| CP-SEC-005 | Aynı operationId iki kez | Bir kez uygulanır |
| CP-SEC-006 | Revizyon uyuşmazlığı | Conflict, veri korunur |
| CP-SEC-007 | HTML/script içeren block text | Düz metin; script çalışmaz |
| CP-SEC-008 | Silinmiş pattern deep link | Safe unavailable |
| CP-SEC-009 | Crash/analytics | Ham metin içermez |

## 7. Release checklist

- [ ] RLS/endpoint owner isolation testleri geçti.
- [ ] Pattern + block parent-child bütünlüğü sunucuda doğrulandı.
- [ ] Offline logout/login ayrımı test edildi.
- [ ] XSS/metin render güvenliği test edildi.
- [ ] Analytics privacy allowlist geçti.
- [ ] İki cihaz çakışmasında veri kaybı yok.
- [ ] Feature-004 içerik görünürlüğü ve medya izinleri ile uyumlu.
