# Project Management — Overview

## Document Information

| Alan               | Değer              |
| ------------------ | ------------------ |
| Product            | Knitwise           |
| Feature ID         | FEATURE-001        |
| Feature Name       | Project Management |
| Requirement Prefix | PM                 |
| Priority           | P0                 |
| Planned Release    | V1                 |
| Status             | Documenting        |
| Product Owner      | Product            |
| Technical Owner    | TBD                |
| Version            | 1.0                |
| Last Updated       | 2026-07-29         |

---

# 1. Purpose

Project Management, Knitwise uygulamasının merkezî özelliğidir.

Bu özellik kullanıcıların örgü ve tığ işi projelerini oluşturmasını, düzenlemesini, takip etmesini, duraklatmasını, tamamlamasını ve arşivlemesini sağlar.

Knitwise içindeki aşağıdaki modüller doğrudan veya dolaylı olarak bir projeye bağlanır:

* Smart Row Counter
* Multi-Part Tracking
* Pattern Library
* Custom Patterns
* Starter Patterns
* Yarn Inventory
* Hook and Needle Inventory
* Smart Material Recommendations
* Yarn Finisher
* Notifications
* Statistics
* Cloud Sync

Project Management yalnızca bir proje listesi değildir.

Bu özellik, kullanıcının örgü sürecine ait tüm bilgileri tek bir çalışma alanında birleştiren ana ürün omurgasıdır.

---

# 2. User Problem

Örgü kullanıcıları aynı anda birden fazla proje üzerinde çalışabilir.

Bir proje sırasında aşağıdaki bilgilerin takip edilmesi gerekir:

* Proje adı
* Proje türü
* Kullanılan tarif
* Kullanılan ip
* Şiş veya tığ bilgisi
* Başlangıç tarihi
* Proje durumu
* Sıra ilerlemesi
* Parça ilerlemesi
* Notlar
* Fotoğraflar
* Tamamlanma tarihi
* Kullanılan veya kalan malzeme

Bu bilgiler genellikle farklı yerlerde tutulur:

* Kâğıt notlarda
* Telefon notlarında
* Tarayıcı sekmelerinde
* Mesajlarda
* Sosyal medya kayıtlarında
* Fiziksel tariflerde
* Ayrı sayaç uygulamalarında
* Excel veya benzeri dosyalarda

Bu da aşağıdaki problemlere yol açar:

* Proje ilerlemesinin unutulması
* Hangi ipin hangi projede kullanıldığının karışması
* Sıra sayısının kaybolması
* Aynı anda yürütülen projelerin yönetilememesi
* Yarım bırakılan projelerin unutulması
* Proje notlarının dağınık kalması
* Kullanılan malzeme miktarının hesaplanamaması
* Daha önce tamamlanan projelere ait bilgilerin tekrar bulunamaması

---

# 3. Proposed Solution

Knitwise, her örgü projesi için merkezî bir proje kaydı oluşturur.

Kullanıcı proje içinde aşağıdaki bilgileri yönetebilir:

* Temel proje bilgileri
* Proje kategorisi
* Proje durumu
* Başlangıç ve tamamlanma tarihi
* Tarif bağlantısı
* Kullanılan ipler
* Kullanılan tığ veya şişler
* Sayaçlar
* Proje parçaları
* İlerleme durumu
* Proje notları
* Kapak görseli
* İlerleme fotoğrafları
* Etiketler
* Proje geçmişi

Kullanıcı bir projeyi oluşturduktan sonra diğer Knitwise özelliklerini proje bağlamında kullanabilir.

Örnek:

```text
Proje
├── Tarif
├── Sayaçlar
├── Parçalar
├── İpler
├── Şişler veya tığlar
├── Notlar
├── Fotoğraflar
└── İlerleme
```

---

# 4. Product Value

## 4.1 User Value

Project Management kullanıcıya aşağıdaki değerleri sunar:

* Tüm projeleri tek yerde görme
* Devam eden projeleri kolayca bulma
* Proje ilerlemesini kaybetmeme
* Kullanılan malzemeyi takip etme
* Yarım kalan projeleri unutmama
* Tamamlanan projeleri kişisel arşiv olarak saklama
* Bir projeye ait tüm yardımcı araçlara tek ekrandan ulaşma

## 4.2 Product Value

Bu özellik Knitwise için:

* Ana günlük kullanım alanını oluşturur
* Kullanıcı retention'ını artırır
* Diğer özelliklerin ortak bağlamını sağlar
* Premium kapasite limitlerinin uygulanabileceği temel alanlardan biridir
* İstatistik üretimi için veri kaynağı oluşturur
* Bildirim ve öneri sistemlerini besler
* Kullanıcının uygulamada uzun vadeli veri biriktirmesini sağlar

## 4.3 Commercial Value

Project Management doğrudan ücretli olmamalıdır.

Temel proje oluşturma ücretsiz planda bulunmalıdır.

Premium değer şu alanlarda oluşturulabilir:

* Daha fazla aktif proje
* Gelişmiş proje şablonları
* Proje çoğaltma
* Gelişmiş filtreleme
* Gelişmiş proje geçmişi
* Daha fazla fotoğraf
* Cloud backup
* Cihazlar arası senkronizasyon
* Gelişmiş istatistikler

---

# 5. Target Users

## 5.1 Primary Users

* Yeni başlayan örgücüler
* Deneyimli örgücüler
* Tığ işi yapan kullanıcılar
* Amigurumi yapan kullanıcılar
* Aynı anda birden fazla proje yürüten kullanıcılar
* Dijital tarif kullanan kullanıcılar
* Projelerini uzun süre saklamak isteyen kullanıcılar

## 5.2 Secondary Users

* Örgü eğitmenleri
* Kendi tariflerini geliştiren kullanıcılar
* El işi ürünleri satan kullanıcılar
* Sosyal medya için proje içeriği üreten kullanıcılar
* Örgü topluluklarında proje paylaşan kullanıcılar

---

# 6. Jobs to Be Done

## JTBD-001

Kullanıcı yeni bir örgü projesine başladığında proje bilgilerini tek yerde kaydetmek ister.

## JTBD-002

Kullanıcı çalışmaya geri döndüğünde kaldığı yeri hızlıca görmek ister.

## JTBD-003

Kullanıcı aynı anda devam eden birden fazla projeyi birbirine karıştırmadan yönetmek ister.

## JTBD-004

Kullanıcı proje sırasında kullandığı tarif, ip ve araç bilgilerini ilişkilendirmek ister.

## JTBD-005

Kullanıcı bir projeyi geçici olarak bırakmak ancak daha sonra kaldığı yerden devam etmek ister.

## JTBD-006

Kullanıcı tamamladığı projeleri geçmiş çalışmaları olarak saklamak ister.

## JTBD-007

Kullanıcı bir projede ne kadar ilerlediğini kolayca görmek ister.

## JTBD-008

Kullanıcı yanlışlıkla yaptığı bir değişiklik nedeniyle proje verisini kaybetmek istemez.

---

# 7. Goals

## PM-G-001

Kullanıcının ilk projesini yardım almadan oluşturabilmesini sağlamak.

## PM-G-002

Kullanıcının aktif projelerine en fazla iki ana etkileşim içinde ulaşabilmesini sağlamak.

## PM-G-003

Proje bilgilerinin uygulama kapatıldığında kaybolmamasını sağlamak.

## PM-G-004

Proje ile tarif, sayaç, parça ve malzeme ilişkilerini yönetmek.

## PM-G-005

Kullanıcının proje durumunu gerçek çalışma sürecine uygun biçimde değiştirebilmesini sağlamak.

## PM-G-006

Tamamlanan ve arşivlenen projelerin verilerini korumak.

## PM-G-007

Offline kullanım sırasında proje oluşturma ve düzenleme işlemlerini desteklemek.

## PM-G-008

Diğer feature'ların kullanabileceği kararlı bir proje veri modeli oluşturmak.

---

# 8. Non-Goals

V1 Project Management özelliği aşağıdaki konuları çözmez:

* Birden fazla kullanıcının aynı projeyi ortak düzenlemesi
* Sosyal proje paylaşım platformu
* Proje veya ürün satışı
* Müşteri sipariş yönetimi
* Üretim planlama
* Ticari stok yönetimi
* Finansal maliyet ve kâr hesabı
* Tam kapsamlı görev yönetimi
* Grup veya sınıf yönetimi
* AI tarafından otomatik proje oluşturma
* Proje yorumları veya sosyal etkileşim
* Gerçek zamanlı ortak çalışma

---

# 9. In Scope

