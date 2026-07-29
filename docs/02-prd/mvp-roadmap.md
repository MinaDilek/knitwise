
# Knitwise MVP Roadmap

## Document Information

| Alan         | Değer       |
| ------------ | ----------- |
| Product      | Knitwise    |
| Document     | MVP Roadmap |
| Version      | 1.0         |
| Status       | Draft       |
| Owner        | Product     |
| Last Updated | 2026-07-29  |

---

# 1. Purpose

Bu belge, Knitwise ürününün sürüm bazlı gelişim planını tanımlar.

Amaç; hangi özelliklerin hangi sürümde geliştirileceğini, bu özelliklerin neden önceliklendirildiğini, aralarındaki bağımlılıkları, kullanıcıya sağladıkları değeri ve geliştirme risklerini açık biçimde kayıt altına almaktır.

Bu belge:

* ürün kapsamını kontrol altında tutmak,
* V1'in gereksiz özelliklerle büyümesini önlemek,
* geliştirme sırasını belirlemek,
* tasarım ve teknik ekiplerin ortak planla ilerlemesini sağlamak,
* Codex görevlerini küçük ve uygulanabilir parçalara ayırmak

için kullanılacaktır.

---

# 2. Roadmap Principles

Knitwise roadmap'i aşağıdaki ilkeler doğrultusunda yönetilecektir.

## 2.1 Kullanıcı Değeri

Öncelik, kullanıcının günlük örgü sürecinde doğrudan fayda sağlayan özelliklere verilir.

## 2.2 Temel Deneyim Önce Gelir

AI, topluluk ve pazaryeri özelliklerinden önce temel proje, sayaç, tarif ve envanter deneyimi tamamlanır.

## 2.3 Bağımlılıklar Gözetilir

Bir özellik, ihtiyaç duyduğu temel modüller tamamlanmadan geliştirilmez.

Örneğin akıllı tarif önerileri için önce ip, şiş ve tığ envanterinin bulunması gerekir.

## 2.4 Ölçülebilirlik

Her sürümün başarısı, önceden tanımlanmış ürün ve kalite metrikleriyle değerlendirilir.

## 2.5 Kontrollü Kapsam

V1 kapsamına sonradan yeni özellik eklenmesi, açık ürün kararı gerektirir.

## 2.6 Güvenilirlik

Veri kaybı, sayaç hatası veya senkronizasyon problemi oluşturabilecek özellikler yeterli test yapılmadan yayınlanmaz.

---

# 3. Version Overview

Knitwise üç ana ürün aşamasında geliştirilecektir.

| Sürüm | Tema                  | Ana Amaç                                                               |
| ----- | --------------------- | ---------------------------------------------------------------------- |
| V1    | Core Product          | Temel örgü yönetimi deneyimini doğrulamak                              |
| V2    | Intelligent Assistant | AI, senkronizasyon ve kişiselleştirme eklemek                          |
| V3    | Platform              | Topluluk, pazaryeri ve kamera tabanlı özelliklerle ekosisteme dönüşmek |

---

# 4. V1 — Core Product

## 4.1 V1 Goal

V1'in amacı, kullanıcıların örgü projelerini ve malzemelerini tek uygulama üzerinden yönetebilmesini sağlamaktır.

V1 sonunda kullanıcı şu ana akışı tamamlayabilmelidir:

1. Uygulamaya giriş yapar.
2. Sahip olduğu ip, şiş ve tığları ekler.
3. Yeni bir proje oluşturur veya tarif seçer.
4. Projeyi sıra sayacıyla takip eder.
5. Çok parçalı bölümleri yönetir.
6. Projede kullanılacak malzemeleri ayırır.
7. Proje tamamlandığında kullanılan malzemeleri envanterden düşer.
8. Kalan malzemeleri görüntüler.
9. Elindeki malzemelere uygun yeni projeler keşfeder.

---

# 5. V1 Feature Set

## MVP-01 — Onboarding and User Account

### Purpose

Kullanıcıya uygulamanın temel değerini açıklamak ve kişisel verilerini güvenli şekilde yönetmesini sağlamak.

### Scope

* Açılış ekranı
* Kısa onboarding akışı
* E-posta ile kayıt
* E-posta ile giriş
* Şifre sıfırlama
* Oturumu kapatma
* Hesabı silme
* Kullanıcı tercihleri
* Dil tercihi
* Ölçü birimi tercihi

### User Value

Kullanıcı verilerinin farklı oturumlarda korunmasını ve ileride senkronize edilmesini sağlar.

### Dependencies

* Supabase Authentication
* Kullanıcı profil modeli
* Gizlilik metinleri

### Risks

* Kayıt sürecinin uzun olması
* E-posta doğrulama sorunları
* Kullanıcının hesap açmadan uygulamayı denemek istemesi

### V1 Decision

Temel hesap yönetimi V1 kapsamındadır.

Sosyal giriş seçenekleri ilk beta için zorunlu değildir, ancak sonraki V1 güncellemesinde değerlendirilebilir.

### Acceptance Summary

