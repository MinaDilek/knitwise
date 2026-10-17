# Pattern Library — Security

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Tehdit modeli
Korunan değerler: kişisel not, özel PDF/görsel, kaynak URL, pattern-proje ilişkileri, private cache, access tokens. Tehditler: IDOR, storage object enumeration, link içeriği sızması, malicious PDF/image parsing, SSRF (URL preview varsa), cache cross-account leakage, offline sync ile unauthorized overwrite, private içeriğin logs/telemetry'ye sızması.

## 2. Authorization / isolation
- `Pattern.ownerId == auth.uid()` ve `PatternAsset.ownerId == auth.uid()` doğrulaması backend zorunludur.
- `ProjectPatternLink` için project ve pattern aynı authenticated owner'a ait olmalıdır.
- List/get/update/delete normal işlemler soft-deleted kayıtları dışlamalıdır.
- RLS hem `USING` hem `WITH CHECK` ile test edilmeli; object store bucket private ve owner-scoped policies kullanılmalı.
- Client'tan gelen `ownerId` yetki kaynağı değildir. ID bilmek dosya okuma izni vermez.

## 3. Dosya güvenliği
- `fileName` display metadata'dır, storage object path oluşturmakta kullanılmaz.
- Dosya uzantısı, MIME ve file signature eşleştirilmeli; parser güvenlik yamaları takip edilmeli.
- Dosya boyutu, sayfa sayısı ve decode kaynak kullanımına bounded limit konulmalı (kesin limit PL-OD-002).
- PDF script/link/action özellikleri otomatik çalıştırılmamalı; güvenli viewer kullanılmalı.
- Gömülü external resource indirimi varsayılan kapalı olmalı.
- Thumbnail oluşturma sandbox/limitli iş ile yapılmalı.
- Upload complete sonrası server-side owner/contentType/size revalidation.

## 4. URL güvenliği
- V1 sadece `https` kabul eder; `javascript:`, `file:`, `data:`, credential-laden URLs reddedilir.
- URL kaydı otomatik backend fetch/preview tetiklemez; böylece SSRF yüzeyi doğmaz.
- Gelecekte URL fetch uygulanırsa allow/deny host policy, private IP block, redirect revalidation, DNS rebinding ve size/time limits şarttır.
- Harici link açma kullanıcı aksiyonuyla ve güvenli sistem browser'ında olur; tam URL telemetry'ye gitmez.

## 5. Local ve remote storage
- OS app-private sandbox; paylaşılan Downloads klasörü kalıcı kaynak olarak kullanılmamalı.
- Dosya seçici izinleri kısa süreli; staged bytes app-private alana kopyalanır.
- Logout/account switch'inde owner partition isolation; private cache cleanup şifreleme/retention politikasıyla uyumlu olmalı.
- Cloud private object erişimi kısa ömürlü signed URL/token ya da authenticated stream ile; kalıcı public link üretme.
- Signed linkler log/analytics'e yazılmaz.

## 6. Sync güvenliği
- Operation `operationId` idempotent; auth token expiration/revocation handling; pending operasyon sync sırasında yeniden authorize.
- Metadata optimistic concurrency (`remoteVersion`) ile update; conflict sırasında sessiz overwrite yok.
- Local success = kalıcı yerel kayıt; remote failure lokal kaydı geri almaz.
- Remote delete vs local pending import, linked project deleted, revoked owner access test edilmeli.

## 7. Logging/telemetry
**Yasak:** `title`, `notes`, tag value, `sourceUrl`, `localUri`, signed URL, `remoteObjectKey`, file contents, raw filename, token, user-supplied URL query. **İzin verilen:** `sourceType`, `errorCode`, `stage`, duration bucket, byte bucket. Crash raporlarında stack context payload redaction uygulanır.

## 8. Hata davranışı
- Yetkisiz veya bulunamayan record için kullanıcıya tek generic mesaj.
- Permission denied → import iptal edilir, input metadata kaybedilmez.
- Disk full → staging cleanup, durable state korunur.
- Corrupt PDF → safe unsupported/corrupt mesajı, raw parser exception gösterilmez.
- Cloud unavailable → önbellek varsa açılır, yoksa offline_unavailable.

## 9. Güvenlik kabul testleri
| ID | Test |
|---|---|
| PL-SEC-001 | User A, User B pattern metadata'sını ID ile okuyamaz. |
| PL-SEC-002 | User A, User B asset object key ile dosyayı indiremez. |
| PL-SEC-003 | Sahte ownerId'li insert/update backend tarafından reddedilir. |
| PL-SEC-004 | Bir user'ın pattern'i diğer user'ın projesine bağlanamaz. |
| PL-SEC-005 | Malformed/truncated PDF decode crash veya unsafe action üretmez. |
| PL-SEC-006 | `file:`/`javascript:`/credential URL reddedilir. |
| PL-SEC-007 | Logout/login kullanıcılar arası cache sızıntısı yaratmaz. |
| PL-SEC-008 | Logs ve analytics'te private content bulunmaz. |
| PL-SEC-009 | Duplicate upload operation ayrı public file oluşturmaz. |
| PL-SEC-010 | Deleted pattern normal detail ve asset download'dan dışlanır. |

## 10. Release gate
RLS + object storage policies gerçek backend ortamında doğrulanmadan PR prod'a alınmaz. Güvenlik gereksinimleri yalnız frontend testlerle kapatılamaz.
