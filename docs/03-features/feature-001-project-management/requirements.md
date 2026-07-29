# Proje Yönetimi — Gereksinimler

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/requirements.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliğinin ürün gereksinimlerini tanımlar.

Project Management, kullanıcının örgü, tığ işi, amigurumi veya benzer el işi projelerini oluşturmasını, takip etmesini, düzenlemesini, duraklatmasını, tamamlamasını, arşivlemesini ve gerektiğinde silmesini sağlayan temel modüldür.

Bu özellik Knitwise içindeki birçok modülün merkezinde yer alır:

- Row Counter
- Multi-Part Tracking
- Pattern Library
- Custom Patterns
- Starter Patterns
- Yarn Inventory
- Hook / Needle Inventory
- Local Persistence
- Cloud Sync
- Premium Entitlement
- Statistics
- Notifications

Bu dosya Codex veya geliştirici ekip tarafından doğrudan referans alınabilecek seviyede hazırlanmıştır.

---

## 2. Kapsam

### 2.1 V1 Kapsamına Dahil Olanlar

V1 kapsamında Project Management özelliği şunları desteklemelidir:

- Yeni proje oluşturma
- Hızlı proje oluşturma
- Detaylı proje oluşturma
- Proje listesini görüntüleme
- Proje detayını görüntüleme
- Proje bilgilerini düzenleme
- Proje durumunu değiştirme
- Projeyi aktif, duraklatılmış, tamamlanmış ve arşivlenmiş olarak yönetme
- Projeyi soft delete yöntemiyle silme
- Silinen projeyi kurtarma altyapısını destekleme
- Proje arama
- Proje filtreleme
- Proje sıralama
- Proje notları ekleme
- Proje kapak görseli ekleme
- Projeye pattern bağlama
- Projeye yarn bağlama
- Projeye hook veya needle bağlama
- Projeden row counter açma
- Projeden part tracking açma
- Proje ilerlemesini görüntüleme
- Offline proje oluşturma
- Offline proje düzenleme
- Free plan aktif proje limiti uygulama
- Premium downgrade sonrası mevcut projeleri koruma

### 2.2 V1 Kapsamı Dışında Olanlar

Aşağıdaki özellikler V1 kapsamına dahil değildir:

- Çok kullanıcılı proje ortaklığı
- Public project profile
- Sosyal paylaşım
- Like, comment, follow gibi sosyal etkileşimler
- Marketplace
- Sipariş yönetimi
- Maliyet ve kar hesabı
- AI ile otomatik proje oluşturma
- AI ile proje ilerleme özeti
- Gerçek zamanlı ortak düzenleme
- Web dashboard
- Public proje linki

---

## 3. Terimler ve Teknik Sabitler

| Terim | Açıklama |
|---|---|
| Project | Kullanıcının takip ettiği örgü veya el işi projesi |
| Active Project | Kullanıcının aktif olarak devam ettiği proje |
| Paused Project | Kullanıcının geçici olarak duraklattığı proje |
| Completed Project | Kullanıcının tamamladığı proje |
| Archived Project | Kullanıcının aktif listeden kaldırdığı ancak sakladığı proje |
| Soft Delete | Verinin hemen kalıcı silinmeyip `deletedAt` ile işaretlenmesi |
| Pattern | Kullanıcının takip ettiği tarif veya model |
| Yarn | Projede kullanılan ip |
| Tool | Hook, needle veya benzeri yardımcı araç |
| Row Counter | Satır veya tur takibi yapan sayaç |
| Part | Kol, gövde, kulak, bacak gibi proje parçası |

Teknik sabitler İngilizce kalmalıdır:

```text
draft
active
paused
completed
archived
projectId
ownerId
createdAt
updatedAt
deletedAt
archivedAt
completedAt
syncStatus
```

---

## 4. Proje Yaşam Döngüsü

Bir proje aşağıdaki durumlara sahip olabilir:

```text
draft
active
paused
completed
archived
```

Silinme durumu normal bir status değildir. Silinme işlemi `deletedAt` alanı ile takip edilir.

### 4.1 Durum Geçişleri

| Mevcut Durum | Hedef Durum | İzin |
|---|---|---|
| `draft` | `active` | Evet |
| `active` | `paused` | Evet |
| `paused` | `active` | Evet |
| `active` | `completed` | Evet |
| `paused` | `completed` | Evet |
| `completed` | `active` | Evet, entitlement kontrolüyle |
| `active` | `archived` | Evet |
| `paused` | `archived` | Evet |
| `completed` | `archived` | Evet |
| `archived` | `active` | Evet, entitlement kontrolüyle |
| `archived` | `paused` | Evet |
| Her durum | Soft delete | Evet, kullanıcı onayıyla |

---

## 5. Fonksiyonel Gereksinimler

### PM-FR-001 — Proje Oluşturma

Sistem, kullanıcının yeni bir proje oluşturmasına izin vermelidir.

Minimum proje oluşturma alanları:

- Proje adı
- Başlangıç durumu

Sistem aşağıdaki alanları otomatik oluşturmalıdır:

- `projectId`
- `ownerId`
- `createdAt`
- `updatedAt`

---

### PM-FR-002 — Hızlı Proje Oluşturma

Kullanıcı yalnızca proje adı girerek proje oluşturabilmelidir.

Opsiyonel alanlar boş bırakıldığında proje oluşturma engellenmemelidir.

