# Project Management — User Stories

## Document Information

| Alan               | Değer              |
| ------------------ | ------------------ |
| Product            | Knitwise           |
| Feature ID         | FEATURE-001        |
| Feature Name       | Project Management |
| Requirement Prefix | PM                 |
| Document           | User Stories       |
| Version            | 1.0                |
| Status             | Draft              |
| Last Updated       | 2026-07-29         |

---

# 1. Purpose

Bu belge, Project Management özelliğine ait kullanıcı hikâyelerini tanımlar.

Her kullanıcı hikâyesi:

* Kullanıcı niyetini
* Kullanıcının elde etmek istediği değeri
* Ana kullanım senaryosunu
* Alternatif senaryoları
* Hata senaryolarını
* İlgili gereksinimleri
* İlgili kabul kriterlerini

tanımlamak için kullanılır.

Bu belge ekran tasarımını veya teknik implementasyonu tek başına belirlemez.

---

# 2. User Story Index

| ID        | Story                               | Priority | Release   |
| --------- | ----------------------------------- | -------: | --------- |
| PM-US-001 | Hızlı proje oluşturma               |     Must | V1        |
| PM-US-002 | Ayrıntılı proje oluşturma           |     Must | V1        |
| PM-US-003 | Proje listesini görüntüleme         |     Must | V1        |
| PM-US-004 | Proje detayını görüntüleme          |     Must | V1        |
| PM-US-005 | Proje bilgilerini düzenleme         |     Must | V1        |
| PM-US-006 | Proje durumunu değiştirme           |     Must | V1        |
| PM-US-007 | Projeyi duraklatma ve devam ettirme |     Must | V1        |
| PM-US-008 | Projeyi tamamlama                   |     Must | V1        |
| PM-US-009 | Projeyi arşivleme                   |     Must | V1        |
| PM-US-010 | Projeyi silme                       |     Must | V1        |
| PM-US-011 | Proje arama                         |     Must | V1        |
| PM-US-012 | Projeleri filtreleme                |   Should | V1        |
| PM-US-013 | Projeleri sıralama                  |   Should | V1        |
| PM-US-014 | Projeye tarif bağlama               |     Must | V1        |
| PM-US-015 | Projeye ip bağlama                  |     Must | V1        |
| PM-US-016 | Projeye şiş veya tığ bağlama        |     Must | V1        |
| PM-US-017 | Projeden sayaca ulaşma              |     Must | V1        |
| PM-US-018 | Projeden parça takibine ulaşma      |     Must | V1        |
| PM-US-019 | Proje ilerlemesini görüntüleme      |     Must | V1        |
| PM-US-020 | Proje kapak görseli ekleme          |   Should | V1        |
| PM-US-021 | Proje notu ekleme                   |     Must | V1        |
| PM-US-022 | Offline proje oluşturma             |     Must | V1        |
| PM-US-023 | Offline proje düzenleme             |     Must | V1        |
| PM-US-024 | Aktif proje limitini yönetme        |     Must | V1        |
| PM-US-025 | Tamamlanan projeyi yeniden açma     |   Should | V1        |
| PM-US-026 | Arşivlenen projeyi geri alma        |     Must | V1        |
| PM-US-027 | Boş proje listesinden başlama       |     Must | V1        |
| PM-US-028 | Kaydedilmemiş değişiklikleri koruma |     Must | V1        |
| PM-US-029 | Proje verisini dışa aktarma         |   Should | V1 / V1.x |
| PM-US-030 | Projeleri farklı cihazda görme      |    Could | V2        |

---

# 3. Project Creation Stories

## PM-US-001 — Hızlı Proje Oluşturma

**As a:** Örgü kullanıcısı

**I want to:** Yalnızca proje adı girerek hızlıca yeni proje oluşturmak

**So that:** Ayrıntıları daha sonra doldurabilsem bile çalışmaya hemen başlayabileyim.

### Preconditions

* Kullanıcı uygulamada oturum açmıştır veya local kullanım yetkisine sahiptir.
* Kullanıcı aktif proje limitini aşmamıştır.
* Proje oluşturma ekranına erişebilir.

### Main Scenario

1. Kullanıcı proje oluşturma aksiyonuna dokunur.
2. Sistem hızlı oluşturma ekranını açar.
3. Kullanıcı proje adını girer.
4. Kullanıcı oluştur aksiyonuna dokunur.
5. Sistem girdiyi doğrular.
6. Sistem projeyi oluşturur.
7. Sistem projeyi varsayılan durumla kaydeder.
8. Sistem kullanıcıyı proje detay ekranına yönlendirir.

### Alternative Scenarios

* Kullanıcı proje tekniğini de seçebilir.
* Kullanıcı proje kategorisini de seçebilir.
* Kullanıcı daha ayrıntılı forma geçebilir.
* Kullanıcı projeyi `draft` olarak kaydedebilir.

### Failure Scenarios

* Proje adı boş bırakılmıştır.
* Proje adı yalnızca boşluklardan oluşur.
* Ücretsiz aktif proje limiti dolmuştur.
* Local kayıt başarısız olmuştur.
* Uygulama kayıt sırasında kapanmıştır.

### Expected Outcome

* Proje benzersiz kimlikle kaydedilir.
* Kullanıcı veri kaybı yaşamaz.
* Proje liste ekranında görünür.
* Gerekli analytics event'i gönderilir.

### Related Requirements

* `PM-FR-001`
* `PM-FR-002`
* `PM-FR-003`
* `PM-FR-004`

### Related Acceptance Criteria

* `PM-AC-001`
* `PM-AC-002`
* `PM-AC-003`

---

## PM-US-002 — Ayrıntılı Proje Oluşturma

**As a:** Projesini ayrıntılı takip etmek isteyen kullanıcı

**I want to:** Proje oluştururken teknik, kategori, tarih, tarif ve malzeme bilgilerini eklemek

**So that:** Projeye başladığım andan itibaren tüm bilgileri düzenli tutabileyim.

### Preconditions

* Kullanıcı proje oluşturma yetkisine sahiptir.
* Gerekli bağlı özellikler kullanılabilir durumdadır.

### Main Scenario

1. Kullanıcı yeni proje oluşturur.
2. Kullanıcı ayrıntılı formu açar.
3. Kullanıcı proje adını girer.
4. Kullanıcı teknik seçer.
5. Kullanıcı kategori seçer.
6. Kullanıcı başlangıç tarihi belirler.
7. Kullanıcı isterse hedef tamamlanma tarihi girer.
8. Kullanıcı isterse tarif bağlar.
9. Kullanıcı isterse ip veya araç bağlar.
10. Kullanıcı projeyi kaydeder.
11. Sistem tüm alanları doğrular.
12. Sistem proje ve ilişkili kayıtları atomik biçimde kaydeder.

