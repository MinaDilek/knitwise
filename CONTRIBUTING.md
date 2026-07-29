# CONTRIBUTING.md

# Contributing to Knitwise

Bu belge Knitwise repository'lerine katkıda bulunan geliştiriciler ve Codex için çalışma kurallarını tanımlar.

## 1. Çalışmaya Başlamadan Önce

Her görevden önce aşağıdaki belgeler okunmalıdır:

1. `README.md`
2. `AGENTS.md`
3. `PROJECT_PRINCIPLES.md`
4. `DECISIONS.md`
5. Görevle ilgili Product Bible, PRD ve feature belgeleri

Mevcut belgelerle çelişen değişiklik yapılmamalıdır.

## 2. Kapsam Kontrolü

Her branch ve pull request tek bir ana amaca odaklanmalıdır.

İlgisiz refactor, dosya taşıma veya özellik ekleme aynı görev içine dahil edilmemelidir.

## 3. Branch İsimlendirme

Aşağıdaki branch formatları kullanılmalıdır:

```text
docs/product-bible
docs/prd-overview
docs/feature-row-counter
feat/yarn-inventory
feat/smart-recommendations
fix/row-counter-state
refactor/project-repository
test/yarn-calculator
security/rls-policies
chore/update-dependencies
```

Branch isimleri:

* Küçük harfle yazılmalı
* Boşluk içermemeli
* Kelimeler tire ile ayrılmalı
* Görevin amacını açıkça ifade etmeli

## 4. Commit Mesajları

Conventional Commits standardı kullanılmalıdır.

### Kullanılabilecek türler

* `feat`: Yeni özellik
* `fix`: Hata düzeltmesi
* `docs`: Dokümantasyon değişikliği
* `refactor`: Davranışı değiştirmeyen kod düzenlemesi
* `test`: Test ekleme veya güncelleme
* `security`: Güvenlik değişikliği
* `perf`: Performans iyileştirmesi
* `chore`: Bakım ve araç değişiklikleri
* `ci`: CI/CD değişiklikleri

### Örnekler

```text
docs: add product principles
docs: define MVP roadmap
feat: add yarn inventory domain model
fix: prevent row counter state loss
security: add Supabase RLS policies
test: add yarn calculator unit tests
refactor: separate project repository interface
```

## 5. Pull Request Kuralları

Her pull request şu bilgileri içermelidir:

* Değişikliğin amacı
* Yapılan değişikliklerin özeti
* İlgili belge veya karar kaydı
* Test sonucu
* Bilinen riskler
* Ekran değişikliği varsa ekran görüntüsü
* Migration varsa geri dönüş planı

## 6. Pull Request Boyutu

Pull request'ler mümkün olduğunca küçük tutulmalıdır.

Büyük özellikler şu şekilde bölünebilir:

1. Veri modeli
2. Repository
3. State yönetimi
4. UI
5. Testler
6. Dokümantasyon

## 7. Dokümantasyon Zorunluluğu

Aşağıdakilerden biri değişirse ilgili Markdown belgeleri de güncellenmelidir:

* Ürün davranışı
* Kullanıcı akışı
* Veri modeli
* API
* Premium sınırları
* Güvenlik politikası
* Mimari karar
* Feature kapsamı

Dokümantasyon güncellenmeden pull request tamamlanmış sayılmaz.

## 8. Markdown Standartları

Her belge:

* Tek bir H1 başlığıyla başlamalıdır.
* Başlık hiyerarşisini atlamamalıdır.
* Açık ve kısa paragraflar kullanmalıdır.
* Teknik terimleri ilk kullanımda açıklamalıdır.
* İlgili dosyalara relative link vermelidir.
* Belirsiz ifadelerden kaçınmalıdır.

Kaçınılması gereken ifadeler:

* Gerekirse yapılabilir.
* Uygun şekilde yönetilir.
* Sistem bunu halleder.
* Kullanıcı dostu olacaktır.

Bunun yerine ölçülebilir davranış yazılmalıdır.

## 9. Belge Durumları

Gerekli belgelerde aşağıdaki durumlar kullanılabilir:

* `Draft`
* `In Review`
* `Approved`
* `Deprecated`

## 10. Front Matter

Uzun ve önemli dokümanlarda aşağıdaki yapı kullanılabilir:

```yaml
---
title: Smart Recommendations
status: Draft
owner: Product
version: 1.0
last_updated: 2026-07-29
---
```

## 11. Kod Kalitesi

Kod:

* Okunabilir olmalı
* Tek sorumluluk ilkesine uymalı
* Gereksiz tekrar içermemeli
* Test edilebilir olmalı
* UI ile veri erişimini doğrudan bağlamamalı
* Feature-first yapıyı korumalı

## 12. Test Kuralları

Değişikliğe uygun testler eklenmelidir.

### Unit Test

* Hesaplamalar
* Domain kuralları
* Dönüşümler
* Repository davranışları

