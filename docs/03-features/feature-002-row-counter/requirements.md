# Row Counter — Requirements

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/requirements.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliğinin gereksinimlerini tanımlar.

Row Counter, kullanıcının proje sırasında satır, tur, tekrar, artırma, eksiltme veya özel sayaç değerlerini takip etmesini sağlayan temel üretkenlik modülüdür.

Bu özellik özellikle aşağıdaki durumlar için kritik öneme sahiptir:

- Kullanıcı projede kaçıncı satırda kaldığını unutmak istemez.
- Kullanıcı birden fazla parçada ayrı sayaç kullanmak ister.
- Kullanıcı pattern tekrarlarını takip etmek ister.
- Kullanıcı target row veya target round ile ilerleme görmek ister.
- Kullanıcı offline durumda da sayaç kullanmak ister.
- Kullanıcı yanlışlıkla sayaç artırırsa bunu düzeltebilmek ister.
- Project Management ekranı içinde counter progress göstermek ister.

Bu dosya Codex veya geliştirici ekip tarafından doğrudan uygulanabilecek seviyede hazırlanmıştır.

---

## 2. Requirement ID Standardı

Bu dosyada requirement ID prefix'i:

```text
RC
```

olarak kullanılır.

| Prefix | Açıklama |
|---|---|
| `RC-FR` | Functional Requirement |
| `RC-NFR` | Non-Functional Requirement |
| `RC-BR` | Business Rule |
| `RC-VR` | Validation Rule |
| `RC-ER` | Error Handling Requirement |
| `RC-EC` | Edge Case |
| `RC-AC` | Acceptance Criteria |

---

## 3. Kapsam

### 3.1 V1 Kapsamına Dahil Olanlar

V1 Row Counter kapsamına şunlar dahildir:

- Counter oluşturma
- Project'e bağlı counter oluşturma
- Counter adı verme
- Counter tipini seçme
- Counter değerini artırma
- Counter değerini azaltma
- Counter değerini manuel değiştirme
- Counter başlangıç değerini belirleme
- Counter target değeri belirleme
- Counter step değeri belirleme
- Counter resetleme
- Counter silme veya soft delete
- Bir project altında birden fazla counter kullanma
- Counter'ı project part ile ilişkilendirme altyapısı
- Counter detail ekranı
- Project detail içinde counter özeti
- Offline counter update
- Sync status takibi
- Basic undo desteği
- Counter progress hesaplama
- Counter analytics eventleri
- Security ve owner isolation
- Validation ve test kapsamı

### 3.2 V1 Kapsam Dışı

Aşağıdakiler V1 kapsamına dahil değildir:

- Sesle counter artırma
- Apple Watch / Wear OS entegrasyonu
- Ana ekran widget'ı üzerinden counter artırma
- Hardware clicker entegrasyonu
- AI ile pattern'den otomatik counter üretme
- Pattern satırına otomatik bağlı smart counter
- Gelişmiş conditional counter
- Public counter paylaşımı
- Shared collaborative counter
- Counter history timeline UI
- Gelişmiş istatistik grafikleri
- Barcode / NFC tetikleme
- Çok karmaşık macro sayaçlar

---

## 4. Temel Entity Tanımı

Counter, kullanıcının bir proje veya proje parçası için takip ettiği sayısal ilerleme kaydıdır.

Minimum counter alanları:

- `counterId`
- `ownerId`
- `projectId`
- `name`
- `counterType`
- `currentValue`
- `stepValue`
- `createdAt`
- `updatedAt`
- `syncStatus`

Opsiyonel alanlar:

- `partId`
- `targetValue`
- `initialValue`
- `notes`
- `deletedAt`
- `lastIncrementedAt`
- `lastDecrementedAt`

---

# 5. Fonksiyonel Gereksinimler

### RC-FR-001 — Counter Oluşturma

Sistem, kullanıcının yeni bir counter oluşturmasına izin vermelidir.

Counter oluşturmak için minimum gerekli alanlar:

- Counter adı
- Counter tipi
- Başlangıç değeri
- Step değeri

Project içinden oluşturulan counter otomatik olarak ilgili `projectId` ile ilişkilendirilmelidir.

---

### RC-FR-002 — Project'e Bağlı Counter Oluşturma

Kullanıcı bir Project Detail ekranından counter oluşturabilmelidir.

Bu durumda:

- `projectId` otomatik atanmalıdır.
- Kullanıcıdan tekrar project seçmesi istenmemelidir.
- Counter, project detail içindeki counter section'da görünmelidir.

---

### RC-FR-003 — Bağımsız Counter Oluşturma

Sistem bağımsız counter oluşturmayı destekleyebilir.

V1 önerisi:

