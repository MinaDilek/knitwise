# Knitwise Release Plan

## Document Information

| Alan         | Değer        |
| ------------ | ------------ |
| Product      | Knitwise     |
| Document     | Release Plan |
| Version      | 1.0          |
| Status       | Draft        |
| Owner        | Product      |
| Last Updated | 2026-07-29   |

---

# 1. Purpose

Bu belge, Knitwise uygulamasının geliştirme ortamından mağaza yayınına kadar izleyeceği sürüm sürecini tanımlar.

Amaç:

* Yayın aşamalarını açık biçimde belirlemek
* Her aşama için giriş ve çıkış kriterleri oluşturmak
* Geliştirme, test, güvenlik ve ürün kontrollerini standartlaştırmak
* Eksik veya riskli bir sürümün mağazaya gönderilmesini önlemek
* Beta geri bildirimlerinin nasıl değerlendirileceğini tanımlamak
* V1 sonrasında V1.x ve V2 geçişini planlamak
* Codex ve geliştirici görevlerini sürüm hedefleriyle ilişkilendirmek

Bu belge takvim tarihinden çok kalite kapılarına odaklanır.

Bir sürüm yalnızca belirlenen tarihe gelindiği için yayınlanmamalıdır.

---

# 2. Release Philosophy

Knitwise sürüm yönetimi aşağıdaki ilkelere dayanır.

## 2.1 Quality Before Deadline

Kritik veri kaybı, güvenlik açığı veya sayaç hatası bulunan bir sürüm yayınlanmaz.

## 2.2 Small and Controlled Releases

Büyük ve kontrol edilmesi zor sürümler yerine küçük, ölçülebilir ve geri alınabilir yayınlar tercih edilir.

## 2.3 Core Journey First

Yeni özelliklerden önce aşağıdaki ana kullanıcı yolculukları güvenilir olmalıdır:

* Hesap oluşturma
* Proje oluşturma
* Sayaç kullanma
* Çok parçalı proje takibi
* Tarif görüntüleme
* Envanter ekleme
* Malzemeyi projeye bağlama
* Projeyi tamamlama
* Veriyi koruma

## 2.4 No Silent Data Loss

Kullanıcı verisini sessizce kaybeden, üzerine yazan veya yanlış senkronize eden sürüm kabul edilemez.

## 2.5 Measured Rollout

Genel kullanıma açılmadan önce sürüm küçük kullanıcı gruplarında doğrulanmalıdır.

## 2.6 Documentation Is Part of Release

Kod tamamlanmış olsa bile ilgili dokümantasyon güncel değilse özellik tamamlanmış kabul edilmez.

---

# 3. Release Environments

Knitwise için en az üç ayrı ortam kullanılmalıdır.

## 3.1 Development

Günlük geliştirme ve erken doğrulama ortamıdır.

### Characteristics

* Geliştirici hesapları
* Test verileri
* Development Supabase projesi
* Ayrı API anahtarları
* Ayrıntılı loglama
* Mock servis kullanımı
* Deneysel özellikler açık olabilir

### Restrictions

* Gerçek kullanıcı verisi kullanılmamalıdır.
* Production secret'ları bulunmamalıdır.
* Mağaza production satın alma ürünleri kullanılmamalıdır.

---

## 3.2 Staging

Production'a en yakın test ortamıdır.

### Characteristics

* Production benzeri yapılandırma
* Ayrı Supabase projesi
* Test mağaza ürünleri
* Release candidate build'leri
* QA ve beta doğrulaması
* Analytics test modu
* Güvenlik ve migration testleri

### Restrictions

* Production kullanıcı verisi doğrudan kopyalanmamalıdır.
* Test verileri anonim veya sentetik olmalıdır.
* Staging ve production secret'ları farklı olmalıdır.

---

## 3.3 Production

Gerçek kullanıcıların kullandığı canlı ortamdır.

### Characteristics

* Gerçek kullanıcı hesapları
* Gerçek abonelik işlemleri
* Production Supabase projesi
* Kısıtlı loglama
* Aktif analytics
* Aktif crash reporting
* Sıkı RLS ve storage politikaları

### Restrictions

* Debug araçları kapalı olmalıdır.
* Hassas bilgiler loglanmamalıdır.
* Test özellikleri görünmemelidir.
* Development endpoint'leri bulunmamalıdır.

---

# 4. Release Types

## 4.1 Internal Build

Geliştirici ve ürün sahibi tarafından kullanılan erken sürümdür.

Amaç:

* Temel ekranları doğrulamak
* Akış hatalarını erken bulmak
* Teknik altyapıyı test etmek
* Tasarım kararlarını incelemek

---

## 4.2 Alpha

Ana özelliklerin bir kısmı çalışır ancak ürün henüz dış kullanıcıya hazır değildir.

Amaç:

* Modüller arası bağlantıları test etmek
* Veri modelini doğrulamak
* Temel kullanılabilirlik sorunlarını bulmak
* Büyük teknik riskleri azaltmak

---

## 4.3 Closed Beta

Davetli kullanıcılarla yapılan kontrollü testtir.

Amaç:

* Gerçek kullanıcı davranışını gözlemlemek
* Kullanılabilirlik problemlerini bulmak
* Sayaç ve proje güvenilirliğini doğrulamak
* Envanter giriş davranışını ölçmek
* Akıllı önerilerin anlaşılabilirliğini test etmek
* Premium algısını değerlendirmek