### Alternative Scenarios

* Kullanıcı bazı isteğe bağlı alanları boş bırakabilir.
* Kullanıcı kapak görselini daha sonra ekleyebilir.
* Kullanıcı bağlı tarif olmadan devam edebilir.
* Kullanıcı `draft` olarak kaydedebilir.

### Failure Scenarios

* Hedef tarih başlangıç tarihinden öncedir.
* Seçilen tarif silinmiştir.
* Bağlanmak istenen malzeme artık mevcut değildir.
* Fotoğraf yükleme başarısız olmuştur.
* Ana proje kaydı başarılı, ilişkili kayıt başarısız olmuştur.

### Expected Outcome

* Proje temel verileri kaydedilir.
* Başarısız ilişkili kayıtlar kullanıcıya açıkça gösterilir.
* Ana proje verisi gereksiz yere kaybedilmez.
* Kısmi başarısızlıklar kurtarılabilir olur.

### Related Requirements

* `PM-FR-005`
* `PM-FR-006`
* `PM-FR-007`
* `PM-FR-008`

### Related Acceptance Criteria

* `PM-AC-004`
* `PM-AC-005`
* `PM-AC-006`

---

# 4. Project Discovery Stories

## PM-US-003 — Proje Listesini Görüntüleme

**As a:** Birden fazla projesi olan kullanıcı

**I want to:** Projelerimi durumlarına göre tek listede görmek

**So that:** Hangi proje üzerinde çalışacağımı kolayca seçebileyim.

### Preconditions

* Kullanıcının en az bir proje kaydı vardır.

### Main Scenario

1. Kullanıcı projeler alanını açar.
2. Sistem varsayılan proje grubunu yükler.
3. Sistem aktif ve duraklatılmış projeleri gösterir.
4. Her kart proje adı, durum ve ilerleme bilgisini gösterir.
5. Kullanıcı bir proje kartına dokunur.
6. Sistem proje detay ekranını açar.

### Alternative Scenarios

* Kullanıcı tamamlanan projeleri seçer.
* Kullanıcı arşivlenen projeleri seçer.
* Kullanıcı tüm projeleri görüntüler.
* Kullanıcı liste veya kart görünümü arasında geçiş yapabilir.

### Failure Scenarios

* Proje listesi local storage'dan okunamaz.
* Remote veri yüklenemez.
* Bazı proje ilişkileri eksiktir.
* Kapak görseli yüklenemez.

### Expected Outcome

* Temel proje bilgileri ilişkili veriler yüklenmese bile gösterilir.
* Kapak görseli yoksa placeholder kullanılır.
* Hata durumunda kullanıcı tekrar deneyebilir.

### Related Requirements

* `PM-FR-009`
* `PM-FR-010`
* `PM-FR-011`

### Related Acceptance Criteria

* `PM-AC-007`
* `PM-AC-008`

---

## PM-US-004 — Proje Detayını Görüntüleme

**As a:** Aktif projesine devam etmek isteyen kullanıcı

**I want to:** Projeye ait tüm önemli bilgileri tek bir detay alanında görmek

**So that:** Tarif, sayaç, malzeme ve ilerleme bilgilerine hızlıca ulaşabileyim.

### Preconditions

* Kullanıcı ilgili projenin sahibidir.
* Proje silinmemiştir.

### Main Scenario

1. Kullanıcı proje kartını seçer.
2. Sistem proje temel bilgilerini açar.
3. Sistem proje durumunu gösterir.
4. Sistem ilerleme özetini gösterir.
5. Sistem bağlı tarif ve malzemeleri gösterir.
6. Sistem sayaç ve parça takibi girişlerini gösterir.
7. Kullanıcı istediği alt özelliğe gider.

### Alternative Scenarios

* Projede tarif yoktur.
* Projede malzeme yoktur.
* Projede sayaç yoktur.
* Proje tamamlanmıştır.
* Proje arşivlenmiştir.

### Failure Scenarios

* Proje kaydı bulunamaz.
* Kullanıcı proje sahibi değildir.
* Bağlı bir kayıt silinmiştir.
* Remote kayıt ile local kayıt arasında uyuşmazlık vardır.

### Expected Outcome

* Eksik alt modüller ekranın tamamını bozmaz.
* Kullanıcının erişim yetkisi yoksa veri gösterilmez.
* Proje bulunamazsa güvenli hata ekranı açılır.

### Related Requirements

* `PM-FR-012`
* `PM-FR-013`

### Related Acceptance Criteria

* `PM-AC-009`
* `PM-AC-010`

---

# 5. Project Editing Stories

## PM-US-005 — Proje Bilgilerini Düzenleme

**As a:** Proje bilgileri zaman içinde değişen kullanıcı

**I want to:** Proje adını, tarihlerini, kategorisini ve açıklamasını düzenlemek

**So that:** Proje kaydı güncel durumu doğru yansıtsın.

### Preconditions

* Kullanıcı projenin sahibidir.
* Proje kalıcı olarak silinmemiştir.

### Main Scenario

1. Kullanıcı proje detay ekranını açar.
2. Kullanıcı düzenleme aksiyonuna dokunur.
3. Sistem mevcut alanları forma yükler.
4. Kullanıcı bir veya daha fazla alanı değiştirir.
5. Kullanıcı kaydeder.
6. Sistem alanları doğrular.
7. Sistem değişiklikleri kaydeder.
8. Sistem güncellenmiş proje detayını gösterir.

### Alternative Scenarios

* Kullanıcı yalnızca proje adını değiştirir.
* Kullanıcı kapak görselini kaldırır.
* Kullanıcı hedef tarihi temizler.
* Değişiklikler otomatik olarak kaydedilir.
* Kullanıcı değişiklikleri iptal eder.

### Failure Scenarios

* Girdi validation'dan geçmez.
* Local kayıt başarısız olur.
* Remote sync başarısız olur.
* Proje başka bir işlem sırasında silinmiştir.
* Kullanıcı yetkisi değişmiştir.

### Expected Outcome

* Hatalı veri kaydedilmez.
* Kullanıcının geçerli değişiklikleri mümkün olduğunca korunur.
* Remote hata local değişikliği yok etmez.
* Güncelleme zamanı yenilenir.

### Related Requirements