```text
Project'e bağlı counter önceliklidir.
Bağımsız counter opsiyoneldir.
```

Bağımsız counter desteklenirse, daha sonra project'e bağlanabilmelidir.

---

### RC-FR-004 — Counter Adı Verme

Kullanıcı counter'a anlamlı bir ad verebilmelidir.

Örnekler:

- Gövde
- Sağ Kol
- Sol Kol
- Yaka
- Pattern Repeat
- Artırma
- Eksiltme
- Round Counter

---

### RC-FR-005 — Counter Tipi Seçme

Kullanıcı counter tipi seçebilmelidir.

Desteklenen V1 tipleri:

```text
row
round
repeat
increase
decrease
custom
```

Counter tipi UI label ile lokalize edilmelidir.

---

### RC-FR-006 — Başlangıç Değeri Belirleme

Kullanıcı counter başlangıç değerini belirleyebilmelidir.

V1 önerilen default:

```text
initialValue = 1
```

Alternatif ürün kararı:

```text
initialValue = 0
```

Bu karar açık ürün kararı olarak takip edilmelidir.

---

### RC-FR-007 — Güncel Değeri Gösterme

Sistem counter'ın güncel değerini net ve büyük şekilde göstermelidir.

Counter value, counter ekranının ana görsel odağı olmalıdır.

---

### RC-FR-008 — Counter Artırma

Kullanıcı counter değerini artırabilmelidir.

Artırma işleminde:

- `currentValue` step kadar artmalıdır.
- UI anında güncellenmelidir.
- Değişiklik lokal kaydedilmelidir.
- `updatedAt` güncellenmelidir.
- `lastIncrementedAt` güncellenebilir.
- Sync gerekiyorsa `syncStatus = pending` yapılmalıdır.

---

### RC-FR-009 — Counter Azaltma

Kullanıcı counter değerini azaltabilmelidir.

Azaltma işleminde:

- `currentValue` step kadar azalmalıdır.
- Minimum değerin altına düşme validasyonla yönetilmelidir.
- UI anında güncellenmelidir.
- Değişiklik lokal kaydedilmelidir.
- `updatedAt` güncellenmelidir.
- `lastDecrementedAt` güncellenebilir.

---

### RC-FR-010 — Step Değeri

Kullanıcı counter step değerini belirleyebilmelidir.

Default:

```text
stepValue = 1
```

Step değeri pozitif integer olmalıdır.

---

### RC-FR-011 — Manuel Counter Değeri Güncelleme

Kullanıcı counter değerini manuel olarak değiştirebilmelidir.

Bu özellik şu durumlar için gereklidir:

- Kullanıcı yanlış değerle başlamıştır.
- Kullanıcı fiziksel sayaçtan dijitale geçmektedir.
- Kullanıcı önceki proje ilerlemesini sonradan eklemektedir.
- Kullanıcı çoklu cihaz sync sonrası düzeltme yapmak istemektedir.

Manuel değişiklik validasyon kurallarına tabi olmalıdır.

---

### RC-FR-012 — Target Değeri Belirleme

Kullanıcı counter için opsiyonel target değeri belirleyebilmelidir.

Target varsa sistem progress hesaplayabilir.

Örnek:

```text
currentValue = 30
targetValue = 100
progress = 30%
```

Target zorunlu değildir.

---

### RC-FR-013 — Target Progress Gösterme

Target değeri varsa sistem counter progress bilgisini gösterebilmelidir.

Progress gösterimi:

- Yüzde
- Progress bar
- Current / Target label

şeklinde olabilir.

---

### RC-FR-014 — Target'a Ulaşma Durumu

Counter target değerine ulaştığında sistem bunu kullanıcıya gösterebilmelidir.

Target'a ulaşmak project'i otomatik tamamlamamalıdır.

---

### RC-FR-015 — Target Aşımı

Counter target değerini aşarsa sistem güvenli davranmalıdır.

Beklenen:

- Uygulama crash olmamalıdır.
- Counter değeri korunmalıdır.
- Progress 100 üzeri olarak veya capped gösterilebilir.
- Kullanıcıya aşım olduğu anlaşılır şekilde gösterilebilir.

---

### RC-FR-016 — Counter Resetleme

Kullanıcı counter değerini resetleyebilmelidir.

Reset davranışı:

```text
currentValue = initialValue
```

Reset işlemi kullanıcı onayı gerektirebilir.

---

### RC-FR-017 — Basic Undo

Sistem son counter değişikliğini geri alma desteği sunmalıdır.

V1 minimum:

- Son increment geri alınabilir.
- Son decrement geri alınabilir.
- Son reset geri alınabilir, mümkünse.

Undo süresi kısa olabilir.