* Kullanıcı kayıt olabilmelidir.
* Kullanıcı giriş yapabilmelidir.
* Kullanıcı şifresini sıfırlayabilmelidir.
* Kullanıcı hesabını silebilmelidir.
* Kullanıcının verileri başka kullanıcılar tarafından görüntülenememelidir.

---

## MVP-02 — Project Management

### Purpose

Kullanıcının devam eden, planlanan, tamamlanan ve beklemeye alınan örgü projelerini yönetmesini sağlamak.

### Scope

* Proje oluşturma
* Proje düzenleme
* Proje silme
* Proje çoğaltma
* Proje durumu
* Başlangıç tarihi
* Hedef bitiş tarihi
* Tamamlanma tarihi
* Proje fotoğrafı
* Notlar
* Kullanılan tarif
* Kullanılan malzemeler
* İlerleme yüzdesi
* Arşivleme
* Filtreleme
* Sıralama

### Project Statuses

* Planned
* Active
* Paused
* Completed
* Abandoned
* Archived

### User Value

Kullanıcının birden fazla projeyi karıştırmadan takip edebilmesini sağlar.

### Dependencies

* Kullanıcı hesabı
* Yerel veri saklama
* Fotoğraf depolama
* Envanter modülü
* Tarif modülü

### Risks

* Fazla zorunlu alan nedeniyle proje oluşturmanın zorlaşması
* Fotoğraf yükleme performansı
* Offline ve online veri çakışması

### V1 Decision

Proje yönetimi V1'in temel modüllerinden biridir ve diğer özelliklerin büyük kısmı bu modüle bağlanacaktır.

### Acceptance Summary

* Proje minimum bilgilerle oluşturulabilmelidir.
* Kullanıcı projeyi sonradan düzenleyebilmelidir.
* Proje offline görüntülenebilmelidir.
* Uygulama kapansa bile proje ilerlemesi korunmalıdır.
* Silme işlemi kullanıcı onayı gerektirmelidir.

---

## MVP-03 — Smart Row Counter

### Purpose

Kullanıcının örgü sırasında bulunduğu sıra veya turu hızlı ve güvenilir biçimde takip etmesini sağlamak.

### Scope

* Sayacı artırma
* Sayacı azaltma
* Sayaç sıfırlama
* Artış miktarı belirleme
* Birden fazla sayaç
* Sayaç adı
* Tekrar grupları
* Hedef sıra
* Titreşimli geri bildirim
* Sesli geri bildirim
* Ekranın açık kalması
* Yanlış işlem için geri alma
* Otomatik kayıt

### User Value

Kağıt kalem ihtiyacını azaltır ve kullanıcıların sıra kaybetmesini önler.

### Dependencies

* Proje yönetimi
* Yerel veri saklama
* Erişilebilirlik sistemi

### Risks

* Yanlışlıkla dokunma
* Sayacın uygulama kapanınca sıfırlanması
* Ekran kilidi sırasında veri kaybı
* Çok karmaşık sayaç arayüzü

### V1 Decision

Temel sıra sayacı ücretsiz olacaktır.

Gelişmiş sayaç seçeneklerinin bir kısmı premium olabilir, ancak temel sayma deneyimi ücret arkasına alınmayacaktır.

### Acceptance Summary

* Her artıştan sonra değer otomatik kaydedilmelidir.
* Kullanıcı son işlemi geri alabilmelidir.
* Sayaç offline çalışmalıdır.
* Sayaç değeri uygulama kapanınca kaybolmamalıdır.
* Büyük dokunma alanları kullanılmalıdır.

---

## MVP-04 — Multi-Part Project Tracking

### Purpose

Amigurumi, oyuncak, kazak ve benzeri çok parçalı projelerin bölüm bazında takip edilmesini sağlamak.

### Scope

* Projeye parça ekleme
* Parça adı
* Gerekli adet
* Tamamlanan adet
* Parça durumu
* Parçaya özel sayaç
* Parça notu
* Parça sıralaması
* Parça çoğaltma
* Toplu tamamlandı işaretleme

### Example Parts

* Kafa
* Gövde
* Sağ kol
* Sol kol
* Sağ bacak
* Sol bacak
* Kulak
* Kuyruk
* Birleştirme
* Süsleme

### User Value

Kullanıcının hangi parçayı kaç kez tamamladığını hatırlamasını kolaylaştırır.

### Dependencies

* Proje yönetimi
* Sıra sayacı

### Risks

* Çok fazla parça ile arayüzün karmaşıklaşması
* Aynı tür parçaların yanlış sayılması
* Parça ve sayaç durumlarının senkronize olmaması

### V1 Decision

Bu özellik Knitwise'ın amigurumi kullanıcılarına yönelik ana farklılaştırıcılarından biridir.

### Acceptance Summary

* Kullanıcı projeye sınırsız sayıda parça ekleyebilmelidir.
* Her parça için gerekli adet tanımlanabilmelidir.
* Tamamlanan adet gerekli adedi aşarsa kullanıcı uyarılmalıdır.
* Proje ilerlemesi parça durumlarından hesaplanabilmelidir.

---

## MVP-05 — Pattern Library

### Purpose