* `PM-FR-014`
* `PM-FR-015`
* `PM-FR-016`

### Related Acceptance Criteria

* `PM-AC-011`
* `PM-AC-012`

---

## PM-US-021 — Proje Notu Ekleme

**As a:** Proje sırasında önemli ayrıntıları unutmamak isteyen kullanıcı

**I want to:** Projeye serbest metin notu eklemek

**So that:** Değişiklikleri, ölçüleri ve hatırlatmaları proje ile birlikte saklayabileyim.

### Preconditions

* Kullanıcı projenin sahibidir.

### Main Scenario

1. Kullanıcı proje notu alanını açar.
2. Kullanıcı metin girer.
3. Sistem metni local olarak kaydeder.
4. Kullanıcı proje ekranından çıkar.
5. Kullanıcı projeye geri döner.
6. Sistem kaydedilmiş notu gösterir.

### Alternative Scenarios

* Kullanıcı mevcut notu düzenler.
* Kullanıcı notu tamamen siler.
* Kullanıcı offline olarak not ekler.

### Failure Scenarios

* Not maksimum uzunluğu aşar.
* Kayıt işlemi başarısız olur.
* Uygulama yazma sırasında kapanır.

### Expected Outcome

* Not mümkün olduğunca otomatik kaydedilir.
* Not analytics veya log sistemine gönderilmez.
* Başarısız kayıt kullanıcıya bildirilir.

### Related Requirements

* `PM-FR-017`
* `PM-NFR-001`

### Related Acceptance Criteria

* `PM-AC-013`

---

# 6. Project Status Stories

## PM-US-006 — Proje Durumunu Değiştirme

**As a:** Projelerini çalışma durumuna göre düzenleyen kullanıcı

**I want to:** Projenin durumunu değiştirmek

**So that:** Aktif, duraklatılmış, tamamlanmış ve arşivlenmiş projeleri ayırabileyim.

### Preconditions

* Kullanıcı projenin sahibidir.
* İstenen durum geçişi iş kurallarına uygundur.

### Main Scenario

1. Kullanıcı proje durum alanını açar.
2. Sistem izin verilen durumları gösterir.
3. Kullanıcı yeni durumu seçer.
4. Sistem geçişi doğrular.
5. Sistem proje durumunu günceller.
6. Sistem ilgili tarih alanlarını günceller.
7. Sistem proje listesini yeniler.

### Alternative Scenarios

* Kullanıcı aktif projeyi duraklatır.
* Kullanıcı duraklatılmış projeyi aktif yapar.
* Kullanıcı projeyi tamamlar.
* Kullanıcı tamamlanan projeyi yeniden açar.
* Kullanıcı projeyi arşivler.

### Failure Scenarios

* Geçersiz durum değeri gönderilir.
* Geçiş iş kurallarına aykırıdır.
* Proje başka cihazda değiştirilmiştir.
* Kullanıcı yetkili değildir.

### Expected Outcome

* Proje yalnızca izin verilen duruma geçer.
* Durum geçmişi gerekirse kaydedilir.
* İlgili tarih alanları tutarlı kalır.

### Related Requirements

* `PM-FR-018`
* `PM-FR-019`

### Related Acceptance Criteria

* `PM-AC-014`
* `PM-AC-015`

---

## PM-US-007 — Projeyi Duraklatma ve Devam Ettirme

**As a:** Bir projeye ara vermek isteyen kullanıcı

**I want to:** Projeyi duraklatıp daha sonra tekrar aktif hale getirmek

**So that:** Yarım bıraktığım projeyi tamamlanmış gibi işaretlemeden listemde düzenli tutabileyim.

### Preconditions

* Proje `active` veya `paused` durumundadır.

### Main Scenario

1. Kullanıcı aktif projeyi açar.
2. Kullanıcı duraklat aksiyonunu seçer.
3. Sistem projeyi `paused` durumuna geçirir.
4. Projenin sayaç, parça ve not verileri korunur.
5. Kullanıcı daha sonra projeyi açar.
6. Kullanıcı devam et aksiyonunu seçer.
7. Sistem projeyi `active` durumuna getirir.

### Alternative Scenarios

* Kullanıcı duraklatılmış projeyi doğrudan proje listesinden devam ettirir.
* Kullanıcı duraklatma sırasında isteğe bağlı not ekler.

### Failure Scenarios

* Proje zaten tamamlanmıştır.
* Proje silinmiştir.
* Kayıt başarısız olmuştur.

### Expected Outcome

* Proje ilerlemesi değişmeden korunur.
* Duraklatma tamamlanma tarihi oluşturmaz.
* Kullanıcı kaldığı yerden devam edebilir.

### Related Requirements

* `PM-FR-020`

### Related Acceptance Criteria

* `PM-AC-016`

---

## PM-US-008 — Projeyi Tamamlama

**As a:** Projesini bitiren kullanıcı

**I want to:** Projeyi tamamlandı olarak işaretlemek

**So that:** Bitirdiğim projeyi kayıt altına alıp aktif çalışma listemden çıkarabileyim.

### Preconditions

* Proje kalıcı olarak silinmemiştir.
* Kullanıcı projenin sahibidir.

### Main Scenario

1. Kullanıcı projeyi açar.
2. Kullanıcı tamamla aksiyonuna dokunur.
3. Sistem onay ister.
4. Kullanıcı işlemi onaylar.
5. Sistem proje durumunu `completed` yapar.
6. Sistem tamamlanma tarihini kaydeder.
7. Proje aktif listeden kaldırılır.
8. Proje tamamlananlar listesine eklenir.

### Alternative Scenarios

* Kullanıcı tamamlanma tarihini değiştirir.
* Kullanıcı projeyi daha sonra yeniden aktif hale getirir.
* Sistem proje istatistiklerini günceller.

### Failure Scenarios

* Tamamlanma tarihi başlangıç tarihinden öncedir.
* Proje kaydı güncellenemez.
* Kullanıcı işlemi iptal eder.

### Expected Outcome

* Proje verileri korunur.
* Proje salt okunur hale gelmek zorunda değildir.
* İlgili istatistikler güncellenebilir.

### Related Requirements

* `PM-FR-021`
* `PM-FR-022`

### Related Acceptance Criteria

* `PM-AC-017`
* `PM-AC-018`

---

## PM-US-009 — Projeyi Arşivleme

**As a:** Ana listemde görmek istemediğim bir projeye sahip kullanıcı

**I want to:** Projeyi silmeden arşivlemek