Hızlı oluşturma akışı, kullanıcının mümkün olan en kısa sürede projeye başlamasını sağlamalıdır.

---

### PM-FR-003 — Detaylı Proje Oluşturma

Sistem, isteğe bağlı detaylı proje oluşturma akışı sunmalıdır.

Detaylı form aşağıdaki alanları destekleyebilir:

- Proje adı
- Açıklama
- Teknik
- Kategori
- Kapak görseli
- Başlangıç tarihi
- Hedef bitiş tarihi
- Pattern seçimi
- Yarn seçimi
- Hook / Needle seçimi
- İlk status

---

### PM-FR-004 — Benzersiz Proje Kimliği

Her proje global olarak benzersiz bir `projectId` almalıdır.

`projectId` şu bilgilere bağlı olmamalıdır:

- Proje adı
- Proje sırası
- Kategori
- Kullanıcı tarafından görülen numara

`projectId` proje yaşam döngüsü boyunca değişmemelidir.

---

### PM-FR-005 — Lokal Öncelikli Kayıt

Proje oluşturma işleminde sistem, önce lokal kayıt yapmalıdır.

Remote sync başarısız olsa bile, lokal kayıt başarılıysa kullanıcı projeyi görmeye ve kullanmaya devam edebilmelidir.

---

### PM-FR-006 — Draft Proje Desteği

Sistem, tamamlanmamış proje oluşturma formlarını draft olarak saklayabilmelidir.

Draft proje minimum şu alanlara sahip olabilir:

- `projectId`
- `ownerId`
- `status = draft`
- `createdAt`
- `updatedAt`

Draft proje, kullanıcının yarım kalan form verisini kaybetmesini önlemek için kullanılmalıdır.

---

### PM-FR-007 — Proje Listesi Görüntüleme

Sistem, kullanıcının kendisine ait projeleri listelemelidir.

Varsayılan liste şunları göstermelidir:

- `active`
- `paused`

Varsayılan listede şu durumlar gösterilmemelidir:

- `draft`
- `completed`
- `archived`
- `deleted`

Bu durumlar ayrı filtre veya görünüm üzerinden erişilebilir olmalıdır.

---

### PM-FR-008 — Proje Kartı

Her proje kartı minimum şu bilgileri göstermelidir:

- Proje adı
- Proje durumu
- Kapak görseli veya placeholder
- Son güncellenme bilgisi

Varsa şu bilgiler de gösterilebilir:

- Teknik
- Kategori
- Pattern adı
- İlerleme özeti
- Aktif counter bilgisi

---

### PM-FR-009 — Boş Liste Durumu

Kullanıcının hiç projesi yoksa sistem boş durum ekranı göstermelidir.

Boş durum ekranında şunlar bulunmalıdır:

- Kısa açıklama
- Proje oluşturma butonu
- İsteğe bağlı başlangıç önerisi

Premium satış mesajı zorunlu olmamalıdır.

---

### PM-FR-010 — Proje Detayı Görüntüleme

Sistem, kullanıcının proje detay ekranını açmasına izin vermelidir.

Proje detay ekranında uygun olan bilgiler gösterilmelidir:

- Proje adı
- Status
- Açıklama
- Teknik
- Kategori
- Başlangıç tarihi
- Hedef bitiş tarihi
- Tamamlanma tarihi
- Kapak görseli
- Pattern ilişkisi
- Yarn ilişkileri
- Tool ilişkileri
- Row counter özeti
- Part özeti
- İlerleme bilgisi
- Notlar

Opsiyonel alanların boş olması ekranın açılmasını engellememelidir.

---

### PM-FR-011 — Proje Sahipliği Kontrolü

Sistem, proje detayını göstermeden önce projenin mevcut kullanıcıya ait olduğunu doğrulamalıdır.

Kullanıcı başka bir kullanıcının projesini:

- URL değiştirerek
- Local route değiştirerek
- API isteği manipüle ederek
- Deep link üzerinden

görememelidir.

---

### PM-FR-012 — Proje Düzenleme

Kullanıcı sahibi olduğu projeyi düzenleyebilmelidir.

Düzenlenebilir alanlar:

- Proje adı
- Açıklama
- Teknik
- Kategori
- Kapak görseli
- Başlangıç tarihi
- Hedef bitiş tarihi
- Tamamlanma tarihi
- Pattern ilişkisi
- Yarn ilişkileri
- Tool ilişkileri
- Manuel ilerleme
- Notlar

---

### PM-FR-013 — Güncelleme Zamanı

Anlamlı bir proje alanı değiştiğinde `updatedAt` güncellenmelidir.

Sadece ekranı açmak, okumak veya görüntülemek `updatedAt` değerini değiştirmemelidir.

---

### PM-FR-014 — Proje Status Değiştirme

Kullanıcı, iş kurallarına uygun şekilde proje status değerini değiştirebilmelidir.

Desteklenen status değerleri:

```text
draft
active
paused
completed
archived
```

Geçersiz status değerleri kabul edilmemelidir.

---

### PM-FR-015 — Projeyi Duraklatma

Kullanıcı `active` durumdaki projeyi `paused` durumuna alabilmelidir.

Duraklatma işlemi şu verileri korumalıdır:

- Counter verileri
- Part verileri
- Pattern ilişkisi
- Yarn ilişkileri
- Tool ilişkileri
- Notlar
- Kapak görseli
- İlerleme bilgisi

Duraklatma işlemi `completedAt` oluşturmaz.

---

### PM-FR-016 — Projeyi Devam Ettirme

Kullanıcı `paused` durumdaki projeyi tekrar `active` hale getirebilmelidir.

Proje kaldığı yerden devam etmelidir.

---

### PM-FR-017 — Projeyi Tamamlama

Kullanıcı projeyi tamamlandı olarak işaretleyebilmelidir.

Tamamlama işleminde:

- `status = completed` olmalıdır
- `completedAt` doldurulmalıdır
- Proje varsayılan aktif listeden çıkmalıdır
- Proje detayları erişilebilir kalmalıdır
- Proje verileri silinmemelidir

---

### PM-FR-018 — Tamamlanan Projeyi Yeniden Açma

Kullanıcı tamamlanmış projeyi yeniden açabilmelidir.

Yeniden açma işleminde:

- Status `active` veya uygun çalışma durumuna alınmalıdır
- Aktif proje limiti kontrol edilmelidir
- Proje verileri korunmalıdır
- `completedAt` davranışı iş kurallarına göre uygulanmalıdır

---

### PM-FR-019 — Projeyi Arşivleme

Kullanıcı projeyi arşivleyebilmelidir.

Arşivleme işleminde:

- `status = archived` olmalıdır
- `archivedAt` doldurulmalıdır
- Proje varsayılan listeden kaldırılmalıdır
- Proje verileri silinmemelidir

---

### PM-FR-020 — Arşivden Geri Alma

Kullanıcı arşivlenmiş projeyi geri alabilmelidir.

Geri alma sırasında sistem:

- Önceki çalışma status değerini geri yükleyebilir
- Kullanıcıdan hedef status seçmesini isteyebilir

Eğer hedef status `active` ise aktif proje limiti uygulanmalıdır.

---

### PM-FR-021 — Soft Delete

Proje silme işlemi ilk aşamada soft delete olarak yapılmalıdır.

Soft delete işleminde:

- `deletedAt` doldurulmalıdır
- Proje normal listelerden kaldırılmalıdır
- Proje kurtarma süresi içinde geri alınabilir olmalıdır

---

### PM-FR-022 — Silme Onayı

Silme işleminden önce kullanıcıya açık onay ekranı gösterilmelidir.

Onay ekranı, arşivleme ile silme arasındaki farkı net şekilde anlatmalıdır.

---

### PM-FR-023 — Proje Arama

Kullanıcı projeleri proje adına göre arayabilmelidir.

Arama:

- Büyük/küçük harf duyarsız olmalıdır
- Türkçe karakterleri desteklemelidir
- Boşluk toleranslı olmalıdır
- Sadece kullanıcının erişebildiği projelerde çalışmalıdır

---

### PM-FR-024 — Proje Filtreleme

Sistem proje filtrelemeyi desteklemelidir.

V1 filtreleri:

- Status
- Teknik
- Kategori
- Pattern bağlı mı?
- Counter var mı?
- Yakın zamanda güncellendi mi?

---

### PM-FR-025 — Proje Sıralama

Sistem proje sıralamayı desteklemelidir.

Desteklenen sıralamalar:

- Son güncellenen
- İlk güncellenen
- Yeni oluşturulan
- Eski oluşturulan
- Ada göre A-Z
- Ada göre Z-A

Varsayılan sıralama:

```text
updatedAt descending
```

---

### PM-FR-026 — Proje Notları

Kullanıcı projeye serbest metin notu ekleyebilmelidir.

Notlar şunlar için kullanılabilir:

- Ölçü bilgileri
- Pattern değişiklikleri
- İp değişiklikleri
- Kişisel hatırlatmalar
- Teknik notlar

Not içeriği analytics veya log sistemlerine gönderilmemelidir.

---

### PM-FR-027 — Kapak Görseli

Kullanıcı projeye kapak görseli ekleyebilmelidir.

Kapak görseli:

- Eklenebilir
- Değiştirilebilir
- Kaldırılabilir

Kapak görseli yoksa standart placeholder gösterilmelidir.

---

### PM-FR-028 — Pattern Bağlama

Kullanıcı projeye erişebildiği bir pattern bağlayabilmelidir.

Pattern kaynakları:

- Pattern Library
- Custom Patterns
- Starter Patterns

Pattern kaldırıldığında pattern kaydı silinmemelidir.

---

### PM-FR-029 — Yarn Bağlama

Kullanıcı projeye bir veya birden fazla yarn bağlayabilmelidir.

Yarn ilişkisi kaldırıldığında yarn inventory kaydı silinmemelidir.

---

### PM-FR-030 — Hook / Needle Bağlama

Kullanıcı projeye bir veya birden fazla hook veya needle bağlayabilmelidir.

Tool ilişkisi kaldırıldığında tool inventory kaydı silinmemelidir.

---

### PM-FR-031 — Row Counter Açma

Kullanıcı proje detayından ilgili row counter ekranına geçebilmelidir.

Proje içinden oluşturulan counter otomatik olarak ilgili `projectId` ile ilişkilendirilmelidir.

---

### PM-FR-032 — Multi-Part Açma

Kullanıcı proje detayından proje parçalarını yönetebilmelidir.

