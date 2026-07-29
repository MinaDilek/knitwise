# Knitwise Feature Documentation

## Document Information

| Alan         | Değer                       |
| ------------ | --------------------------- |
| Product      | Knitwise                    |
| Document     | Feature Documentation Index |
| Version      | 1.0                         |
| Status       | Active                      |
| Owner        | Product                     |
| Last Updated | 2026-07-29                  |

---

# 1. Purpose

Bu klasör, Knitwise uygulamasında bulunan veya ürün yol haritasında planlanan özelliklerin ayrıntılı ürün ve geliştirme belgelerini içerir.

Her özellik ayrı bir klasör altında belgelenir.

Amaç:

* Her özelliğin kapsamını açık biçimde tanımlamak
* Ürün kararları ile teknik uygulama arasında ortak bir kaynak oluşturmak
* Codex ve geliştiricilerin ek açıklama gerektirmeden geliştirme yapabilmesini sağlamak
* Kullanıcı hikâyelerini, iş kurallarını ve kabul kriterlerini merkezi biçimde tutmak
* Özellikler arası bağımlılıkları görünür hale getirmek
* Test, güvenlik, analytics ve veri modeli gereksinimlerini geliştirme öncesinde belirlemek
* Kapsam değişikliklerinin kontrolsüz biçimde koda yansımasını önlemek

Bu klasördeki belgeler, özellik geliştirme sürecinin ana ürün doğruluk kaynağıdır.

---

# 2. Documentation Principles

Knitwise feature belgeleri aşağıdaki ilkelere göre hazırlanır.

## 2.1 Documentation Before Implementation

Bir özellik, gerekli belgeleri tamamlanmadan geliştirmeye hazır kabul edilmez.

Kodlama başlamadan önce en az aşağıdaki belgeler hazırlanmalıdır:

* `overview.md`
* `user-stories.md`
* `requirements.md`
* `business-rules.md`
* `user-flows.md`
* `data-model.md`
* `edge-cases.md`
* `acceptance-criteria.md`
* `testing.md`

Özelliğin yapısına bağlı olarak diğer belgeler de zorunlu olabilir.

## 2.2 One Feature, One Folder

Her ana ürün özelliği ayrı klasör altında tutulur.

Örnek:

```text
feature-001-project-management/
feature-002-row-counter/
feature-007-yarn-inventory/
```

Birbirinden bağımsız kullanıcı değeri oluşturan özellikler aynı klasörde birleştirilmemelidir.

## 2.3 Product Language First

Belgeler öncelikle ürün davranışını tanımlamalıdır.

Teknik çözüm, ürün davranışının önüne geçmemelidir.

Örneğin:

Yanlış yaklaşım:

> Bu ekranda Riverpod StateNotifier kullanılacaktır.

Doğru yaklaşım:

> Kullanıcının sayaç değeri her değişiklikten sonra yerel olarak kaydedilmelidir.

Riverpod veya repository seçimi teknik mimari belgesinde tanımlanabilir.

## 2.4 Explicit Rules

Belirsiz ifadeler kullanılmamalıdır.

Kaçınılması gereken ifadeler:

* Gerektiğinde
* Uygun şekilde
* Kullanıcı dostu
* Hızlı çalışmalıdır
* Güvenli olmalıdır
* Mümkünse
* Benzeri
* Ve sair

Bu ifadeler ölçülebilir veya uygulanabilir kurallarla değiştirilmelidir.

Örnek:

Belirsiz:

> Sayaç hızlı çalışmalıdır.

Açık:

> Kullanıcının artırma düğmesine dokunması ile ekrandaki sayının değişmesi arasındaki gecikme normal cihazlarda 100 milisaniyenin altında hedeflenmelidir.

## 2.5 No Hidden Decisions

Bir özelliğin kapsamını etkileyen kararlar yalnızca kod içinde bırakılmamalıdır.

Önemli kararlar:

* feature belgesine,
* `DECISIONS.md` dosyasına,
* teknik mimari belgesine

uygun biçimde eklenmelidir.

## 2.6 Traceability

Her gereksinim mümkün olduğunda benzersiz bir kimlik taşımalıdır.

Örnek:

```text
PM-FR-001
PM-BR-001
PM-AC-001
PM-EC-001
```

Kimlik yapısı:

```text
FEATURE_PREFIX-DOCUMENT_TYPE-NUMBER
```

Örnek belge türleri:

| Kod  | Anlamı                     |
| ---- | -------------------------- |
| FR   | Functional Requirement     |
| NFR  | Non-Functional Requirement |
| BR   | Business Rule              |
| US   | User Story                 |
| AC   | Acceptance Criterion       |
| EC   | Edge Case                  |
| AN   | Analytics Event            |
| SEC  | Security Requirement       |
| TEST | Test Scenario              |

---

# 3. Feature Documentation Structure

Standart bir feature klasörü aşağıdaki yapıyı kullanır:

```text
feature-xxx-feature-name/
├── overview.md
├── user-stories.md
├── requirements.md
├── business-rules.md
├── user-flows.md
├── data-model.md
├── edge-cases.md
├── acceptance-criteria.md
├── analytics.md
├── security-privacy.md
└── testing.md
```

Bazı özellikler konuya özel ek belgeler içerebilir.

Örnek:

```text
feature-009-material-recommendations/
├── matching-rules.md
└── scoring-model.md
```

---

# 4. Standard Document Responsibilities

## 4.1 `overview.md`

Özelliğin genel ürün tanımını içerir.

En az şu başlıkları kapsamalıdır:

* Amaç
* Kullanıcı problemi
* Çözüm
* Ürün değeri
* Hedef kullanıcılar
* Kapsam
* Kapsam dışı
* Bağımlılıklar
* Varsayımlar
* Riskler
* Başarı ölçütleri
* Açık kararlar

Bu belge, bir kişinin özelliği ilk kez tanımak için okuyacağı ana belgedir.

## 4.2 `user-stories.md`

Özelliğin kullanıcı hikâyelerini içerir.

Her kullanıcı hikâyesi şu formatta yazılmalıdır:

```text
Bir [kullanıcı tipi] olarak,
[aksiyon] yapmak istiyorum,
böylece [elde edilen değer].
```

Her hikâyede mümkün olduğunda:

* ön koşullar,
* ana senaryo,
* alternatif senaryo,
* hata senaryosu,
* ilgili gereksinimler,
* ilgili kabul kriterleri

bulunmalıdır.

## 4.3 `requirements.md`

Fonksiyonel ve fonksiyonel olmayan gereksinimleri içerir.

### Functional Requirements

Sistemin ne yapacağını tanımlar.

Örnek:

> Kullanıcı aktif bir projeyi arşivleyebilmelidir.

### Non-Functional Requirements

Sistemin hangi kalite seviyesinde çalışacağını tanımlar.

Örnek kategoriler:

* Performans
* Güvenilirlik
* Erişilebilirlik
* Offline davranış
* Ölçeklenebilirlik
* Yerelleştirme
* Uyumluluk

## 4.4 `business-rules.md`

Ürün davranışını yöneten zorunlu kuralları içerir.

Örnek:

* Bir proje aynı anda yalnızca bir ana duruma sahip olabilir.
* Tamamlanan proje varsayılan olarak salt okunur olmamalıdır.
* Proje adı boş bırakılamaz.
* Ücretsiz kullanıcı aktif proje limitini aşamaz.

İş kuralları ekran tasarımından bağımsız yazılmalıdır.

## 4.5 `user-flows.md`

Kullanıcının özelliği kullanırken izlediği adımları içerir.

Her akışta:

* başlangıç noktası,
* ön koşullar,
* ana adımlar,
* alternatif yollar,
* hata yolları,
* tamamlanma durumu

bulunmalıdır.

Gerekirse Mermaid diyagramı kullanılabilir.

## 4.6 `data-model.md`

Özelliğin kullandığı entity, alan ve ilişkileri tanımlar.

Her entity için:

* alan adı,
* veri tipi,
* zorunluluk,
* varsayılan değer,
* validation,
* açıklama,
* gizlilik seviyesi

belirtilmelidir.

Bu belge nihai database migration dosyasının yerine geçmez ancak migration tasarımının ürün temelini oluşturur.

## 4.7 `edge-cases.md`

Normal kullanıcı akışının dışındaki senaryoları içerir.

Örnek:

* Kullanıcı uygulamayı kayıt sırasında kapatır.
* Sayaç değeri cihaz kapanırken değişir.
* Aynı proje iki cihazda düzenlenir.
* Kullanıcının internet bağlantısı kesilir.
* Envanter miktarı projeye ayrılan miktarın altına düşürülür.

Her edge case için beklenen sistem davranışı açıkça yazılmalıdır.

## 4.8 `acceptance-criteria.md`

Özelliğin tamamlanmış kabul edilme koşullarını içerir.