---

### RC-FR-018 — Counter Silme

Kullanıcı counter'ı silebilmelidir.

V1 önerisi:

```text
Counter soft delete ile silinmelidir.
```

Silme işleminde:

- `deletedAt` doldurulmalıdır.
- Counter normal listelerde görünmemelidir.
- Project silinmedikçe diğer project verileri etkilenmemelidir.

---

### RC-FR-019 — Counter Arşivleme

Counter için ayrı archive davranışı V1'de zorunlu değildir.

V1 önerisi:

```text
Counter lifecycle için active + deleted yeterlidir.
```

Ancak ileride completed/archived counter state eklenebilir.

---

### RC-FR-020 — Project Detail Counter Section

Project Detail ekranı, projeye bağlı counter özetini göstermelidir.

Gösterilebilecek bilgiler:

- Counter sayısı
- Primary counter
- Güncel değer
- Target varsa progress
- Son güncelleme bilgisi
- Add Counter aksiyonu

---

### RC-FR-021 — Counter Listesi

Sistem project'e bağlı counterları listeleyebilmelidir.

Liste varsayılan olarak:

```text
deletedAt == null
projectId == currentProjectId
```

kayıtlarını göstermelidir.

---

### RC-FR-022 — Multiple Counter Desteği

Bir project birden fazla counter'a sahip olabilmelidir.

Örnekler:

- Gövde
- Sağ kol
- Sol kol
- Yaka
- Pattern repeat

---

### RC-FR-023 — Primary Counter

V1'de project için primary counter desteği opsiyoneldir.

Eğer desteklenirse:

- Project progress için primary counter kullanılabilir.
- Kullanıcı primary counter seçebilir.
- Aynı project içinde yalnızca bir primary counter olabilir.

---

### RC-FR-024 — Part ile İlişkilendirme

Counter, opsiyonel olarak bir project part ile ilişkilendirilebilmelidir.

Bu alan Multi-Part Tracking özelliği ile uyumlu olmalıdır.

Alan:

```text
partId
```

Part silinirse counter silinmemelidir; broken relationship safe state uygulanmalıdır.

---

### RC-FR-025 — Counter Notes

Kullanıcı counter'a kısa not ekleyebilir.

V1'de opsiyoneldir.

Not örnekleri:

- Her 4 satırda artır.
- 12. turdan sonra renk değiştir.
- Kol için tekrar başlatıldı.

Counter notes analytics veya logs içine gönderilmemelidir.

---

### RC-FR-026 — Haptic Feedback

Mobil cihaz destekliyorsa increment/decrement sonrası haptic feedback verilebilir.

V1 için öneri:

```text
Could
```

Bu davranış kullanıcı ayarlarına bağlı olabilir.

---

### RC-FR-027 — Sound Feedback

Sesli feedback V1 için zorunlu değildir.

Kullanıcı deneyimini bozabileceği için default kapalı olmalıdır.

---

### RC-FR-028 — Offline Increment

Kullanıcı internet bağlantısı yokken counter artırabilmelidir.

Beklenen:

- Counter lokal güncellenir.
- Sync status pending olur.
- Kullanıcı çalışmaya devam eder.

---

### RC-FR-029 — Offline Decrement

Kullanıcı internet bağlantısı yokken counter azaltabilmelidir.

Beklenen:

- Counter lokal güncellenir.
- Sync status pending olur.
- Kullanıcı çalışmaya devam eder.

---

### RC-FR-030 — Offline Manual Edit

Kullanıcı offline durumda counter değerini manuel değiştirebilmelidir.

Değişiklik lokal saklanmalı ve sync queue'ya alınmalıdır.

---

### RC-FR-031 — Sync Status Gösterme

Counter sync bekliyorsa veya failed durumdaysa UI bunu gösterebilmelidir.

Örnek statuslar:

```text
synced
pending
failed
conflict
```

---

### RC-FR-032 — Sync Retry

Counter sync başarısız olursa sistem uygun zamanda tekrar denemelidir.

Retry işlemi duplicate increment üretmemelidir.

---

### RC-FR-033 — Counter Conflict Handling

Aynı counter iki cihazda offline güncellenirse conflict oluşabilir.

V1 için conflict stratejisi açıkça tanımlanmalıdır.

Öneriler:

- Increment operation tabanlı merge
- Last write wins
- Kullanıcı seçimi
- Higher value wins, önerilmez çünkü her zaman doğru değildir

V1 için en güvenli öneri:

```text
Operation-based sync mümkünse tercih edilmeli.
Değilse conflict state gösterilmeli.
```

---

### RC-FR-034 — Increment Operation Log

Sistem increment/decrement işlemlerini sync güvenliği için operation olarak kaydedebilir.