Proje içinden oluşturulan part otomatik olarak ilgili `projectId` ile ilişkilendirilmelidir.

---

### PM-FR-033 — Proje İlerlemesi

Sistem proje ilerlemesini uygun kaynak varsa göstermelidir.

İlerleme kaynağı önceliği:

1. Multi-part completion
2. Target-based row counter progress
3. Manual progress
4. No progress

Sistem farklı ilerleme kaynaklarını izinsiz şekilde ortalamamalıdır.

---

### PM-FR-034 — Offline Proje Oluşturma

Kullanıcı internet bağlantısı yokken proje oluşturabilmelidir.

Proje lokal olarak kaydedilmeli ve sync bekleyen kayıt olarak işaretlenmelidir.

---

### PM-FR-035 — Offline Proje Düzenleme

Kullanıcı lokal olarak mevcut projeleri internet olmadan düzenleyebilmelidir.

Remote sync daha sonra yapılmalıdır.

---

### PM-FR-036 — Sync Durumu

Proje değişiklikleri için sync durumu tutulmalıdır.

Örnek değerler:

```text
synced
pending
failed
conflict
```

---

### PM-FR-037 — Free Plan Aktif Proje Limiti

Free plan için aktif proje limiti uygulanmalıdır.

Önerilen V1 limiti:

```text
3 active projects
```

Aktif proje sayımına dahil olan status değerleri:

```text
active
paused
```

Dahil olmayanlar:

```text
draft
completed
archived
deleted
```

---

### PM-FR-038 — Limit Doldu Deneyimi

Free kullanıcı aktif proje limitine ulaştığında sistem şu seçenekleri sunmalıdır:

- Mevcut projeyi arşivle
- Mevcut projeyi tamamla
- Premium'a yükselt
- İşlemi iptal et

Sistem hiçbir mevcut projeyi otomatik silmemelidir.

---

### PM-FR-039 — Premium Downgrade

Premium kullanıcı Free plana döndüğünde ve limitten fazla aktif projesi varsa:

- Mevcut projeler görünür kalmalıdır
- Mevcut projeler erişilebilir kalmalıdır
- Hiçbir proje silinmemelidir
- Yeni aktif proje oluşturma kısıtlanabilir
- Kullanıcıya açıklama gösterilmelidir

---

### PM-FR-040 — Eksik İlişki Güvenliği

Pattern, yarn, tool, counter veya part ilişkisi bozulsa bile proje detay ekranı açılmalıdır.

Eksik ilişki güvenli şekilde gösterilmeli ve kullanıcıya kaldırma/değiştirme seçeneği sunulmalıdır.

---

## 6. Fonksiyonel Olmayan Gereksinimler

### PM-NFR-001 — Performans

Proje listesi lokal veri mevcutken tipik cihazlarda 2 saniye içinde kullanılabilir hale gelmelidir.

### PM-NFR-002 — Detay Ekranı Performansı

Proje detay ekranı temel proje bilgilerini lokal veriyle 1 saniye içinde göstermelidir.

İlişkili modüller kademeli yüklenebilir.

### PM-NFR-003 — Veri Kaybı Önleme

Sistem geçerli kullanıcı değişikliklerini sessizce kaybetmemelidir.

Kaydetme başarısız olursa kullanıcı bilgilendirilmelidir.

### PM-NFR-004 — Lokal Öncelikli Deneyim

Remote servislerin yavaş veya erişilemez olması, lokal olarak kaydedilmiş projenin kullanılmasını engellememelidir.

### PM-NFR-005 — Kullanıcı İzolasyonu

Bir kullanıcının proje verisi başka kullanıcıya gösterilmemelidir.

### PM-NFR-006 — Private Media

Proje görselleri private storage üzerinde saklanmalı ve yetkisiz erişime kapalı olmalıdır.

### PM-NFR-007 — Analytics Gizliliği

Analytics eventleri şu verileri içermemelidir:

- Proje adı
- Proje açıklaması
- Proje notu
- Pattern metni
- Görsel yolu
- Kullanıcıya ait özel içerik

### PM-NFR-008 — Erişilebilirlik

Proje ekranları screen reader desteği sunmalıdır.

Status bilgisi yalnızca renk ile anlatılmamalıdır.

### PM-NFR-009 — Lokalizasyon

Tüm kullanıcı arayüzü metinleri lokalize edilebilir olmalıdır.

Tarih formatları kullanıcı lokasyonuna göre gösterilmelidir.

### PM-NFR-010 — Türkçe Karakter Desteği

Arama, sıralama ve metin gösterimi Türkçe karakterleri doğru desteklemelidir.

Örnekler:

```text
ç, ğ, ı, İ, ö, ş, ü
```

### PM-NFR-011 — Migration Desteği

Proje veri modeli değişikliklerinde migration stratejisi bulunmalıdır.

Mevcut kullanıcı projeleri uygulama güncellemesi sonrası erişilemez hale gelmemelidir.

### PM-NFR-012 — Test Edilebilirlik

İş kuralları UI katmanından bağımsız test edilebilir olmalıdır.

---

## 7. İş Kuralları

### PM-BR-001 — Proje Sahipliği

Her proje tam olarak bir kullanıcıya ait olmalıdır.

V1 ortak sahiplik desteklemez.

### PM-BR-002 — Proje Adı Zorunluluğu