Kabul kriterleri mümkün olduğunda Given–When–Then formatında yazılmalıdır.

Örnek:

```gherkin
Given kullanıcının aktif bir projesi vardır
When kullanıcı projeyi arşivler
Then proje aktif proje listesinden kaldırılmalıdır
And arşivlenmiş projeler listesinde gösterilmelidir
And proje verileri korunmalıdır
```

## 4.9 `analytics.md`

Özellik için izlenecek olayları ve metrikleri içerir.

Her event için:

* event adı,
* tetiklenme anı,
* parametreler,
* kullanıcı amacı,
* gizlilik kısıtları,
* başarı metriği bağlantısı

tanımlanmalıdır.

Kullanıcının özel proje metinleri, tarif içerikleri ve kişisel notları analytics parametresi olarak gönderilmemelidir.

## 4.10 `security-privacy.md`

Özellik özelindeki güvenlik ve gizlilik gereksinimlerini içerir.

En az şu konular değerlendirilmelidir:

* Yetkilendirme
* Kullanıcılar arası veri izolasyonu
* Row Level Security
* Storage erişimi
* Hassas veri
* Loglama
* Analytics gizliliği
* Veri dışa aktarma
* Veri silme
* Offline veri güvenliği

## 4.11 `testing.md`

Özelliğin test stratejisini içerir.

Test kategorileri:

* Unit tests
* Widget tests
* Integration tests
* End-to-end tests
* Manual tests
* Accessibility tests
* Performance tests
* Security tests
* Offline tests
* Migration tests

Her test, mümkün olduğunda ilgili gereksinim ve kabul kriterine bağlanmalıdır.

---

# 5. Feature Status Lifecycle

Her özellik aşağıdaki yaşam döngüsünden geçer.

```text
Proposed
↓
Discovery
↓
Documenting
↓
Ready for Design
↓
Ready for Development
↓
In Development
↓
In Review
↓
In Testing
↓
Ready for Release
↓
Released
↓
Measuring
```

## Proposed

Özellik fikir aşamasındadır.

Henüz yol haritasına alınmamış olabilir.

## Discovery

Kullanıcı problemi ve ürün değeri araştırılır.

## Documenting

Feature belgeleri hazırlanır.

## Ready for Design

Ürün kapsamı ve kullanıcı akışları tasarım için yeterlidir.

## Ready for Development

Gereksinimler, iş kuralları ve kabul kriterleri tamamlanmıştır.

## In Development

Kodlama devam etmektedir.

## In Review

Kod, ürün ve tasarım kontrolleri yapılmaktadır.

## In Testing

QA ve kabul testleri uygulanmaktadır.

## Ready for Release

Özellik tüm yayın kriterlerini karşılamıştır.

## Released

Özellik production ortamına yayınlanmıştır.

## Measuring

Gerçek kullanım verileri ve geri bildirimler incelenmektedir.

---

# 6. Feature Priority Levels

Özellik öncelikleri `02-prd/feature-priorities.md` belgesine göre yönetilir.

| Priority | Meaning                            |
| -------- | ---------------------------------- |
| P0       | V1 için zorunlu                    |
| P1       | Yüksek değerli, V1 veya erken V1.x |
| P2       | Sonraki sürüm adayı                |
| P3       | Gelecek değerlendirmesi            |

Feature klasöründe yazan öncelik ile ana öncelik belgesi çelişirse:

```text
02-prd/feature-priorities.md
```

ana kaynak kabul edilir.

Çelişki giderilmeden geliştirmeye başlanmamalıdır.

---

# 7. Feature Index

## Core Product Features

| ID  | Feature                   | Priority | Planned Release | Status      |
| --- | ------------------------- | -------: | --------------- | ----------- |
| 001 | Project Management        |       P0 | V1              | Documenting |
| 002 | Smart Row Counter         |       P0 | V1              | Documenting |
| 003 | Multi-Part Tracking       |       P0 | V1              | Documenting |
| 004 | Pattern Library           |       P0 | V1              | Documenting |
| 005 | Custom Patterns           |       P1 | V1 / V1.x       | Documenting |
| 006 | Starter Patterns          |       P1 | V1              | Documenting |
| 007 | Yarn Inventory            |       P0 | V1              | Documenting |
| 008 | Hook and Needle Inventory |       P0 | V1              | Documenting |

## Smart Tools

