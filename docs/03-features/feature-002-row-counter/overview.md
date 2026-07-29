# Row Counter — Overview

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/overview.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

**Row Counter**, Knitwise uygulamasında kullanıcının örgü, tığ işi, amigurumi veya benzer el işi projelerinde satır, tur, tekrar, artırma, eksiltme ve özel sayaç adımlarını takip etmesini sağlayan temel üretkenlik özelliğidir.

Birçok örgü ve tığ işi projesinde kullanıcı şu bilgileri sürekli takip etmek zorundadır:

- Kaçıncı satırda olduğu
- Kaçıncı turda olduğu
- Pattern repeat içinde nerede olduğu
- Artırma veya eksiltme yapılan satırlar
- Kol, gövde, yaka, bacak gibi parçalarda ayrı ayrı ilerleme
- Hangi sayaçların hangi project veya part ile ilişkili olduğu

Row Counter özelliğinin amacı, kullanıcının bu bilgileri manuel kağıt, not uygulaması veya fiziksel sayaç yerine Knitwise içinde güvenli ve pratik şekilde takip etmesini sağlamaktır.

---

## 2. Problem Tanımı

Örgü ve tığ işi yapan kullanıcılar genellikle proje sırasında ilerleme takibini şu yöntemlerle yapar:

- Kağıda çizik atma
- Telefon notlarına sayı yazma
- Fiziksel sıra sayacı kullanma
- Pattern PDF üzerine not alma
- Hafızadan takip etmeye çalışma
- Mesajlaşma uygulamasına kendine not gönderme

Bu yöntemlerin problemleri:

- Veri kolay kaybolur.
- Hangi sayaç hangi projeye ait karışır.
- Aynı projede birden fazla sayaç yönetmek zordur.
- Kullanıcı yanlışlıkla fazla artırabilir veya azaltabilir.
- Pattern repeat takibi zorlaşır.
- Proje birkaç gün bırakıldığında nerede kalındığı unutulur.
- Birden fazla cihazda takip yapılamaz.
- Fiziksel sayaç varsa proje bağlamı yoktur.
- Dijital not varsa structured data yoktur.

Knitwise Row Counter bu problemi proje bağlamına bağlı, güvenli, esnek ve offline çalışabilen bir sayaç sistemiyle çözer.

---

## 3. Ürün Hedefleri

Row Counter özelliğinin V1 hedefleri:

1. Kullanıcının bir project içinde sayaç oluşturmasını sağlamak.
2. Kullanıcının sayaç değerini hızlıca artırıp azaltmasını sağlamak.
3. Bir project içinde birden fazla counter desteklemek.
4. Counter değerini lokal olarak güvenli saklamak.
5. Offline kullanımda counter değerini kaybetmemek.
6. Counter'ı project ve gerektiğinde part ile ilişkilendirmek.
7. Target row veya round belirlenirse progress hesaplamasına katkı sağlamak.
8. Kullanıcı yanlışlıkla artırma/azaltma yaptığında geri alabilmesini desteklemek.
9. Counter geçmişi veya son değişiklik bilgisini en azından teknik olarak takip edilebilir yapmak.
10. Basit ama güçlü bir V1 deneyimi sunmak.

---

## 4. V1 Kapsamı

V1 Row Counter kapsamına şunlar dahildir:

- Project içinden counter oluşturma
- Bağımsız counter oluşturma, product kararı onaylanırsa
- Counter adı verme
- Counter değerini artırma
- Counter değerini azaltma
- Counter değerini manuel düzenleme
- Counter başlangıç değerini belirleme
- Counter target değeri belirleme
- Counter step değerini belirleme
- Bir project içinde birden fazla counter
- Counter'ı project ile ilişkilendirme
- Counter'ı project part ile ilişkilendirme, Multi-Part feature ile uyumlu olarak
- Counter detail ekranı
- Counter listesi veya project detail içindeki counter section
- Counter resetleme
- Counter silme veya arşivleme
- Offline counter update
- Sync pending state
- Counter progress hesaplama
- Basic undo desteği
- Counter analytics eventleri
- Counter validation ve test kapsamı

---

## 5. V1 Kapsamı Dışı

Aşağıdaki özellikler V1 kapsamına dahil değildir:

- Sesle sayaç artırma
- Apple Watch / Wear OS desteği
- Widget üzerinden sayaç artırma
- Hardware clicker entegrasyonu
- Pattern satırına otomatik bağlı akıllı sayaç
- AI ile pattern parsing ve otomatik counter oluşturma
- Karmaşık conditional counter logic
- Çok kullanıcılı shared counter
- Public counter paylaşımı
- Gerçek zamanlı collaborative counting
- Makine öğrenmesiyle hata tahmini
- Gelişmiş chart / historical graph
- Tam audit timeline UI
- Barcode veya NFC sayaç tetikleme

Bu özellikler V1 sonrası değerlendirilebilir.

---

## 6. Kullanıcı Değeri

Row Counter kullanıcının şu faydaları elde etmesini sağlar:

- Projede nerede kaldığını unutmaz.
- Aynı project içinde birden fazla parçayı takip edebilir.
- Yanlış satır sayma riski azalır.
- Fiziksel sayaç ihtiyacı azalır.
- Pattern ve project bağlamı korunur.
- Offline durumda bile çalışmaya devam eder.
- Project progress daha anlamlı hale gelir.
- Uygulama günlük kullanım alışkanlığı kazanır.
- Kullanıcı uygulamaya tekrar dönmek için güçlü bir sebep bulur.

---

## 7. Hedef Kullanıcılar

### 7.1 Yeni Başlayan Kullanıcı

Yeni başlayan kullanıcı basit bir projede kaçıncı satırda olduğunu takip etmek ister.

İhtiyacı:

- Tek butonla artırma
- Yanlış artırdıysa azaltma
- Karmaşık ayarlarla uğraşmama
- Project içinde sayaç görme

### 7.2 Orta Seviye Kullanıcı

Orta seviye kullanıcı birden fazla sayaçla çalışır.

İhtiyacı:

- Ana gövde sayacı
- Kol sayacı
- Pattern repeat sayacı
- Target row
- Progress görünümü

### 7.3 İleri Seviye Kullanıcı

İleri kullanıcı daha karmaşık projelerde farklı parçalar ve tekrarlar takip eder.

İhtiyacı:

- Çoklu counter
- Part ilişkisi
- Step değeri
- Reset
- Counter geçmişi
- Daha detaylı kontrol

### 7.4 Amigurumi Kullanıcısı

Amigurumi kullanıcısı genellikle tur bazlı ilerler.

İhtiyacı:

- Round counter
- Parça bazlı counter
- Kulak, kol, bacak gibi benzer parçaları takip etme
- Target round

---

## 8. Ana Kullanım Senaryoları

### 8.1 Project İçinden Counter Oluşturma

Kullanıcı bir project açar ve project detail ekranından yeni counter oluşturur.

Örnek:

```text
Project Detail
→ Counters
→ Add Counter
→ Name: Gövde
→ Type: Row
→ Start: 1
→ Target: 120
→ Save
```

### 8.2 Counter Değerini Artırma

Kullanıcı bir satır ördükten sonra sayaçtaki `+` butonuna basar.

Beklenen:

- Counter değeri step kadar artar.
- Değer lokal kaydedilir.
- UI anında güncellenir.
- Sync gerekiyorsa pending state oluşur.

### 8.3 Counter Değerini Azaltma

Kullanıcı yanlışlıkla fazla artırdıysa `-` butonuna basar.

Beklenen:

- Counter değeri step kadar azalır.
- Minimum değerin altına düşmez.
- Değişiklik lokal kaydedilir.

### 8.4 Target ile Progress Görme

Kullanıcı target değeri belirlerse counter progress hesaplanabilir.

Örnek:

```text
Current row: 30
Target row: 100
Progress: 30%
```

### 8.5 Birden Fazla Counter

Bir project içinde şu counterlar olabilir:

- Gövde
- Sağ kol
- Sol kol
- Yaka
- Pattern repeat
- Artırma sayacı

---

## 9. Temel Kavramlar

| Kavram | Açıklama |
|---|---|
| Counter | Satır, tur veya tekrar saymak için kullanılan sayaç |
| Counter Value | Güncel sayaç değeri |
| Step | Her artırma/azaltmada uygulanacak değer |
| Target | Ulaşılması hedeflenen değer |
| Counter Type | Row, round, repeat veya custom sayaç tipi |
| Linked Project | Counter'ın bağlı olduğu project |
| Linked Part | Counter'ın bağlı olduğu project part |
| Reset | Counter değerini başlangıç değerine döndürme |
| Undo | Son counter değişikliğini geri alma |
| Sync Status | Counter'ın lokal/remote sync durumu |

