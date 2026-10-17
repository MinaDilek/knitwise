# Pattern Library — Overview

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature | FEATURE-004 — Pattern Library |
| Sürüm | V1 / Draft 1.0 |
| Dil | Türkçe; teknik adlar İngilizce |

> **Karar durumu:** Bu belgeler taslak ürün sözleşmesidir. `PL-OD-*` kararları onaylanmadan Codex kesin teknoloji veya davranış varsaymamalıdır. Feature-001/002/003 ve merkezi güvenlik/senkronizasyon sözleşmeleriyle çelişen noktalar uygulama öncesi uzlaştırılmalıdır.

## 1. Amaç ve problem
Kullanıcı PDF, resim veya kendi yazdığı örgü/tığ işi tariflerini dağınık dosyalarda tutmak yerine **özel Pattern Library** içinde saklar, arar, projeye bağlar ve çalışırken açar. V1 kullanıcıya ait, private-by-default bir kütüphanedir; herkese açık pattern pazarı değildir.

## 2. Kullanıcı senaryoları
- PDF tarifini içe aktarır, başlık ve kategori verir, çevrimdışı açar.
- Fotoğraftan aldığı tarifin görselini saklar ve projeye iliştirir.
- Harici bir kaynağın URL'sini referans olarak kaydeder; link kaybolsa bile metadata saklanır.
- Kendi metin notlarını oluşturur; içerik üzerinde arama yerine V1'de metadata araması kullanır.
- Bir pattern'i birden fazla projeye bağlar; projeyi silmek pattern'i silmez.
- Dosya eksik, desteklenmeyen format veya upload hatasında veri kaybetmez.

## 3. V1 kapsamı
Private pattern list/detail; PDF ve görsel import; manual/text entry (opsiyonel işaretlenir); kaynak URL ve atıf alanı; başlık, kategori, craft, etiketler, favori, not; yerel dosya erişimi; proje bağlantısı; soft delete; offline-first metadata ve indirilen dosyalar; güvenli senkronizasyon; erişilebilirlik; privacy-safe analytics.

## 4. Kapsam dışı
Pattern marketplace, pattern satırlarının AI ile ayrıştırılması, otomatik telif lisansı doğrulama, kamuya açık paylaşım, işbirliği, OCR/full-text PDF indexing, ücretli pattern satışı, pattern editöründe gelişmiş şema/diagram tasarımı. Bunlar ayrı karar/feature gerektirir.

## 5. Temel iş kuralları
- `Pattern` kullanıcının özel metadata kaydıdır; `PatternAsset` saklanan dosyayı temsil eder; `ProjectPatternLink` Feature-001'in ortak bağlantı sözleşmesini kullanır, ikinci ayrı ilişki kaynağı yaratılmaz.
- Pattern silinirse bağlantılar pasif/broken-state olur, proje silinmez.
- URL'ler güvenilmeyen kullanıcı girdisidir; uygulama bunları otomatik sunucu tarafında taramaz.
- V1'de orijinal dosyanın sahibinin kullanıcı olduğu varsayılmaz; içe aktarma, içeriği kamuya açık yayınlama hakkı vermez.

## 6. Bağımlılıklar ve metrikler
Feature-001 Project Management; Feature-017 Local Persistence; Feature-018 Cloud Sync; auth/storage; Feature-014 Premium yalnızca ileride onaylı kota varsa. Ölçümler: first pattern import, import success, patterns linked to projects, revisit, offline open success, failed import; isimler ve dosya içeriği analytics'e gitmez.

## 7. Açık kararlar
| ID | Karar | Geçici öneri |
|---|---|---|
| PL-OD-001 | V1 kaynak tipleri | PDF, image, URL; text entry doğrulansın |
| PL-OD-002 | Dosya boyut sınırı | Platform kapasitesi test edilerek belirlensin; sabit uydurulmasın |
| PL-OD-003 | Cloud dosya yedekleme | Kullanıcı oturumlu storage + local cache; henüz mimari onaysız |
| PL-OD-004 | Çoklu asset | V1 tek ana dosya, genişlemeye uygun model |
| PL-OD-005 | PDF sayfa araması/OCR | V1 dışı |
| PL-OD-006 | Etiket limiti | Ürün kararı bekliyor |
| PL-OD-007 | Kaynak linki doğrulama | Sadece şema ve sözdizimi; fetch yok |
| PL-OD-008 | Silineni geri yükleme | Veri modelinde hazır, UI sonra |

## 8. Sonraki belge
`requirements.md`