---

## 4.4 Soft Launch

Uygulamanın sınırlı kullanıcı kitlesi veya sınırlı bölgeyle mağazada yayınlanmasıdır.

Amaç:

* Production altyapısını düşük riskle doğrulamak
* Crash ve performans metriklerini ölçmek
* Mağaza satın alma sistemini test etmek
* Gerçek kullanıcı tutma oranlarını görmek
* Destek taleplerini analiz etmek

---

## 4.5 General Availability

Uygulamanın hedef pazarda genel kullanıma açılmasıdır.

V1 için ilk genel pazar Türkiye'dir.

---

## 4.6 Global Release

Yerelleştirme, fiyatlandırma ve destek süreçleri hazırlandıktan sonra uygulamanın diğer ülkelere açılmasıdır.

---

# 5. Versioning Strategy

Knitwise Semantic Versioning yaklaşımına yakın bir sürümleme kullanacaktır.

```text
MAJOR.MINOR.PATCH
```

Örnek:

```text
1.0.0
1.1.0
1.1.1
2.0.0
```

## MAJOR

Büyük ürün veya veri modeli değişiklikleri.

Örnek:

* V2 AI özellikleri
* Büyük navigasyon değişikliği
* Geriye dönük uyumsuz veri modeli
* Temel ürün yaklaşımının değişmesi

## MINOR

Geriye dönük uyumlu yeni özellikler.

Örnek:

* Yeni hesaplayıcı
* Bildirim sistemi
* Yeni starter tarifler
* Gelişmiş istatistikler

## PATCH

Hata düzeltmeleri ve küçük iyileştirmeler.

Örnek:

* Sayaç kayıt hatası
* Metin düzeltmesi
* Görsel taşma sorunu
* Crash düzeltmesi

---

# 6. Release Branch Strategy

Önerilen branch yapısı:

```text
main
develop
feature/*
fix/*
release/*
hotfix/*
```

## main

Production'da yayınlanan kararlı kodu içerir.

## develop

Bir sonraki sürüm için birleştirilen geliştirmeleri içerir.

## feature/*

Tek bir özellik veya sınırlı kapsamlı geliştirme için kullanılır.

Örnek:

```text
feature/project-management
feature/row-counter
feature/yarn-inventory
```

## fix/*

Henüz production'a çıkmamış hata düzeltmeleri için kullanılır.

## release/*

Release candidate hazırlamak için kullanılır.

Örnek:

```text
release/1.0.0
```

## hotfix/*

Production'daki kritik sorunları hızlı biçimde düzeltmek için kullanılır.

Örnek:

```text
hotfix/1.0.1-counter-data-loss
```

---

# 7. Phase 0 — Product and Technical Readiness

Bu aşama özellik geliştirmesinden önce tamamlanmalıdır.

## Required Deliverables

* Ürün vizyonu
* PRD belgeleri
* Feature öncelikleri
* Premium stratejisi
* Teknik mimari kararı
* Veri modeli taslağı
* Tasarım sistemi temeli
* Repository kuralları
* CI/CD temeli
* Test stratejisi
* Güvenlik stratejisi
* Analytics planı

## Entry Criteria

* Repository oluşturulmuştur.
* Governance belgeleri mevcuttur.
* V1 kapsamı belirlenmiştir.
* Temel teknik kararlar alınmıştır.

## Exit Criteria

* Feature geliştirmeye başlanabilecek dokümantasyon seviyesi oluşmuştur.
* Development ve staging ortamları tanımlanmıştır.
* Codex çalışma kuralları belirlenmiştir.
* P0 özelliklerin bağımlılıkları bilinmektedir.

---

# 8. Phase 1 — Internal Development

## Goal

Temel ürün altyapısını ve ilk kullanıcı yolculuğunu oluşturmak.

## Scope

* Flutter proje yapısı
* Riverpod
* Routing
* Tasarım sistemi
* Localization
* Yerel veri katmanı
* Supabase bağlantısı
* Authentication
* Project Management
* Basic Row Counter
* Settings foundation
* Analytics foundation
* Crash reporting

## Entry Criteria

* Phase 0 tamamlanmıştır.
* İlgili feature belgeleri hazırlanmıştır.
* Geliştirme ortamları çalışmaktadır.

## Exit Criteria

* Kullanıcı giriş yapabilir.
* Proje oluşturabilir.
* Sayaç kullanabilir.
* Veriler uygulama kapanınca korunur.
* Temel navigation kararlıdır.
* P0 akışlarda bilinen veri kaybı yoktur.
* CI kontrolleri çalışmaktadır.

---

# 9. Phase 2 — Alpha

## Goal

Knitwise'ın temel modüllerini birbirine bağlamak ve ürün çekirdeğini doğrulamak.

## Scope

* Multi-Part Tracking
* Pattern Library
* Custom Patterns
* Starter Patterns
* Yarn Inventory
* Hook and Needle Inventory
* Project Material Allocation
* Basic privacy screens
* Accessibility foundation

## Alpha Test Group

* Product Owner
* Geliştiriciler
* Tasarım değerlendiren kişiler
* Güvenilir birkaç teknik kullanıcı

## Entry Criteria