| ID  | Feature                        | Priority | Planned Release | Status      |
| --- | ------------------------------ | -------: | --------------- | ----------- |
| 009 | Smart Material Recommendations |       P1 | V1 / V1.x       | Documenting |
| 010 | Yarn Finisher                  |       P1 | V1 / V1.x       | Documenting |
| 011 | Yarn Calculator                |       P1 | V1 / V1.x       | Documenting |
| 012 | Knitting and Crochet Glossary  |       P1 | V1              | Documenting |
| 013 | Voice Commands                 |       P2 | V1.x / V2       | Documenting |

## Platform and Commercial Features

| ID  | Feature                       | Priority | Planned Release | Status      |
| --- | ----------------------------- | -------: | --------------- | ----------- |
| 014 | Premium and Subscriptions     |       P0 | V1              | Documenting |
| 015 | Onboarding and Authentication |       P0 | V1              | Documenting |
| 016 | Settings and Privacy          |       P0 | V1              | Documenting |
| 017 | Local Persistence             |       P0 | V1              | Documenting |
| 018 | Cloud Sync                    |       P2 | V2              | Documenting |
| 019 | Notifications                 |       P2 | V1.x            | Documenting |
| 020 | Statistics                    |       P2 | V1.x / V2       | Documenting |

Planlanan sürümler nihai taahhüt değildir.

Gerçek kapsam:

* kullanıcı araştırması,
* teknik risk,
* geliştirme kapasitesi,
* beta geri bildirimleri,
* yayın kalitesi

doğrultusunda değişebilir.

---

# 8. Cross-Feature Dependencies

Özellikler birbirinden bağımsız geliştirilse bile bazı temel bağımlılıklar bulunur.

## 8.1 Project Management Dependencies

Project Management aşağıdaki özelliklerin merkezidir:

* Row Counter
* Multi-Part Tracking
* Pattern Library
* Yarn Inventory
* Material Recommendations
* Statistics
* Notifications

Bu nedenle proje veri modeli diğer feature'lar başlamadan önce kararlı hale getirilmelidir.

## 8.2 Pattern Dependencies

Pattern Library aşağıdaki özelliklerle ilişkilidir:

* Custom Patterns
* Starter Patterns
* Project Management
* Smart Material Recommendations
* Glossary
* AI Pattern Helper

## 8.3 Inventory Dependencies

Yarn Inventory aşağıdaki özelliklerin temelidir:

* Material Recommendations
* Yarn Finisher
* Yarn Calculator
* Project Material Allocation
* Statistics

## 8.4 Platform Dependencies

Aşağıdaki platform özellikleri tüm modülleri etkiler:

* Authentication
* Local Persistence
* Settings and Privacy
* Premium
* Cloud Sync
* Analytics

---

# 9. Requirement ID Prefixes

Her özellik için önerilen prefix aşağıdaki gibidir.

| Feature                       | Prefix |
| ----------------------------- | ------ |
| Project Management            | PM     |
| Row Counter                   | RC     |
| Multi-Part Tracking           | MPT    |
| Pattern Library               | PL     |
| Custom Patterns               | CP     |
| Starter Patterns              | SP     |
| Yarn Inventory                | YI     |
| Hook and Needle Inventory     | HNI    |
| Material Recommendations      | MR     |
| Yarn Finisher                 | YF     |
| Yarn Calculator               | YC     |
| Glossary                      | GL     |
| Voice Commands                | VC     |
| Premium                       | PRM    |
| Onboarding and Authentication | OA     |
| Settings and Privacy          | SET    |
| Local Persistence             | LP     |
| Cloud Sync                    | CS     |
| Notifications                 | NOT    |
| Statistics                    | STA    |

Örnek:

```text
RC-FR-001
YI-BR-004
PRM-AC-012
CS-EC-006
```

---

# 10. Definition of Ready

Bir özellik geliştirmeye hazır kabul edilmek için:

* Kullanıcı problemi açıkça tanımlanmalıdır.
* Hedef kullanıcı belirlenmelidir.
* Kapsam ve kapsam dışı alanlar yazılmalıdır.
* Kullanıcı hikâyeleri tamamlanmalıdır.
* Fonksiyonel gereksinimler tanımlanmalıdır.
* Fonksiyonel olmayan gereksinimler tanımlanmalıdır.
* İş kuralları yazılmalıdır.
* Ana kullanıcı akışları belirlenmelidir.
* Veri modeli taslağı hazırlanmalıdır.
* Edge case'ler değerlendirilmelidir.
* Kabul kriterleri yazılmalıdır.
* Güvenlik ve gizlilik gereksinimleri belirlenmelidir.
* Test yaklaşımı hazırlanmalıdır.
* Özellik bağımlılıkları tanımlanmalıdır.
* Açık kritik ürün kararı bulunmamalıdır.
* Product Owner kapsamı onaylamalıdır.

