# Multi-Part Tracking — Overview

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/overview.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

**Multi-Part Tracking**, Knitwise uygulamasında bir projenin birden fazla parça, bölüm veya alt iş kalemi halinde takip edilmesini sağlayan temel proje yönetimi özelliğidir.

Örgü, tığ işi ve amigurumi projelerinde birçok ürün tek bir bütün olarak ilerlemez. Kullanıcı çoğu zaman şu parçaları ayrı ayrı takip eder:

- Gövde
- Sağ kol
- Sol kol
- Ön parça
- Arka parça
- Yaka
- Manşet
- Pat
- Kulak
- Kol
- Bacak
- Kuyruk
- Motif
- Kare
- Panel
- Renk bölümü

Multi-Part Tracking özelliğinin amacı, kullanıcının bir project içinde bu parçaları ayrı tanımlamasını, her parçanın durumunu ve ilerlemesini takip etmesini, ilgili row counter veya yarn kullanımını parça düzeyinde ilişkilendirmesini sağlamaktır.

---

## 2. Problem Tanımı

Kullanıcı bir project içinde yalnızca genel progress görürse karmaşık projelerde nerede kaldığını takip etmek zorlaşır.

Örnek problemler:

- Sağ kol tamamlandı ama sol kol yarım kaldı.
- Gövde bitti ama yaka başlanmadı.
- Amigurumi içinde kulaklar tamamlandı ama bacaklar eksik.
- Bir battaniye projesinde motiflerin kaç tanesinin bittiği unutuldu.
- Her parçanın ayrı row counter ihtiyacı var.
- Her parçaya farklı yarn kullanılabilir.
- Project genel durumu completed görünse bile alt parçaların bazıları eksik olabilir.
- Kullanıcı birkaç gün ara verdiğinde hangi parçada kaldığını hatırlamayabilir.

Multi-Part Tracking bu sorunu project içi parça modeliyle çözer.

---

## 3. Ürün Hedefleri

V1 hedefleri:

1. Kullanıcının project içinde part oluşturmasını sağlamak.
2. Her part için ad, tip, status ve progress tutmak.
3. Bir project içinde birden fazla part yönetmek.
4. Part sıralamasını kullanıcıya anlaşılır göstermek.
5. Part status lifecycle tanımlamak.
6. Part ile Row Counter ilişkisi kurmak.
7. Part ile yarn/tool ilişkilerine zemin hazırlamak.
8. Project progress hesaplamasında part progress bilgisini kullanmak.
9. Offline durumda part oluşturma ve güncellemeyi desteklemek.
10. Broken relationship durumlarında project ekranının bozulmasını engellemek.

---

## 4. V1 Kapsamı

V1 kapsamına dahildir:

- Project içinden part oluşturma
- Part adı girme
- Part type seçme
- Part status yönetimi
- Part progress yüzdesi
- Part sıralaması
- Part detail ekranı
- Part edit
- Part soft delete
- Part duplicate, opsiyonel
- Part ile counter ilişkilendirme
- Part completion project progress’e katkı
- Offline part create/update
- Sync status
- Analytics eventleri
- Security ve owner isolation
- Testing kapsamı

---

## 5. V1 Kapsam Dışı

V1 kapsam dışı:

- AI ile pattern’den otomatik part çıkarma
- Gelişmiş dependency graph
- Partlar arası zorunlu sıralı çalışma kilidi
- Gantt chart
- Çok kullanıcılı collaborative part tracking
- Public part sharing
- Advanced BOM / material allocation
- Pattern satırına otomatik bağlı part generation
- Fotoğraf tabanlı part progress tahmini
- Tam üretim takvimi

---

## 6. Kullanıcı Değeri

Multi-Part Tracking kullanıcıya şunları sağlar:

- Büyük projeleri daha küçük takip edilebilir parçalara böler.
- Hangi parçanın tamamlandığını net görür.
- Birden fazla row counter’ı parça bağlamında yönetebilir.
- Project progress daha doğru hesaplanır.
- Amigurumi ve giyim projeleri daha düzenli takip edilir.
- Kullanıcı proje ara verdiğinde nerede kaldığını kolay hatırlar.
- Project detail ekranı daha anlamlı olur.

---

## 7. Hedef Kullanıcılar

### 7.1 Giyim Ürünü Ören Kullanıcı

Örnek parçalar:

- Ön
- Arka
- Sağ kol
- Sol kol
- Yaka
- Manşet

### 7.2 Amigurumi Kullanıcısı

Örnek parçalar:

- Baş
- Gövde
- Kol
- Bacak
- Kulak
- Kuyruk
- Aksesuar

### 7.3 Battaniye / Motif Kullanıcısı

Örnek parçalar:

- Kare motifler
- Panel
- Kenar bordür
- Birleştirme
- Son işlem

### 7.4 İleri Seviye Kullanıcı

İhtiyaçları:

- Çoklu part
- Part counter
- Part progress
- Part status
- Part notları
- Parça sırası

---

## 8. Temel Kavramlar

| Kavram | Açıklama |
|---|---|
| `ProjectPart` | Project içindeki takip edilebilir parça |
| `partType` | Gövde, kol, motif gibi sınıflandırma |
| `partStatus` | Not started, active, paused, completed gibi durum |
| `partOrder` | Project içindeki sıralama |
| `partProgress` | Parça progress değeri |
| `linkedCounter` | Part ile ilişkili row counter |
| `linkedYarn` | Part ile ilişkili yarn bilgisi, V1 opsiyonel |
| `soft delete` | Part’ın normal listeden kaldırılması |
| `broken relationship` | Part’ın ilişkili counter/project kaydının eksik olması |

---

## 9. Part Tipleri

V1 önerilen part type değerleri:

```text
body
front
back
sleeve
right_sleeve
left_sleeve
collar
cuff
hem
panel
motif
square
head
arm
leg
ear
tail
accessory
border
assembly
finishing
custom
```

UI label lokalize edilmelidir. Enum value İngilizce ve stabil kalmalıdır.

---

## 10. Başarı Kriterleri

Feature başarılı sayılırsa:

- Kullanıcı project içinde part oluşturabiliyor.
- Partlar project detail içinde anlaşılır listeleniyor.
- Kullanıcı her part’ın status bilgisini yönetebiliyor.
- Completed partlar project progress’e katkı sağlıyor.
- Part ile counter bağlanabiliyor.
- Offline durumda part değişiklikleri kaybolmuyor.
- Deleted part normal listelerde görünmüyor.
- Analytics PII içermiyor.
- Owner isolation korunuyor.

---

## 11. Bağımlılıklar

- `feature-001-project-management`
- `feature-002-row-counter`
- `feature-004-pattern-library`
- `feature-006-yarn-inventory`
- `feature-017-local-persistence`
- `feature-018-cloud-sync`
- `feature-020-statistics`

---

## 12. Açık Ürün Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| MPT-OD-001 | Part progress manuel mi otomatik mi? | V1 manuel + counter destekli | Open |
| MPT-OD-002 | Default part template olacak mı? | V1’de opsiyonel | Open |
| MPT-OD-003 | Part duplicate olacak mı? | Amigurumi için faydalı | Open |
| MPT-OD-004 | Part dependency olacak mı? | V1 dışı | Open |
| MPT-OD-005 | Project progress partlardan mı hesaplanacak? | V1’de varsa part öncelikli | Open |
| MPT-OD-006 | Part recovery UI olacak mı? | V1’de zorunlu değil | Open |

---

## 13. Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/requirements.md
```