* Internal build temel akışları çalışmaktadır.
* Büyük mimari belirsizlikler çözülmüştür.
* Ana veri modelleri oluşturulmuştur.

## Exit Criteria

* Ana özellikler birlikte çalışmaktadır.
* Temel migration sistemi test edilmiştir.
* Authentication ve RLS kontrolleri yapılmıştır.
* Tariften proje oluşturulabilir.
* Envanter projeye bağlanabilir.
* Çok parçalı proje ilerlemesi doğru hesaplanır.
* Kritik crash bulunmamaktadır.
* Bilinen P0 hata bulunmamaktadır.

---

# 10. Phase 3 — Closed Beta

## Goal

Gerçek hedef kullanıcılarla ürün değerini, kullanılabilirliği ve güvenilirliği doğrulamak.

## Suggested Beta Group

İlk kapalı beta için yaklaşık:

* Yeni başlayan örgücüler
* Deneyimli örgücüler
* Amigurumi kullanıcıları
* Birden fazla proje yöneten kullanıcılar
* Dijital tarif kullanan kullanıcılar
* Farklı yaş ve teknik yeterlilik seviyesindeki kullanıcılar

Beta yalnızca arkadaş çevresinden oluşmamalıdır.

Hedef kullanıcı profiline gerçekten uyan kişiler seçilmelidir.

## Recommended Beta Size

İlk dalga:

```text
10–20 kullanıcı
```

İkinci dalga:

```text
30–50 kullanıcı
```

Daha büyük gruba geçmeden önce ilk dalgadaki kritik sorunlar çözülmelidir.

---

# 11. Closed Beta Scope

Beta sürümünde bulunması gereken ana özellikler:

* Onboarding
* Authentication
* Project Management
* Smart Row Counter
* Multi-Part Tracking
* Pattern Library
* Starter Patterns
* Custom Patterns
* Yarn Inventory
* Hook and Needle Inventory
* Settings
* Privacy controls
* Basic analytics
* Crash reporting

Beta sırasında opsiyonel olarak bulunabilecekler:

* Smart Material Recommendations beta
* Yarn Finisher beta
* Yarn Calculator
* Premium test ekranları
* Voice Commands prototipi

---

# 12. Beta Research Questions

Kapalı beta sırasında aşağıdaki sorular cevaplanmalıdır.

## Onboarding

* Kullanıcı Knitwise'ın ne işe yaradığını anlıyor mu?
* İlk değer anı ne zaman oluşuyor?
* Kayıt süreci fazla uzun mu?

## Project Management

* Kullanıcı ilk projesini yardım almadan oluşturabiliyor mu?
* Hangi alanlar gereksiz veya anlaşılmaz?
* Proje durumları anlaşılır mı?

## Row Counter

* Sayaç kontrol alanları yeterince büyük mü?
* Yanlış dokunma yaşanıyor mu?
* Geri alma özelliği bulunabiliyor mu?
* Kullanıcı sayaç verisine güveniyor mu?

## Multi-Part Tracking

* Parça ve adet mantığı anlaşılır mı?
* Amigurumi kullanıcılarına gerçek fayda sağlıyor mu?
* Proje ilerlemesi doğru algılanıyor mu?

## Pattern Library

* Kullanıcı tarif eklemeyi kolay buluyor mu?
* Tarif adımları yeterince okunabilir mi?
* Tarif ile proje bağlantısı anlaşılır mı?

## Inventory

* Kullanıcı ip eklerken hangi alanları dolduruyor?
* Hangi alanlar gereksiz görülüyor?
* Miktar ve birim sistemi anlaşılır mı?
* Envanter girişi fazla zahmetli mi?

## Smart Recommendations

* Kullanıcı önerinin neden gösterildiğini anlıyor mu?
* Uyum puanına güveniyor mu?
* Eksik malzeme açıklaması yeterli mi?

## Premium

* Kullanıcı ücretsiz planı yeterli buluyor mu?
* Premium faydası anlaşılır mı?
* Hangi özellikler ödeme isteği oluşturuyor?
* Limitler adil algılanıyor mu?

---

# 13. Beta Feedback Collection

Geri bildirim aşağıdaki kanallardan toplanabilir:

* Uygulama içi geri bildirim formu
* Kısa anket
* Birebir kullanıcı görüşmesi
* TestFlight geri bildirimi
* Google Play test geri bildirimi
* E-posta
* Kullanım analitiği
* Crash raporları
* Oturum gözlem notları

Kullanıcıdan tarif, proje notu veya özel görsel gibi kişisel içerikler izinsiz istenmemelidir.

---

# 14. Feedback Classification

Beta geri bildirimleri aşağıdaki kategorilere ayrılmalıdır.

## Critical

* Veri kaybı
* Güvenlik açığı
* Uygulamanın açılmaması
* Hesaba erişememe
* Sayaç değerinin bozulması
* Başka kullanıcının verisine erişim

## High

* Ana akışın tamamlanamaması
* Proje oluşturma sorunu
* Tarif kaydetme sorunu
* Envanter miktar hatası
* Ödeme veya restore hatası

## Medium

* Kullanılabilirlik problemi
* Yanlış yönlendiren metin
* Belirgin performans problemi
* Eksik hata mesajı
* Filtre veya sıralama sorunu

## Low