V1 kapsamında:

* Proje oluşturma
* Proje görüntüleme
* Proje düzenleme
* Proje silme
* Proje arşivleme
* Proje durumunu değiştirme
* Proje listesini görüntüleme
* Aktif projeleri görüntüleme
* Tamamlanan projeleri görüntüleme
* Arşivlenen projeleri görüntüleme
* Proje arama
* Temel filtreleme
* Temel sıralama
* Proje kapak görseli
* Proje açıklaması veya notu
* Proje türü
* Başlangıç tarihi
* Hedef tamamlanma tarihi
* Gerçek tamamlanma tarihi
* Tarif bağlantısı
* İp bağlantısı
* Şiş veya tığ bağlantısı
* Sayaç bağlantısı
* Çok parçalı yapı bağlantısı
* Otomatik kayıt
* Offline proje erişimi
* Proje ilerleme özeti
* Ücretsiz aktif proje limiti
* Premium proje entitlement kontrolü

---

# 10. Out of Scope

V1 kapsamında değildir:

* Proje işbirliği
* Proje paylaşım linki
* Public proje profili
* Projeye yorum ekleme
* Proje beğenileri
* Sipariş bazlı proje yönetimi
* Müşteri bilgisi tutma
* Proje maliyet hesaplama
* Otomatik fiyat hesaplama
* Takvim görünümü
* Gantt görünümü
* Gelişmiş proje şablonları
* Proje toplu düzenleme
* Masaüstü web uygulaması
* Gerçek zamanlı multi-device sync
* AI proje özeti
* AI proje planlama
* Public pattern marketplace

---

# 11. Future Scope

V1 sonrası değerlendirilebilecek özellikler:

* Proje çoğaltma
* Proje şablonları
* Proje koleksiyonları
* Özel etiketler
* Gelişmiş filtreleme
* Gelişmiş proje geçmişi
* Proje zaman çizelgesi
* Proje paylaşma
* PDF proje özeti
* Cloud sync
* Cihazlar arası devam etme
* AI ilerleme özeti
* Tahmini tamamlanma süresi
* Proje bazlı maliyet hesabı
* Sipariş projeleri
* Creator veya seller modu
* Ortak proje düzenleme

---

# 12. Project Lifecycle

Bir proje aşağıdaki yaşam döngüsünü kullanır:

```text
draft
↓
active
↔
paused
↓
completed
↓
archived
```

Alternatif akışlar:

```text
draft → archived
active → archived
paused → archived
completed → active
archived → active
```

## 12.1 Draft

Proje oluşturulmuş ancak aktif olarak başlatılmamıştır.

## 12.2 Active

Kullanıcının şu anda üzerinde çalıştığı projedir.

## 12.3 Paused

Kullanıcının geçici olarak bıraktığı ancak tamamlamadığı projedir.

## 12.4 Completed

Kullanıcı tarafından tamamlandığı belirtilen projedir.

## 12.5 Archived

Ana çalışma listelerinde gösterilmeyen ancak verileri korunan projedir.

---

# 13. Project Types

V1 için önerilen proje türleri:

* Knitting
* Crochet
* Amigurumi
* Accessory
* Clothing
* Home Decor
* Toy
* Blanket
* Other

Proje tek bir teknik ve tek bir kategoriyle sınırlandırılmamalıdır.

Teknik ve kategori ayrı alanlar olarak tutulmalıdır.

Örnek:

```text
Technique: Crochet
Category: Toy
```

---

# 14. Core Project Information

Her proje en az aşağıdaki bilgileri desteklemelidir:

| Alan              | Zorunlu | Açıklama                                       |
| ----------------- | ------: | ---------------------------------------------- |
| Project ID        |    Evet | Benzersiz sistem kimliği                       |
| Owner ID          |    Evet | Projenin sahibi                                |
| Project Name      |    Evet | Kullanıcının verdiği ad                        |
| Status            |    Evet | Draft, active, paused, completed veya archived |
| Technique         |   Hayır | Knitting, crochet veya diğer                   |
| Category          |   Hayır | Clothing, toy, accessory ve benzeri            |
| Description       |   Hayır | Kullanıcı notu veya proje açıklaması           |
| Cover Image       |   Hayır | Proje ana görseli                              |
| Start Date        |   Hayır | Projenin başlangıç tarihi                      |
| Target Date       |   Hayır | Kullanıcının hedef tamamlanma tarihi           |
| Completed Date    |   Hayır | Gerçek tamamlanma tarihi                       |
| Pattern Reference |   Hayır | Bağlı tarif                                    |
| Created At        |    Evet | Oluşturulma zamanı                             |
| Updated At        |    Evet | Son güncellenme zamanı                         |
| Archived At       |   Hayır | Arşivlenme zamanı                              |
| Deleted At        |   Hayır | Soft delete zamanı                             |

