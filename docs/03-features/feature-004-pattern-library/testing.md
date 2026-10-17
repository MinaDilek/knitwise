# Pattern Library — Testing

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Test stratejisi
Unit: validation, state machine, search/filter, policy. Repository: owner scope, soft delete, transactional outbox. Integration: import → open → link → delete. Backend: RLS + private object. Offline: cached/uncached states, retry/idempotency. Widget/E2E: PDF & image preview, picker, permissions, accessibility. Telemetry: payload allowlist.

## 2. Fixture'lar
- `owner_A`/`owner_B` ve ayrı projects.
- Geçerli PDF, bozuk PDF, uzantısı `.pdf` ama JPG signature, büyük PDF, JPEG/PNG, unsupported file.
- URL `https://example.org/pattern`, `file:///...`, `javascript:...`, `https://u:p@example.org/`.
- Pattern A: `pdf/local_available/pending`; Pattern B: `pdf/remote_available/no cache`; Pattern C: `url`; Pattern D: `deletedAt` dolu.
- 2 projeye bağlı tek pattern, duplicate link denemesi.
- 500 kayıt arama/perf fixture; permission revoked, disk full, dropped network, app force kill.

## 3. Unit / validation testleri
| ID | Beklenen |
|---|---|
| PL-TEST-001 | Trim edilmiş geçerli başlık kabul edilir; boş başlık reddedilir. |
| PL-TEST-002 | Başlık > 120 karakter validasyon hatası (onaylı limit varsayımı). |
| PL-TEST-003 | `sourceType` allowlist dışında parse safe failure. |
| PL-TEST-004 | `https` URL geçerli; `javascript:`, `file:`, `data:` ve credentials reddedilir. |
| PL-TEST-005 | Dosya adı uzantısı sahteyse signature doğrulaması yakalar. |
| PL-TEST-006 | Bozuk PDF/decode sınırı crash üretmez. |
| PL-TEST-007 | Query normalize edilince Türkçe Unicode karakterleri bozulmaz. |
| PL-TEST-008 | Soft-deleted kayıt list/search filtrelenir. |

## 4. Import/repository testleri
| ID | Adım / beklenen |
|---|---|
| PL-TEST-009 | Geçerli PDF import → Pattern + PatternAsset + outbox durable oluşturulur. |
| PL-TEST-010 | Geçerli görsel import → private local ref ve preview çalışır. |
| PL-TEST-011 | File picker cancel → DB'de veya kalıcı storage'da yeni kayıt yok. |
| PL-TEST-012 | Local save failure → yarım Pattern/Asset yok; staged orphan cleanup. |
| PL-TEST-013 | Promote sonrası crash/restart → journal recovery metadata ile dosyayı uzlaştırır. |
| PL-TEST-014 | Disk full → açıklanabilir error, mevcut kayıtlar korunur. |
| PL-TEST-015 | Permission revoked → import başarısız ama UI crash olmaz. |
| PL-TEST-016 | Search by title/tags + filter category/favorite doğru owner sonuçlarını döner. |
| PL-TEST-017 | 500 kayıt listesinde scroll/search ölçümü cihaz testinde kabul sınırını aşmaz. |
| PL-TEST-018 | Metadata edit sync pending; uygulama restart sonrası korunur. |
| PL-TEST-019 | Delete onayı → soft delete, cancel → değişiklik yok. |

## 5. Asset ve offline testleri
| ID | Beklenen |
|---|---|
| PL-TEST-020 | Cached PDF offline açılır. |
| PL-TEST-021 | Uncached remote PDF offline iken `offline_unavailable`, yanlış success yok. |
| PL-TEST-022 | Cloud download kesilirse cached sağlam veri korunur; retry mümkün. |
| PL-TEST-023 | Corrupt cache için `failed` + recovery path, metadata korunur. |
| PL-TEST-024 | Logout/account switch cross-owner asset preview engeller. |
| PL-TEST-025 | URL reference offline detail metadata açar; external link auto-fetch etmez. |

## 6. Project entegrasyonu
| ID | Beklenen |
|---|---|
| PL-TEST-026 | Pattern tek projeye linklenir ve Feature-001 detail'de görünür. |
| PL-TEST-027 | Aynı pattern iki projeye bağlanır, duplicate active link oluşturulmaz. |
| PL-TEST-028 | Unlink pattern ve asset'i silmez. |
| PL-TEST-029 | Project soft delete pattern'i silmez; link görünümü güvenli olur. |
| PL-TEST-030 | Pattern soft delete picker'dan gizlenir; project bozulmaz. |

## 7. Sync ve conflict
| ID | Beklenen |
|---|---|
| PL-TEST-031 | Offline import pending kalır, connectivity sonrası sync edilir. |
| PL-TEST-032 | Retry aynı operationId ile; duplicate asset veya metadata oluşmaz. |
| PL-TEST-033 | Asset upload başarısız olsa local preview devam eder. |
| PL-TEST-034 | Aynı pattern iki cihazdan değiştirilince version conflict sessiz overwrite yapmaz. |
| PL-TEST-035 | Deleted remote + local pending → conflict state, veri sessiz silinmez. |
| PL-TEST-036 | Yanlış/expired auth → remote yazım reddi; pending data korunur. |

## 8. Security / analytics
| ID | Beklenen |
|---|---|
| PL-TEST-037 | User A, User B metadata'sını API/RLS ile okuyamaz. |
| PL-TEST-038 | User A, User B private object'ini indiremez. |
| PL-TEST-039 | Farklı owner/project için link API reddedilir. |
| PL-TEST-040 | URL scheme/credentials input validation aşılmaz. |
| PL-TEST-041 | `title`, `notes`, rawURL, localURI, remoteKey, content hash telemetry/log payload'ında yok. |
| PL-TEST-042 | Event retry de-dup, analytics failure UI'yı durdurmaz. |
| PL-TEST-043 | PDF embedded action/auto external resource tetiklenmez. |

## 9. UX/a11y/localization
| ID | Beklenen |
|---|---|
| PL-TEST-044 | Import progress, error, retry screen reader etiketi taşır. |
| PL-TEST-045 | Preview error/empty state yalnız renk ile anlatılmaz. |
| PL-TEST-046 | Türkçe karakterler, büyük yazı boyutu, uzun başlık card'da taşma yaratmaz. |
| PL-TEST-047 | Favori ve filtre state UI ile data source arasında tutarlı. |
| PL-TEST-048 | Deleted/unauthorized deep link güvenli not-found. |

## 10. E2E kabul akışı
Kullanıcı PDF seçer → metadata verir → yerel preview açar → projeye bağlar → offline ilerler → tekrar online olup sync eder → başka cihazda açar → linki kaldırır → pattern kaydı korunur → silme onayıyla kütüphaneden gizler. Her basamağın gerçek backend yetkilendirmesi test edilmelidir.

## 11. Release gate
MUST gereksinim testleri, RLS/storage izolasyonu, offline önbellek doğruluğu, dosya parser güvenliği, idempotent sync, proje link bütünlüğü, gizlilik allowlist, migrasyon ve erişilebilirlik smoke geçmeden release kabul edilmez. PL-OD açık kararları kapatılmalıdır.

## 12. Feature-004 set
`overview.md`, `requirements.md`, `flows.md`, `data-model.md`, `analytics.md`, `security.md`, `implementation-notes.md`, `testing.md`.