Kullanıcıların tarifleri uygulama içinde saklamasını, düzenlemesini ve projelere bağlamasını sağlamak.

### Scope

* Tarif listesi
* Tarif detay ekranı
* Kategori
* Zorluk seviyesi
* Tahmini süre
* Gerekli malzemeler
* Kullanılan teknikler
* Tarif adımları
* Favoriler
* Arama
* Filtreleme
* Etiketleme
* Projeye dönüştürme

### User Value

Tarifleri farklı dosya, ekran görüntüsü ve not uygulamalarında arama ihtiyacını azaltır.

### Dependencies

* Proje yönetimi
* Envanter
* Dosya ve görsel depolama

### Risks

* Telif hakkıyla korunan tariflerin izinsiz paylaşımı
* Büyük tarif verilerinin performansa etkisi
* Farklı tarif biçimlerinin standartlaştırılması

### V1 Decision

V1'de kullanıcıya ait kişisel tarif yönetimi bulunacaktır.

Topluluk tarif paylaşımı V1 kapsamında değildir.

### Acceptance Summary

* Kullanıcı tarif oluşturabilmeli ve düzenleyebilmelidir.
* Tarif bir projeye bağlanabilmelidir.
* Tarif offline görüntülenebilmelidir.
* Tarif arama ve filtreleme çalışmalıdır.

---

## MVP-06 — Custom Patterns

### Purpose

Kullanıcının kendi tarifini adım adım oluşturmasını sağlamak.

### Scope

* Tarif adı
* Açıklama
* Kategori
* Zorluk
* Malzemeler
* Adımlar
* Bölümler
* Fotoğraf
* Not
* Teknik terimler
* Sıra bazlı talimat
* Projeye dönüştürme

### User Value

Kullanıcının kendi tariflerini güvenli ve düzenli şekilde saklamasını sağlar.

### Dependencies

* Pattern Library
* Görsel depolama
* Proje yönetimi

### Risks

* Tarif oluşturma formunun uzun ve yorucu olması
* Karmaşık örgü yapılarının mevcut veri modeline sığmaması
* Yanlışlıkla veri kaybı

### V1 Decision

Temel özel tarif oluşturma V1 kapsamındadır.

Gelişmiş çizim, şema ve grafik editörü V1 kapsamı dışındadır.

### Acceptance Summary

* Kullanıcı taslak tarif kaydedebilmelidir.
* Tarif adımları sıralanabilmelidir.
* Tarif sonradan düzenlenebilmelidir.
* Tarif silinmeden önce onay alınmalıdır.

---

## MVP-07 — Starter Patterns

### Purpose

Yeni kullanıcının uygulamayı boş bir içerikle karşılaşmadan deneyebilmesini sağlamak.

### Scope

* Uygulamayla birlikte gelen örnek tarifler
* Başlangıç seviyesi içerikler
* Farklı proje kategorileri
* Malzeme listeleri
* Adım adım açıklamalar
* Örnek proje oluşturma

### User Value

Yeni başlayan kullanıcıların uygulamayı ve temel özellikleri hızlı biçimde anlamasını sağlar.

### Dependencies

* Pattern Library
* Proje yönetimi
* İçerik hazırlama

### Risks

* Tarif içeriklerinin hatalı olması
* Telif sorunları
* Yetersiz içerik çeşitliliği

### V1 Decision

Tüm starter tarifleri Knitwise için özgün olarak hazırlanmalı veya uygun lisanslı içerik kullanılmalıdır.

### Acceptance Summary

* En az birkaç temel kategori için örnek tarif bulunmalıdır.
* Tarifler uygulamanın temel özelliklerini göstermelidir.
* Kullanıcı starter tariften yeni proje başlatabilmelidir.

---

## MVP-08 — Yarn Inventory

### Purpose

Kullanıcının sahip olduğu ipleri ve kalan miktarları takip etmesini sağlamak.

### Scope

* İp ekleme
* Marka
* Seri
* Renk adı
* Renk kodu
* Lot numarası
* Lif türü
* Kalınlık
* Başlangıç gramı
* Kalan gram
* Uzunluk
* Birim
* Adet
* Fotoğraf
* Satın alma tarihi
* Fiyat
* Saklama konumu
* Notlar
* Düşük stok durumu
* Projeye ayırma
* Kullanımı kaydetme

### User Value

Kullanıcının evindeki ipleri unutmasını ve gereksiz tekrar alışveriş yapmasını azaltır.

### Dependencies

* Kullanıcı hesabı
* Yerel veri saklama
* Proje yönetimi
* Ölçü birimi sistemi

### Risks

* Kullanıcının tüm bilgileri girmek istememesi
* Gram ve metre dönüşüm hataları
* Aynı ipin birden fazla kayıtla eklenmesi
* Gerçek kalan miktar ile sistemdeki değerin farklılaşması

### V1 Decision

İp ekleme hızlı ve esnek olmalıdır.

Marka, seri ve detaylı teknik bilgilerin tamamı zorunlu olmamalıdır.

### Acceptance Summary