**So that:** Verilerini korurken proje listemi sade tutabileyim.

### Preconditions

* Kullanıcı projenin sahibidir.
* Proje silinmemiştir.

### Main Scenario

1. Kullanıcı proje aksiyonlarını açar.
2. Kullanıcı arşivle seçeneğini seçer.
3. Sistem arşivlemenin veri silmeyeceğini açıklar.
4. Kullanıcı onaylar.
5. Sistem proje durumunu `archived` yapar.
6. Sistem arşivlenme zamanını kaydeder.
7. Proje varsayılan listeden kaldırılır.
8. Proje arşiv ekranında görünür.

### Alternative Scenarios

* Kullanıcı aktif projeyi arşivler.
* Kullanıcı tamamlanan projeyi arşivler.
* Kullanıcı işlemi iptal eder.

### Failure Scenarios

* Kayıt başarısız olur.
* Proje zaten arşivlenmiştir.
* Kullanıcı yetkili değildir.

### Expected Outcome

* Tüm proje ilişkileri korunur.
* Arşivleme aktif proje limitini etkileyebilir.
* Kullanıcı projeyi daha sonra geri alabilir.

### Related Requirements

* `PM-FR-023`

### Related Acceptance Criteria

* `PM-AC-019`

---

## PM-US-026 — Arşivlenen Projeyi Geri Alma

**As a:** Daha önce arşivlediği projeye geri dönmek isteyen kullanıcı

**I want to:** Arşivlenen projeyi yeniden aktif veya tamamlanmış duruma almak

**So that:** Proje verilerini yeniden kullanabileyim.

### Preconditions

* Proje `archived` durumundadır.
* Kullanıcı projenin sahibidir.

### Main Scenario

1. Kullanıcı arşivlenmiş projeler ekranını açar.
2. Kullanıcı projeyi seçer.
3. Kullanıcı arşivden çıkar aksiyonuna dokunur.
4. Sistem hedef durumu belirler veya kullanıcıya sorar.
5. Sistem aktif proje limitini kontrol eder.
6. Sistem projeyi uygun duruma getirir.
7. Proje ilgili listede görünür.

### Alternative Scenarios

* Proje tamamlanmış duruma geri alınır.
* Proje aktif duruma alınır.
* Limit doluysa proje `paused` veya `draft` yapılabilir.

### Failure Scenarios

* Aktif proje limiti doludur.
* Proje bulunamaz.
* Kayıt başarısız olur.

### Expected Outcome

* Arşivlenme tarihi temizlenir veya geçmişte tutulur.
* Proje verileri değişmeden korunur.
* Limit kuralları uygulanır.

### Related Requirements

* `PM-FR-024`

### Related Acceptance Criteria

* `PM-AC-020`

---

## PM-US-025 — Tamamlanan Projeyi Yeniden Açma

**As a:** Tamamlandı olarak işaretlediği projede tekrar çalışması gereken kullanıcı

**I want to:** Tamamlanan projeyi yeniden aktif hale getirmek

**So that:** Düzeltme veya ekleme yapmaya devam edebileyim.

### Preconditions

* Proje `completed` durumundadır.
* Aktif proje limiti izin verir.

### Main Scenario

1. Kullanıcı tamamlanan projeyi açar.
2. Kullanıcı yeniden aç aksiyonuna dokunur.
3. Sistem aktif proje limitini kontrol eder.
4. Sistem kullanıcıdan onay alır.
5. Sistem proje durumunu `active` yapar.
6. Sistem tamamlanma tarihini temizler veya geçmiş kaydı olarak tutar.
7. Proje aktif listede görünür.

### Failure Scenarios

* Aktif proje limiti doludur.
* Kayıt başarısızdır.
* Proje silinmiştir.

### Expected Outcome

* Sayaç ve parça ilerlemesi korunur.
* Kullanıcı projeye devam edebilir.
* Önceki tamamlanma bilgisi audit geçmişinde tutulabilir.

### Related Requirements

* `PM-FR-025`

### Related Acceptance Criteria

* `PM-AC-021`

---

# 7. Delete Story

## PM-US-010 — Projeyi Silme

**As a:** Artık saklamak istemediği bir projeye sahip kullanıcı

**I want to:** Projeyi silmek

**So that:** Gereksiz veya yanlış oluşturulmuş kayıtları hesabımdan kaldırabileyim.

### Preconditions

* Kullanıcı projenin sahibidir.
* Proje mevcut durumdadır.

### Main Scenario

1. Kullanıcı proje aksiyonlarını açar.
2. Kullanıcı sil seçeneğini seçer.
3. Sistem silme işleminin etkisini açıklar.
4. Sistem arşivleme alternatifini gösterebilir.
5. Kullanıcı silmeyi onaylar.
6. Sistem projeyi soft delete durumuna geçirir.
7. Sistem projeyi normal listelerden kaldırır.
8. İlişkili veriler erişilemez hale gelir ancak kurtarma süresince korunabilir.

### Alternative Scenarios

* Kullanıcı arşivlemeyi seçer.
* Kullanıcı silme işlemini iptal eder.
* Kullanıcı kurtarma süresi içinde projeyi geri yükler.

### Failure Scenarios

* Kullanıcı yanlış projeyi silmeye çalışır.
* Kayıt işlemi yarıda kalır.
* İlişkili kayıtların bir kısmı güncellenemez.
* Kullanıcı yetkili değildir.

### Expected Outcome

* Silinen proje normal sorgularda görünmez.
* Başka kullanıcılar veriye erişemez.
* İşlem kullanıcıya açıkça doğrulanır.
* İlgili dosyaların kalıcı silme zamanı ayrı yönetilebilir.

### Related Requirements

* `PM-FR-026`
* `PM-FR-027`

### Related Acceptance Criteria

* `PM-AC-022`
* `PM-AC-023`

---

# 8. Search, Filter and Sort Stories

## PM-US-011 — Proje Arama

**As a:** Çok sayıda projeye sahip kullanıcı

**I want to:** Proje adına göre arama yapmak

**So that:** Aradığım projeyi hızlıca bulabileyim.

### Preconditions

* Kullanıcının proje listesi açıktır.

### Main Scenario

1. Kullanıcı arama alanını açar.
2. Kullanıcı metin girer.
3. Sistem kullanıcıya ait projeleri filtreler.
4. Sistem eşleşen projeleri gösterir.
5. Kullanıcı sonucu seçer.

### Alternative Scenarios

* Arama büyük-küçük harf duyarsız çalışır.
* Türkçe karakterler doğru desteklenir.
* Kullanıcı aramayı temizler.

