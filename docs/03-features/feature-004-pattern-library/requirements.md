# Pattern Library — Requirements

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Kapsam ve önkoşullar
Feature-004, kullanıcının kendi hesabına bağlı özel pattern kayıtlarını oluşturmasını, listelemesini, açmasını, düzenlemesini, projeye bağlamasını ve güvenli biçimde silmesini sağlar. İnternet olmadan metadata CRUD ve daha önce indirilen varlıklar çalışır; henüz indirilmemiş uzak dosyalar offline açılmaz.

## 2. İşlevsel gereksinimler
| ID | Zorunluluk | Gereksinim |
|---|---|---|
| PL-FR-001 | MUST | Kullanıcı PDF dosyasından pattern oluşturabilmeli. |
| PL-FR-002 | MUST | Kullanıcı JPEG/PNG (ve platformda doğrulanan türler) görselden pattern oluşturabilmeli. |
| PL-FR-003 | MUST | Kullanıcı güvenli `https` URL referansını, dosyayı otomatik indirmeden kaydedebilmeli. |
| PL-FR-004 | SHOULD | Kullanıcı salt metin pattern girebilmeli; PL-OD-001 onayına bağlı. |
| PL-FR-005 | MUST | Her kayıt `ownerId`, başlık, `sourceType`, `createdAt`, `updatedAt` taşımalı. |
| PL-FR-006 | MUST | Kullanıcı başlık, kategori, craft, etiket, kaynak/atfı ve kişisel notu düzenleyebilmeli. |
| PL-FR-007 | MUST | Kullanıcı yalnızca kendisine ait, `deletedAt == null` pattern'ları listelemeli. |
| PL-FR-008 | MUST | Başlık ve etiket metadata araması; kategori/craft filtreleme desteklenmeli. |
| PL-FR-009 | SHOULD | Favori işaretleme ve favori filtresi sunulmalı. |
| PL-FR-010 | MUST | Pattern detail metadata'yı ve erişilebilir ana asset'i göstermeli. |
| PL-FR-011 | MUST | Asset indirilmemişse offline empty/unavailable durumu gösterilmeli; yanlış başarı mesajı verilmemeli. |
| PL-FR-012 | MUST | Seçilen PDF/görsel lokalde geçici alan üzerinden doğrulanmalı, kalıcı alana taşınmalı. |
| PL-FR-013 | MUST | Bozuk veya desteklenmeyen dosyalar açıklanabilir hata ile reddedilmeli. |
| PL-FR-014 | MUST | Tekrarlanan import otomatik sessiz birleştirilmemeli; kullanıcı iki kayıt oluşturabilir. |
| PL-FR-015 | MUST | Bir pattern birden çok projeye bağlanabilmeli; Feature-001 `ProjectPatternLink` kullanılmalı. |
| PL-FR-016 | MUST | Link kaldırmak pattern veya project'i silmemeli. |
| PL-FR-017 | MUST | Project silinince pattern korunmalı; proje bağlantısı görünümde güvenli ele alınmalı. |
| PL-FR-018 | MUST | Pattern silmek `deletedAt` ile soft delete etmeli, önce onay istemeli. |
| PL-FR-019 | MUST | Soft deleted pattern normal arama/liste/detail'de gösterilmemeli. |
| PL-FR-020 | MUST | Pattern metadata offline oluşturulup düzenlenebilmeli; sync bekleyen işler kalıcı kuyruğa yazılmalı. |
| PL-FR-021 | MUST | Sync hataları lokal metadata ve mevcut asset'i silmemeli. |
| PL-FR-022 | MUST | Owner izolasyonu backend tarafında sağlanmalı; istemci filtrelemesi yeterli sayılmamalı. |
| PL-FR-023 | MUST | Analytics adı, notu, dosya yolu, raw URL, dosya içeriği göndermemeli. |
| PL-FR-024 | MUST | Pattern preview başarısızsa retry ve metadata'ya dönüş seçenekleri sunulmalı. |
| PL-FR-025 | SHOULD | Aynı dosyanın tekrar seçildiği tespit edilebilirse karar kullanıcıya bırakılmalı. |
| PL-FR-026 | SHOULD | İçe aktarma sırasında durum (`validating`, `copying`, `queued`, `available`, `failed`) görünmeli. |
| PL-FR-027 | MUST | Silinmiş/kullanıcıya ait olmayan pattern deep link'i güvenli not-found ile kapanmalı. |
| PL-FR-028 | MUST | Uygulama kendi özel alanındaki dosyaları genel erişime açmamalı. |
| PL-FR-029 | SHOULD | Storage kotası doluysa kullanıcı verisinin hangisinin korunabildiği açıkça gösterilmeli. |
| PL-FR-030 | MUST | Kullanıcının erişimini kaldırdığı dosya izinlerinde uygulama crash olmamalı. |