`active`, `paused` ve `completed` durumundaki projelerde proje adı boş olamaz.

Draft proje kurtarma amacıyla geçici placeholder kullanabilir.

### PM-BR-003 — Status Değerleri

Geçerli status değerleri:

```text
draft
active
paused
completed
archived
```

### PM-BR-004 — Silme Ayrı Bir Durumdur

Silme işlemi `status = archived` ile temsil edilmemelidir.

Silme için `deletedAt` kullanılmalıdır.

### PM-BR-005 — Varsayılan Status

Hızlı proje oluşturma için varsayılan status:

```text
active
```

Draft olarak kaydetme seçilirse status:

```text
draft
```

### PM-BR-006 — Active Project Tanımı

Entitlement hesaplamasında aktif proje sayımı için şu status değerleri sayılır:

```text
active
paused
```

### PM-BR-007 — Completed Limit Dışı

`completed` projeler aktif proje limitine dahil edilmez.

### PM-BR-008 — Archived Limit Dışı

`archived` projeler aktif proje limitine dahil edilmez.

### PM-BR-009 — Draft Limit Dışı

V1 önerisi: `draft` projeler aktif proje limitine dahil edilmez.

### PM-BR-010 — Completion Date

Proje tamamlandığında kullanıcı tarih seçmemişse sistem geçerli lokal tarihi `completedAt` olarak atamalıdır.

### PM-BR-011 — Reopen Davranışı

Tamamlanmış proje yeniden açıldığında aktif `completedAt` değeri temizlenebilir.

Geçmiş bilgi audit history içinde saklanabilir.

### PM-BR-012 — Archive Timestamp

Proje arşivlendiğinde `archivedAt` doldurulmalıdır.

Proje arşivden çıkarıldığında aktif `archivedAt` temizlenmelidir.

### PM-BR-013 — Soft Delete Timestamp

Proje silindiğinde `deletedAt` doldurulmalıdır.

Kurtarma işleminde `deletedAt` temizlenmelidir.

### PM-BR-014 — Pattern Opsiyoneldir

Bir proje pattern olmadan geçerli olabilir.

### PM-BR-015 — Yarn Opsiyoneldir

Bir proje yarn ilişkisi olmadan geçerli olabilir.

### PM-BR-016 — Tool Opsiyoneldir

Bir proje hook veya needle ilişkisi olmadan geçerli olabilir.

### PM-BR-017 — Counter Opsiyoneldir

Bir proje row counter olmadan geçerli olabilir.

### PM-BR-018 — Part Opsiyoneldir

Bir proje multi-part tracking olmadan geçerli olabilir.

### PM-BR-019 — Pattern Cardinality

V1 önerisi: Bir projenin bir primary pattern ilişkisi olmalıdır.

Birden fazla primary pattern V1 sonrası değerlendirilebilir.

### PM-BR-020 — Arşivleme Veri Korur

Arşivleme şu verileri silmemelidir:

- Proje bilgileri
- Notlar
- Counter
- Parts
- Pattern ilişkisi
- Yarn ilişkisi
- Tool ilişkisi
- Görseller
- İlerleme

### PM-BR-021 — İlişki Silme Davranışı

Projeden pattern, yarn veya tool ilişkisi kaldırıldığında bağlı ana kayıt silinmemelidir.

### PM-BR-022 — Completed Project Editing

V1'de tamamlanmış projeler düzenlenebilir kalmalıdır.

Arayüz, projenin tamamlanmış olduğunu net göstermelidir.

### PM-BR-023 — Limit Üstünde Restore

Free kullanıcı aktif proje limitindeyse archived veya completed projeyi `active` olarak geri alamaz.

Önce başka bir projeyi arşivlemeli/tamamlamalı veya Premium'a geçmelidir.

### PM-BR-024 — Offline Owner

Offline oluşturulan projeler de doğru `ownerId` ile kaydedilmelidir.

### PM-BR-025 — Remote Sync Data Loss Yapamaz

Remote sync başarısızlığı lokal geçerli veriyi silmemelidir.

---

## 8. Validasyon Kuralları

### PM-VR-001 — Proje Adı

Trim sonrası proje adı boş olamaz.

Geçerli limit önerisi:

```text
Minimum: 1 karakter
Maximum: 120 karakter
```

### PM-VR-002 — Açıklama

Proje açıklaması için önerilen maksimum uzunluk:

```text
2000 karakter
```

### PM-VR-003 — Notlar

Proje notları için önerilen maksimum uzunluk:

```text
10000 karakter
```

### PM-VR-004 — Tarih Tutarlılığı

`targetCompletionDate`, `startDate` değerinden önce olamaz.

`completedAt`, `startDate` değerinden önce olamaz.

### PM-VR-005 — Manuel Progress

Manual progress değeri 0 ile 100 arasında olmalıdır.

```text
0 <= manualProgress <= 100
```

### PM-VR-006 — Counter Target

Counter progress hesaplanacaksa hedef değer 0'dan büyük olmalıdır.

### PM-VR-007 — Part Quantity

Part sayısı pozitif integer olmalıdır.

Tamamlanan part sayısı negatif olamaz.

### PM-VR-008 — Image Type

Desteklenen görsel formatları:

- JPEG
- PNG
- HEIC, platform destekliyorsa
- WebP, platform destekliyorsa

### PM-VR-009 — Relationship Ownership