V1 için UI history zorunlu değildir, ancak sync engine için operation log önerilir.

Operation örnekleri:

```text
increment
decrement
manual_set
reset
undo
```

---

### RC-FR-035 — Counter Detail Ekranı

Counter detail ekranı şu bilgileri göstermelidir:

- Counter adı
- Counter tipi
- Güncel değer
- Step değeri
- Target değeri
- Progress
- Bağlı project
- Bağlı part, varsa
- Sync status
- Increment / decrement aksiyonları
- Edit / reset / delete aksiyonları

---

### RC-FR-036 — Quick Increment UI

Counter increment aksiyonu hızlı erişilebilir olmalıdır.

Özellikle Project Detail veya Counter Detail ekranında `+` aksiyonu belirgin olmalıdır.

---

### RC-FR-037 — Accidental Tap Protection

Sistem yanlışlıkla tap durumlarını azaltacak UX sunmalıdır.

V1 yöntemleri:

- Büyük ama kontrollü tap area
- Decrement butonu
- Undo snackbar
- Haptic feedback
- Reset için confirmation

Increment için confirmation istenmemelidir; bu akışı yavaşlatır.

---

### RC-FR-038 — Counter Edit

Kullanıcı counter metadata alanlarını düzenleyebilmelidir.

Düzenlenebilir alanlar:

- Name
- Counter type
- Step value
- Initial value
- Target value
- Notes
- Linked part
- Primary flag, varsa

---

### RC-FR-039 — Counter Value Persistence

Counter değeri her değişiklikte lokal olarak saklanmalıdır.

Uygulama kapanırsa son başarılı lokal değer korunmalıdır.

---

### RC-FR-040 — Project Silinirse Counter Davranışı

Project soft delete edilirse bağlı counterlar normal counter listelerinde görünmemelidir.

Project recover edilirse counterlar tekrar erişilebilir olmalıdır.

Project permanent delete edilirse bağlı counterlar da cleanup sürecine dahil edilmelidir.

---

# 6. Fonksiyonel Olmayan Gereksinimler

### RC-NFR-001 — Increment Performansı

Counter increment aksiyonu kullanıcıya anlık hissettirmelidir.

UI update hedefi:

```text
< 100 ms perceived update
```

Remote sync bu aksiyonu bloklamamalıdır.

---

### RC-NFR-002 — Lokal Persistence Güvenilirliği

Counter değeri lokal kaydedilmeden işlem başarılı sayılmamalıdır.

UI optimistic update kullanabilir, ancak lokal save başarısız olursa kullanıcı bilgilendirilmelidir.

---

### RC-NFR-003 — Offline-First

Counter update için internet bağlantısı zorunlu olmamalıdır.

---

### RC-NFR-004 — Veri Kaybı Önleme

Counter değeri sessizce kaybedilmemelidir.

Özellikle:

- Uygulama kapanması
- Network kesilmesi
- Remote sync hatası
- App background'a alınması

durumlarında lokal değer korunmalıdır.

---

### RC-NFR-005 — Concurrency Güvenliği

Hızlı art arda tap durumunda counter değeri tutarlı kalmalıdır.

Örnek:

```text
currentValue = 10
Kullanıcı 5 kez hızlı tap yaptı
Beklenen final = 15
```

---

### RC-NFR-006 — Accessibility

Counter increment ve decrement kontrolleri screen reader ile kullanılabilir olmalıdır.

Counter değeri erişilebilir label ile okunmalıdır.

---

### RC-NFR-007 — Touch Target

Increment ve decrement butonları mobil minimum touch target gereksinimlerini karşılamalıdır.

---

### RC-NFR-008 — Localization

Counter tipi, hata mesajları, reset confirmation ve diğer UI metinleri lokalize edilebilir olmalıdır.

---

### RC-NFR-009 — Analytics Privacy

Counter name, notes veya user-entered text analytics'e gönderilmemelidir.

---

### RC-NFR-010 — Security

Counter yalnızca owner tarafından okunabilir ve değiştirilebilir olmalıdır.

---

### RC-NFR-011 — Scalability

Bir kullanıcı en az aşağıdaki hacimleri destekleyebilmelidir:

```text
1000 counter
100 project
20 counter per active project
```

UI bu hacimlerde kullanılabilir kalmalıdır.

---

### RC-NFR-012 — Battery ve Resource Kullanımı

Increment/decrement sırasında gereksiz ağır işlem yapılmamalıdır.

Her tap remote request'e bağımlı olmamalıdır.

Sync batching veya operation compaction değerlendirilebilir.

---

# 7. İş Kuralları

### RC-BR-001 — Counter Ownership