---

# 15. Project List Experience

Proje listesi aşağıdaki görünüm gruplarını desteklemelidir:

* Active
* Paused
* Draft
* Completed
* Archived
* All

Varsayılan görünüm:

```text
Active + Paused
```

Her proje kartı en az şu bilgileri göstermelidir:

* Proje adı
* Kapak görseli veya placeholder
* Proje durumu
* Teknik veya kategori
* İlerleme özeti
* Son güncelleme bilgisi

Kart fazla bilgiyle doldurulmamalıdır.

Detaylı bilgiler proje detay ekranında gösterilmelidir.

---

# 16. Project Detail Experience

Proje detay ekranı aşağıdaki ana alanları içerebilir:

## Header

* Kapak görseli
* Proje adı
* Durum
* Hızlı düzenleme

## Progress

* Genel ilerleme
* Sayaç özeti
* Parça özeti

## Pattern

* Bağlı tarif
* Tarif adımları veya tarife gitme

## Materials

* Kullanılan ipler
* Şiş veya tığlar
* Ayrılan malzeme

## Notes

* Proje açıklaması
* Kullanıcı notları

## Activity

* Son düzenleme
* Durum değişikliği
* Tamamlanma bilgisi

V1'de tüm alanların aynı ekranda gösterilmesi zorunlu değildir.

Bilgi mimarisi UX belgelerinde ayrıntılandırılacaktır.

---

# 17. Project Progress

Genel proje ilerlemesi tek bir kaynaktan hesaplanmayabilir.

Olası ilerleme kaynakları:

* Manuel ilerleme yüzdesi
* Sayaç ilerlemesi
* Çok parçalı proje ilerlemesi
* Tarif adımı tamamlanma oranı

V1 için öncelik sırası:

1. Multi-Part Tracking varsa parça ilerlemesi
2. Tanımlı hedef sayaç varsa sayaç ilerlemesi
3. Kullanıcının manuel ilerleme değeri
4. İlerleme verisi yoksa gösterilmez

Birden fazla ilerleme kaynağı otomatik olarak birleştirilmemelidir.

İlerleme hesaplama kuralları ilgili feature belgelerinde ayrıntılandırılacaktır.

---

# 18. Dependencies

## 18.1 Product Dependencies

* `feature-002-row-counter`
* `feature-003-multi-part-tracking`
* `feature-004-pattern-library`
* `feature-005-custom-patterns`
* `feature-006-starter-patterns`
* `feature-007-yarn-inventory`
* `feature-008-hook-needle-inventory`
* `feature-014-premium`
* `feature-015-onboarding-authentication`
* `feature-016-settings-privacy`
* `feature-017-local-persistence`
* `feature-018-cloud-sync`
* `feature-019-notifications`
* `feature-020-statistics`

## 18.2 Technical Dependencies

* Authentication
* Local persistence
* User-scoped database records
* Image storage
* Navigation
* Analytics
* Feature entitlement service
* Error reporting
* Localization

## 18.3 Design Dependencies

* Project card
* Project list
* Project detail
* Create and edit project form
* Status chip
* Empty state
* Error state
* Archive confirmation
* Delete confirmation
* Limit reached paywall

---

# 19. Assumptions

* Her proje tek bir kullanıcıya aittir.
* Kullanıcı offline olarak proje oluşturabilir.
* Her proje bir tarif kullanmak zorunda değildir.
* Her proje bir ip kaydıyla ilişkilendirilmek zorunda değildir.
* Her proje sayaç kullanmak zorunda değildir.
* Proje tamamlandıktan sonra verileri düzenlenebilir.
* Arşivleme veri silme anlamına gelmez.
* Silme işlemi doğrudan kalıcı silme olarak uygulanmayacaktır.
* Project Management tüm kullanıcılar için temel bir özelliktir.
* Ücretsiz kullanıcılar sınırlı sayıda aktif proje oluşturabilir.
* Tamamlanan ve arşivlenen projelerin görüntülenmesi premium olmamalıdır.