### Failure Scenarios

* Sonuç bulunmaz.
* Arama index'i kullanılamaz.
* Çok uzun arama metni girilir.

### Expected Outcome

* Sonuç bulunmazsa boş durum gösterilir.
* Kullanıcının özel proje metni analytics'e gönderilmez.
* Arama yalnızca kullanıcının erişebildiği kayıtlarda yapılır.

### Related Requirements

* `PM-FR-028`

### Related Acceptance Criteria

* `PM-AC-024`

---

## PM-US-012 — Projeleri Filtreleme

**As a:** Projelerini durum veya kategoriye göre ayırmak isteyen kullanıcı

**I want to:** Proje listesini filtrelemek

**So that:** Yalnızca ilgilendiğim projeleri görebileyim.

### Preconditions

* Kullanıcının proje listesi açıktır.

### Main Scenario

1. Kullanıcı filtre aksiyonunu açar.
2. Sistem mevcut filtre seçeneklerini gösterir.
3. Kullanıcı bir veya daha fazla filtre seçer.
4. Sistem sonuçları günceller.
5. Sistem aktif filtreleri görünür biçimde gösterir.

### Suggested Filters

* Status
* Technique
* Category
* Has pattern
* Has active counter
* Recently updated

### Alternative Scenarios

* Kullanıcı birden fazla filtre kullanır.
* Kullanıcı tüm filtreleri temizler.
* Sistem son kullanılan filtreleri hatırlayabilir.

### Failure Scenarios

* Filtre sonucu yoktur.
* Eski veya geçersiz filtre değeri saklanmıştır.

### Expected Outcome

* Filtre seçimi proje verisini değiştirmez.
* Sonuç yoksa açıklayıcı boş durum gösterilir.
* Filtre temizlenebilir olmalıdır.

### Related Requirements

* `PM-FR-029`

### Related Acceptance Criteria

* `PM-AC-025`

---

## PM-US-013 — Projeleri Sıralama

**As a:** Projelerini belirli bir düzende görmek isteyen kullanıcı

**I want to:** Projeleri son güncelleme, oluşturma tarihi veya ada göre sıralamak

**So that:** En ilgili projelere daha hızlı ulaşabileyim.

### Preconditions

* Kullanıcı proje listesini görüntülemektedir.

### Main Scenario

1. Kullanıcı sıralama aksiyonunu açar.
2. Sistem sıralama seçeneklerini gösterir.
3. Kullanıcı bir seçenek belirler.
4. Sistem proje listesini yeniden sıralar.
5. Seçili sıralama tercihi görünür olur.

### Suggested Sort Options

* Recently updated
* Oldest updated
* Newest created
* Oldest created
* Name A–Z
* Name Z–A
* Progress high to low
* Progress low to high

### Failure Scenarios

* Bazı projelerde ilerleme değeri yoktur.
* Sıralama tercihi bozulmuştur.

### Expected Outcome

* Eksik değerlerin sıralama davranışı tutarlı olmalıdır.
* Kullanıcının tercihi local olarak saklanabilir.

### Related Requirements

* `PM-FR-030`

### Related Acceptance Criteria

* `PM-AC-026`

---

# 9. Relationship Stories

## PM-US-014 — Projeye Tarif Bağlama

**As a:** Bir tarife göre çalışan kullanıcı

**I want to:** Tarifi projeme bağlamak

**So that:** Proje üzerinde çalışırken tarif bilgilerine doğrudan ulaşabileyim.

### Preconditions

* Proje mevcuttur.
* Kullanıcının erişebildiği en az bir tarif vardır.

### Main Scenario

1. Kullanıcı proje detay ekranını açar.
2. Kullanıcı tarif bağla aksiyonunu seçer.
3. Sistem tarif listesini gösterir.
4. Kullanıcı tarifi seçer.
5. Sistem ilişkiyi kaydeder.
6. Tarif proje ekranında görünür.

### Alternative Scenarios

* Kullanıcı yeni bir custom pattern oluşturur.
* Kullanıcı starter pattern seçer.
* Kullanıcı bağlı tarifi değiştirir.
* Kullanıcı tarif bağlantısını kaldırır.

### Failure Scenarios

* Tarif silinmiştir.
* Tarif erişilebilir değildir.
* İlişki kaydı başarısız olur.

### Expected Outcome

* Proje tarif olmadan da kullanılabilir.
* Tarif silinirse proje kaydı silinmez.
* Tarif bağlantısı güvenli biçimde kaldırılabilir.

### Related Requirements

* `PM-FR-031`

### Related Acceptance Criteria

* `PM-AC-027`

---

## PM-US-015 — Projeye İp Bağlama

**As a:** Projede kullandığı ipleri takip etmek isteyen kullanıcı

**I want to:** Envanterimdeki ipleri projeye bağlamak

**So that:** Hangi ipin hangi projede kullanıldığını görebileyim.

### Preconditions

* Proje mevcuttur.
* Kullanıcının ip envanterinde kayıt vardır.

### Main Scenario

1. Kullanıcı proje malzemeleri alanını açar.
2. Kullanıcı ip ekle aksiyonuna dokunur.
3. Sistem kullanıcının ip kayıtlarını gösterir.
4. Kullanıcı bir ip seçer.
5. Kullanıcı isteğe bağlı miktar belirler.
6. Sistem ilişkiyi kaydeder.
7. İp proje detayında görünür.

### Alternative Scenarios

* Kullanıcı birden fazla ip bağlar.
* Kullanıcı envanterde olmayan geçici ip bilgisi girer.
* Kullanıcı ayrılan miktarı daha sonra değiştirir.
* Kullanıcı ip bağlantısını kaldırır.

### Failure Scenarios

* İp kaydı silinmiştir.
* Girilen miktar mevcut miktardan fazladır.
* Miktar negatif girilmiştir.
* İlişki kaydedilemez.

### Expected Outcome

* Envanter miktarı iş kurallarına göre korunur.
* Projeden ip bağlantısı kaldırıldığında ip kaydı silinmez.
* Miktar tutarlılığı sağlanır.

### Related Requirements

* `PM-FR-032`

### Related Acceptance Criteria

* `PM-AC-028`

---

## PM-US-016 — Projeye Şiş veya Tığ Bağlama

**As a:** Projede kullandığı aracı hatırlamak isteyen kullanıcı

**I want to:** Şiş veya tığ kaydını projeme bağlamak

**So that:** Projeye geri döndüğümde doğru aracı kullanabileyim.