* Küçük görsel hata
* Metin iyileştirmesi
* Mikro animasyon sorunu
* Nadir edge case

## Feature Request

* Yeni özellik önerisi
* Mevcut özelliğin genişletilmesi
* V2 veya V3 fikri

Feature request'ler otomatik olarak V1 kapsamına alınmamalıdır.

---

# 15. Closed Beta Exit Criteria

Kapalı beta tamamlanmış kabul edilmek için:

* Açık kritik hata bulunmamalıdır.
* Açık yüksek öncelikli hatalar kabul edilebilir seviyeye düşürülmelidir.
* Ana kullanıcı yolculukları başarıyla tamamlanmalıdır.
* Sayaç verisi kaybolmamalıdır.
* Proje verisi güvenilir biçimde korunmalıdır.
* Envanter miktarları doğru çalışmalıdır.
* RLS testleri geçmelidir.
* Crash-free session oranı kabul edilebilir seviyede olmalıdır.
* Beta kullanıcılarının çoğu temel değer önerisini anlamalıdır.
* Geri bildirimlerin ana temaları belgelenmelidir.
* Soft launch için kabul edilen riskler kayıt altına alınmalıdır.

---

# 16. Phase 4 — Release Candidate

## Goal

Mağazaya gönderilecek sürümün son doğrulamasını yapmak.

Release candidate yalnızca hata düzeltmeleri ve zorunlu mağaza düzenlemeleri almalıdır.

Yeni özellik eklenmemelidir.

## Release Candidate Requirements

* Version number güncellenmiş
* Build number güncellenmiş
* Changelog hazırlanmış
* Migration test edilmiş
* Production environment kontrol edilmiş
* Analytics production yapılandırması doğrulanmış
* Crash reporting production yapılandırması doğrulanmış
* RLS policies test edilmiş
* Storage policies test edilmiş
* Secret taraması tamamlanmış
* Dependency audit yapılmış
* Store purchase ürünleri doğrulanmış
* Privacy policy hazır
* Terms of use hazır
* Support contact hazır
* Store screenshots hazır
* Store descriptions hazır
* App icons hazır
* Splash screen hazır

---

# 17. Release Candidate Test Matrix

## Authentication

* Yeni kayıt
* E-posta doğrulama
* Giriş
* Çıkış
* Şifre sıfırlama
* Oturum süresi
* Hesap silme

## Project Management

* Proje oluşturma
* Düzenleme
* Silme
* Arşivleme
* Durum değiştirme
* Offline açma
* Uygulama yeniden başlatma

## Row Counter

* Artırma
* Azaltma
* Geri alma
* Sıfırlama
* Otomatik kayıt
* Ekran kilidi
* Uygulamanın arka plana alınması
* Uygulamanın zorla kapatılması

## Multi-Part

* Parça ekleme
* Parça silme
* Adet artırma
* Gerekli adet kontrolü
* Parça sıralama
* Proje ilerleme hesabı

## Patterns

* Tarif oluşturma
* Tarif düzenleme
* Adım sıralama
* Favori
* Arama
* Filtreleme
* Projeye dönüştürme

## Inventory

* İp ekleme
* Miktar güncelleme
* Projeye ayırma
* Kullanımı düşme
* Manuel düzeltme
* Negatif miktar engeli
* Şiş ve tığ ekleme
* Ölçü dönüşümü

## Premium

* Aylık satın alma
* Yıllık satın alma
* İptal
* Beklemede
* Geri yükleme
* Abonelik sona ermesi
* Offline premium erişimi
* Downgrade
* Mevcut veri korunması

## Privacy and Security

* Başka kullanıcı verisine erişim
* Hesap silme
* Veri dışa aktarma
* Hassas log kontrolü
* Storage erişimi
* RLS kontrolü
* Secret kontrolü

---

# 18. Phase 5 — Soft Launch

## Goal

Production ortamını ve gerçek kullanıcı davranışını kontrollü şekilde doğrulamak.

## Recommended Initial Market

İlk soft launch Türkiye'de sınırlı kullanıcı kitlesiyle yapılmalıdır.

Bu sınırlama şu yollarla uygulanabilir:

* Davet bağlantısı
* Sınırlı mağaza görünürlüğü
* Kademeli dağıtım
* Küçük pazarlama kampanyası
* Belirli kullanıcı toplulukları

## Initial Rollout

Önerilen dağıtım:

```text
%5 → %10 → %25 → %50 → %100
```

Her artıştan önce temel sağlık metrikleri kontrol edilmelidir.

---

# 19. Soft Launch Monitoring

## Technical Metrics

* Crash-free sessions
* Crash-free users
* Uygulama açılış süresi
* API hata oranı
* Authentication hata oranı
* Database hata oranı
* Storage hata oranı
* Satın alma başarı oranı
* Restore başarı oranı
* Senkronizasyon hata oranı

## Product Metrics

* Onboarding tamamlama oranı
* Kayıt tamamlama oranı
* İlk proje oluşturma oranı
* İlk sayaç kullanımı
* İlk envanter öğesi ekleme
* Tariften proje başlatma
* Proje tamamlama
* Gün 1 geri dönüş
* Gün 7 geri dönüş
* Gün 30 geri dönüş

## Premium Metrics

* Paywall görüntüleme
* Paywall kapatma
* Deneme başlatma
* Satın alma
* İptal
* İade
* Restore başarısı