---

# 20. Constraints

* Mobil cihazlarda sınırlı ekran alanı
* Offline-first gereksinimi
* Düşük donanımlı cihaz desteği
* Fotoğraf dosya boyutu
* Kullanıcıların eksik proje bilgisi girmesi
* Birden fazla feature'ın aynı proje kaydına bağlanması
* Gelecekte cloud sync desteği
* Veri modeli migration gereksinimleri
* Türkçe ve İngilizce terminoloji farkları
* Ücretsiz ve premium proje limitleri

---

# 21. Risks

| Risk                                                | Probability | Impact     | Mitigation                                  |
| --------------------------------------------------- | ----------- | ---------- | ------------------------------------------- |
| Proje formunun fazla uzun olması                    | Yüksek      | Yüksek     | Minimum zorunlu alanla hızlı oluşturma      |
| Çok fazla proje durumunun kullanıcıyı karıştırması  | Orta        | Orta       | Beş temel durumla sınırlandırma             |
| İlerleme kaynaklarının çelişmesi                    | Yüksek      | Yüksek     | Açık kaynak önceliği tanımlama              |
| Kullanıcının yanlışlıkla proje silmesi              | Orta        | Yüksek     | Soft delete ve onay ekranı                  |
| Offline kayıt sırasında veri kaybı                  | Orta        | Çok yüksek | Local-first kayıt ve kurtarma mekanizması   |
| Fotoğraf yüklemelerinin performansı düşürmesi       | Orta        | Orta       | Sıkıştırma ve lazy loading                  |
| Premium limitinin adaletsiz algılanması             | Orta        | Yüksek     | Mevcut veriyi koruyan limit davranışı       |
| Diğer feature'ların proje modelini sık değiştirmesi | Yüksek      | Yüksek     | Kararlı çekirdek entity ve ilişki tabloları |
| Arşivleme ve tamamlama davranışının karıştırılması  | Orta        | Orta       | Açık durum açıklamaları                     |
| Kullanıcıların zorunlu alanları doldurmadan çıkması | Yüksek      | Orta       | Draft ve auto-save desteği                  |

---

# 22. Success Metrics

## Primary Metric

İlk projesini başarıyla oluşturan yeni kullanıcı oranı.

## Secondary Metrics

* İlk proje oluşturma süresi
* Proje oluşturma tamamlama oranı
* Kullanıcı başına aktif proje sayısı
* Haftalık proje açma oranı
* Sayaç veya malzeme bağlanan proje oranı
* Tamamlanan proje oranı
* Arşivlenen proje oranı
* Proje düzenleme tekrar oranı
* Proje oluşturduktan sonraki 7 günlük retention

## Guardrail Metrics

* Proje kayıt hata oranı
* Proje veri kaybı bildirimi
* Yanlışlıkla silme şikâyeti
* Proje ekranı crash oranı
* Proje yükleme süresi
* Premium limit şikâyeti
* Proje oluşturma terk oranı

---

# 23. Initial Product Targets

V1 için başlangıç hedefleri:

* Yeni kullanıcıların en az yüzde 60'ı ilk oturumunda proje oluşturabilmelidir.
* Proje oluşturma ana akışı iki dakikadan kısa tamamlanabilmelidir.
* Kullanıcı yalnızca proje adı girerek hızlı proje oluşturabilmelidir.
* Proje kayıt işlemlerinde doğrulanmış veri kaybı yaşanmamalıdır.
* Aktif projeye ana ekrandan en fazla iki etkileşimle ulaşılabilmelidir.
* Proje listesinin tipik kullanıcı verisinde iki saniyeden kısa açılması hedeflenmelidir.
* Proje düzenlemeleri local storage'a gecikmeden yazılmalıdır.

Bu hedefler beta verilerine göre güncellenebilir.

---

# 24. Security and Privacy Summary

* Kullanıcı yalnızca kendi projelerini görüntüleyebilmelidir.
* Project tablosu için Row Level Security zorunludur.
* Proje fotoğrafları private storage içinde tutulmalıdır.
* Kullanıcının proje adı veya notu analytics'e gönderilmemelidir.
* Soft delete kayıtları diğer kullanıcılar tarafından erişilebilir olmamalıdır.
* Kullanıcı hesabını sildiğinde proje verileri silme sürecine dahil edilmelidir.
* Export işlemi proje bilgilerini kullanıcı tarafından okunabilir formatta sunmalıdır.
* Debug loglarında özel not veya görsel yolu bulunmamalıdır.