Proje sadece aynı kullanıcıya ait veya kullanıcının erişebildiği kayıtlarla ilişkilendirilebilir.

Bu kural şunlar için geçerlidir:

- Pattern
- Yarn
- Tool
- Counter
- Part

### PM-VR-010 — Status Değeri

Status alanı yalnızca izin verilen enum değerlerinden biri olabilir.

---

## 9. Hata Yönetimi

### PM-ER-001 — Kaydetme Hatası

Proje kaydetme başarısız olursa sistem:

- Kullanıcı girdisini korumalı
- Açık hata mesajı göstermeli
- Uygunsa yeniden deneme seçeneği sunmalı
- Duplicate proje oluşturmamalıdır

### PM-ER-002 — Lokal Storage Hatası

Lokal kayıt başarısız olursa proje kaydedildi gibi gösterilmemelidir.

### PM-ER-003 — Remote Sync Hatası

Remote sync başarısız olursa:

- Lokal proje kullanılabilir kalmalı
- Sync status `failed` veya `pending` olmalı
- Retry mekanizması çalışmalıdır

### PM-ER-004 — Görsel Upload Hatası

Kapak görseli upload edilemezse:

- Proje kaydı korunmalı
- Görsel pending kalabilir
- Kullanıcı uyarılmalıdır

### PM-ER-005 — Proje Bulunamadı

İstenen proje bulunamazsa safe not-found state gösterilmelidir.

Teknik detay kullanıcıya gösterilmemelidir.

### PM-ER-006 — Yetkisiz Erişim

Kullanıcı yetkisiz bir projeye erişmeye çalışırsa proje verisi gösterilmemelidir.

### PM-ER-007 — Kırık İlişki

Pattern, yarn, tool, counter veya part ilişkisi bozuksa proje ekranı yine açılmalıdır.

### PM-ER-008 — Entitlement Kontrol Hatası

Entitlement kontrolü geçici olarak yapılamıyorsa sistem yıkıcı davranmamalıdır.

Mevcut projeler erişilemez hale getirilmemelidir.

### PM-ER-009 — Duplicate Submit

Kullanıcının create butonuna birden fazla kez basması birden fazla proje oluşturmamalıdır.

---

## 10. Edge Case'ler

### PM-EC-001 — Sadece Boşluk İçeren Proje Adı

Input:

```text
"     "
```

Beklenen:

- Trim uygulanır
- Validasyon hatası gösterilir
- Active proje oluşturulmaz

### PM-EC-002 — Aynı İsimli Projeler

Kullanıcı aynı isimde iki proje oluşturabilir.

Beklenen:

- İki proje de oluşturulur
- `projectId` değerleri farklıdır
- Duplicate name uyarısı opsiyoneldir

### PM-EC-003 — Çok Uzun Proje Adı

Kullanıcı limitten uzun proje adı girerse:

- Veri sessiz kırpılmamalıdır
- Validasyon mesajı gösterilmelidir

### PM-EC-004 — Uygulama Oluşturma Sırasında Kapanır

Beklenen:

- Mümkünse draft korunur
- Bozuk proje kaydı görünmez

### PM-EC-005 — Uygulama Kaydetme Sırasında Kapanır

Beklenen:

- Lokal veri tutarlılığı korunur
- Zorunlu alanları eksik proje aktif listede görünmez

### PM-EC-006 — Proje Başka Cihazda Silinir

Beklenen:

- Sync conflict stratejisi uygulanır
- Lokal değişiklikler sessizce kaybedilmez

### PM-EC-007 — İki Cihazda Offline Edit

Beklenen:

- Conflict tespit edilir
- Bir versiyon diğerini sessizce ezmez

### PM-EC-008 — Offline Limit Aşımı

Kullanıcı offline iken limit üstü proje oluşturursa:

- Cached entitlement uygulanır
- Veri silinmez
- Online olunca reconciliation yapılır

### PM-EC-009 — Premium Süresi Biter

Premium kullanıcı Free plana döner ve limitten fazla aktif projesi vardır.

Beklenen:

- Mevcut projeler erişilebilir kalır
- Yeni aktif proje kısıtlanabilir
- Proje silinmez

### PM-EC-010 — Kapak Görseli Dosyası Yok

Beklenen:

- Placeholder gösterilir
- Proje kullanılabilir kalır
- Kullanıcı yeni görsel seçebilir

### PM-EC-011 — Pattern Silinmiş

Beklenen:

- Proje açılır
- Pattern unavailable state gösterilir
- Kullanıcı ilişkiyi kaldırabilir

### PM-EC-012 — Progress 100'ü Aşar

Beklenen:

- UI güvenli davranır
- Yüzde cap edilebilir veya uyarı gösterilebilir
- Raw data sessizce değiştirilmez

### PM-EC-013 — Deep Link Silinmiş Projeye Gider

Beklenen:

- Normal proje içeriği gösterilmez
- Safe not-found veya recovery state gösterilir

---

## 11. Kabul Kriterleri

### PM-AC-001 — Hızlı Proje Oluşturma

**Given** kullanıcı aktif proje oluşturma hakkına sahiptir  
**When** geçerli proje adı girer ve oluştur butonuna basar  
**Then** sistem tam olarak bir proje oluşturur  
**And** proje lokal olarak kaydedilir  
**And** proje detay ekranı açılır.

### PM-AC-002 — Boş Ad Reddedilir