## 3. İş kuralları
| ID | Kural |
|---|---|
| PL-BR-001 | `sourceType` = `pdf`, `image`, `url`, veya onaylanırsa `text`; tip ile asset tutarlılığı doğrulanır. |
| PL-BR-002 | Başlık trim sonrası 1–120 karakter (önerilen V1 kuralı; ürün onayı bekler). |
| PL-BR-003 | `https` harici URL ilk sürümde kabul edilmez; `javascript:`, `file:`, `data:` reddedilir. |
| PL-BR-004 | Dosya uzantısına güvenilmez; MIME / signature doğrulaması yapılır. |
| PL-BR-005 | `deletedAt` ile silinen pattern'ın linkleri istemcide normal kullanımda görünmez. |
| PL-BR-006 | Proje/kütüphane verileri hesap değiştirilince birbirine karışmaz. |
| PL-BR-007 | Cloud asset yoksa bile lokal metadata silinmez; geçici `unavailable` state korunur. |
| PL-BR-008 | Pattern PDF veya resim görüntülemek kullanıcı adına sahiplik/telif hakları vermez. |

## 4. NFR ve kabul hedefleri
| ID | Gereksinim / doğrulama |
|---|---|
| PL-NFR-001 | Liste sanallaştırılmalı; en az 500 metadata kaydıyla cihazda performans testi geçmeli. |
| PL-NFR-002 | Metadata operasyonları local-first ve transactional olmalı. |
| PL-NFR-003 | Dosya import işlemleri iptal/hata halinde orphan temp file bırakmamalı. |
| PL-NFR-004 | Görüntüleyici büyük PDF'leri belleğe tümüyle almamalı; stream/page rendering tercih edilmeli. |
| PL-NFR-005 | Ekran okuyucu, yükleme durumu, preview error ve buton etiketleri erişilebilir olmalı. |
| PL-NFR-006 | Türkçe başlık, etiket, dosya adı Unicode olarak bozulmadan saklanmalı. |
| PL-NFR-007 | Auth/RLS, private storage policy ve signed URL expiration test edilmeli. |
| PL-NFR-008 | Hata logları içerik veya access token sızdırmamalı. |

## 5. Kabul senaryoları
- **PL-AC-001:** Geçerli PDF seçildiğinde metadata ve yerel dosya kaydı oluşur, detail önizlemesi açılır.
- **PL-AC-002:** Geçersiz görsel/PDF seçildiğinde import reddedilir; kütüphanede yarım kayıt kalmaz.
- **PL-AC-003:** İnternet yokken metadata ekleme/düzenleme kalıcı `pending` olur; uygulama yeniden açılınca kaybolmaz.
- **PL-AC-004:** Aynı pattern iki ayrı projeye bağlanabilir; bir bağlantıyı kaldırmak diğerini etkilemez.
- **PL-AC-005:** Pattern silme onayından sonra liste ve aramada görünmez, proje kaydı korunur.
- **PL-AC-006:** Başka kullanıcının pattern ID'si ile okuma/asset indirme engellenir.
- **PL-AC-007:** Uzak asset hiç indirilmemişken offline detail metadata'yı açar, dosya için unavailable mesajı gösterir.
- **PL-AC-008:** Telemetri payload'ı kullanıcı metinlerini, raw URL ve dosya yollarını içermez.
- **PL-AC-009:** Dosya okunamıyorsa uygulama hata ve yeniden dene gösterir, crash olmaz.

## 6. Blokaj kararları
`PL-OD-001` kaynak tipleri, `PL-OD-002` dosya sınırı, `PL-OD-003` cloud storage, `PL-OD-004` çoklu asset. Onaysız varsayımlar açık TODO olarak işaretlenir; rastgele kota uygulanmaz.

## 7. Definition of Done
Tüm MUST kabul senaryoları, owner izolasyonu, offline yeniden açma, asset fallback, link bütünlüğü, erişilebilirlik ve privacy testleri başarılı; açık kararlar kapatılmış veya ertelenmiş olmalı.