* Kullanıcı yalnızca isim ve miktarla ip ekleyebilmelidir.
* Kalan miktar negatif olamamalıdır.
* Kullanıcı miktarı manuel düzeltebilmelidir.
* Projeye ayrılan miktar açıkça gösterilmelidir.
* Envanter offline görüntülenebilmelidir.

---

## MVP-09 — Hook and Needle Inventory

### Purpose

Kullanıcının sahip olduğu şiş ve tığları yönetmesini sağlamak.

### Scope

* Tığ ekleme
* Şiş ekleme
* Tür
* Ölçü
* Uzunluk
* Malzeme
* Marka
* Set bilgisi
* Fotoğraf
* Saklama konumu
* Not
* Kullanım durumu
* Projeye bağlama

### User Value

Kullanıcının hangi araçlara sahip olduğunu ve bir proje için uygun ekipmanı bulunup bulunmadığını görmesini sağlar.

### Dependencies

* Kullanıcı hesabı
* Ölçü birimi sistemi
* Proje yönetimi

### Risks

* Farklı ülkelerdeki şiş numarası sistemleri
* Aynı aracın birden fazla kez eklenmesi
* Setlerin karmaşık yönetimi

### V1 Decision

Milimetre temel ölçü birimi olarak saklanacak, diğer sistemler kullanıcıya dönüşümlü gösterilecektir.

### Acceptance Summary

* Kullanıcı tığ ve şiş ekleyebilmelidir.
* Araçlar tür ve ölçüye göre filtrelenebilmelidir.
* Tarif gereksinimi ile kullanıcı araçları karşılaştırılabilmelidir.

---

## MVP-10 — Smart Material Recommendations

### Purpose

Kullanıcının sahip olduğu ip, şiş ve tığlara göre uygun tarif ve proje önerileri sunmak.

### Scope

* Tarif ile envanter karşılaştırma
* Malzeme uyumluluk puanı
* Eksik malzeme listesi
* Tam uyumlu tarifler
* Kısmen uyumlu tarifler
* Alternatif ip önerileri
* Aynı kalınlıkta ip eşleşmesi
* Uygun tığ veya şiş kontrolü
* Malzemeye göre filtreleme
* Öneri gerekçesi

### Compatibility Example

Bir tarif için:

* Gerekli ip türü uygun
* İp kalınlığı uygun
* Renk farklı
* Miktarın %85'i mevcut
* Uygun tığ mevcut

Sonuç:

> Bu proje için gerekli malzemelerin %85'i envanterinizde bulunuyor.

### User Value

Kullanıcının sahip olduğu malzemeleri daha verimli kullanmasını sağlar.

### Dependencies

* Yarn Inventory
* Hook and Needle Inventory
* Pattern Library
* Standardized material model
* Compatibility rules

### Risks

* Yanlış öneri
* İp özelliklerinin eksik girilmesi
* Farklı markaların teknik değerlerinin karşılaştırılması
* Kullanıcının öneriye gereğinden fazla güvenmesi

### V1 Decision

V1'de öneriler kural tabanlı ve açıklanabilir olacaktır.

AI tabanlı öneriler V2 kapsamında değerlendirilecektir.

### Acceptance Summary

* Her öneri için neden önerildiği gösterilmelidir.
* Sistem eksik veya belirsiz veriyi kesin sonuç gibi sunmamalıdır.
* Kullanıcı önerileri filtreleyebilmelidir.
* Uyum puanı hesaplama mantığı belgelenmelidir.

---

## MVP-11 — Yarn Finisher

### Purpose

Kullanıcının az miktarda kalan iplerini değerlendirebileceği küçük proje önerileri sunmak.

### Scope

* Kalan ipleri listeleme
* Kalan miktara göre filtreleme
* Küçük proje önerileri
* Birden fazla kalan ipi birlikte değerlendirme
* Renk uyumu önerileri
* Kategori filtreleri
* Tahmini tüketim
* Eksik miktar gösterimi

### Example Use Cases

* Anahtarlık
* Bardak altlığı
* Küçük motif
* Saç bandı
* Bebek patiği
* Mini amigurumi
* Yama
* Dekoratif parça

### User Value

Kalan ip israfını azaltır ve kullanıcıya yeni proje fikri verir.

### Dependencies

* Yarn Inventory
* Pattern Library
* Smart Material Recommendations

### Risks

* Tahmini tüketimin yanlış olması
* Çok az miktar için uygun tarif bulunamaması
* Renk kombinasyonlarının kullanıcı zevkine uymaması

### V1 Decision

V1'de kurallı filtreleme kullanılacaktır.

AI tabanlı renk ve proje kombinasyonu V2 için planlanabilir.

### Acceptance Summary

* Kullanıcı belirli bir ip veya tüm kalan ipler için öneri görebilmelidir.
* Öneride tahmini ip tüketimi gösterilmelidir.
* Eksik miktar varsa açıkça belirtilmelidir.

---

## MVP-12 — Yarn Calculator

### Purpose

Kullanıcının proje için ihtiyaç duyduğu ip miktarını tahmin etmesini sağlamak.

### Scope