Her counter tam olarak bir kullanıcıya ait olmalıdır.

Ana alan:

```text
ownerId
```

---

### RC-BR-002 — Project Bağlantısı

V1 için counterların project'e bağlı olması önerilir.

Project bağlı counter için `projectId` zorunludur.

Bağımsız counter desteklenirse `projectId` nullable olabilir.

---

### RC-BR-003 — Counter Name Zorunluluğu

Counter adı trim sonrası boş olamaz.

---

### RC-BR-004 — Default Counter Name

Kullanıcı ad girmezse sistem default isim önerebilir.

Örnek:

```text
Counter 1
Row Counter
Round Counter
```

Ancak kayıt öncesi final name valid olmalıdır.

---

### RC-BR-005 — Counter Type

Counter type izin verilen enum değerlerinden biri olmalıdır:

```text
row
round
repeat
increase
decrease
custom
```

---

### RC-BR-006 — Current Value

`currentValue` integer olmalıdır.

V1 önerisi:

```text
currentValue >= 0
```

---

### RC-BR-007 — Initial Value

`initialValue` integer olmalıdır.

V1 önerisi:

```text
initialValue >= 0
```

---

### RC-BR-008 — Step Value

`stepValue` pozitif integer olmalıdır.

```text
stepValue > 0
```

---

### RC-BR-009 — Target Value

`targetValue` opsiyoneldir.

Eğer doluysa:

```text
targetValue > 0
```

olmalıdır.

---

### RC-BR-010 — Target Auto Complete Etmez

Counter target'a ulaşınca project otomatik completed yapılmamalıdır.

---

### RC-BR-011 — Reset Davranışı

Reset işlemi:

```text
currentValue = initialValue
```

şeklinde çalışmalıdır.

---

### RC-BR-012 — Undo Son İşlem İçindir

V1 basic undo yalnızca son counter operation için geçerli olabilir.

---

### RC-BR-013 — Deleted Counter Normal Listede Görünmez

`deletedAt != null` olan counterlar normal counter listesinde gösterilmemelidir.

---

### RC-BR-014 — Project Delete Counterları Gizler

Project soft delete olduğunda bağlı counterlar normal project context içinde görünmez.

---

### RC-BR-015 — Counter Unlink Project'i Silmez

Counter silme veya kaldırma project kaydını silmemelidir.

---

### RC-BR-016 — Part Silinirse Counter Silinmez

Counter'ın bağlı olduğu part silinirse counter silinmemelidir.

UI broken part relationship state gösterebilir.

---

### RC-BR-017 — Active Project Limit Counter'ı Etkilemez

Free active project limiti counter sayısını doğrudan engellememelidir.

İleride counter sayısı Premium limitine bağlanabilir, ancak V1 kapsamı değildir.

---

### RC-BR-018 — Increment Remote Sync Beklemez

Increment işlemi remote sync başarısını beklememelidir.

---

### RC-BR-019 — Duplicate Tap Kaybolmamalı

Hızlı tap durumunda her geçerli tap sayılmalıdır.

---

### RC-BR-020 — Analytics Eventleri İçerik Göndermez

Counter name ve notes analytics eventlerinde yer almamalıdır.

---

# 8. Validasyon Kuralları

### RC-VR-001 — Counter Name

Counter name trim sonrası boş olamaz.

Önerilen limit:

```text
Minimum: 1 karakter
Maximum: 80 karakter
```

---

### RC-VR-002 — Counter Type

Counter type yalnızca desteklenen enum değerlerinden biri olabilir.

---

### RC-VR-003 — Current Value

`currentValue` integer olmalıdır.

V1 önerisi:

```text
currentValue >= 0
```

---

### RC-VR-004 — Initial Value

`initialValue` integer olmalıdır.

V1 önerisi:

```text
initialValue >= 0
```

---

### RC-VR-005 — Step Value

`stepValue` pozitif integer olmalıdır.

```text
stepValue >= 1
```

Önerilen maksimum:

```text
100
```

---

### RC-VR-006 — Target Value

Target value opsiyoneldir.

Eğer girilirse:

```text
targetValue >= 1
```

olmalıdır.

---

### RC-VR-007 — Target Current'dan Küçük Olabilir mi?

V1 önerisi:

```text
Target currentValue'dan küçük olabilir ama UI bunu target exceeded olarak göstermelidir.
```

Bu durum özellikle kullanıcı target'ı sonradan düşürürse oluşabilir.

---

### RC-VR-008 — Notes Length

Counter notes için önerilen maksimum uzunluk:

```text
1000 karakter
```

---

### RC-VR-009 — Part Relationship

`partId` doluysa:

- Aynı owner'a ait olmalıdır.
- Aynı project içinde olmalıdır.
- Deleted part olmamalıdır veya broken state uygulanmalıdır.

---

### RC-VR-010 — Project Relationship

`projectId` doluysa:

- Project aynı owner'a ait olmalıdır.
- Project soft deleted olmamalıdır.
- Project archived ise counter edit policy uygulanmalıdır.

---

# 9. Hata Yönetimi

### RC-ER-001 — Counter Create Failure

Counter oluşturma başarısız olursa:

- Kullanıcı girdisi korunmalıdır.
- Anlaşılır hata gösterilmelidir.
- Duplicate counter oluşmamalıdır.

---

### RC-ER-002 — Increment Local Save Failure

Increment sonrası lokal save başarısız olursa:

- Kullanıcı bilgilendirilmelidir.
- UI tutarlı hale getirilmelidir.
- Veri kaybı riski açıkça yönetilmelidir.

---

### RC-ER-003 — Decrement Minimum Hatası

Counter minimum değerin altına düşmeye çalışırsa:

- İşlem engellenmelidir.
- UI hafif feedback verebilir.
- Hata modal olmak zorunda değildir.

---

### RC-ER-004 — Sync Failure

Remote sync başarısız olursa:

- Lokal counter değeri korunmalıdır.
- `syncStatus = failed` veya `pending` olmalıdır.
- Retry mümkün olmalıdır.

---

### RC-ER-005 — Conflict

Counter conflict oluşursa:

- Lokal veri sessizce silinmemelidir.
- Kullanıcıya conflict state gösterilebilir.
- SyncStatus `conflict` yapılmalıdır.

---

### RC-ER-006 — Unauthorized Counter Access

Kullanıcı başka kullanıcıya ait counter'a erişemez.

Yetkisiz durumda:

- Counter verisi gösterilmez.
- Generic not-found veya access denied state gösterilir.

---

### RC-ER-007 — Missing Project

Counter'ın bağlı olduğu project yoksa:

- Counter ekranı crash olmamalıdır.
- Missing project state gösterilebilir.
- Counter silinmemelidir.

---

### RC-ER-008 — Missing Part

Counter'ın bağlı olduğu part yoksa:

- Counter çalışmaya devam edebilir.
- Broken part relationship state gösterilebilir.
- Kullanıcı part ilişkisinin kaldırılmasını seçebilir.

---

### RC-ER-009 — Reset Confirmation Cancelled

Kullanıcı reset confirmation'dan vazgeçerse:

- Counter değeri değişmemelidir.

---

# 10. Edge Case'ler

### RC-EC-001 — Hızlı Çoklu Tap

Kullanıcı increment butonuna çok hızlı 10 kez basar.

Beklenen:

- 10 geçerli tap sayılır.
- Final value doğru olur.
- UI donmaz.
- Sync operation duplicate veya kayıp üretmez.

---

### RC-EC-002 — Uygulama Increment Sırasında Kapanır

Beklenen:

- Son başarılı lokal save korunur.
- Bozuk counter state oluşmaz.
- Açılışta counter güvenli değerle gelir.

---

### RC-EC-003 — Offline Çoklu Increment

Kullanıcı offline iken counter'ı 50 kez artırır.

Beklenen:

- Lokal değer doğru güncellenir.
- Sync pending operation oluşur.
- Online olunca remote doğru sonuca ulaşır.

---

### RC-EC-004 — İki Cihazda Offline Increment

Aynı counter iki cihazda offline artırılır.

Beklenen:

- Conflict veya operation-based merge uygulanır.
- Bir cihazın değeri sessizce kaybolmaz.

---

### RC-EC-005 — Target Sıfır Girilir

Beklenen:

- Target value reddedilir.
- Progress hesaplanmaz.

---

### RC-EC-006 — Step Sıfır Girilir

Beklenen:

- Step value reddedilir.
- Counter oluşturulmaz veya update edilmez.

---

### RC-EC-007 — Step Çok Büyük Girilir

Beklenen:

- Maksimum step validasyonu uygulanır.
- Kullanıcı uyarılır.

---

### RC-EC-008 — Decrement Minimum Altına İner

Örnek:

```text
currentValue = 0
stepValue = 1
```

Beklenen:

- Decrement engellenir.
- Value negatif olmaz.

---

### RC-EC-009 — Reset Yanlışlıkla Seçilir

Beklenen:

- Confirmation gösterilir.
- Kullanıcı cancel ederse value değişmez.
- Onaylarsa reset uygulanır.

---

### RC-EC-010 — Linked Project Deleted

Counter'ın bağlı olduğu project soft delete edilir.

Beklenen:

- Counter normal project context'te görünmez.
- Project recover edilirse counter tekrar görünür.