## Support Metrics

* Destek talebi sayısı
* En sık hata konusu
* Ortalama çözüm süresi
* Tekrarlanan şikâyet
* Veri kaybı bildirimi
* Ödeme şikâyeti

---

# 20. Soft Launch Pause Conditions

Aşağıdaki durumlarda rollout durdurulmalıdır:

* Veri kaybı tespit edilmesi
* Sayaç değerlerinin yanlış kaydedilmesi
* Kullanıcılar arası veri erişimi
* Kritik güvenlik açığı
* Crash oranında ani artış
* Authentication sisteminin yaygın çalışmaması
* Satın alma sonrası premium erişimin verilmemesi
* Restore işleminin yaygın başarısızlığı
* Migration nedeniyle veri bozulması
* Kullanıcı verisinin silinmesi
* Production secret sızıntısı

Rollout durdurulduğunda yeni kullanıcı dağıtımı artırılmamalıdır.

---

# 21. Soft Launch Exit Criteria

Genel yayına geçmek için:

* Kritik hata bulunmamalıdır.
* Yüksek öncelikli hatalar kontrol altında olmalıdır.
* Crash-free oranı hedefe yakın olmalıdır.
* Ana kullanıcı yolculukları çalışmalıdır.
* Sayaç ve proje verisi güvenilir olmalıdır.
* Satın alma ve restore akışları doğrulanmalıdır.
* Kullanıcı destek süreci çalışmaktadır.
* Premium açıklamaları kullanıcılar tarafından anlaşılmaktadır.
* Mağaza yorumlarında sistematik kritik problem görülmemektedir.
* Teknik altyapı beklenen yükü karşılamaktadır.
* Product Owner yayın onayı vermiştir.

---

# 22. Phase 6 — Türkiye General Availability

## Goal

Knitwise V1'i Türkiye'deki tüm hedef kullanıcılara açmak.

## Launch Scope

* Türkçe kullanıcı deneyimi
* Türkiye mağaza açıklamaları
* Türkiye bölgesel fiyatlandırması
* Aktif destek kanalı
* Yerel onboarding
* Yerel örgü terminolojisi
* Uygun starter tarifler
* Gizlilik ve kullanım koşulları
* Premium sistem

## Launch Activities

* App Store yayını
* Google Play yayını
* Ürün web sayfası
* Destek sayfası
* Sosyal medya duyuruları
* Örgü topluluklarına tanıtım
* Beta kullanıcılarına bilgilendirme
* Mağaza yorumlarının izlenmesi
* İlk hafta günlük metrik kontrolü

---

# 23. First 72 Hours

Genel yayından sonraki ilk 72 saatte:

* Crash raporları sık kontrol edilmelidir.
* Authentication sorunları izlenmelidir.
* Satın alma hataları kontrol edilmelidir.
* Restore sorunları izlenmelidir.
* Destek talepleri sınıflandırılmalıdır.
* Mağaza yorumları değerlendirilmelidir.
* Analytics event akışı doğrulanmalıdır.
* Sunucu ve database yükü kontrol edilmelidir.
* Kritik problem varsa rollout veya pazarlama durdurulmalıdır.

---

# 24. First 30 Days

İlk 30 günde aşağıdaki analizler yapılmalıdır.

## Week 1

* Kritik teknik sorunlar
* Onboarding terk oranı
* İlk proje oluşturma
* Sayaç güvenilirliği
* Satın alma hataları
* En sık destek konusu

## Week 2

* İlk haftanın retention verileri
* Envanter kullanım oranı
* Tarif kullanım davranışı
* Premium paywall performansı
* Kullanıcı yorumlarının ortak temaları

## Week 3

* Özellik kullanım dağılımı
* Ücretsiz limit davranışı
* Premium dönüşüm
* Tamamlanan proje oranı
* İyileştirme adayları

## Week 4

* V1.1 kapsam kararı
* Ertelenecek özellikler
* Teknik borç listesi
* Premium limit değerlendirmesi
* V2 hazırlık sinyalleri

---

# 25. Post-Launch Release Categories

## Hotfix Release

Production'daki kritik sorunu düzeltir.

Örnek:

```text
1.0.1
```

Hotfix gerektiren durumlar:

* Veri kaybı
* Güvenlik açığı
* Yaygın crash
* Giriş yapılamaması
* Satın alma erişim sorunu
* Sayaç değerinin bozulması

## Maintenance Release

Küçük hata düzeltmeleri ve performans iyileştirmeleri içerir.

Örnek:

```text
1.0.2
```

## Feature Release

Yeni ve geri uyumlu özellikler içerir.

Örnek:

```text
1.1.0
```

## Major Release

Büyük ürün aşamasını temsil eder.

Örnek:

```text
2.0.0
```

---

# 26. Hotfix Process

Kritik production sorunu oluştuğunda:

1. Sorun doğrulanır.
2. Etkilenen kullanıcı kapsamı belirlenir.
3. Gerekirse rollout veya ilgili özellik durdurulur.
4. `hotfix/*` branch'i oluşturulur.
5. Minimum kapsamlı düzeltme yapılır.
6. Unit ve regression testleri çalıştırılır.
7. Güvenlik kontrolü yapılır.
8. Staging'de doğrulanır.
9. Mağaza incelemesine gönderilir.
10. Kullanıcı iletişimi gerekiyorsa hazırlanır.
11. `CHANGELOG.md` güncellenir.
12. Kök neden analizi yapılır.

