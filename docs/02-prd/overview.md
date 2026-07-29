# Product Requirements Document (PRD)

## Document Information

| Alan         | Değer                         |
| ------------ | ----------------------------- |
| Product      | Knitwise                      |
| Document     | Product Requirements Document |
| Version      | 1.0                           |
| Status       | Draft                         |
| Owner        | Product                       |
| Last Updated | 2026-07-29                    |

---

# 1. Purpose

Bu belge, Knitwise mobil uygulamasının ürün gereksinimlerini tanımlar.

Amacı; ürünün kapsamını, hedeflerini, kullanıcı ihtiyaçlarını, fonksiyonel gereksinimlerini ve teknik beklentilerini tek bir referans altında toplamak ve geliştiricilerin, tasarımcıların, test ekiplerinin ve yapay zekâ destekli geliştirme araçlarının aynı doğrultuda çalışmasını sağlamaktır.

Bu belge ürün geliştirme sürecindeki ana referans dokümanıdır.

---

# 2. Product Summary

Knitwise; örgü, amigurumi ve tığ işi ile ilgilenen kullanıcıların projelerini planlamasını, takip etmesini ve yönetmesini sağlayan akıllı bir mobil uygulamadır.

Uygulama yalnızca bir sıra sayacı değildir.

Kullanıcının;

* projelerini,
* ip envanterini,
* şiş ve tığlarını,
* kullandığı tarifleri,
* kalan malzemelerini,
* ilerleme durumunu

tek merkezden yönetmesini sağlar.

Bunun yanında sahip olduğu malzemelere göre uygun tarif önerileri sunarak klasik örgü uygulamalarından ayrılır.

---

# 3. Problem Statement

Bugün piyasadaki örgü uygulamalarının büyük bölümü aşağıdaki sorunlardan en az birine sahiptir:

* yalnızca sayaç görevi görmeleri
* eski kullanıcı deneyimi
* zayıf mobil tasarım
* envanter yönetiminin bulunmaması
* proje yönetiminin yetersiz olması
* malzemeye göre öneri sunmamaları
* kullanıcıların aynı bilgileri tekrar tekrar girmek zorunda kalması
* çok parçalı projeleri yönetememesi
* Türkçe desteğinin yetersiz olması

Bu nedenle kullanıcılar birden fazla uygulama veya fiziksel not kullanmak zorunda kalmaktadır.

Knitwise bu parçalı deneyimi tek uygulamada toplamayı hedefler.

---

# 4. Vision

Knitwise'ın uzun vadeli vizyonu;

örgü ile ilgili tüm süreçlerin yönetilebildiği dünyanın en kapsamlı mobil platformlarından biri olmaktır.

---

# 5. Product Goals

Birinci yıl hedefleri:

* Türkiye'de örgü alanındaki en kaliteli mobil uygulamalardan biri olmak
* Sadık kullanıcı kitlesi oluşturmak
* Premium abonelik modelini doğrulamak
* Kullanıcı davranışlarını ölçerek ürünü geliştirmek

Uzun vadeli hedefler:

* Global pazara açılmak
* Çoklu dil desteği
* AI destekli örgü asistanı
* Kamera destekli analiz
* Topluluk ve paylaşım özellikleri
* Dijital tarif ekosistemi

---

# 6. Target Users

Ana hedef kullanıcılar:

* Amigurumi yapanlar
* Örgü seven hobi kullanıcıları
* Deneyimli örgücüler
* Yeni başlayanlar
* El işi eğitmenleri
* Küçük ölçekli üreticiler

---

# 7. Success Metrics

Başarı aşağıdaki metriklerle ölçülecektir.

Ürün metrikleri

* Günlük aktif kullanıcı
* Haftalık aktif kullanıcı
* Aylık aktif kullanıcı
* Proje oluşturma oranı
* Tamamlanan proje oranı
* Ortalama uygulama kullanım süresi

Premium metrikleri

* Premium dönüşüm oranı
* Abonelik yenileme oranı

Kalite metrikleri

* Crash Free Sessions
* Senkronizasyon başarısı
* Hata oranı
* Ortalama açılış süresi

---

# 8. Product Scope