### Preconditions

* Proje mevcuttur.
* Kullanıcının araç envanterinde kayıt vardır veya manuel girişe izin verilir.

### Main Scenario

1. Kullanıcı proje araçları alanını açar.
2. Kullanıcı araç ekle aksiyonuna dokunur.
3. Sistem kullanıcının araçlarını gösterir.
4. Kullanıcı bir araç seçer.
5. Sistem ilişkiyi kaydeder.
6. Araç proje detayında görünür.

### Alternative Scenarios

* Kullanıcı birden fazla araç bağlar.
* Kullanıcı manuel ölçü girer.
* Kullanıcı araç bağlantısını değiştirir veya kaldırır.

### Failure Scenarios

* Araç kaydı silinmiştir.
* Ölçü formatı geçersizdir.
* İlişki kaydı başarısız olur.

### Expected Outcome

* Proje araçsız kullanılabilir.
* Araç bağlantısı envanter kaydını sahiplik kurallarıyla korur.

### Related Requirements

* `PM-FR-033`

### Related Acceptance Criteria

* `PM-AC-029`

---

## PM-US-017 — Projeden Sayaca Ulaşma

**As a:** Proje sırasında sıra sayan kullanıcı

**I want to:** Proje detayından ilgili sayaca ulaşmak

**So that:** Kaldığım sıradan çalışmaya devam edebileyim.

### Preconditions

* Proje mevcuttur.
* Row Counter özelliği kullanılabilir durumdadır.

### Main Scenario

1. Kullanıcı proje detay ekranını açar.
2. Kullanıcı sayaç alanına dokunur.
3. Sistem projeye ait sayaçları gösterir.
4. Kullanıcı mevcut sayacı açar veya yeni sayaç oluşturur.
5. Kullanıcı sayacı kullanır.
6. Sistem değişiklikleri proje bağlamında kaydeder.

### Alternative Scenarios

* Projede sayaç yoktur.
* Projede birden fazla sayaç vardır.
* Kullanıcı son kullanılan sayaca doğrudan gider.

### Failure Scenarios

* Sayaç verisi yüklenemez.
* Sayaç kaydı silinmiştir.
* Kullanıcı proje sahibi değildir.

### Expected Outcome

* Sayaç proje kimliğiyle doğru ilişkilendirilir.
* Sayaç hatası proje detay ekranını tamamen bozmaz.

### Related Requirements

* `PM-FR-034`

### Related Acceptance Criteria

* `PM-AC-030`

---

## PM-US-018 — Projeden Parça Takibine Ulaşma

**As a:** Birden fazla parçadan oluşan proje yapan kullanıcı

**I want to:** Proje içindeki parçaları ayrı ayrı takip etmek

**So that:** Hangi parçadan kaç adet tamamladığımı görebileyim.

### Preconditions

* Proje mevcuttur.
* Multi-Part Tracking kullanılabilir durumdadır.

### Main Scenario

1. Kullanıcı proje detay ekranını açar.
2. Kullanıcı parçalar alanına gider.
3. Sistem projeye ait parçaları gösterir.
4. Kullanıcı parça ekler veya mevcut parçayı günceller.
5. Sistem ilerleme özetini günceller.
6. Proje detayındaki genel ilerleme yenilenir.

### Alternative Scenarios

* Projede hiç parça yoktur.
* Kullanıcı parçalı proje şablonu kullanır.
* Kullanıcı parçayı tamamlandı olarak işaretler.

### Failure Scenarios

* Parça verisi yüklenemez.
* Gerekli adet geçersizdir.
* İlişki kaydı bozulmuştur.

### Expected Outcome

* Parça verileri proje altında doğru saklanır.
* Genel ilerleme kuralı tutarlı uygulanır.

### Related Requirements

* `PM-FR-035`

### Related Acceptance Criteria

* `PM-AC-031`

---

# 10. Progress Stories

## PM-US-019 — Proje İlerlemesini Görüntüleme

**As a:** Projesinde ne kadar ilerlediğini bilmek isteyen kullanıcı

**I want to:** Genel proje ilerlemesini görmek

**So that:** Ne kadar işimin kaldığını anlayabileyim.

### Preconditions

* Projede en az bir ilerleme kaynağı bulunur.

### Main Scenario

1. Kullanıcı proje kartını veya detayını görüntüler.
2. Sistem kullanılabilir ilerleme kaynaklarını değerlendirir.
3. Sistem öncelikli ilerleme kaynağını seçer.
4. Sistem ilerleme bilgisini gösterir.
5. Kullanıcı ilerlemenin kaynağını görebilir.

### Alternative Scenarios

* İlerleme multi-part verisinden gelir.
* İlerleme sayaç hedefinden gelir.
* İlerleme manuel girilmiştir.
* Hiç ilerleme verisi yoktur.

### Failure Scenarios

* İlerleme değeri yüzde 100'ü aşar.
* Hedef değer sıfırdır.
* Parça verileri tutarsızdır.
* Sayaç verisi eksiktir.

### Expected Outcome

* Geçersiz değer kullanıcıya yanlış ilerleme göstermemelidir.
* İlerleme yoksa sahte yüzde üretilmemelidir.
* Kullanıcı ilerleme kaynağını anlayabilmelidir.

### Related Requirements

* `PM-FR-036`
* `PM-FR-037`

### Related Acceptance Criteria

* `PM-AC-032`
* `PM-AC-033`

---

# 11. Media Story

## PM-US-020 — Proje Kapak Görseli Ekleme

**As a:** Projelerini görsel olarak ayırmak isteyen kullanıcı

**I want to:** Projeye kapak fotoğrafı eklemek

**So that:** Projeyi listede hızlıca tanıyabileyim.

### Preconditions

* Kullanıcı fotoğraf erişim iznine sahip olabilir.
* Proje mevcuttur veya oluşturulmaktadır.

### Main Scenario

1. Kullanıcı kapak görseli alanına dokunur.
2. Sistem kamera veya galeri seçeneklerini gösterir.
3. Kullanıcı görsel seçer.
4. Sistem dosya türü ve boyutunu doğrular.
5. Sistem görseli optimize eder.
6. Sistem local önizlemeyi gösterir.
7. Uygunsa görsel private storage'a yüklenir.
8. Proje kapak alanı güncellenir.

### Alternative Scenarios

* Kullanıcı görseli kırpar.
* Kullanıcı mevcut kapağı değiştirir.
* Kullanıcı kapağı kaldırır.
* Kullanıcı offline olarak local kapak ekler.