* Gramdan metreye dönüşüm
* Metreden grama dönüşüm
* Yumak adedi hesaplama
* Numune ölçüsüne göre tahmin
* Proje boyutuna göre tahmin
* Fire payı
* Kullanıcı birimi seçimi
* Sonucun açıklanması

### User Value

Yanlış miktarda ip satın alma riskini azaltır.

### Dependencies

* Ölçü birimi sistemi
* İp teknik özellikleri
* Proje kategorileri

### Risks

* Kullanıcının yanlış veri girmesi
* Farklı örgü sıklıkları
* Hesaplamaların kesin sonuç gibi algılanması

### V1 Decision

Tüm hesaplama sonuçları tahmini olarak gösterilecektir.

### Acceptance Summary

* Kullanılan formül kullanıcıya açıklanabilmelidir.
* Eksik veri durumunda hesaplama yapılmamalı veya güven seviyesi düşürülmelidir.
* Sonuç birimleri kullanıcının tercihine göre gösterilmelidir.

---

## MVP-13 — Knitting and Crochet Glossary

### Purpose

Kullanıcının örgü ve tığ işi terimlerini uygulama içinde öğrenmesini sağlamak.

### Scope

* Türkçe terimler
* İngilizce karşılıklar
* Kısaltmalar
* Açıklamalar
* Kategori
* Arama
* Favoriler
* Tarif içinden terime gitme

### User Value

Özellikle yabancı tarif kullanan ve yeni başlayan kullanıcıların terimleri anlamasını kolaylaştırır.

### Dependencies

* İçerik hazırlama
* Arama altyapısı
* Yerelleştirme sistemi

### Risks

* Terminoloji tutarsızlığı
* Bölgesel terim farklılıkları
* Yanlış çeviri

### V1 Decision

Terimler uzman kontrolünden geçirilmelidir.

### Acceptance Summary

* Kullanıcı terim ve kısaltma arayabilmelidir.
* Türkçe ve İngilizce karşılıklar bulunmalıdır.
* Terimler kategorilere göre filtrelenebilmelidir.

---

## MVP-14 — Voice Commands

### Purpose

Kullanıcının elleri örgüyle meşgulken temel sayaç işlemlerini sesle gerçekleştirmesini sağlamak.

### Scope

* Sayacı artır
* Sayacı azalt
* Mevcut sırayı söyle
* Son işlemi geri al
* Sonraki parçaya geç
* Sesli geri bildirim
* Mikrofon izni
* Komut açıklamaları

### User Value

Ekrana dokunmadan sayaç kontrolü sağlar.

### Dependencies

* Smart Row Counter
* Cihaz ses tanıma servisi
* Erişilebilirlik sistemi

### Risks

* Gürültülü ortamda yanlış algılama
* Türkçe komut tanıma başarısı
* Mikrofon gizliliği
* Pil tüketimi

### V1 Decision

V1'de sınırlı ve açık komut seti kullanılacaktır.

Sürekli dinleme yerine kullanıcı tarafından başlatılan kısa dinleme tercih edilmelidir.

### Acceptance Summary

* Kullanıcı mikrofon iznini açıkça vermelidir.
* Algılanan komut kullanıcıya gösterilmelidir.
* Kritik işlemler geri alınabilmelidir.
* Sesli komut kullanımı zorunlu olmamalıdır.

---

## MVP-15 — Premium System

### Purpose

Knitwise'ın sürdürülebilir bir gelir modeli oluşturmasını sağlamak.

### Scope

* Ücretsiz plan
* Premium aylık abonelik
* Premium yıllık abonelik
* Deneme süresi
* Satın alma geri yükleme
* Abonelik durumunu gösterme
* Premium özellik işaretleri
* Paywall ekranları
* Abonelik yönetimi

### User Value

Premium kullanıcı gelişmiş özelliklere ve daha yüksek limitlere erişebilir.

### Dependencies

* App Store ödeme sistemi
* Google Play ödeme sistemi
* Kullanıcı hesabı
* Entitlement yönetimi
* Premium strateji belgesi

### Risks

* Fazla agresif paywall
* Mağaza abonelik senkronizasyonu
* Kullanıcının satın almayı geri yükleyememesi
* Temel deneyimin gereksiz sınırlandırılması

### V1 Decision

Temel proje ve sayaç deneyimi ücretsiz kalmalıdır.

Premium sınırları `premium-strategy.md` içinde ayrıntılı olarak tanımlanacaktır.

### Acceptance Summary

* Satın alma durumu güvenilir şekilde doğrulanmalıdır.
* Kullanıcı satın almayı geri yükleyebilmelidir.
* Abonelik fiyatı ve yenileme koşulları açıkça gösterilmelidir.
* Premium olmayan kullanıcı mevcut ücretsiz verilerini kaybetmemelidir.

---

## MVP-16 — Settings, Privacy and Data Management

### Purpose

Kullanıcının uygulama tercihlerini ve kişisel verilerini yönetmesini sağlamak.

### Scope

* Dil
* Tema
* Ölçü birimleri
* Bildirim tercihleri
* Ses ve titreşim
* Erişilebilirlik tercihleri
* Veri dışa aktarma
* Hesap silme
* Gizlilik politikası
* Kullanım koşulları
* Uygulama sürümü
* Destek bağlantısı