Hotfix sırasında alakasız özellik eklenmemelidir.

---

# 27. Rollback Strategy

Mobil mağaza sürümleri her zaman anında geri alınamaz.

Bu nedenle rollback yalnızca uygulama binary'sine bağlı olmamalıdır.

Aşağıdaki mekanizmalar değerlendirilmelidir:

* Feature flags
* Remote configuration
* Server-side özellik kapatma
* Sorunlu öneri algoritmasını devre dışı bırakma
* Yeni kayıtları geçici durdurma
* Migration sonrası geri dönüş planı
* Eski API sürümünü geçici destekleme
* Premium entitlement fallback

## Rollback Rules

* Kullanıcı verisi geri dönüş sırasında silinmemelidir.
* Eski uygulama sürümü yeni veriyi bozacaksa uyumluluk katmanı oluşturulmalıdır.
* Geri alınamayan migration dikkatle planlanmalıdır.
* Her kritik backend değişikliğinde rollback senaryosu yazılmalıdır.

---

# 28. Database Migration Strategy

Her veri modeli değişikliği için:

* Migration dosyası oluşturulmalıdır.
* Development'ta test edilmelidir.
* Staging'de production benzeri veriyle test edilmelidir.
* Geriye uyumluluk kontrol edilmelidir.
* Eski uygulama sürümlerinin davranışı değerlendirilmelidir.
* Null ve eksik veri senaryoları test edilmelidir.
* Büyük tablolar için performans etkisi ölçülmelidir.
* Gerekirse rollback migration hazırlanmalıdır.

## Migration Must Not

* Kullanıcı verisini sessizce silmemelidir.
* Varsayılan değer olmadan zorunlu kolon eklememelidir.
* Uzun süre tabloyu kilitlememelidir.
* Eski client sürümünü anında bozacak şekilde yayınlanmamalıdır.

---

# 29. Feature Flag Strategy

Riskli veya aşamalı yayınlanacak özelliklerde feature flag kullanılabilir.

Örnek özellikler:

* Smart Material Recommendations
* Yarn Finisher
* Voice Commands
* Premium trial
* Yeni onboarding
* Cloud sync
* AI özellikleri
* Yeni paywall

## Feature Flag Requirements

* Varsayılan durum tanımlanmalıdır.
* Hedef kullanıcı grubu belirlenmelidir.
* Kapatma etkisi bilinmelidir.
* Kalıcı flag'ler düzenli temizlenmelidir.
* Güvenlik kontrolü yalnızca feature flag'e bırakılmamalıdır.
* Premium entitlement ile feature flag birbirine karıştırılmamalıdır.

---

# 30. Store Submission Requirements

## App Store

* Uygulama adı
* Alt başlık
* Açıklama
* Anahtar kelimeler
* Ekran görüntüleri
* App icon
* Privacy nutrition bilgileri
* Support URL
* Privacy policy URL
* In-app purchase ürünleri
* Review notes
* Test hesabı gerekiyorsa bilgiler
* Hesap silme akışı
* Restore purchases

## Google Play

* Uygulama adı
* Kısa açıklama
* Uzun açıklama
* Ekran görüntüleri
* Feature graphic
* App icon
* Data Safety formu
* İçerik derecelendirmesi
* Privacy policy
* In-app product bilgileri
* Test hesapları
* Account deletion bilgileri

Mağaza formlarındaki veri toplama beyanları gerçek uygulama davranışıyla uyuşmalıdır.

---

# 31. Release Notes Standard

Her sürüm için kullanıcı odaklı release notes hazırlanmalıdır.

İyi örnek:

> Proje ilerlemenizi daha güvenli takip edebilmeniz için sayaç kayıt sistemini geliştirdik. Ayrıca ip envanteri ekranındaki bazı hataları düzelttik.

Kötü örnek:

> Refactor, bug fixes, DB migration and Riverpod improvements.

Teknik detaylar `CHANGELOG.md` içinde yer alabilir.

Mağaza notları kullanıcı faydasını açıklamalıdır.

---

# 32. User Communication

Kullanıcı iletişimi aşağıdaki durumlarda gerekli olabilir:

* Planlı bakım
* Veri riski
* Büyük özellik değişikliği
* Premium fiyat değişikliği
* Hizmet kesintisi
* Hesap veya güvenlik problemi
* Kullanım koşulu güncellemesi
* Gizlilik politikası güncellemesi

İletişim:

* açık,
* dürüst,
* zamanında,
* teknik olmayan,
* yapılması gereken aksiyonu belirten

bir dille hazırlanmalıdır.

---

# 33. Release Roles

## Product Owner

* Sürüm kapsamını onaylar.
* Öncelikleri belirler.
* Kabul kriterlerini doğrular.
* Go veya no-go kararı verir.

## Product Architect

* PRD ve feature belgelerini hazırlar.
* Bağımlılıkları kontrol eder.
* Kapsam değişikliklerini değerlendirir.
* Codex görevlerini oluşturur.

## Engineering

* Özellikleri geliştirir.
* Testleri yazar.
* Migration'ları hazırlar.
* Teknik riskleri bildirir.
* Release build üretir.