İlk sürüm aşağıdaki ana modülleri kapsar.

* Kullanıcı hesabı
* Proje yönetimi
* Akıllı sıra sayacı
* Çok parçalı proje yönetimi
* Tarif yönetimi
* Başlangıç tarifleri
* İp envanteri
* Şiş ve tığ envanteri
* Malzemeye göre öneriler
* Kalan ip önerileri
* Temel istatistikler
* Premium sistemi

---

# 9. Out of Scope (V1)

İlk sürüm kapsamında olmayacak özellikler:

* Sosyal ağ
* Pazaryeri
* Canlı sohbet
* Kamera ile ilmek tanıma
* AI tarafından otomatik tarif oluşturma
* Video paylaşım sistemi
* Masaüstü uygulaması

---

# 10. Functional Requirements

Ürün aşağıdaki temel yetenekleri sağlamalıdır.

* Kullanıcı hesap oluşturabilmelidir.
* Kullanıcı proje oluşturabilmelidir.
* Kullanıcı sınırsız proje yönetebilmelidir (Premium politikalarına göre değişebilir).
* Kullanıcı sıra sayacı kullanabilmelidir.
* Kullanıcı çok parçalı projeleri takip edebilmelidir.
* Kullanıcı ip envanterini yönetebilmelidir.
* Kullanıcı sahip olduğu malzemelere göre uygun projeleri görebilmelidir.
* Kullanıcı tariflerini kaydedebilmelidir.
* Kullanıcı ilerleme durumunu takip edebilmelidir.

---

# 11. Non-Functional Requirements

Uygulama;

* hızlı açılmalıdır,
* çevrimdışı temel işlevleri desteklemelidir,
* güvenli kimlik doğrulaması kullanmalıdır,
* kullanıcı verisini korumalıdır,
* erişilebilirlik standartlarını desteklemelidir,
* düşük donanımlı cihazlarda da akıcı çalışmalıdır.

---

# 12. Constraints

Ürün geliştirilirken aşağıdaki kararlar korunacaktır.

* Flutter kullanılacaktır.
* Supabase kullanılacaktır.
* Riverpod kullanılacaktır.
* Feature-first mimari kullanılacaktır.
* Markdown tabanlı dokümantasyon sürdürülecektir.

---

# 13. Risks

Başlıca riskler:

* AI maliyetleri
* Premium dönüşümünün beklenenden düşük olması
* Çok fazla özelliğin ilk sürüme eklenmeye çalışılması
* Offline senkronizasyon karmaşıklığı
* Büyük veri modellerinin yönetimi

---

# 14. Assumptions

Bu PRD hazırlanırken aşağıdaki varsayımlar kabul edilmiştir.

* İlk kullanıcı kitlesi Türkiye'de olacaktır.
* Kullanıcıların büyük kısmı mobil cihaz kullanacaktır.
* Premium model sürdürülebilir olacaktır.
* İnternet bağlantısı her zaman mevcut olmayabilir.

---

# 15. Dependencies

Ürünün başarısı aşağıdaki bağımlılıklardan etkilenmektedir.

* Flutter ekosistemi
* Supabase servisleri
* App Store
* Google Play
* Bildirim servisleri
* Analitik servisleri

---

# 16. Release Strategy

V1

MVP yayınlanacaktır.

V2

AI destekli özellikler ve gelişmiş analizler eklenecektir.

V3

Topluluk, pazaryeri ve kamera tabanlı özellikler eklenecektir.

---

# 17. Acceptance Criteria

Bu PRD aşağıdaki durumlarda tamamlanmış kabul edilir.

* Tüm temel ürün gereksinimleri tanımlanmıştır.
* Ürün kapsamı net olarak belirlenmiştir.
* V1 kapsamı açıkça ayrılmıştır.
* Ürün hedefleri ölçülebilir hale getirilmiştir.
* Sonraki PRD belgeleri için referans oluşturacak yeterli bilgi sağlanmıştır.

---

# 18. References

Bu belge aşağıdaki dokümanlarla birlikte değerlendirilmelidir.

* README.md
* PROJECT_PRINCIPLES.md
* DECISIONS.md
* AGENTS.md
* 01-product/*
* 03-features/*