---

## 10. Counter Tipleri

V1 için önerilen counter tipleri:

```text
row
round
repeat
increase
decrease
custom
```

| Tip | Açıklama |
|---|---|
| `row` | Satır sayacı |
| `round` | Tur sayacı |
| `repeat` | Pattern repeat sayacı |
| `increase` | Artırma sayacı |
| `decrease` | Eksiltme sayacı |
| `custom` | Kullanıcının özel amacı için sayaç |

---

## 11. Başarı Kriterleri

Row Counter başarılı sayılırsa:

- Kullanıcı project içinde kolayca counter oluşturabiliyor.
- Counter değeri tek dokunuşla artırılıp azaltılabiliyor.
- Counter değerleri uygulama kapansa bile korunuyor.
- Offline kullanımda counter kaybolmuyor.
- Bir project içinde birden fazla counter yönetilebiliyor.
- Target değeri varsa progress hesaplanabiliyor.
- Counter güncellemeleri Project Management progress sistemiyle uyumlu çalışıyor.
- Yanlışlıkla yapılan sayaç değişiklikleri kullanıcıyı çaresiz bırakmıyor.
- Analytics PII içermeden kullanım davranışını ölçüyor.
- Security ve ownership kuralları korunuyor.

---

## 12. Ürün Metrikleri

V1 için izlenebilecek metrikler:

| Metrik | Amaç |
|---|---|
| Counter creation rate | Project oluşturan kullanıcıların counter oluşturma oranı |
| Counter increment frequency | Counter'ın aktif kullanılıp kullanılmadığı |
| Multiple counter usage rate | Bir project içinde birden fazla counter kullanımı |
| Target usage rate | Kullanıcıların target belirleme oranı |
| Counter linked to project rate | Counterların project bağlamında kullanımı |
| Offline counter update rate | Offline sayaç kullanım oranı |
| Counter reset rate | Kullanıcıların reset ihtiyacı |
| Undo usage rate | Yanlış artırma/azaltma sıklığı |
| Counter completion rate | Target'a ulaşan counter oranı |

---

## 13. Riskler

| Risk | Açıklama | Önlem |
|---|---|---|
| Yanlışlıkla fazla artırma | Kullanıcı yanlış tap yapabilir | Decrease ve undo desteği |
| Offline sync conflict | İki cihazda aynı counter değişebilir | Conflict strategy |
| Counter UI karmaşası | Çok fazla ayar yeni kullanıcıyı yorabilir | Quick counter flow |
| Project ile ilişki kopması | Project silinirse counter orphan kalabilir | Soft delete ve relationship rule |
| Target yanlış girilir | Progress anlamsız olur | Validation |
| Event spam | Her increment analytics'e giderse çok fazla event oluşur | Aggregated analytics veya throttle |

---

## 14. Bağımlılıklar

Row Counter şu feature'larla ilişkilidir:

- `feature-001-project-management`
- `feature-003-multi-part-tracking`
- `feature-004-pattern-library`
- `feature-017-local-persistence`
- `feature-018-cloud-sync`
- `feature-020-statistics`
- `feature-014-premium`, ileride gelişmiş counter limiti olursa

---

## 15. Açık Ürün Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-OD-001 | Bağımsız counter V1'de olacak mı? | Project bağlı counter öncelikli, bağımsız opsiyonel | Open |
| RC-OD-002 | Default başlangıç değeri ne? | 1 veya 0 ürün kararına bağlı | Open |
| RC-OD-003 | Counter target zorunlu mu? | Hayır | Open |
| RC-OD-004 | Undo V1'de olacak mı? | Basit son işlem undo önerilir | Open |
| RC-OD-005 | Counter history UI olacak mı? | V1'de şart değil | Open |
| RC-OD-006 | Increment tap analytics detay seviyesi | Aggregated önerilir | Open |
| RC-OD-007 | Counter silme mi arşivleme mi? | Soft delete önerilir | Open |
| RC-OD-008 | Haptic feedback olacak mı? | Mobil deneyim için önerilir | Open |

---

## 16. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/requirements.md
```

`requirements.md` içinde Row Counter için fonksiyonel gereksinimler, iş kuralları, validasyon kuralları, edge case'ler ve acceptance criteria detaylandırılacaktır.