Analytics belgesi ilk sürümde eksikse özellik geliştirmeye başlanabilir ancak release öncesinde tamamlanmalıdır.

---

# 11. Definition of Done

Bir özellik tamamlanmış kabul edilmek için:

* Tüm zorunlu gereksinimler uygulanmalıdır.
* Kabul kriterleri geçmelidir.
* Unit testler tamamlanmalıdır.
* Widget veya UI testleri tamamlanmalıdır.
* Integration testleri tamamlanmalıdır.
* Offline davranış test edilmelidir.
* Erişilebilirlik kontrolleri yapılmalıdır.
* Analytics event'leri doğrulanmalıdır.
* Güvenlik gereksinimleri uygulanmalıdır.
* RLS politikaları test edilmelidir.
* Bilinen kritik veya yüksek hata bulunmamalıdır.
* İlgili feature belgeleri güncellenmelidir.
* `CHANGELOG.md` güncellenmelidir.
* Product Owner kabulü alınmalıdır.

---

# 12. Change Management

Bir feature belgesinde önemli değişiklik yapıldığında:

1. Değişiklik ilgili dosyada uygulanır.
2. Etkilenen diğer feature belgeleri kontrol edilir.
3. Teknik mimariye etkisi değerlendirilir.
4. Test senaryoları güncellenir.
5. Gerekirse `DECISIONS.md` güncellenir.
6. Gerekirse `CHANGELOG.md` güncellenir.
7. Değişiklik commit mesajında açıkça belirtilir.

Örnek commit mesajı:

```text
docs(project-management): define project archive rules
```

---

# 13. Source of Truth Hierarchy

Belgeler arasında çelişki bulunursa aşağıdaki sıralama kullanılır:

1. Onaylanmış `DECISIONS.md` kararı
2. Güncel feature iş kuralları
3. Güncel feature gereksinimleri
4. Feature acceptance criteria
5. Ana PRD
6. Roadmap
7. Kod davranışı

Kod ile belge çelişiyorsa kod otomatik olarak doğru kabul edilmez.

Beklenen ürün davranışı belirlenmeli ve belge ile kod aynı hale getirilmelidir.

---

# 14. Codex Usage

Codex'e feature geliştirme görevi verilirken ilgili klasörün tamamı bağlam olarak sunulmalıdır.

Minimum okunması gereken dosyalar:

```text
overview.md
user-stories.md
requirements.md
business-rules.md
user-flows.md
data-model.md
edge-cases.md
acceptance-criteria.md
security-privacy.md
testing.md
```

Codex'ten geliştirmeye başlamadan önce:

* çelişkili gereksinimleri listelemesi,
* açık kararları belirtmesi,
* mevcut mimariyle uyumu kontrol etmesi,
* geliştirme planı oluşturması

istenmelidir.

Codex belgede açıkça bulunmayan önemli ürün kararlarını kendi başına vermemelidir.

---

# 15. Maintenance Rules

Feature belgeleri yalnızca başlangıçta hazırlanıp bırakılmamalıdır.

Aşağıdaki durumlarda güncellenmelidir:

* Kullanıcı davranışı değiştiğinde
* Yeni iş kuralı eklendiğinde
* Veri modeli değiştiğinde
* Premium sınırı değiştiğinde
* Yeni edge case bulunduğunda
* Güvenlik gereksinimi değiştiğinde
* Yeni analytics event'i eklendiğinde
* Özellik farklı sürüme taşındığında
* Özellik kaldırıldığında veya deprecated olduğunda

Her büyük sürüm öncesinde feature belgeleri gözden geçirilmelidir.

---

# 16. References

Bu klasör aşağıdaki ana belgelerle birlikte değerlendirilmelidir:

* `README.md`
* `AGENTS.md`
* `PROJECT_PRINCIPLES.md`
* `DECISIONS.md`
* `CONTRIBUTING.md`
* `CHANGELOG.md`
* `01-product/`
* `02-prd/overview.md`
* `02-prd/mvp-roadmap.md`
* `02-prd/feature-priorities.md`
* `02-prd/premium-strategy.md`
* `02-prd/release-plan.md`
* `04-ux/`
* `05-ui/`
* `06-technical/`
* `09-testing/`