---

### RC-EC-011 — Linked Part Deleted

Beklenen:

- Counter silinmez.
- Part relationship unavailable state gösterilir.

---

### RC-EC-012 — Counter Deleted Deep Link

Kullanıcı deleted counter deep link açar.

Beklenen:

- Normal counter detail gösterilmez.
- Safe not-found state gösterilir.

---

### RC-EC-013 — Unknown Counter Type

Remote'dan bilinmeyen counter type gelir.

Beklenen:

- Uygulama crash olmaz.
- Safe fallback veya migration error üretilir.

---

# 11. Acceptance Criteria

### RC-AC-001 — Project İçinden Counter Oluşturma

**Given** kullanıcı project detail ekranındadır  
**When** kullanıcı geçerli bilgilerle counter oluşturur  
**Then** counter oluşturulur  
**And** `projectId` otomatik atanır  
**And** counter project detail içinde görünür.

---

### RC-AC-002 — Counter Adı Boş Olamaz

**Given** kullanıcı counter oluşturma formundadır  
**When** counter name boş veya sadece whitespace içerir  
**Then** counter oluşturulmaz  
**And** validasyon mesajı gösterilir.

---

### RC-AC-003 — Increment Başarılıdır

**Given** counter currentValue `10` ve stepValue `1` değerindedir  
**When** kullanıcı increment aksiyonuna basar  
**Then** currentValue `11` olur  
**And** lokal kayıt güncellenir.

---

### RC-AC-004 — Decrement Başarılıdır

**Given** counter currentValue `10` ve stepValue `1` değerindedir  
**When** kullanıcı decrement aksiyonuna basar  
**Then** currentValue `9` olur  
**And** lokal kayıt güncellenir.

---

### RC-AC-005 — Minimum Altına Decrement Engellenir

**Given** counter currentValue `0` değerindedir  
**When** kullanıcı decrement aksiyonuna basar  
**Then** currentValue negatif olmaz  
**And** kullanıcıya uygun feedback verilir.

---

### RC-AC-006 — Step Değeri Uygulanır

**Given** counter currentValue `10` ve stepValue `5` değerindedir  
**When** kullanıcı increment aksiyonuna basar  
**Then** currentValue `15` olur.

---

### RC-AC-007 — Manuel Value Update

**Given** kullanıcı counter edit ekranındadır  
**When** currentValue değerini geçerli bir değerle değiştirir  
**Then** yeni değer kaydedilir  
**And** UI güncellenir.

---

### RC-AC-008 — Target Progress Gösterilir

**Given** counter currentValue `30` ve targetValue `100` değerindedir  
**When** counter detail açılır  
**Then** progress `30%` olarak hesaplanır veya eşdeğer gösterilir.

---

### RC-AC-009 — Target Zorunlu Değildir

**Given** kullanıcı targetValue girmemiştir  
**When** counter oluşturur  
**Then** counter başarıyla oluşturulur  
**And** progress alanı no-target state gösterebilir.

---

### RC-AC-010 — Reset Confirmation

**Given** counter currentValue initialValue'dan farklıdır  
**When** kullanıcı reset seçer  
**Then** confirmation gösterilir  
**When** kullanıcı onaylar  
**Then** currentValue initialValue olur.

---

### RC-AC-011 — Undo Son İşlemi Geri Alır

**Given** kullanıcı increment yapmıştır  
**When** undo aksiyonuna basar  
**Then** counter değeri önceki değere döner.

---

### RC-AC-012 — Counter Soft Delete

**Given** kullanıcı counter silmeyi onaylar  
**When** işlem başarılı olur  
**Then** `deletedAt` dolar  
**And** counter normal listede görünmez.

---

### RC-AC-013 — Multiple Counter

**Given** bir project vardır  
**When** kullanıcı aynı project altında birden fazla counter oluşturur  
**Then** tüm counterlar project counter listesinde görünür.

---

### RC-AC-014 — Offline Increment

**Given** cihaz offline durumdadır  
**When** kullanıcı increment yapar  
**Then** counter lokal güncellenir  
**And** `syncStatus = pending` olur.

---

### RC-AC-015 — Sync Failure Veri Kaybettirmez

**Given** counter lokal güncellenmiştir  
**When** remote sync başarısız olur  
**Then** lokal counter değeri korunur  
**And** sync retry mümkün olur.

---

### RC-AC-016 — Unauthorized Access Engellenir

**Given** kullanıcı başka kullanıcıya ait counter'a erişmeye çalışır  
**When** counter detail istenir  
**Then** counter verisi gösterilmez.

---

### RC-AC-017 — Missing Project Crash Üretmez