### User Value

Kullanıcıya uygulama deneyimi ve kişisel verileri üzerinde kontrol sağlar.

### Dependencies

* Kullanıcı hesabı
* Yerelleştirme
* Veri dışa aktarma sistemi
* Yasal belgeler

### Risks

* Hesap silme sırasında veri kalması
* Veri dışa aktarmanın eksik olması
* Ayarların cihazlar arasında senkronize olmaması

### Acceptance Summary

* Kullanıcı ayarları kalıcı olmalıdır.
* Kullanıcı hesabını ve verilerini silebilmelidir.
* Veri silme işlemi açık onay gerektirmelidir.
* Yasal belgeler uygulama içinden erişilebilir olmalıdır.

---

# 6. V1 Development Phases

## Phase 0 — Foundation

### Deliverables

* Flutter proje yapısı
* Feature-first mimari
* Riverpod kurulumu
* Supabase ortamları
* Yerel veri stratejisi
* Tasarım sistemi temeli
* Yerelleştirme sistemi
* Analytics altyapısı
* Crash reporting
* CI/CD
* Güvenlik taramaları

### Exit Criteria

* Uygulama development ortamında çalışır.
* Test altyapısı hazırdır.
* Secret taraması aktiftir.
* Temel navigation çalışır.
* Tema ve yerelleştirme altyapısı kuruludur.

---

## Phase 1 — Core Project Experience

### Features

* Onboarding
* Authentication
* Project Management
* Smart Row Counter
* Multi-Part Tracking
* Settings temeli

### Goal

Kullanıcının ilk projesini oluşturup sayaçla takip edebilmesi.

### Exit Criteria

* Kullanıcı kayıt olabilir.
* Proje oluşturabilir.
* Proje içinde sayaç kullanabilir.
* Çok parçalı ilerleme kaydedebilir.
* Veriler uygulama kapanınca korunur.

---

## Phase 2 — Pattern Experience

### Features

* Pattern Library
* Custom Patterns
* Starter Patterns
* Glossary

### Goal

Kullanıcının tarifleri yönetebilmesi ve tariften proje başlatabilmesi.

### Exit Criteria

* Tarif oluşturulabilir.
* Tarif projeye bağlanabilir.
* Starter tariften proje oluşturulabilir.
* Terimler aranabilir.

---

## Phase 3 — Material Management

### Features

* Yarn Inventory
* Hook and Needle Inventory
* Project Material Allocation
* Material Consumption

### Goal

Kullanıcının sahip olduğu malzemeleri doğru biçimde takip edebilmesi.

### Exit Criteria

* İp eklenebilir ve düzenlenebilir.
* Şiş ve tığ eklenebilir.
* Malzeme projeye ayrılabilir.
* Proje tamamlandığında kullanım kaydedilebilir.
* Kalan miktar negatif olamaz.

---

## Phase 4 — Smart Utility Layer

### Features

* Smart Material Recommendations
* Yarn Finisher
* Yarn Calculator
* Voice Commands

### Goal

Temel verileri kullanıcıya somut öneri ve kolaylık olarak geri sunmak.

### Exit Criteria

* Malzemeye uygun tarifler gösterilir.
* Öneri gerekçesi açıklanır.
* Kalan ipler için proje önerisi sunulur.
* Hesaplayıcılar doğru formüllerle çalışır.
* Temel sesli komutlar kullanılabilir.

---

## Phase 5 — Monetization and Launch Readiness

### Features

* Premium System
* Paywall
* Purchase Restore
* Analytics finalization
* Privacy and legal screens
* App Store assets
* Google Play assets

### Goal

Uygulamayı beta ve mağaza yayınına hazır hale getirmek.

### Exit Criteria

* Abonelik satın alma çalışır.
* Satın alma geri yüklenebilir.
* Gizlilik ve kullanım koşulları erişilebilir.
* Kritik analitik olayları gönderilir.
* Güvenlik kontrolleri tamamlanır.
* Mağaza gereksinimleri karşılanır.

---

# 7. V1 Priority Tiers

## P0 — Launch Blockers

Bu özellikler olmadan V1 yayınlanamaz.

* Authentication
* Project Management
* Smart Row Counter
* Multi-Part Tracking
* Yarn Inventory
* Hook and Needle Inventory
* Pattern Library
* Settings and Privacy
* Local Persistence
* Security Controls
* Crash Reporting

## P1 — Core Differentiators

V1'in değer önerisini güçlendiren özelliklerdir.

* Smart Material Recommendations
* Yarn Finisher
* Custom Patterns
* Starter Patterns
* Yarn Calculator
* Premium System

## P2 — Launch Enhancers

İlk mağaza sürümünden kısa süre sonra yayınlanabilir.

* Voice Commands
* Gelişmiş filtreler
* Gelişmiş istatistikler
* Ek starter tarifler
* Sosyal giriş
* Ek tema seçenekleri

---

# 8. V1 Exclusions

Aşağıdaki özellikler V1'e eklenmeyecektir:

* Kullanıcılar arası takip
* Yorum
* Beğeni
* Mesajlaşma
* Tarif satışı
* Pazaryeri
* Kamera ile ilmek tanıma
* Fotoğraftan tarif çıkarma
* AI ile tam tarif oluşturma
* Canlı görüntülü eğitim
* Web uygulaması
* Masaüstü uygulaması
* Eğitmen paneli
* Satıcı paneli

Bu özelliklerin V1 içine alınması için `DECISIONS.md` içinde yeni bir ürün kararı oluşturulmalıdır.

---

# 9. V2 — Intelligent Assistant

## 9.1 V2 Goal

V2'nin amacı, V1'de toplanan proje ve malzeme verilerini kullanarak daha kişisel, akıllı ve otomatik bir kullanıcı deneyimi sunmaktır.

## 9.2 Planned Features

### AI Pattern Helper

* Tarif açıklama
* Terim açıklama
* Adımları sadeleştirme
* Kullanıcının seviyesine göre anlatım
* Hata ihtimali olan adımları işaretleme

### Smart PDF

* PDF tarif yükleme
* Metin bölümlerini algılama
* Malzeme listesini çıkarma
* Sıra adımlarını ayırma
* Tarif içinden proje oluşturma
* Kullanıcı onaylı veri aktarımı

### Cloud Sync

* Cihazlar arası senkronizasyon
* Çakışma yönetimi
* Versiyon takibi
* Yedekleme
* Geri yükleme

### Home Screen Widgets

* Hızlı sayaç
* Aktif proje
* Günlük ilerleme
* Kalan parça

### Statistics

* Proje tamamlama süresi
* Kullanılan ip miktarı
* En çok kullanılan araç
* Aylık aktivite
* Tamamlanan proje sayısı
* Kalan ip değerlendirme oranı

### Translation

* Tarif terimi çevirisi
* Dil bazlı ölçü dönüşümü
* Kullanıcının seçtiği dile göre içerik gösterimi

### Notifications

* Proje hatırlatmaları
* Uzun süredir bekleyen proje
* Düşük stok
* Hedef tarih
* Yeni öneri

### Personalized Dashboard

* Aktif projeler
* Malzemeye uygun öneriler
* Kalan ip önerileri
* Son kullanılan tarifler
* Kullanıcı alışkanlıklarına göre düzenleme

### Advanced Recommendations

* Kullanıcı beceri seviyesine göre öneri
* Tamamlama süresine göre öneri
* Mevsime göre öneri
* Renk tercihine göre öneri
* Geçmiş proje davranışına göre öneri

---

# 10. V2 Entry Criteria

V2 geliştirmesine başlamadan önce:

* V1 mağazada yayınlanmış olmalıdır.
* Kritik crash oranı kabul edilebilir seviyede olmalıdır.
* Temel senkronizasyon stratejisi doğrulanmış olmalıdır.
* Kullanıcı geri bildirimleri analiz edilmiş olmalıdır.
* V1'in ana metrikleri ölçülmüş olmalıdır.
* AI kullanım maliyeti modellenmiş olmalıdır.
* Gizlilik ve kullanıcı onayı süreçleri hazırlanmış olmalıdır.

---

# 11. V3 — Platform

## 11.1 V3 Goal

V3'ün amacı Knitwise'ı bireysel örgü aracından kapsamlı bir örgü ekosistemine dönüştürmektir.

## 11.2 Planned Features

### Camera AI

* İp türü tahmini
* Renk algılama
* Proje kategorisi tahmini
* Örgü hatası işaretleme
* Görüntüden ilerleme önerisi

### AI Coach

* Adım adım yönlendirme
* Kullanıcı sorularına bağlama göre cevap
* Hata çözme yardımı
* Beceri gelişimi önerileri
* Kişiselleştirilmiş eğitim

### Marketplace

* Dijital tarif satışı
* Üretici profili
* Satıcı yönetimi
* Ödeme
* Komisyon
* İade politikaları
* İçerik raporlama

### Community

* Profil
* Proje paylaşımı
* Takip
* Beğeni
* Yorum
* Koleksiyon
* Moderasyon
* Raporlama

### AI Pattern Generation

* Kullanıcı girdisine göre taslak tarif
* Malzemeye göre tarif üretme
* Boyut ve seviye özelleştirme
* Kullanıcı doğrulaması
* Güvenlik uyarıları

### Photo to Pattern

* Fotoğraftan proje fikri
* Yapı ve parça analizi
* Tahmini malzeme listesi
* Düzenlenebilir tarif taslağı

### Affiliate Commerce

* Eksik malzemeyi satın alma bağlantısı
* Mağaza karşılaştırması
* Bölgesel satıcılar
* Affiliate gelir modeli

---

# 12. V3 Entry Criteria

V3 geliştirmesine başlamadan önce:

* V1 ve V2 ürün-pazar uyumu sinyali göstermelidir.
* Yeterli aktif kullanıcı kitlesi bulunmalıdır.
* Moderasyon ve içerik politikaları hazırlanmalıdır.
* Pazaryeri için yasal ve finansal süreçler tamamlanmalıdır.
* AI özellikleri için yeterli güvenlik ve kalite ölçümleri bulunmalıdır.
* Operasyonel destek kapasitesi oluşturulmalıdır.