### Failure Scenarios

* İzin verilmez.
* Dosya türü desteklenmez.
* Dosya çok büyüktür.
* Upload başarısız olur.
* Local dosya silinmiştir.

### Expected Outcome

* Görsel yükleme hatası proje kaydını bozmaz.
* Kullanıcı placeholder ile devam edebilir.
* Private görsel URL'si analytics'e gönderilmez.

### Related Requirements

* `PM-FR-038`

### Related Acceptance Criteria

* `PM-AC-034`

---

# 12. Offline and Reliability Stories

## PM-US-022 — Offline Proje Oluşturma

**As a:** İnternet bağlantısı olmayan kullanıcı

**I want to:** Offline olarak proje oluşturmak

**So that:** İnternet olmasa bile örgü çalışmama başlayabileyim.

### Preconditions

* Uygulamanın local storage alanı kullanılabilir.
* Kullanıcı daha önce gerekli local oturum durumuna sahiptir.

### Main Scenario

1. Kullanıcı offline durumdayken yeni proje oluşturur.
2. Sistem projeyi local olarak kaydeder.
3. Sistem kullanıcıya kayıt durumunu gösterir.
4. Proje listede görünür.
5. İnternet geri geldiğinde sistem sync için kuyruğa alır.
6. Başarılı sync sonrası durum güncellenir.

### Failure Scenarios

* Local storage yazılamaz.
* Cihaz depolaması doludur.
* Sync sırasında conflict oluşur.
* Kullanıcı oturum bilgisi geçersizdir.

### Expected Outcome

* Proje internet olmadığı için kaybolmaz.
* Kullanıcı remote sync beklemeden projeyi kullanabilir.
* Sync hatası açıkça gösterilir.

### Related Requirements

* `PM-FR-039`
* `PM-NFR-002`

### Related Acceptance Criteria

* `PM-AC-035`

---

## PM-US-023 — Offline Proje Düzenleme

**As a:** İnternet bağlantısı olmadan çalışmaya devam eden kullanıcı

**I want to:** Mevcut projemi offline düzenlemek

**So that:** Proje bilgilerimi daha sonra kaybetmeden güncelleyebileyim.

### Preconditions

* Proje local cihazda mevcuttur.

### Main Scenario

1. Kullanıcı offline durumda projeyi açar.
2. Kullanıcı proje bilgilerini değiştirir.
3. Sistem değişiklikleri local olarak kaydeder.
4. Sistem sync durumunu pending olarak işaretler.
5. İnternet geldiğinde değişiklikler remote sisteme gönderilir.

### Failure Scenarios

* Local kayıt bozulmuştur.
* Aynı proje başka cihazda değiştirilmiştir.
* Sync sırasında remote kayıt silinmiştir.
* Authentication süresi dolmuştur.

### Expected Outcome

* Kullanıcının local değişiklikleri sessizce kaybolmaz.
* Conflict davranışı cloud sync belgesine göre yönetilir.
* Kullanıcı sync durumunu anlayabilir.

### Related Requirements

* `PM-FR-040`
* `PM-NFR-003`

### Related Acceptance Criteria

* `PM-AC-036`

---

## PM-US-028 — Kaydedilmemiş Değişiklikleri Koruma

**As a:** Yanlışlıkla uygulamadan çıkan kullanıcı

**I want to:** Proje formunda yaptığım değişikliklerin korunmasını

**So that:** Tekrar aynı bilgileri girmek zorunda kalmayayım.

### Preconditions

* Kullanıcı proje oluşturma veya düzenleme formundadır.

### Main Scenario

1. Kullanıcı form alanlarını değiştirir.
2. Sistem değişiklikleri local draft olarak kaydeder.
3. Kullanıcı uygulamayı arka plana alır veya kapatır.
4. Kullanıcı uygulamaya geri döner.
5. Sistem kaydedilmiş değişiklikleri geri yükler.
6. Kullanıcı kaldığı yerden devam eder.

### Alternative Scenarios

* Kullanıcı draft'ı siler.
* Kullanıcı değişiklikleri bilerek iptal eder.
* Sistem form alanlarını otomatik kaydeder.

### Failure Scenarios

* Draft kaydı bozulur.
* Şema değişikliği nedeniyle draft uyumsuzdur.
* Cihaz depolaması doludur.

### Expected Outcome

* Geçerli değişiklikler mümkün olduğunca korunur.
* Bozuk draft ana proje kaydını bozmaz.
* Kullanıcıya kurtarma seçeneği sunulur.

### Related Requirements

* `PM-FR-041`

### Related Acceptance Criteria

* `PM-AC-037`

---

# 13. Premium Limit Story

## PM-US-024 — Aktif Proje Limitini Yönetme

**As a:** Ücretsiz plan kullanan kullanıcı

**I want to:** Aktif proje limitime ulaştığımda seçeneklerimi anlamak

**So that:** Veri kaybetmeden yeni proje oluşturmak için ne yapacağımı bilebileyim.

### Preconditions

* Kullanıcı ücretsiz plandadır.
* Kullanıcı aktif proje limitine ulaşmıştır.

### Main Scenario

1. Kullanıcı yeni proje oluşturmaya çalışır.
2. Sistem entitlement ve aktif proje sayısını kontrol eder.
3. Sistem limitin dolduğunu açıkça gösterir.
4. Sistem mevcut kullanım miktarını gösterir.
5. Sistem kullanıcıya seçenekler sunar:

   * Bir projeyi tamamla
   * Bir projeyi arşivle
   * Premium'a geç
6. Kullanıcı bir seçenek belirler.
7. Sistem seçilen akışa yönlendirir.

### Alternative Scenarios

* Kullanıcı premium satın alır ve projeye devam eder.
* Kullanıcı aktif projeyi arşivler.
* Kullanıcı işlemi iptal eder.
* Kullanıcı mevcut projelerini yönetir.

### Failure Scenarios

* Entitlement doğrulanamaz.
* Proje sayısı local ve remote sistemde farklıdır.
* Satın alma başarılı ancak entitlement henüz güncellenmemiştir.

### Expected Outcome

* Mevcut projeler gizlenmez veya silinmez.
* Kullanıcı sürpriz paywall ile karşılaşmaz.
* Limit mesajı açık ve manipülatif olmayan biçimde gösterilir.

### Related Requirements

* `PM-FR-042`
* `PM-FR-043`

### Related Acceptance Criteria

* `PM-AC-038`
* `PM-AC-039`

---

# 14. Empty State Story