Ayrıntılı kurallar `security-privacy.md` dosyasında tanımlanacaktır.

---

# 25. Accessibility Summary

* Tüm proje aksiyonları screen reader ile kullanılabilmelidir.
* Durum yalnızca renk ile gösterilmemelidir.
* Proje kartlarının erişilebilir adı bulunmalıdır.
* Dokunma hedefleri platform minimumlarının altında olmamalıdır.
* Dynamic text açıkken metinler kesilmemelidir.
* Silme ve arşivleme aksiyonları açıkça ayrılmalıdır.
* Form hata mesajları yalnızca görsel renkle belirtilmemelidir.

---

# 26. Open Questions

| ID        | Question                                                      | Owner   | Target Decision               | Status        |
| --------- | ------------------------------------------------------------- | ------- | ----------------------------- | ------------- |
| PM-OQ-001 | Ücretsiz aktif proje limiti kesin olarak kaç olmalıdır?       | Product | Premium implementation öncesi | Open          |
| PM-OQ-002 | Draft proje aktif proje limitine dahil edilmeli mi?           | Product | Requirements öncesi           | Open          |
| PM-OQ-003 | Proje kapak görseli V1'de zorunlu mu?                         | Product | Design öncesi                 | Open          |
| PM-OQ-004 | Manuel ilerleme yüzdesi V1'de sunulmalı mı?                   | Product | Requirements öncesi           | Open          |
| PM-OQ-005 | Tamamlanan projeler düzenlenebilir kalmalı mı?                | Product | Business rules öncesi         | Proposed: Yes |
| PM-OQ-006 | Silinen projeler kullanıcı tarafından geri yüklenebilmeli mi? | Product | Data model öncesi             | Open          |
| PM-OQ-007 | Proje türleri sabit liste mi, özel değer mi desteklemeli?     | Product | UX öncesi                     | Open          |
| PM-OQ-008 | Proje etiketi sistemi V1 kapsamına alınmalı mı?               | Product | Scope freeze öncesi           | Open          |
| PM-OQ-009 | Bir proje birden fazla tarife bağlanabilir mi?                | Product | Data model öncesi             | Proposed: No  |
| PM-OQ-010 | Proje fotoğraf galerisi V1'de bulunmalı mı?                   | Product | Scope freeze öncesi           | Open          |

---

# 27. Definition of Ready

Project Management geliştirmeye hazır kabul edilmek için:

* `overview.md` tamamlanmalıdır.
* `user-stories.md` tamamlanmalıdır.
* `requirements.md` tamamlanmalıdır.
* `business-rules.md` tamamlanmalıdır.
* `user-flows.md` tamamlanmalıdır.
* `data-model.md` tamamlanmalıdır.
* `edge-cases.md` tamamlanmalıdır.
* `acceptance-criteria.md` tamamlanmalıdır.
* `security-privacy.md` tamamlanmalıdır.
* `testing.md` tamamlanmalıdır.
* Proje durum modeli onaylanmalıdır.
* Ücretsiz proje limiti kararlaştırılmalıdır.
* Draft limit davranışı kararlaştırılmalıdır.
* Proje silme ve kurtarma davranışı kararlaştırılmalıdır.
* Proje–tarif ilişkisi kararlaştırılmalıdır.
* Product Owner kapsamı onaylamalıdır.

---

# 28. References

* `PROJECT_PRINCIPLES.md`
* `DECISIONS.md`
* `02-prd/overview.md`
* `02-prd/mvp-roadmap.md`
* `02-prd/feature-priorities.md`
* `02-prd/premium-strategy.md`
* `02-prd/release-plan.md`
* `03-features/README.md`
* `03-features/FEATURE_TEMPLATE.md`
* `03-features/feature-002-row-counter/`
* `03-features/feature-003-multi-part-tracking/`
* `03-features/feature-004-pattern-library/`
* `03-features/feature-007-yarn-inventory/`
* `03-features/feature-008-hook-needle-inventory/`
* `03-features/feature-014-premium/`
* `03-features/feature-017-local-persistence/`