**Given** counter bağlı olduğu project'i bulamaz  
**When** counter detail açılır  
**Then** ekran crash olmaz  
**And** safe missing project state gösterilir.

---

### RC-AC-018 — Analytics İçerik Göndermez

**Given** counter name ve notes doludur  
**When** analytics event gönderilir  
**Then** counter name ve notes payload içinde bulunmaz.

---

### RC-AC-019 — Project Soft Delete Counterları Gizler

**Given** project soft deleted olmuştur  
**When** project context counter listesi açılır  
**Then** bağlı counterlar normal project counter listesinde görünmez.

---

### RC-AC-020 — Hızlı Tap Doğru Sayılır

**Given** currentValue `0` ve stepValue `1`  
**When** kullanıcı hızlıca 10 kez increment yapar  
**Then** final currentValue `10` olur.

---

# 12. Requirement Traceability

| Kullanıcı İhtiyacı | Gereksinimler |
|---|---|
| Counter oluşturmak | RC-FR-001 - RC-FR-006 |
| Counter değerini yönetmek | RC-FR-007 - RC-FR-011 |
| Target ve progress görmek | RC-FR-012 - RC-FR-015 |
| Reset ve undo yapmak | RC-FR-016 - RC-FR-017 |
| Counter silmek | RC-FR-018 - RC-FR-019 |
| Project içinde counter görmek | RC-FR-020 - RC-FR-024 |
| Offline kullanmak | RC-FR-028 - RC-FR-033 |
| Sync güvenliği | RC-FR-031 - RC-FR-034 |
| Counter detail ve edit | RC-FR-035 - RC-FR-038 |
| Veri kalıcılığı | RC-FR-039 - RC-FR-040 |

---

# 13. Açık Ürün Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-OD-001 | Bağımsız counter V1'de olacak mı? | Project bağlı öncelikli | Open |
| RC-OD-002 | Default initialValue 0 mı 1 mi? | 1 önerilir | Open |
| RC-OD-003 | Undo V1'e dahil mi? | Basit undo dahil | Open |
| RC-OD-004 | Primary counter olacak mı? | Opsiyonel | Open |
| RC-OD-005 | Counter history UI olacak mı? | V1 dışında | Open |
| RC-OD-006 | Counter soft delete recovery olacak mı? | Project recovery ile uyumlu | Open |
| RC-OD-007 | Operation-based sync uygulanacak mı? | Önerilir | Open |
| RC-OD-008 | Haptic feedback default açık mı? | Açık olabilir | Open |

---

# 14. Bağımlılıklar

Row Counter şu feature'larla ilişkilidir:

- `feature-001-project-management`
- `feature-003-multi-part-tracking`
- `feature-004-pattern-library`
- `feature-017-local-persistence`
- `feature-018-cloud-sync`
- `feature-020-statistics`
- `feature-014-premium`, ileride counter limiti eklenirse

---

# 15. Definition of Ready

Row Counter geliştirmeye hazır sayılırsa:

- Default initial value kararı verilmiş olmalı.
- Counter type enumları onaylanmış olmalı.
- Project relationship modeli netleşmiş olmalı.
- Offline sync stratejisi belirlenmiş olmalı.
- Undo kapsamı netleşmiş olmalı.
- Counter data model hazırlanmış olmalı.
- Analytics privacy kuralları tanımlanmış olmalı.
- Security ownership kuralları tanımlanmış olmalı.
- Test kapsamı hazırlanmış olmalı.

---

# 16. Definition of Done

Row Counter tamamlanmış sayılırsa:

- Counter create çalışıyor.
- Counter increment/decrement çalışıyor.
- Manuel value edit çalışıyor.
- Step value uygulanıyor.
- Target progress hesaplanıyor.
- Reset çalışıyor.
- Undo çalışıyor veya bilinçli ertelenmiş durumda.
- Soft delete çalışıyor.
- Project detail içinde counter section çalışıyor.
- Multiple counter destekleniyor.
- Offline increment/decrement çalışıyor.
- Sync failure veri kaybettirmiyor.
- Ownership güvenliği test edilmiş durumda.
- Analytics PII içermiyor.
- Accessibility kontrolleri geçiyor.
- Localization tamamlanmış durumda.
- Unit, integration ve acceptance testleri geçiyor.
- Product Owner kabul vermiş durumda.

---

# 17. Referanslar

- `overview.md`
- `flows.md`
- `data-model.md`
- `analytics.md`
- `security.md`
- `implementation-notes.md`
- `testing.md`
- `../feature-001-project-management/`
- `../feature-003-multi-part-tracking/`
- `../feature-017-local-persistence/`
- `../feature-018-cloud-sync/`

---

# 18. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/flows.md
```