**Given** kullanıcı non-draft proje oluşturur  
**When** proje adı boş veya sadece whitespace içerir  
**Then** proje oluşturulmaz  
**And** validasyon mesajı gösterilir.

### PM-AC-003 — Opsiyonel Alanlar Boşken Oluşturma

**Given** kullanıcı geçerli proje adı girmiştir  
**When** diğer alanları boş bırakır  
**Then** proje başarıyla oluşturulur.

### PM-AC-004 — Offline Oluşturma

**Given** cihaz offline durumdadır  
**When** kullanıcı geçerli proje oluşturur  
**Then** proje lokal kaydedilir  
**And** proje listede görünür  
**And** sync status pending olur.

### PM-AC-005 — Varsayılan Liste

**Given** kullanıcının active, paused, completed ve archived projeleri vardır  
**When** proje listesi açılır  
**Then** active ve paused projeler gösterilir  
**And** completed ve archived projeler varsayılan listede görünmez.

### PM-AC-006 — Proje Düzenleme

**Given** kullanıcı projenin sahibidir  
**When** geçerli bir alanı değiştirip kaydeder  
**Then** değişiklik saklanır  
**And** `updatedAt` güncellenir.

### PM-AC-007 — Projeyi Duraklatma

**Given** proje `active` durumdadır  
**When** kullanıcı projeyi duraklatır  
**Then** status `paused` olur  
**And** proje verileri korunur.

### PM-AC-008 — Projeyi Tamamlama

**Given** proje silinmemiştir  
**When** kullanıcı tamamla aksiyonunu onaylar  
**Then** status `completed` olur  
**And** `completedAt` doldurulur.

### PM-AC-009 — Projeyi Arşivleme

**Given** kullanıcı projenin sahibidir  
**When** arşivleme işlemini onaylar  
**Then** status `archived` olur  
**And** `archivedAt` dolar  
**And** proje varsayılan listeden çıkar.

### PM-AC-010 — Soft Delete

**Given** kullanıcı silme işlemini onaylar  
**When** işlem başarılı olur  
**Then** `deletedAt` dolar  
**And** proje normal listelerde görünmez.

### PM-AC-011 — Arama Türkçe Karakter Desteği

**Given** proje adı Türkçe karakter içerir  
**When** kullanıcı eşleşen arama yapar  
**Then** proje doğru şekilde bulunur.

### PM-AC-012 — Pattern Bağlama

**Given** kullanıcı projeye ve pattern'e erişebilir  
**When** pattern seçer  
**Then** ilişki kaydedilir  
**And** pattern proje detayında görünür.

### PM-AC-013 — Yarn Bağlama

**Given** kullanıcı yarn kaydına sahiptir  
**When** yarn projeye bağlanır  
**Then** ilişki kaydedilir  
**And** yarn inventory kaydı silinmez.

### PM-AC-014 — Row Counter Oluşturma

**Given** kullanıcı proje detayındadır  
**When** proje içinden counter oluşturur  
**Then** counter doğru `projectId` ile kaydedilir.

### PM-AC-015 — İlerleme Önceliği

**Given** projede multi-part progress ve manual progress vardır  
**When** ilerleme gösterilir  
**Then** multi-part progress kullanılır.

### PM-AC-016 — Limit Doldu

**Given** Free kullanıcı aktif proje limitine ulaşmıştır  
**When** yeni active proje oluşturmak ister  
**Then** işlem engellenir  
**And** mevcut projeler korunur  
**And** upgrade veya proje arşivleme seçenekleri sunulur.

### PM-AC-017 — Downgrade Veri Korur

**Given** Premium kullanıcı limitten fazla aktif projeye sahiptir  
**When** Free plana döner  
**Then** mevcut projeler erişilebilir kalır  
**And** hiçbir proje silinmez.

### PM-AC-018 — Yetkisiz Erişim Engellenir

**Given** kullanıcı başka kullanıcıya ait projeye erişmeye çalışır  
**When** proje detayı istenir  
**Then** proje verisi gösterilmez.

### PM-AC-019 — Kırık İlişki Ekranı Bozmaz

**Given** projede eksik pattern veya yarn ilişkisi vardır  
**When** proje detayı açılır  
**Then** ana proje bilgileri gösterilir  
**And** eksik ilişki güvenli state ile gösterilir.

### PM-AC-020 — Lokal Veri Restart Sonrası Korunur

**Given** proje değişikliği lokal kaydedilmiştir  
**When** uygulama kapanıp açılır  
**Then** değişiklik korunur.

---

## 12. Requirement Traceability

| Kullanıcı İhtiyacı | Gereksinimler |
|---|---|
| Proje oluşturmak | PM-FR-001 - PM-FR-006 |
| Projeleri listelemek | PM-FR-007 - PM-FR-009 |
| Proje detayını görmek | PM-FR-010 - PM-FR-011 |
| Proje düzenlemek | PM-FR-012 - PM-FR-013 |
| Proje yaşam döngüsünü yönetmek | PM-FR-014 - PM-FR-022 |
| Arama, filtreleme, sıralama | PM-FR-023 - PM-FR-025 |
| Not ve görsel yönetmek | PM-FR-026 - PM-FR-027 |
| Pattern, yarn ve tool bağlamak | PM-FR-028 - PM-FR-030 |
| Counter ve part yönetmek | PM-FR-031 - PM-FR-032 |
| Progress görmek | PM-FR-033 |
| Offline çalışmak | PM-FR-034 - PM-FR-036 |
| Premium limit uygulamak | PM-FR-037 - PM-FR-039 |
| Kırık ilişkileri güvenli yönetmek | PM-FR-040 |