## PM-US-027 — Boş Proje Listesinden Başlama

**As a:** Knitwise'ı ilk kez kullanan kullanıcı

**I want to:** Projem olmadığında ne yapacağımı anlamak

**So that:** İlk projemi kolayca oluşturabileyim.

### Preconditions

* Kullanıcının hiç proje kaydı yoktur.

### Main Scenario

1. Kullanıcı proje ekranını açar.
2. Sistem boş durum ekranını gösterir.
3. Sistem Project Management özelliğinin değerini kısa biçimde açıklar.
4. Sistem görünür bir proje oluşturma aksiyonu gösterir.
5. Kullanıcı aksiyona dokunur.
6. Sistem hızlı proje oluşturma akışını açar.

### Alternative Scenarios

* Kullanıcı starter pattern seçerek proje oluşturur.
* Kullanıcı onboarding içinden proje oluşturur.

### Failure Scenarios

* Proje oluşturma aksiyonu açılmaz.
* Kullanıcı offline durumdadır.
* Entitlement kontrolü başarısızdır.

### Expected Outcome

* Boş ekran hatalı veya eksik görünmez.
* Kullanıcı ilk aksiyonu kolayca anlayabilir.
* Premium paywall ilk proje öncesinde zorunlu gösterilmez.

### Related Requirements

* `PM-FR-044`

### Related Acceptance Criteria

* `PM-AC-040`

---

# 15. Export and Sync Stories

## PM-US-029 — Proje Verisini Dışa Aktarma

**As a:** Kendi verisinin kopyasını almak isteyen kullanıcı

**I want to:** Proje bilgilerimi dışa aktarmak

**So that:** Verilerimi uygulama dışında saklayabileyim.

### Preconditions

* Kullanıcı proje sahibidir.
* Export özelliği kullanılabilir durumdadır.

### Main Scenario

1. Kullanıcı proje aksiyonlarını açar.
2. Kullanıcı dışa aktar seçeneğini seçer.
3. Sistem export formatlarını gösterir.
4. Kullanıcı format seçer.
5. Sistem proje verisini hazırlar.
6. Sistem dosyayı güvenli biçimde oluşturur.
7. Kullanıcı dosyayı paylaşır veya kaydeder.

### Possible Formats

* JSON
* CSV
* PDF summary

### Failure Scenarios

* Dosya oluşturma başarısız olur.
* Cihazda yeterli alan yoktur.
* Bazı görseller erişilebilir değildir.
* Paylaşım uygulaması bulunmaz.

### Expected Outcome

* Export yalnızca kullanıcının kendi verisini içerir.
* Hassas sistem alanları gereksiz yere export edilmez.
* Başarısız export proje verisini değiştirmez.

### Related Requirements

* `PM-FR-045`

### Related Acceptance Criteria

* `PM-AC-041`

---

## PM-US-030 — Projeleri Farklı Cihazda Görme

**As a:** Birden fazla cihaz kullanan premium kullanıcı

**I want to:** Projelerimi diğer cihazlarımda da görmek

**So that:** Çalışmama farklı cihazlardan devam edebileyim.

### Planned Release

V2

### Preconditions

* Kullanıcı premium cloud sync entitlement'ına sahiptir.
* İki cihazda da aynı kullanıcı hesabıyla giriş yapılmıştır.
* Cloud sync aktiftir.

### Main Scenario

1. Kullanıcı ilk cihazda proje oluşturur veya düzenler.
2. Sistem local veriyi kaydeder.
3. Sistem remote sisteme sync eder.
4. Kullanıcı ikinci cihazda uygulamayı açar.
5. Sistem remote değişiklikleri alır.
6. Proje ikinci cihazda görünür.

### Failure Scenarios

* Aynı proje iki cihazda düzenlenmiştir.
* Cihazlardan biri uzun süre offline kalmıştır.
* Kullanıcının aboneliği sona ermiştir.
* Remote kayıt silinmiştir.
* Sync token geçersizdir.

### Expected Outcome

* Conflict kullanıcı verisini sessizce ezmez.
* Abonelik sona erdiğinde mevcut local proje verileri korunur.
* Sync davranışı `feature-018-cloud-sync` kurallarına uyar.

### Related Requirements

* `CS-FR-001`
* `PM-FR-046`

### Related Acceptance Criteria

* `CS-AC-001`
* `PM-AC-042`

---

# 16. Cross-Story Rules

Tüm Project Management kullanıcı hikâyelerinde aşağıdaki ortak ilkeler uygulanır:

* Kullanıcı yalnızca kendi projelerine erişebilir.
* Proje adı dışında tüm alanlar varsayılan olarak isteğe bağlıdır.
* Arşivleme veri silme anlamına gelmez.
* Silme işleminde kullanıcı açıkça bilgilendirilir.
* Local kayıt remote sync'ten önce tamamlanmalıdır.
* Offline kullanım ana proje akışlarını engellememelidir.
* Kullanıcı tarafından yazılan metin analytics'e gönderilmemelidir.
* Premium limiti mevcut verilere erişimi engellememelidir.
* İlişkili alt feature hataları proje ana kaydını bozmaz.
* Proje durumu yalnızca tanımlı enum değerlerinden biri olabilir.

---

# 17. User Story Acceptance Readiness

Bir kullanıcı hikâyesi geliştirmeye hazır kabul edilmek için:

* Ön koşulları tanımlanmalıdır.
* Ana senaryo tamamlanmalıdır.
* Alternatif senaryolar değerlendirilmelidir.
* Hata durumları yazılmalıdır.
* İlgili functional requirement'lar tanımlanmalıdır.
* İlgili business rule'lar tanımlanmalıdır.
* En az bir acceptance criterion bulunmalıdır.
* Gerekli analytics event'i tanımlanmalıdır.
* Security ve privacy etkisi değerlendirilmelidir.
* UI state'leri tanımlanmalıdır.
* Offline davranış gerekiyorsa açıklanmalıdır.

---

# 18. References

* `overview.md`
* `requirements.md`
* `business-rules.md`
* `user-flows.md`
* `data-model.md`
* `edge-cases.md`
* `acceptance-criteria.md`
* `analytics.md`
* `security-privacy.md`
* `testing.md`
* `../feature-002-row-counter/`
* `../feature-003-multi-part-tracking/`
* `../feature-004-pattern-library/`
* `../feature-007-yarn-inventory/`
* `../feature-008-hook-needle-inventory/`
* `../feature-014-premium/`
* `../feature-017-local-persistence/`
* `../feature-018-cloud-sync/`
