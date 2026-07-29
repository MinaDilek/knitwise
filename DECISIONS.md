
# DECISIONS.md

# Knitwise Decision Log

Bu belge Knitwise için alınan önemli ürün, teknik, güvenlik ve kapsam kararlarını kayıt altına alır.

## Karar Durumları

* `Proposed`: Önerildi, henüz onaylanmadı.
* `Approved`: Onaylandı ve geçerlidir.
* `Deprecated`: Artık önerilmemektedir.
* `Superseded`: Daha yeni bir karar tarafından değiştirilmiştir.

---

## PD-001 — İlk Hedef Pazar Türkiye

**Tür:** Product Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Bağlam

Türkçe örgü uygulamaları alanında kapsamlı ve modern mobil ürün sayısı sınırlıdır.

### Karar

Knitwise ilk olarak Türkiye pazarında ve Türkçe dil desteğiyle yayınlanacaktır.

### Gerekçe

* Türkçe örgü terminolojisine yönelik belirgin ihtiyaç
* İlk kullanıcı grubuna daha kolay erişim
* Daha kontrollü ürün doğrulaması
* Global pazara açılmadan önce ürün davranışlarını test etme fırsatı

### Sonuçlar

* İlk kullanıcı araştırmaları Türkiye merkezli yürütülecek.
* Türkçe içerik kalitesi öncelikli olacak.
* Teknik sistem global yerelleştirmeye hazır kurulacak.

### Alternatifler

* Doğrudan İngilizce ve global yayın
* Aynı anda çoklu dil yayını

### İlgili Belgeler

* `01-product/vision.md`
* `01-product/roadmap.md`
* `PROJECT_PRINCIPLES.md`

---

## PD-002 — V1'de Topluluk ve Pazaryeri Olmayacak

**Tür:** Product Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Bağlam

Topluluk ve pazaryeri özellikleri moderasyon, güvenlik, ödeme, içerik yönetimi ve operasyon yükü oluşturur.

### Karar

Topluluk ve pazaryeri özellikleri V1 kapsamına dahil edilmeyecektir.

### Gerekçe

V1'in temel amacı bireysel örgü proje yönetimi deneyimini doğrulamaktır.

### Sonuçlar

* Kullanıcı profili sosyal profil şeklinde tasarlanmayacak.
* Takipçi, yorum, beğeni ve mesajlaşma özellikleri eklenmeyecek.
* Pazaryeri V3 için değerlendirilecek.

### Alternatifler

* Basit topluluk akışı
* Sadece tarif paylaşımı
* Harici topluluk entegrasyonu

### İlgili Belgeler

* `01-product/roadmap.md`
* `02-prd/mvp-roadmap.md`

---

## PD-003 — Kamera ile İlmek Tanıma V1 Kapsamında Değil

**Tür:** Product Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Bağlam

Kamera ile ilmek tanıma yüksek doğruluk, veri seti ve gelişmiş görüntü işleme gerektirir.

### Karar

Kamera ile otomatik ilmek tanıma V1 kapsamına alınmayacaktır.

### Gerekçe

* Teknik risk yüksek
* Yanlış sonuç kullanıcı güvenini azaltabilir
* V1'in yayın süresini uzatabilir

### Sonuçlar

Bu özellik V3 AI Coach ve Camera AI çalışmaları kapsamında değerlendirilecektir.

### Alternatifler

* Manuel fotoğraf notu
* Fotoğraftan yalnızca proje kategorisi tahmini

### İlgili Belgeler

* `01-product/roadmap.md`
* `07-ai/`

---

## PD-004 — Akıllı Malzeme Asistanı Ana Farklılaştırıcıdır

**Tür:** Product Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Bağlam

Mevcut örgü uygulamalarının çoğu sayaç veya tarif yönetimine odaklanmaktadır.

### Karar

Kullanıcının sahip olduğu ip, şiş ve tığlara göre proje ve tarif önerme özelliği V1'in temel farklılaştırıcılarından biri olacaktır.

### Gerekçe

Bu özellik doğrudan şu kullanıcı sorusunu çözer:

> Elimdeki malzemelerle ne örebilirim?

### Sonuçlar

V1 içinde şu özellikler bulunmalıdır:

* İp envanteri
* Şiş ve tığ envanteri
* Malzeme uyumluluk puanı
* Eksik malzeme gösterimi
* Malzemeye uygun tarif filtresi
* Kalan ip önerileri
* Projeye malzeme ayırma

### Alternatifler

* Sadece manuel envanter
* Sadece tarif arama filtresi

### İlgili Belgeler

* `03-features/feature-007-yarn-inventory.md`
* `03-features/feature-008-hooks-needles.md`
* `03-features/feature-009-smart-recommendations.md`
* `03-features/feature-010-yarn-finisher.md`

---

## PD-005 — Temel Sıra Sayacı Ücretsiz Olacaktır

**Tür:** Product Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Temel sıra sayacı ücretsiz kullanıcılar tarafından kullanılabilecektir.

### Gerekçe

Sıra sayacı örgü uygulamasının temel kullanım alanlarından biridir. Bu özelliğin tamamen ücret arkasında olması kullanıcı değerini azaltır.