## QA

* Test planını uygular.
* Regression testi yapar.
* Hataları önceliklendirir.
* Release candidate doğrulaması yapar.

## Design

* Kullanıcı akışlarını doğrular.
* Görsel tutarlılığı kontrol eder.
* Erişilebilirlik ve responsive davranışı inceler.

## Security

* Secret taraması
* Dependency kontrolleri
* RLS doğrulaması
* Storage güvenliği
* Kritik bulgu değerlendirmesi

Tek kişilik ekipte bu roller aynı kişi tarafından yürütülebilir ancak kontroller atlanmamalıdır.

---

# 34. Go / No-Go Meeting

Her büyük sürüm öncesinde kısa bir yayın değerlendirmesi yapılmalıdır.

## Go Checklist

* Scope tamamlandı mı?
* Açık kritik hata var mı?
* Veri kaybı riski var mı?
* Güvenlik kontrolleri geçti mi?
* Migration test edildi mi?
* Analytics çalışıyor mu?
* Crash reporting çalışıyor mu?
* Premium akışlar test edildi mi?
* Restore çalışıyor mu?
* Yasal belgeler hazır mı?
* Store materyalleri hazır mı?
* Destek süreci hazır mı?
* Rollback veya özellik kapatma planı var mı?

## Possible Decisions

### Go

Sürüm yayınlanabilir.

### Conditional Go

Kabul edilen sınırlı risklerle yayınlanabilir.

Riskler yazılı olarak kaydedilmelidir.

### No-Go

Sürüm yayınlanamaz.

No-go kararı başarısızlık olarak değerlendirilmemelidir.

Bu karar kullanıcı güvenini ve veri bütünlüğünü korumak için alınır.

---

# 35. Release Acceptance Criteria

Bir production sürümü aşağıdaki koşullarda kabul edilir:

* Belirlenen kapsam tamamlanmıştır.
* Tüm P0 testleri geçmiştir.
* Açık kritik hata yoktur.
* Açık yüksek hata kabul edilmiş ve belgelenmiştir.
* Migration doğrulanmıştır.
* Güvenlik kontrolleri tamamlanmıştır.
* Analytics event'leri çalışmaktadır.
* Crash reporting aktiftir.
* Temel erişilebilirlik kontrolleri yapılmıştır.
* Offline davranış test edilmiştir.
* Store satın alma işlemleri test edilmiştir.
* Kullanıcı verisi korunmaktadır.
* İlgili dokümantasyon günceldir.
* Product Owner onayı vardır.

---

# 36. Release Health Metrics

Her sürüm sonrasında şu metrikler izlenmelidir.

## Stability

* Crash-free sessions
* Crash-free users
* ANR oranı
* Açılış başarısızlığı
* Uygulama donma oranı

## Performance

* Cold start süresi
* Warm start süresi
* Ana ekran yükleme süresi
* Proje açılma süresi
* Sayaç işlem gecikmesi
* Envanter listeleme süresi

## Reliability

* Sayaç kayıt başarısı
* Proje kayıt başarısı
* Envanter güncelleme başarısı
* Authentication başarısı
* Purchase restore başarısı
* Migration başarısı

## Adoption

* Yeni özellik görüntüleme
* Yeni özellik kullanma
* İlk başarılı işlem
* Tekrar kullanım
* Özellik terk oranı

## Sentiment

* Mağaza puanı
* Destek şikâyetleri
* Kullanıcı geri bildirimi
* İade talepleri
* İptal nedenleri

---

# 37. V1.1 Planning

V1.1 aşağıdaki kaynaklardan beslenecektir:

* Beta geri bildirimleri
* Soft launch verileri
* Mağaza yorumları
* Destek talepleri
* Analytics
* Crash raporları
* Ertelenen P2 özellikleri
* Teknik borç

Muhtemel V1.1 adayları:

* Voice Commands
* Bildirimler
* Basic Statistics
* Sosyal giriş
* Gelişmiş filtreleme
* Ek starter tarifler
* Envanter giriş kolaylaştırmaları
* Sayaç widget'ı
* UX iyileştirmeleri

V1.1 kapsamı gerçek kullanıcı verisine göre belirlenmelidir.

---

# 38. V2 Entry Criteria

V2 geliştirmesine başlanabilmesi için:

* V1 production'da kararlı olmalıdır.
* Kritik veri kaybı sorunu bulunmamalıdır.
* Kullanıcıların temel ürün değerini kullandığı doğrulanmalıdır.
* Proje ve sayaç kullanım oranları anlamlı olmalıdır.
* Envanter kullanımı gözlemlenmelidir.
* Premium model hakkında yeterli veri toplanmalıdır.
* Teknik borç kontrol altında olmalıdır.
* Cloud sync stratejisi hazır olmalıdır.
* AI maliyet modeli hazırlanmalıdır.
* AI güvenlik ve doğruluk standartları tanımlanmalıdır.
* V2 feature belgeleri hazırlanmalıdır.

V2 yalnızca V1 yayınlandı diye otomatik başlamamalıdır.

---

# 39. Global Release Readiness

Türkiye dışındaki pazarlara açılmadan önce:

* İngilizce kullanıcı deneyimi tamamlanmalıdır.
* Tüm hard-coded metinler kaldırılmalıdır.
* Tarih, sayı ve ölçü birimleri yerelleştirilmelidir.
* UK ve US örgü terminolojisi farkları ele alınmalıdır.
* Mağaza açıklamaları yerelleştirilmelidir.
* Global fiyatlandırma hazırlanmalıdır.
* Destek dili belirlenmelidir.
* Gizlilik ve yasal metinler güncellenmelidir.
* Kullanılan tarif içeriklerinin lisansı kontrol edilmelidir.
* Bölgesel ödeme ve vergi etkileri değerlendirilmelidir.
* Farklı cihaz ve ekran boyutları test edilmelidir.

---

# 40. Release Documentation Requirements

Her sürümde aşağıdaki belgeler güncellenmelidir:

* `CHANGELOG.md`
* İlgili `03-features/*` dosyaları
* Teknik mimari belgeleri
* Veri modeli belgeleri
* Test planı
* Analytics event listesi
* Premium entitlement listesi
* Security notes
* Migration notes
* Store release notes

Önemli ürün kararı değişmişse:

* `DECISIONS.md`

güncellenmelidir.

---

# 41. Release Checklist

## Product

* [ ] Sürüm kapsamı onaylandı
* [ ] Kabul kriterleri tamamlandı
* [ ] Open questions kapatıldı
* [ ] Feature flag kararları alındı
* [ ] Premium sınırları doğrulandı

## Engineering

* [ ] Kod review tamamlandı
* [ ] Lint başarılı
* [ ] Unit testler başarılı
* [ ] Widget testler başarılı
* [ ] Integration testler başarılı
* [ ] Build başarılı
* [ ] Migration başarılı
* [ ] Environment doğru

## Security

* [ ] Gitleaks başarılı
* [ ] Semgrep kontrolü tamamlandı
* [ ] Dependency audit tamamlandı
* [ ] RLS test edildi
* [ ] Storage policies test edildi
* [ ] Production secret kontrol edildi
* [ ] Hassas log bulunmuyor

## Quality

* [ ] Regression testi tamamlandı
* [ ] Kritik hata yok
* [ ] Yüksek hatalar değerlendirildi
* [ ] Erişilebilirlik kontrol edildi
* [ ] Offline kullanım test edildi
* [ ] Düşük donanımlı cihaz test edildi
* [ ] Farklı ekran boyutları test edildi

## Premium

* [ ] Aylık satın alma test edildi
* [ ] Yıllık satın alma test edildi
* [ ] Restore test edildi
* [ ] Downgrade test edildi
* [ ] Grace period test edildi
* [ ] Entitlement doğrulandı
* [ ] Paywall yasal metinleri görünür

## Store

* [ ] App icon hazır
* [ ] Screenshots hazır
* [ ] Açıklamalar hazır
* [ ] Privacy policy hazır
* [ ] Support URL hazır
* [ ] Data Safety bilgileri doğru
* [ ] App Store privacy bilgileri doğru
* [ ] Review notes hazır
* [ ] Version ve build number doğru

## Operations

* [ ] Analytics aktif
* [ ] Crash reporting aktif
* [ ] Destek kanalı aktif
* [ ] Monitoring hazır
* [ ] Rollback planı hazır
* [ ] Feature flag kontrolü hazır

---

# 42. Definition of Release Ready

Bir sürüm `Release Ready` kabul edilmek için:

* Sürüm kapsamı dondurulmuş olmalıdır.
* Yeni özellik eklenmesi durdurulmalıdır.
* Tüm P0 gereksinimler tamamlanmalıdır.
* Release candidate oluşturulmalıdır.
* Test matrisi tamamlanmalıdır.
* Açık kritik hata bulunmamalıdır.
* Güvenlik kontrolleri geçmelidir.
* Migration doğrulanmalıdır.
* Premium işlemleri test edilmelidir.
* Yasal ve mağaza içerikleri hazır olmalıdır.
* Monitoring ve rollback planı bulunmalıdır.
* Product Owner onayı alınmalıdır.

---

# 43. Definition of Release Done

Bir sürüm tamamlanmış kabul edilmek için:

* Mağaza incelemesinden geçmiş olmalıdır.
* Hedef kullanıcı grubuna yayınlanmış olmalıdır.
* Production analytics doğrulanmış olmalıdır.
* Crash reporting veri almalıdır.
* Satın alma ve restore production'da çalışmalıdır.
* İlk sağlık kontrolleri tamamlanmalıdır.
* Kritik kullanıcı problemi bulunmamalıdır.
* Release notes yayınlanmalıdır.
* `CHANGELOG.md` güncellenmelidir.
* Sürüm sonrası değerlendirme yapılmalıdır.
* Sonraki sürüm adayları kayıt altına alınmalıdır.

---

# 44. References

Bu belge aşağıdaki dokümanlarla birlikte değerlendirilmelidir:

* `README.md`
* `AGENTS.md`
* `PROJECT_PRINCIPLES.md`
* `DECISIONS.md`
* `CONTRIBUTING.md`
* `CHANGELOG.md`
* `01-product/roadmap.md`
* `01-product/success-metrics.md`
* `02-prd/overview.md`
* `02-prd/mvp-roadmap.md`
* `02-prd/feature-priorities.md`
* `02-prd/premium-strategy.md`
* `03-features/`
* `06-technical/`
* `09-testing/`