---

## 13. Açık Ürün Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-OD-001 | Free aktif proje limiti | 3 | Open |
| PM-OD-002 | Draft projeler limite dahil mi? | Hayır | Open |
| PM-OD-003 | Soft delete recovery süresi | 30 gün | Open |
| PM-OD-004 | Archived proje direkt düzenlenebilir mi? | Önce restore | Open |
| PM-OD-005 | Bir projede birden fazla primary pattern olur mu? | V1'de hayır | Open |
| PM-OD-006 | Manual progress V1'e dahil mi? | Evet | Open |
| PM-OD-007 | Kapak görseli V1'e dahil mi? | Evet, opsiyonel | Open |
| PM-OD-008 | Progress photo V1'e dahil mi? | V1.x'e ertelenebilir | Open |
| PM-OD-009 | Maksimum proje notu uzunluğu | 10000 karakter | Open |
| PM-OD-010 | Recovery ekranı kullanıcıya görünür mü? | Evet | Open |

---

## 14. Bağımlılıklar

### 14.1 Ürün Bağımlılıkları

Project Management aşağıdaki feature'larla ilişkilidir:

- `feature-002-row-counter`
- `feature-003-multi-part-tracking`
- `feature-004-pattern-library`
- `feature-005-custom-patterns`
- `feature-006-starter-patterns`
- `feature-007-yarn-inventory`
- `feature-008-hook-needle-inventory`
- `feature-014-premium`
- `feature-015-onboarding-authentication`
- `feature-017-local-persistence`
- `feature-018-cloud-sync`
- `feature-019-notifications`
- `feature-020-statistics`

### 14.2 Teknik Bağımlılıklar

Gerekli teknik yetenekler:

- Authentication
- Local database
- User-scoped access control
- Private media storage
- Deep link handling
- Analytics infrastructure
- Entitlement service
- Error reporting
- Localization
- Migration infrastructure
- Offline sync queue

### 14.3 Tasarım Bağımlılıkları

Gerekli UI bileşenleri:

- Project card
- Project list
- Project detail
- Quick create form
- Detailed create form
- Edit form
- Status selector
- Empty state
- No-results state
- Error state
- Archive confirmation
- Delete confirmation
- Recovery screen
- Limit reached screen
- Sync status indicator

---

## 15. Definition of Ready

Project Management geliştirmeye hazır sayılırsa:

- Tüm Must gereksinimler onaylanmış olmalı
- Aktif proje limiti kararı verilmiş olmalı
- Status geçişleri onaylanmış olmalı
- Soft delete ve recovery davranışı netleşmiş olmalı
- Pattern cardinality kararı verilmiş olmalı
- Data model `data-model.md` içinde tanımlanmış olmalı
- User flow'lar `flows.md` içinde hazırlanmış olmalı
- Analytics eventleri `analytics.md` içinde tanımlanmış olmalı
- Security gereksinimleri `security.md` içinde netleşmiş olmalı
- Teknik yaklaşım `implementation-notes.md` içinde yazılmış olmalı
- Test kapsamı `testing.md` içinde hazırlanmış olmalı
- Product Owner V1 kapsamını onaylamış olmalı

---

## 16. Definition of Done

Project Management tamamlanmış sayılırsa:

- Tüm Must fonksiyonel gereksinimler uygulanmış olmalı
- Tüm Must acceptance criteria geçmeli
- İş kuralları enforce edilmeli
- Validasyonlar frontend ve backend tarafında tutarlı olmalı
- Lokal persistence çalışmalı
- Offline create çalışmalı
- Offline edit çalışmalı
- Sync status doğru yönetilmeli
- Project ownership güvenliği test edilmeli
- Premium limit data loss yaratmadan çalışmalı
- Soft delete çalışmalı
- Recovery davranışı uygulanmalı veya bilinçli şekilde ertelenmiş olmalı
- Kırık optional ilişkiler ekranı bozmamalı
- Accessibility kontrolleri geçmeli
- Localization kontrolleri geçmeli
- Analytics PII içermemeli
- Migration testleri geçmeli
- Kritik veya release-blocker hata kalmamalı
- Product Owner kabul vermeli

---

## 17. Referanslar

- `overview.md`
- `flows.md`
- `data-model.md`
- `analytics.md`
- `security.md`
- `implementation-notes.md`
- `testing.md`
- `../../PROJECT_PRINCIPLES.md`
- `../../DECISIONS.md`
- `../../02-prd/overview.md`
- `../../02-prd/mvp-roadmap.md`
- `../../02-prd/feature-priorities.md`
- `../../02-prd/premium-strategy.md`
- `../../02-prd/release-plan.md`

---

## 18. Notlar

Bu belge V1 için uygulanabilir, genişletilebilir ve Codex tarafından geliştirilebilir bir gereksinim dokümanı olacak şekilde hazırlanmıştır.

Sonraki doküman sırası:

1. `flows.md`
2. `data-model.md`
3. `analytics.md`
4. `security.md`
5. `implementation-notes.md`
6. `testing.md`