---

# 13. Feature Dependencies

## Core Dependency Chain

```text
Authentication
    ↓
User Profile
    ↓
Project Management
    ↓
Row Counter + Multi-Part Tracking
```

## Material Dependency Chain

```text
Yarn Inventory
    +
Hook and Needle Inventory
    +
Pattern Library
    ↓
Smart Material Recommendations
    ↓
Yarn Finisher
```

## Pattern Dependency Chain

```text
Pattern Library
    ↓
Custom Patterns
    ↓
Starter Patterns
    ↓
Smart PDF
    ↓
AI Pattern Helper
```

## Platform Dependency Chain

```text
User Profile
    +
Cloud Sync
    +
Content Moderation
    +
Payment Infrastructure
    ↓
Community + Marketplace
```

---

# 14. Key Product Risks by Version

## V1 Risks

* Çok geniş MVP kapsamı
* Offline veri yönetimi
* Sayaç veri kaybı
* Envanter girişinin kullanıcıya zahmetli gelmesi
* Premium sınırlarının yanlış belirlenmesi
* İlk içeriklerin yetersiz olması

## V2 Risks

* AI maliyeti
* AI yanlış sonuçları
* PDF telif ve içerik sorunları
* Senkronizasyon çakışmaları
* Bildirim yorgunluğu

## V3 Risks

* Moderasyon maliyeti
* Dolandırıcılık ve ödeme riskleri
* Telif hakkı ihlalleri
* Topluluk güvenliği
* Kamera AI doğruluğu
* Marketplace operasyonu

---

# 15. Roadmap Success Metrics

## V1 Success Indicators

* Kullanıcı ilk oturumda proje oluşturabiliyor.
* Sayaç oturumları veri kaybı olmadan tamamlanıyor.
* Kullanıcıların anlamlı bir bölümü envanter oluşturuyor.
* Kullanıcılar tariften proje başlatıyor.
* Malzemeye uygun öneriler görüntüleniyor.
* Kritik crash oranı düşük tutuluyor.
* Kullanıcılar birden fazla gün uygulamaya geri dönüyor.

## V2 Success Indicators

* AI özellikleri düzenli kullanılıyor.
* PDF'den çıkarılan veriler kullanıcı tarafından yüksek oranda onaylanıyor.
* Birden fazla cihaz kullanan kullanıcılar senkronizasyondan faydalanıyor.
* Kişisel önerilere tıklama oranı artıyor.
* Bildirimler uygulamaya geri dönüş sağlıyor.

## V3 Success Indicators

* Kullanıcılar içerik oluşturuyor ve paylaşıyor.
* Marketplace işlemleri güvenli şekilde tamamlanıyor.
* Topluluk moderasyonu sürdürülebilir durumda.
* Üreticiler platformda düzenli gelir elde ediyor.
* Kamera ve AI özellikleri kabul edilebilir doğrulukta çalışıyor.

---

# 16. Change Management

Roadmap üzerinde önemli bir değişiklik yapılırken aşağıdaki süreç izlenmelidir:

1. Değişiklik önerisi hazırlanır.
2. Kullanıcı değeri açıklanır.
3. Teknik etkisi değerlendirilir.
4. Mevcut bağımlılıklar kontrol edilir.
5. V1 kapsamını büyütüyorsa çıkarılacak başka bir özellik belirlenir.
6. `DECISIONS.md` içine ürün kararı eklenir.
7. İlgili PRD ve feature belgeleri güncellenir.
8. `CHANGELOG.md` güncellenir.

---

# 17. Definition of MVP Complete

Knitwise V1 aşağıdaki koşulların tamamı sağlandığında tamamlanmış kabul edilir:

* P0 özelliklerinin tamamı geliştirilmiştir.
* Ana kullanıcı yolculukları başarıyla tamamlanmaktadır.
* Kritik ve yüksek öncelikli hatalar kapatılmıştır.
* Sayaç ve proje verilerinde bilinen veri kaybı sorunu bulunmamaktadır.
* RLS politikaları test edilmiştir.
* Secret taraması başarılıdır.
* Temel erişilebilirlik kontrolleri yapılmıştır.
* Gizlilik politikası ve kullanım koşulları hazırdır.
* Analytics olayları doğrulanmıştır.
* Crash reporting aktiftir.
* App Store ve Google Play gereksinimleri karşılanmıştır.
* Beta kullanıcı geri bildirimleri değerlendirilmiştir.
* İlgili dokümantasyon günceldir.

---

# 18. References

Bu belge aşağıdaki belgelerle birlikte değerlendirilmelidir:

* `README.md`
* `PROJECT_PRINCIPLES.md`
* `DECISIONS.md`
* `CONTRIBUTING.md`
* `01-product/roadmap.md`
* `02-prd/overview.md`
* `02-prd/feature-priorities.md`
* `02-prd/premium-strategy.md`
* `02-prd/release-plan.md`
* `03-features/`