### Widget Test

* Kritik ekran davranışları
* Form doğrulamaları
* Sayaç etkileşimleri

### Integration Test

* Kullanıcı girişi
* Proje oluşturma
* Sayaç ilerlemesi
* Envanter tüketimi
* Senkronizasyon

## 13. Güvenlik Kuralları

Hiçbir zaman commit edilmemesi gerekenler:

* API key
* Access token
* Refresh token
* Şifre
* Private key
* Service role key
* Production `.env`
* Gerçek kullanıcı verisi

Güvenlik açısından uygun durumlarda şu kontroller çalıştırılmalıdır:

* Gitleaks
* Semgrep
* Security Review
* Production Audit
* Santa Method

## 14. Supabase Kuralları

* Kullanıcı verisi içeren tablolarda RLS açık olmalıdır.
* Mobil uygulama service role key kullanmamalıdır.
* Migration dosyaları Git içinde tutulmalıdır.
* Veritabanı değişiklikleri belgelenmelidir.
* RLS politikaları test edilmelidir.

## 15. Bağımlılık Ekleme

Yeni paket eklemeden önce şu kriterler değerlendirilmelidir:

* Paket aktif olarak geliştiriliyor mu?
* Son güncelleme tarihi makul mü?
* Lisansı uygun mu?
* Bilinen güvenlik açığı var mı?
* Aynı iş mevcut bağımlılıklarla yapılabilir mi?
* Paket uygulama boyutunu gereksiz artırıyor mu?

## 16. Kırık Link Kontrolü

Dokümantasyon değişikliklerinde relative linkler kontrol edilmelidir.

Silinen veya taşınan dosyalara verilen bağlantılar güncellenmelidir.

## 17. Codex Çalışma Kuralları

Codex:

* Önce ilgili belgeleri okumalıdır.
* Kapsam dışı dosyaları değiştirmemelidir.
* Belirsiz varsayımları belirtmelidir.
* Oluşturduğu dosyaları özetlemelidir.
* Test sonucunu açıkça bildirmelidir.
* Çalıştırmadığı testleri çalıştırılmış gibi göstermemelidir.
* Güvenlik bulgularını gizlememelidir.

## 18. Review Checklist

Pull request onaylanmadan önce kontrol edilmelidir:

* [ ] Değişiklik görev kapsamına uygun mu?
* [ ] PROJECT_PRINCIPLES.md ile uyumlu mu?
* [ ] DECISIONS.md ile çelişiyor mu?
* [ ] İlgili dokümantasyon güncellendi mi?
* [ ] Testler eklendi mi?
* [ ] Testler başarılı mı?
* [ ] Secret veya hassas veri var mı?
* [ ] Güvenlik etkisi değerlendirildi mi?
* [ ] Erişilebilirlik etkisi değerlendirildi mi?
* [ ] Offline davranış değerlendirildi mi?
* [ ] Migration gerekiyor mu?
* [ ] Geri dönüş planı var mı?
* [ ] Kırık link bulunuyor mu?

---

# CHANGELOG.md

# Changelog

Knitwise projesindeki önemli değişiklikler bu dosyada kayıt altına alınır.

Bu dosya Keep a Changelog yaklaşımını kullanır.

## [Unreleased]

### Added

* Repository dokümantasyon klasör yapısı oluşturuldu.
* `README.md` eklendi.
* `AGENTS.md` eklendi.
* `PROJECT_PRINCIPLES.md` eklendi.
* `DECISIONS.md` karar kayıt sistemi oluşturuldu.
* `CONTRIBUTING.md` katkı standartları oluşturuldu.
* Product Bible belgeleri `01-product` klasörüne eklendi.
* Ürün vizyonu, misyonu ve benzersiz değer önerisi belgelendi.
* Ürün yol haritası belgelendi.
* Kullanıcı personaları ve kullanıcı yolculukları tanımlandı.
* Başarı metrikleri tanımlandı.

### Changed

* Dokümantasyon geliştirme süreci Git ve Markdown tabanlı hale getirildi.
* Codex'in repository üzerinde çalışması için temel yönetişim kuralları tanımlandı.

### Fixed

* Henüz kayıtlı bir düzeltme bulunmamaktadır.

### Security

* Secret bilgilerinin repository içinde tutulmaması kararı kaydedildi.
* Supabase tablolarında Row Level Security kullanılması kararı kaydedildi.
* Güvenlik kontrollerinde Gitleaks ve Semgrep kullanılması ilkesi tanımlandı.

---

## Changelog Güncelleme Kuralları

Yeni değişiklikler önce `[Unreleased]` bölümüne eklenmelidir.

Kullanılabilecek başlıklar:

* `Added`
* `Changed`
* `Deprecated`
* `Removed`
* `Fixed`
* `Security`

Yeni sürüm yayınlandığında örnek format:

```md
## [1.0.0] - 2026-12-01

### Added

- İlk production sürümü yayınlandı.
```

