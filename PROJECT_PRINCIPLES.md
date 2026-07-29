
# PROJECT_PRINCIPLES.md

# Knitwise Project Principles

Bu belge, Knitwise ürününün geliştirilmesi sırasında değişmemesi gereken temel ürün, tasarım, teknik ve güvenlik ilkelerini tanımlar.

Tüm ürün kararları, teknik kararlar, tasarım kararları ve Codex görevleri bu ilkelerle uyumlu olmalıdır.

## 1. Kullanıcı Değeri Önceliklidir

Her özellik kullanıcıya açık ve ölçülebilir bir fayda sağlamalıdır.

Sadece teknolojik olarak ilgi çekici olduğu için özellik geliştirilmemelidir.

## 2. Sadelik Temel Tasarım İlkesidir

Kullanıcılar gereksiz seçenekler, teknik terimler veya karmaşık ekranlarla karşılaştırılmamalıdır.

Bir işlem mümkün olan en az adımla tamamlanmalıdır.

## 3. Türkçe Öncelikli Deneyim

Knitwise ilk olarak Türkçe konuşan kullanıcılar için tasarlanacaktır.

Türkçe örgü terminolojisi doğru, anlaşılır ve tutarlı kullanılmalıdır.

Global dil desteği eklenirken Türkçe deneyimin kalitesi azaltılmamalıdır.

## 4. Global Genişlemeye Uygun Yapı

Ürün ilk olarak Türkiye pazarına çıkacak olsa da teknik ve tasarımsal yapı global genişlemeye uygun olmalıdır.

Metinler doğrudan kod içine yazılmamalı, yerelleştirme sistemi kullanılmalıdır.

## 5. Temel Özellikler Erişilebilir Olmalıdır

Proje oluşturma, temel sıra sayacı ve temel örgü takibi gibi ana özellikler tamamen ücretli hale getirilmemelidir.

Premium üyelik kullanıcıya ek değer sunmalı, temel deneyimi kullanılmaz hale getirmemelidir.

## 6. Offline Kullanım Desteklenmelidir

Aşağıdaki temel özellikler internet bağlantısı olmadan çalışabilmelidir:

* Proje görüntüleme
* Sıra sayacı
* Bölüm ve parça takibi
* Kayıtlı tarifleri görüntüleme
* Yerel envanteri görüntüleme

İnternet bağlantısı geri geldiğinde veriler güvenli biçimde senkronize edilmelidir.

## 7. Kullanıcı Verisi Kullanıcıya Aittir

Kullanıcı verileri açık izin olmadan üçüncü taraflarla paylaşılmamalıdır.

Kullanıcı verisini dışa aktarma ve hesabını silme seçenekleri sağlanmalıdır.

## 8. Gizlilik Varsayılan Olmalıdır

Kullanıcıdan yalnızca ürünün çalışması için gerekli veriler istenmelidir.

Gereksiz kişisel veri toplanmamalıdır.

## 9. Güvenlik Sonradan Eklenen Bir Özellik Değildir

Güvenlik geliştirme sürecinin başından itibaren uygulanmalıdır.

Secret, token, API anahtarı ve şifreler repository içinde tutulmamalıdır.

## 10. Açıklanabilir Öneriler Sunulmalıdır

Kullanıcıya sunulan tarif, ip veya malzeme önerileri mümkün olduğunda açıklanmalıdır.

Örnek:

> Bu tarif önerildi çünkü elinizde tarif için gereken ipin yaklaşık %90'ı bulunuyor.

## 11. AI Sonuçları Kesin Gerçek Gibi Sunulmamalıdır

AI tarafından oluşturulan veya yorumlanan içerikler açıkça belirtilmelidir.

Kullanıcı, AI önerisini kontrol edebilmeli, düzenleyebilmeli veya reddedebilmelidir.

## 12. AI Kullanımı Amaç Odaklı Olmalıdır

AI yalnızca kullanıcıya gerçek fayda sağlayan alanlarda kullanılmalıdır.

