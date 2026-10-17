# Feature-005 Custom Patterns — Testing

| Alan | Değer |
|---|---|
| Feature ID | FEATURE-005 |
| Konum | `03-features/feature-005-custom-patterns/testing.md` |
| Sürüm | V1 (taslak) |
| Durum | Draft — ürün kararları onay bekliyor |
| Dil | Türkçe; teknik anahtarlar İngilizce |

> **Sınır:** Custom Patterns, kullanıcının **kendisi tarafından yazılan** tarifleri oluşturup düzenlemesidir. Feature-004 Pattern Library, içe aktarılan / kaydedilen tarifleri kataloglamayı ve ilişkilendirmeyi de kapsar. Yeni modül aynı tarif varlığını ikinci kez kopyalamamalı; ortak `patternId` üzerinden çalışmalıdır.

## 1. Strateji

Unit → local repository/transaction → widget → integration → iki cihaz sync/security testleri. Tek başına mock üzerinden başarılı görünen testler yeterli değildir; gerçek veritabanı transaction rollback, RLS ve optimistic concurrency staging ortamında doğrulanmalıdır.

## 2. Test verileri

- `ownerA` ve `ownerB` (ayrılmış hesap alanları)
- `draftEmpty`, `readyTwoBlocks`, `archivedPattern`, `deletedPattern`
- Türkçe, emoji, satır sonu içeren uzun bir talimat
- 100 bloklu karmaşık tarif, aynı anda reordering + editing
- Offline düzenlenmiş kopya ve daha yeni remote revision
- Shared project-link fixture; silinmiş pattern'e işaret eden link

## 3. Test matrisi

| ID | Katman | Senaryo | Beklenen |
|---|---|---|---|
| CP-T-001 | Unit | Boş title draft save | Geçer |
| CP-T-002 | Unit | Boş title `ready` | Validation failure |
| CP-T-003 | Unit | Ready + dolu title ama instruction yok | Validation failure |
| CP-T-004 | Unit | Çok uzun title (121 karakter) | Reddedilir |
| CP-T-005 | Unit | Unsupported blockType | Güvenli hata |
| CP-T-006 | Unit | 501 blok sınırı | Reddedilir veya ürün onaylı sınıra göre |
| CP-T-007 | Unit | Sıralama 5→1 | Deterministik sıra |
| CP-T-008 | Unit | Türkçe/emoji metin | Kaynakla aynı bayt içeriğiyle korunur (encoding normalizasyon politikası hariç) |
| CP-T-009 | Repository | Metadata + block + op yazımı | Tek atomic commit |
| CP-T-010 | Repository | Operation insert hata verir | Tüm transaction rollback |
| CP-T-011 | Repository | İki hızlı save çakışır | En son lokal versiyon korunur |
| CP-T-012 | Integration | Taslak oluştur/kapat/aç | Son başarılı lokal yazım geri gelir |
| CP-T-013 | Integration | Offline ready kaydı | Lokal başarı, sync pending |
| CP-T-014 | Integration | Background geçişinde dirty buffer | Flush ya da açık uyarı |
| CP-T-015 | Widget | Blok ekle/düzenle/sil | Liste ve önizleme doğru |
| CP-T-016 | Widget | Screen reader ve büyük font | Erişilebilir kontroller |
| CP-T-017 | Integration | Library kendi tarif filtresi | Pattern bir kez görünür |
| CP-T-018 | Integration | Projeye link/unlink | Tarif silinmez |
| CP-T-019 | Integration | Duplicate | Yeni pattern + block ID; bağımsız veri |
| CP-T-020 | Integration | Archive ve restore | Durum ve filtre doğru |
| CP-T-021 | Integration | Soft delete | Normal arama dışı; link unavailable |
| CP-T-022 | Sync | Remote ack ağ kesintisi | Local veri + pending op korunur |
| CP-T-023 | Sync | Aynı operation iki kez | Remote'da bir kez uygulanır |
| CP-T-024 | Sync | İki cihaz farklı blokları edit eder | Güvenliyse merge, değilse conflict |
| CP-T-025 | Sync | İki cihaz aynı bloku edit eder | Sessiz overwrite yok |
| CP-T-026 | Sync | Remote delete + local edit | Data korunur, conflict |
| CP-T-027 | Security | ownerA, ownerB pattern'ini okur | Engellenir |
| CP-T-028 | Security | ownerA, ownerB pattern'ine block enjekte eder | Engellenir |
| CP-T-029 | Security | ownerA, ownerB project'ine link | Engellenir |
| CP-T-030 | Security | A offline op B oturumuyla sync | Engellenir |
| CP-T-031 | Security | HTML/script block text | Kod çalışmaz |
| CP-T-032 | Analytics | Payload başlık, not, metin içeriyor | Test fail olmalı |
| CP-T-033 | Migration | Mevcut Feature-004 pattern kayıtları | Tip, link ve içerik kaybı yok |
| CP-T-034 | Localization | TR UI/Unicode | Kayıp yok |
| CP-T-035 | Reliability | Yerel disk full | Başarısız kayıt saklanmış gibi sunulmaz |

## 4. E2E kabul senaryoları

### CP-ACC-001 — Offline tariften projeye

Given user offline; When draft + iki blok kaydeder, tekrar açar ve ready yapar; Then lokal veri aynı, Library'de tekil kayıt ve pending state vardır. Online olunca aynı ID remote'a sync edilir. Sonra kendine ait project'e link verilir.

### CP-ACC-002 — Cihazlar arası çakışma

Given aynı tarif iki cihazda açıldı; When aynı block farklı metinle güncellendi; Then conflict görünür, kullanıcı iki içeriği karşılaştırabilir, çözüm seçmeden metin silinmez.

### CP-ACC-003 — Gizlilik

Given A tarif başlığı ve özel talimat kaydetti; When B API/URI ile erişir veya analytics flush çalışır; Then B'ye içerik verilmez, telemetride metin görünmez.

## 5. Risk-temelli release engelleri

- Sessiz metin kaybı, sync sonucu yanlış “kaydedildi” mesajı
- Cross-user erişim veya unauthorized link
- İki cihazdan gelen revision'un sessiz overwrite edilmesi
- Analytics/crash log içerik sızması
- Feature-004 kayıtlarında migration veri kaybı

## 6. Release checklist

- [ ] Bütün MUST acceptance kriterleri geçti.
- [ ] Local DB rollback ve background flush testi geçti.
- [ ] İki cihaz çakışması denendi.
- [ ] Sunucu RLS + parent-child ownership testleri geçti.
- [ ] Pattern Library ile tek kayıt/link entegrasyonu doğrulandı.
- [ ] Analytics allowlist ve production logger kontrol edildi.
- [ ] Türkçe karakter/uzun blok/a11y smoke geçti.
- [ ] Açık kararlar ürün sahibi onayına bağlandı.

## 7. Sonraki çalışma

Feature-005 seti tamamlandıktan sonra feature registry'deki sıradaki modülün adı kontrol edilerek çalışılacaktır; numaraya bakıp isim uydurulmaz.