### Sonuçlar

Premium sürüm şu tür gelişmiş özellikleri sunabilir:

* Sınırsız sayaç
* Gelişmiş sesli komutlar
* Otomatik tekrar grupları
* Gelişmiş istatistikler
* Bulut senkronizasyonu

### İlgili Belgeler

* `03-features/feature-002-row-counter.md`
* `02-prd/premium-strategy.md`

---

## TD-001 — Flutter Kullanılacaktır

**Tür:** Technical Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Mobil uygulama Flutter ile geliştirilecektir.

### Gerekçe

* Tek kod tabanı
* iOS ve Android desteği
* Güçlü arayüz geliştirme araçları
* Geniş paket ekosistemi
* Hızlı MVP geliştirme

### Sonuçlar

* Ana dil Dart olacaktır.
* UI bileşenleri Flutter widget sistemiyle geliştirilecektir.
* Platforma özel kod yalnızca gerekli durumlarda kullanılacaktır.

### Alternatifler

* React Native
* Native Swift ve Kotlin
* Kotlin Multiplatform

---

## TD-002 — Backend için Supabase Kullanılacaktır

**Tür:** Technical Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Kimlik doğrulama, veritabanı, dosya depolama ve temel backend ihtiyaçları için Supabase kullanılacaktır.

### Gerekçe

* PostgreSQL tabanlı yapı
* Hızlı MVP geliştirme
* Authentication desteği
* Storage desteği
* Row Level Security
* Gerektiğinde özel backend genişletme imkânı

### Sonuçlar

* Veri erişimi RLS politikalarıyla korunacaktır.
* Supabase istemcisi doğrudan UI katmanında kullanılmayacaktır.
* Repository katmanı üzerinden erişilecektir.

### Alternatifler

* Firebase
* Appwrite
* Özel .NET backend
* Node.js backend

---

## TD-003 — State Management için Riverpod Kullanılacaktır

**Tür:** Technical Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Flutter uygulamasında state management için Riverpod kullanılacaktır.

### Gerekçe

* Test edilebilir yapı
* Dependency injection desteği
* Compile-time güvenliği
* Async state yönetimi
* Modüler mimariye uygunluk

### Alternatifler

* Bloc
* Provider
* GetX
* MobX

---

## TD-004 — Feature-First Architecture Kullanılacaktır

**Tür:** Technical Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Kod tabanı feature-first mimariyle düzenlenecektir.

Her özellik gerektiğinde kendi içinde şu katmanları barındırabilir:

* Presentation
* Application
* Domain
* Data

### Gerekçe

Özelliklerin bağımsız geliştirilmesini, test edilmesini ve bakımını kolaylaştırır.

### Sonuçlar

Ortak bileşenler yalnızca gerçekten birden fazla özellik tarafından kullanıldığında `core` veya `shared` alanına taşınacaktır.

---

## TD-005 — Dokümantasyon Markdown ve Git Tabanlıdır

**Tür:** Technical Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Ürün ve teknik dokümantasyon Markdown dosyalarıyla tutulacak ve Git üzerinden versiyonlanacaktır.

### Gerekçe

* Değişiklik geçmişi
* Kodla birlikte sürümleme
* Pull request üzerinden inceleme
* Codex tarafından kolay okunabilme

---

## SD-001 — Secret Bilgileri Repository İçinde Tutulmayacaktır

**Tür:** Security Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

API anahtarları, token'lar, şifreler, private key dosyaları ve diğer secret bilgileri repository içine yazılmayacaktır.

### Sonuçlar

* `.env` dosyaları Git'e eklenmeyecek.
* Örnek değerler `.env.example` içinde tutulabilecek.
* CI/CD secret yönetimi kullanılacaktır.
* Secret taraması için Gitleaks kullanılacaktır.

---

## SD-002 — Supabase Erişimi RLS ile Korunacaktır

**Tür:** Security Decision
**Durum:** Approved
**Tarih:** 2026-07-29

### Karar

Kullanıcı verisi içeren tüm Supabase tablolarında Row Level Security etkinleştirilecektir.

### Gerekçe

Kullanıcıların yalnızca kendi verilerine erişebilmesini sağlamak.

### Sonuçlar

* RLS politikası olmayan kullanıcı tablosu production ortamına alınmayacaktır.
* Service role key mobil uygulama içinde kullanılmayacaktır.
* RLS politikaları test edilecektir.

---

## Yeni Karar Şablonu

Yeni karar eklerken aşağıdaki şablonu kullan:

```md
## XX-000 — Karar Başlığı

**Tür:** Product Decision | Technical Decision | Security Decision  
**Durum:** Proposed | Approved | Deprecated | Superseded  
**Tarih:** YYYY-MM-DD

### Bağlam

Kararın alınmasına neden olan durum.

### Karar

Alınan karar.

### Gerekçe

Bu kararın neden seçildiği.

### Sonuçlar

Kararın olumlu ve olumsuz etkileri.

### Alternatifler

Değerlendirilen diğer seçenekler.

### İlgili Belgeler

- `ilgili/dosya.md`
```