Sırf ürünün AI özelliği varmış gibi görünmesi için AI entegrasyonu yapılmamalıdır.

## 13. Kamera ve Görsel Analiz Sonuçları Doğrulanmalıdır

Fotoğraftan örgü, ip veya ilmek analizi gibi özellikler kullanıcı onayı olmadan kalıcı veri oluşturmamalıdır.

## 14. Erişilebilirlik Zorunludur

Uygulama mümkün olduğunca şu ihtiyaçları desteklemelidir:

* Ekran okuyucu
* Yeterli renk kontrastı
* Dinamik yazı boyutu
* Büyük dokunma alanları
* Sadece renge bağlı olmayan durum göstergeleri
* Sesli geri bildirim
* Klavye ve yardımcı teknoloji kullanımı

## 15. Mobil Kullanım Koşulları Önceliklidir

Kullanıcıların örgü yaparken uygulamayı tek elle veya sınırlı dikkatle kullanabileceği varsayılmalıdır.

Sayaç ve proje kontrolleri kolay erişilebilir olmalıdır.

## 16. Performans Kullanıcı Deneyiminin Parçasıdır

Ana ekran, proje ekranı ve sıra sayacı gereksiz bekleme oluşturmamalıdır.

Uzun süren işlemler kullanıcıya görünür durum bilgisi vermelidir.

## 17. Veri Kaybı Kabul Edilemez

Sayaç, proje ilerlemesi ve envanter değişiklikleri mümkün olan en kısa sürede güvenli şekilde kaydedilmelidir.

Uygulamanın kapanması ilerleme kaybına neden olmamalıdır.

## 18. Geri Alma İmkânı Sağlanmalıdır

Kullanıcıların yanlışlıkla yaptığı kritik işlemler mümkün olduğunda geri alınabilmelidir.

Silme işlemleri doğrudan ve geri dönüşsüz olmamalıdır.

## 19. Envanter Verileri Tahmin Edilebilir Olmalıdır

İp miktarı, kullanılan malzeme ve kalan miktar hesaplamaları kullanıcı tarafından anlaşılabilir olmalıdır.

Tahmini ve kesin değerler birbirinden ayrılmalıdır.

## 20. Ölçü Birimleri Tutarlı Yönetilmelidir

Gram, metre, yard, milimetre ve numara gibi birimler merkezi bir sistem üzerinden yönetilmelidir.

Dönüşüm kaynaklı veri kaybı önlenmelidir.

## 21. Ürün Kararları Ölçülebilir Olmalıdır

Yeni özellikler için mümkün olduğunda başarı kriterleri tanımlanmalıdır.

Örnek metrikler:

* Özellik kullanım oranı
* Proje tamamlama oranı
* Haftalık aktif kullanıcı
* Sayaç kullanım sıklığı
* Premium dönüşüm oranı

## 22. Gereksiz Kapsam Genişlemesinden Kaçınılmalıdır

Bir sürüm için belirlenen kapsam, açık karar alınmadan genişletilmemelidir.

Yeni fikirler doğrudan geliştirmeye alınmak yerine backlog içine eklenmelidir.

## 23. V1 Bireysel Kullanıcı Deneyimine Odaklanmalıdır

Topluluk, pazaryeri ve sosyal özellikler V1 kapsamına alınmamalıdır.

Öncelik kullanıcının kendi örgü projelerini daha iyi yönetmesidir.

## 24. Dokümantasyon Kodla Birlikte Güncellenmelidir

Bir özellik, veri modeli, API veya ürün davranışı değiştiğinde ilgili dokümanlar da güncellenmelidir.

Dokümantasyon güncellenmeden iş tamamlanmış sayılmamalıdır.

## 25. Tek Bir Doğruluk Kaynağı Olmalıdır

Aynı bilgi farklı belgelerde çelişkili şekilde tanımlanmamalıdır.

Çelişki olması durumunda daha üst seviyeli ve daha güncel belge esas alınmalıdır.

Önerilen öncelik sırası:

1. PROJECT_PRINCIPLES.md
2. DECISIONS.md
3. Product Bible
4. PRD
5. Feature belgeleri
6. Teknik belgeler
7. Kod içi açıklamalar

## 26. Kararlar Kayıt Altına Alınmalıdır

Önemli ürün, mimari, güvenlik ve kapsam kararları `DECISIONS.md` içinde kaydedilmelidir.

## 27. Test Edilmeyen Özellik Tamamlanmış Sayılmaz

Her önemli özellik için uygun seviyede test yazılmalıdır.

Bunlar gerektiğinde şunları içerebilir:

* Unit test
* Widget test
* Integration test
* Güvenlik testi
* Manuel kabul testi

## 28. Hatalar Sessizce Gizlenmemelidir

Hatalar loglanmalı ve kullanıcıya anlaşılır geri bildirim verilmelidir.

Teknik hata mesajları doğrudan kullanıcıya gösterilmemelidir.

## 29. Geriye Dönük Uyumluluk Gözetilmelidir

Veri modeli ve uygulama güncellemeleri mevcut kullanıcı verilerini bozmamalıdır.

Gerekli durumlarda migration hazırlanmalıdır.

## 30. Bağımlılıklar Kontrollü Kullanılmalıdır

Her yeni paket için şu sorular cevaplanmalıdır:

* Gerçekten gerekli mi?
* Aktif olarak geliştiriliyor mu?
* Güvenli mi?
* Lisansı uygun mu?
* Uzun vadede bakım riski oluşturuyor mu?

## 31. Kod Okunabilir Olmalıdır

Kod yalnızca çalışmakla kalmamalı, anlaşılır ve sürdürülebilir olmalıdır.

Gereksiz soyutlama ve aşırı karmaşık mimariden kaçınılmalıdır.

## 32. Feature-First Mimari Korunmalıdır

Kod yapısı teknik katmanlar yerine mümkün olduğunca ürün özellikleri etrafında organize edilmelidir.

## 33. Güvenlik Kontrolleri Atlanmamalıdır

Önemli değişikliklerde uygun olan araçlar çalıştırılmalıdır:

* Gitleaks
* Semgrep
* Security Review
* Production Audit
* Santa Method

## 34. Kullanıcıya Yanıltıcı Bilgi Verilmemelidir

Tahmini değerler, AI çıktıları ve eksik veriler kesin sonuç gibi gösterilmemelidir.

## 35. Bildirimler Kullanıcıyı Rahatsız Etmemelidir

Bildirimler açık izinle ve anlamlı amaçlarla kullanılmalıdır.

Gereksiz bildirim gönderilmemelidir.

## 36. Premium Tasarımı Adil Olmalıdır

Kullanıcıyı zorlayan karanlık tasarım kalıpları kullanılmamalıdır.

Abonelik koşulları, fiyatlar ve iptal seçenekleri açıkça gösterilmelidir.

## 37. Kullanıcı Geri Bildirimi Ürün Kararlarına Dahil Edilmelidir

Yeni özellikler yalnızca varsayımlara göre değil, kullanıcı geri bildirimleri ve ürün verileriyle değerlendirilmelidir.

## 38. Küçük ve Kontrollü Değişiklikler Tercih Edilmelidir

Tek bir görev içinde gereksiz ve ilgisiz alanlar değiştirilmemelidir.

Her pull request mümkün olduğunca tek bir amaca hizmet etmelidir.

## 39. Belirsizlikler Açıkça Belirtilmelidir

Codex veya geliştirici bir konuda kesin bilgiye sahip değilse varsayımı gizlememelidir.

Varsayımlar belge veya pull request açıklamasında belirtilmelidir.

## 40. Bu İlkelerle Çelişen Değişiklikler Onay Gerektirir

Bu dosyada bulunan bir ilkeyle çelişen değişiklik doğrudan uygulanmamalıdır.

Önce karar kaydı oluşturulmalı ve ürün sahibi tarafından onaylanmalıdır.
