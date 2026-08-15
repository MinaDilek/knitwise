# Row Counter — Flows

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/flows.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliği için kullanıcı akışlarını, sistem davranışlarını, alternatif yolları, hata durumlarını ve ekranlar arası geçişleri tanımlar.

Bu dosya aşağıdaki sorulara cevap verir:

- Kullanıcı project içinden counter'ı nasıl oluşturur?
- Kullanıcı counter değerini nasıl artırır veya azaltır?
- Kullanıcı yanlış sayaç değişikliğini nasıl düzeltir?
- Kullanıcı counter değerini manuel nasıl günceller?
- Target değeri varsa progress nasıl gösterilir?
- Counter resetleme nasıl çalışır?
- Counter silme nasıl çalışır?
- Offline durumda increment/decrement nasıl davranır?
- Sync conflict olduğunda kullanıcı deneyimi nasıl olmalıdır?
- Counter project veya part ilişkisi bozulursa ne olur?

Bu doküman `requirements.md` içindeki gereksinimlerin kullanıcı yolculuklarına dönüştürülmüş halidir.

---

## 2. Genel Akış Prensipleri

Row Counter akışları şu prensiplere göre tasarlanmalıdır:

1. Increment ve decrement işlemleri çok hızlı olmalıdır.
2. Kullanıcı sayaç artırmak için gereksiz onaylarla yavaşlatılmamalıdır.
3. Reset ve delete gibi riskli işlemler onay istemelidir.
4. Counter değeri lokal olarak güvenli saklanmalıdır.
5. Remote sync increment deneyimini bloklamamalıdır.
6. Offline kullanım normal bir kullanım senaryosu olarak ele alınmalıdır.
7. Yanlış tap durumlarında kullanıcıya düzeltme imkanı verilmelidir.
8. Counter project bağlamını korumalıdır.
9. Counter optional olarak part bağlamını desteklemelidir.
10. Broken relationship durumları uygulamayı çökertmemelidir.

---

## 3. İlgili Ekranlar ve Bileşenler

| Ekran / Bileşen | Açıklama |
|---|---|
| Project Detail Counter Section | Project içindeki counter özet alanı |
| Counter List Screen | Project'e bağlı counter listesi |
| Counter Detail Screen | Tek counter'ın ana kullanım ekranı |
| Quick Create Counter Sheet | Hızlı counter oluşturma formu |
| Detailed Counter Form | Counter detaylı oluşturma/düzenleme formu |
| Counter Value Display | Büyük sayaç değeri göstergesi |
| Increment Button | Counter değerini artıran ana aksiyon |
| Decrement Button | Counter değerini azaltan aksiyon |
| Undo Snackbar | Son counter değişikliğini geri alma aksiyonu |
| Reset Confirmation Dialog | Counter reset onayı |
| Delete Confirmation Dialog | Counter silme onayı |
| Sync Status Indicator | Counter sync durumunu gösteren bileşen |
| Broken Project State | Counter'ın bağlı project'i bulunamazsa gösterilir |
| Broken Part State | Counter'ın bağlı part'ı bulunamazsa gösterilir |
| Target Progress Indicator | Target varsa progress gösterimi |

---

## 4. Aktörler

| Aktör | Açıklama |
|---|---|
| Project Owner | Counter'ın bağlı olduğu project'in sahibi |
| Free User | Ücretsiz plan kullanıcısı |
| Premium User | Premium plan kullanıcısı |
| Offline User | İnternet bağlantısı olmayan kullanıcı |
| Returning User | Daha önce counter oluşturmuş kullanıcı |
| Beginner User | Basit tek sayaç kullanan yeni kullanıcı |
| Advanced User | Birden fazla counter ve target kullanan kullanıcı |

---

# 5. Counter Oluşturma Akışları

---

## FLOW-RC-001 — Project İçinden İlk Counter Oluşturma

### Amaç

Kullanıcının bir project içinde hızlıca ilk counter'ını oluşturmasını sağlamak.

### Başlangıç Koşulları

- Kullanıcı Project Detail ekranındadır.
- Project mevcut kullanıcıya aittir.
- Project soft deleted değildir.
- Project archived ise counter oluşturma policy izin vermelidir veya restore gerekebilir.

### Ana Akış

1. Kullanıcı Project Detail ekranında Counter section'ı görür.
2. Project'te henüz counter yoksa empty counter state gösterilir.
3. Kullanıcı `Add Counter` aksiyonuna basar.
4. Sistem Quick Create Counter Sheet açar.
5. Sistem `projectId` değerini context'ten otomatik alır.
6. Kullanıcı counter adını girer.
7. Kullanıcı counter tipini seçer veya default bırakır.
8. Sistem başlangıç değerini default olarak belirler.
9. Sistem step değerini default olarak belirler.
10. Kullanıcı `Create` aksiyonuna basar.
11. Sistem validasyonları çalıştırır.
12. Sistem counter'ı lokal kaydeder.
13. Sistem sync operation oluşturur.
14. Counter Project Detail içindeki counter section'da görünür.
15. Kullanıcı isterse Counter Detail ekranına yönlendirilir.

### Başarılı Sonuç

- Counter oluşturulur.
- `projectId` otomatik atanır.
- Counter lokal olarak saklanır.
- Counter project context içinde görünür.

### Alternatif Akışlar

#### A1 — Kullanıcı Detaylı Forma Geçer

1. Kullanıcı Quick Create içinden `More Options` seçer.
2. Detailed Counter Form açılır.
3. Kullanıcı target, step, initial value ve notes girer.
4. Ana akış validasyon adımından devam eder.

#### A2 — Kullanıcı Vazgeçer

1. Kullanıcı sheet'i kapatır.
2. Veri girilmişse sistem discard confirmation gösterebilir.
3. Counter oluşturulmaz.

### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Counter adı boş | Validasyon mesajı |
| Step geçersiz | Validasyon mesajı |
| Project bulunamadı | Counter oluşturulmaz |
| Unauthorized project | Counter oluşturulmaz |
| Lokal kayıt başarısız | Kullanıcı girdisi korunur |
| Remote sync başarısız | Counter lokal kalır, sync pending/failed olur |

---

## FLOW-RC-002 — Detaylı Counter Oluşturma

### Amaç

Kullanıcının counter'ı gelişmiş ayarlarla oluşturmasını sağlamak.

### Ana Akış

1. Kullanıcı `Add Counter` aksiyonunu seçer.
2. Detailed Counter Form açılır.
3. Sistem project context'i otomatik doldurur.
4. Kullanıcı counter name girer.
5. Kullanıcı counter type seçer:
   - `row`
   - `round`
   - `repeat`
   - `increase`
   - `decrease`
   - `custom`
6. Kullanıcı initial value belirler.
7. Kullanıcı step value belirler.
8. Kullanıcı target value belirleyebilir.
9. Kullanıcı part seçebilir.
10. Kullanıcı notes ekleyebilir.
11. Kullanıcı save eder.
12. Sistem validasyonları çalıştırır.
13. Sistem owner ve project doğrulaması yapar.
14. Sistem counter'ı lokal kaydeder.
15. Sistem sync operation oluşturur.
16. Kullanıcı Counter Detail veya Project Detail'e yönlendirilir.

### Alternatif Akışlar

#### A1 — Target Boş Bırakılır

- Counter oluşturulur.
- Progress no-target state gösterir.

#### A2 — Part Seçilmez

- Counter yalnızca project'e bağlı olur.

#### A3 — Custom Type Seçilir

- Kullanıcı custom label girebilir, ürün kararı onaylandıysa.
- Custom text analytics'e gönderilmez.

---

## FLOW-RC-003 — Bağımsız Counter Oluşturma

### Amaç

V1'de desteklenirse, kullanıcının project seçmeden counter oluşturmasını sağlamak.

### Ürün Notu

V1 için project bağlı counter önceliklidir. Bağımsız counter opsiyonel karardır.

### Ana Akış

1. Kullanıcı global counter ekranından `Create Counter` seçer.
2. Sistem project seçimi olmadan form açar.
3. Kullanıcı counter bilgilerini girer.
4. Sistem `projectId = null` olarak kaydedebilir.
5. Counter independent list içinde görünür.
6. Kullanıcı daha sonra project'e bağlayabilir.

### Risk

Bağımsız counter, Knitwise'ın project merkezli yapısını zayıflatabilir. V1'de ertelenmesi uygundur.

---

# 6. Counter Kullanım Akışları

---

## FLOW-RC-004 — Counter Detail Ekranına Girme

### Amaç

Kullanıcının counter'ı ana sayaç ekranında kullanmasını sağlamak.

### Başlangıç Koşulları

- Counter mevcut kullanıcıya aittir.
- Counter soft deleted değildir.
- Bağlı project mevcut veya broken state ile yönetilebilir durumdadır.

### Ana Akış

1. Kullanıcı Project Detail içinden counter seçer.
2. Sistem `counterId` ile counter kaydını yükler.
3. Sistem `ownerId` kontrolü yapar.
4. Sistem `deletedAt` kontrolü yapar.
5. Sistem bağlı project'i yükler.
6. Sistem bağlı part varsa yükler.
7. Counter Detail ekranı açılır.
8. Büyük counter value gösterilir.
9. Increment/decrement aksiyonları gösterilir.
10. Target varsa progress gösterilir.
11. Sync status varsa gösterilir.

### Hata Durumları

| Durum | Beklenen Davranış |
|---|---|
| Counter bulunamadı | Safe not-found state |
| Unauthorized | Generic not-found/access denied |
| Counter deleted | Normal detail gösterilmez |
| Project missing | Broken project state |
| Part missing | Broken part state |

---

## FLOW-RC-005 — Counter Increment

### Amaç

Kullanıcının counter değerini hızlıca artırmasını sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail ekranındadır.
2. Kullanıcı `+` butonuna basar.
3. Sistem geçerli counter state'i alır.
4. Sistem yeni değeri hesaplar:

```text
newValue = currentValue + stepValue
```

5. UI değeri anında günceller.
6. Sistem lokal kaydı günceller.
7. `updatedAt` güncellenir.
8. `lastIncrementedAt` güncellenebilir.
9. Sync status `pending` yapılır.
10. Sync operation oluşturulur.
11. Undo snackbar gösterilebilir.
12. Haptic feedback verilebilir.

### Başarılı Sonuç

- Counter value step kadar artar.
- Kullanıcı remote sync beklemez.
- Lokal veri korunur.

### Alternatif Akışlar

#### A1 — Hızlı Çoklu Tap

1. Kullanıcı `+` butonuna hızlıca birçok kez basar.
2. Sistem tap'leri kaybetmeden sıraya alır veya atomik günceller.
3. Final value doğru olur.
4. UI donmaz.

#### A2 — Haptic Kapalı

- Haptic feedback verilmez.
- Counter işleminden etkilenmez.

### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Lokal save başarısız | UI tutarlı hale getirilir, kullanıcı uyarılır |
| Sync başarısız | Lokal değer korunur, sync failed/pending |
| Counter deleted | İşlem engellenir |
| Unauthorized | İşlem engellenir |

---

## FLOW-RC-006 — Counter Decrement

### Amaç

Kullanıcının yanlışlıkla fazla artırdığı counter değerini azaltmasını sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail ekranındadır.
2. Kullanıcı `-` butonuna basar.
3. Sistem yeni değeri hesaplar:

```text
newValue = currentValue - stepValue
```

4. Sistem minimum değer kontrolü yapar.
5. Değer geçerliyse UI güncellenir.
6. Lokal kayıt güncellenir.
7. `updatedAt` güncellenir.
8. `lastDecrementedAt` güncellenebilir.
9. Sync operation oluşturulur.
10. Undo snackbar gösterilebilir.

### Alternatif

#### A1 — Minimum Altına Düşme

Örnek:

```text
currentValue = 0
stepValue = 1
```

Beklenen:

- Decrement uygulanmaz.
- Value negatif olmaz.
- Kullanıcıya hafif feedback verilir.
- Modal hata gösterilmesi zorunlu değildir.

---

## FLOW-RC-007 — Counter Manuel Değer Güncelleme

### Amaç

Kullanıcının counter değerini doğrudan değiştirebilmesini sağlamak.

### Kullanım Nedenleri

- Fiziksel sayaçtan uygulamaya geçiş
- Yanlış değerle başlama
- Önceki günlerden kalan ilerlemeyi ekleme
- Sync conflict sonrası düzeltme
- Target değişikliği sonrası değer düzeltme

### Ana Akış

1. Kullanıcı Counter Detail veya Edit ekranında manual value edit seçer.
2. Sistem mevcut value değerini gösterir.
3. Kullanıcı yeni değeri girer.
4. Sistem integer validasyonu yapar.
5. Sistem minimum kontrolü yapar.
6. Kullanıcı kaydeder.
7. Sistem lokal kaydı günceller.
8. Sync operation `manual_set` olarak oluşturulur.
9. Counter Detail güncel değeri gösterir.

### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Boş değer | Validasyon |
| Negatif değer | Validasyon |
| Integer olmayan değer | Validasyon |
| Çok büyük değer | Validasyon veya uyarı |

---

## FLOW-RC-008 — Undo Akışı

### Amaç

Kullanıcının son counter işlemini hızlıca geri almasını sağlamak.

### Başlangıç Koşulları

- Kullanıcı yeni bir counter operation yapmıştır.
- Undo süresi devam etmektedir.
- Son operation undo desteklemektedir.

### Ana Akış

1. Kullanıcı increment, decrement, reset veya manual set işlemi yapar.
2. Sistem son operation'ı geçici undo state'e alır.
3. UI snackbar veya benzeri yapı gösterir.
4. Kullanıcı `Undo` aksiyonuna basar.
5. Sistem önceki value değerini geri yükler.
6. Lokal kayıt güncellenir.
7. Sync operation `undo` veya corrective update olarak oluşturulur.
8. UI güncel değeri gösterir.

### Alternatif

#### A1 — Undo Süresi Doldu

- Undo aksiyonu kaybolur.
- Kullanıcı manuel edit veya decrement ile düzeltme yapabilir.

#### A2 — Yeni Operation Yapıldı

- Önceki undo hakkı düşebilir.
- En son operation undo edilebilir.

### V1 Notu

Undo için full history şart değildir. V1'de son operation yeterlidir.

---

# 7. Target ve Progress Akışları

---

## FLOW-RC-009 — Target Belirleme

### Amaç

Kullanıcının counter için hedef değer belirlemesini sağlamak.

### Ana Akış

1. Kullanıcı counter create veya edit ekranındadır.
2. Target value alanına değer girer.
3. Sistem target validasyonu yapar.
4. Kullanıcı kaydeder.
5. Counter detail progress göstermeye başlar.

### Validasyon

```text
targetValue >= 1
```

### Alternatif

#### A1 — Target Boş Bırakılır

- Counter yine geçerli kalır.
- Progress no-target state gösterilir.

---

## FLOW-RC-010 — Target Progress Gösterme

### Amaç

Counter'ın hedefe göre ilerleme bilgisini göstermek.

### Ana Akış

1. Counter detail ekranı açılır.
2. Sistem `targetValue` var mı kontrol eder.
3. Target varsa progress hesaplar:

```text
progress = currentValue / targetValue
```

4. Progress bar veya yüzde gösterilir.
5. Current/target label gösterilir.

### Örnek

```text
30 / 100
30%
```

### Edge Durumları

| Durum | Beklenen |
|---|---|
| targetValue null | Progress gösterilmez |
| targetValue 0 | Invalid, progress hesaplanmaz |
| currentValue > targetValue | Target exceeded state |
| currentValue == targetValue | Target reached state |

---

## FLOW-RC-011 — Target'a Ulaşma

### Amaç

Kullanıcının counter hedefe ulaştığında bunu fark etmesini sağlamak.

### Ana Akış

1. Kullanıcı increment yapar.
2. Sistem new value ile target karşılaştırır.
3. `currentValue == targetValue` ise target reached state oluşur.
4. UI küçük başarı feedback gösterebilir.
5. Project otomatik completed yapılmaz.

### Önemli Kural

Target'a ulaşmak:

- Counter'ı otomatik silmez.
- Project'i otomatik completed yapmaz.
- Kullanıcıyı zorunlu akışa sokmaz.

---

# 8. Reset ve Delete Akışları

---

## FLOW-RC-012 — Counter Resetleme

### Amaç

Kullanıcının counter değerini başlangıç değerine döndürmesini sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail action menu açar.
2. `Reset Counter` seçer.
3. Sistem Reset Confirmation Dialog gösterir.
4. Kullanıcı onaylar.
5. Sistem önceki value değerini undo için saklar.
6. `currentValue = initialValue` yapılır.
7. Lokal kayıt güncellenir.
8. Sync operation `reset` oluşturulur.
9. UI yeni değeri gösterir.
10. Undo aksiyonu sunulabilir.

### Alternatif

#### A1 — Kullanıcı Vazgeçer

- Dialog kapanır.
- Counter değeri değişmez.

### Dialog Mesajı

Mesaj şu fikri vermelidir:

```text
Bu sayaç başlangıç değerine dönecek. Bu işlemi kısa süre içinde geri alabilirsiniz.
```

---

## FLOW-RC-013 — Counter Silme

### Amaç

Kullanıcının counter'ı güvenli şekilde kaldırmasını sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail action menu açar.
2. `Delete Counter` seçer.
3. Sistem Delete Confirmation Dialog gösterir.
4. Kullanıcı onaylar.
5. Sistem `deletedAt` doldurur.
6. Counter normal listeden çıkar.
7. Project detail counter section güncellenir.
8. Sync operation `soft_delete` oluşturulur.
9. Kullanıcı Project Detail veya Counter List ekranına döner.

### Alternatif

#### A1 — Kullanıcı Vazgeçer

- Dialog kapanır.
- Counter değişmez.

### V1 Notu

Counter recovery UI V1'de zorunlu değildir. Ancak soft delete data model ileride recovery'ye uygun olmalıdır.

---

# 9. Counter Edit Akışları

---

## FLOW-RC-014 — Counter Metadata Düzenleme

### Amaç

Kullanıcının counter ayarlarını değiştirmesini sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail ekranında `Edit` seçer.
2. Counter Edit Form açılır.
3. Mevcut counter bilgileri forma doldurulur.
4. Kullanıcı alanları değiştirir:
   - Name
   - Type
   - Step
   - Initial value
   - Target
   - Notes
   - Linked part
   - Primary flag, varsa
5. Kullanıcı save eder.
6. Sistem validasyonları çalıştırır.
7. Sistem lokal kaydı günceller.
8. `updatedAt` güncellenir.
9. Sync operation oluşturulur.
10. Counter Detail güncel bilgilerle açılır.

### Alternatif

#### A1 — Kullanıcı Cancel Seçer

- Unsaved changes varsa confirmation gösterilir.
- Kullanıcı discard seçerse değişiklikler atılır.

---

## FLOW-RC-015 — Step Değeri Değiştirme

### Amaç

Kullanıcının increment/decrement miktarını değiştirmesini sağlamak.

### Ana Akış

1. Kullanıcı Counter Edit ekranında step value değiştirir.
2. Sistem step validasyonu yapar.
3. Kullanıcı save eder.
4. Yeni step sonraki increment/decrement işlemlerinde kullanılır.

### Örnek

```text
currentValue = 10
stepValue = 5
increment sonrası currentValue = 15
```

---

# 10. Project ve Part İlişki Akışları

---

## FLOW-RC-016 — Counter'ı Part ile İlişkilendirme

### Amaç

Counter'ın belirli bir project part için kullanılmasını sağlamak.

### Başlangıç Koşulları

- Project içinde part tracking mevcuttur.
- Counter project'e bağlıdır.

### Ana Akış

1. Kullanıcı Counter Edit ekranında linked part alanını açar.
2. Sistem aynı project'e ait part listesini gösterir.
3. Kullanıcı part seçer.
4. Sistem part ownership ve project ilişkisini doğrular.
5. `partId` counter'a atanır.
6. Lokal kayıt güncellenir.
7. Counter detail linked part bilgisini gösterir.

### Alternatif

#### A1 — Part Yok

- Empty part state gösterilir.
- Kullanıcı part oluşturmaya yönlendirilebilir.

---

## FLOW-RC-017 — Linked Part Silinirse

### Amaç

Counter'ın bağlı olduğu part silinse bile counter'ın güvenli kalmasını sağlamak.

### Ana Akış

1. Kullanıcı Counter Detail açar.
2. Sistem `partId` ile part kaydını yüklemeye çalışır.
3. Part bulunamaz veya deleted durumdadır.
4. Counter detail açılmaya devam eder.
5. Broken part relationship state gösterilir.
6. Kullanıcı part ilişkisini kaldırabilir veya yeni part seçebilir.

---

## FLOW-RC-018 — Linked Project Silinirse

### Amaç

Project soft delete olduğunda counter davranışını güvenli yönetmek.

### Ana Akış

1. Project soft delete edilir.
2. Project'e bağlı counterlar normal project context'ten gizlenir.
3. Counterlar kalıcı olarak hemen silinmez.
4. Project recovery yapılırsa counterlar tekrar görünür.

### Deep Link Durumu

Kullanıcı deleted project'e bağlı counter deep link açarsa:

- Normal counter detail gösterilmemelidir.
- Safe not-found veya project deleted state gösterilmelidir.

---

# 11. Offline ve Sync Akışları

---

## FLOW-RC-019 — Offline Increment

### Amaç

Kullanıcının internet olmadan counter artırmasını sağlamak.

### Ana Akış

1. Cihaz offline durumdadır.
2. Kullanıcı increment yapar.
3. UI anında güncellenir.
4. Lokal kayıt güncellenir.
5. Sync status `pending` yapılır.
6. Sync operation lokal queue'ya eklenir.
7. Kullanıcı çalışmaya devam eder.
8. Bağlantı geldiğinde sync engine operation'ları gönderir.

### Başarılı Sonuç

- Kullanıcı internet yokken sayaç kullanabilir.
- Veri kaybolmaz.
- Remote sync sonradan yapılır.

---

## FLOW-RC-020 — Offline Çoklu Increment

### Amaç

Offline durumda çok sayıda counter değişikliğinin doğru saklanmasını sağlamak.

### Ana Akış

1. Kullanıcı offline iken counter'ı birçok kez artırır.
2. Sistem her tap'i güvenli şekilde işler.
3. Lokal final value doğru olur.
4. Sync queue operation bazlı veya compacted update olarak saklar.
5. Bağlantı gelince remote doğru sonuca taşınır.

### Operation Compaction Önerisi

Örnek:

```text
10 increment operation
```

şuna sıkıştırılabilir:

```text
delta +10
```

Ancak conflict stratejisi buna uygun olmalıdır.

---

## FLOW-RC-021 — Sync Retry

### Amaç

Başarısız counter sync işlemlerinin güvenli tekrar denenmesini sağlamak.

### Ana Akış

1. Sistem `syncStatus = failed` veya `pending` counter operation bulur.
2. Connectivity uygun hale gelir.
3. Retry policy çalışır.
4. Operation remote'a gönderilir.
5. Başarılıysa counter `synced` olur.
6. Başarısızsa attempt count artar.
7. Max retry aşılırsa failed state korunur.

---

## FLOW-RC-022 — Counter Conflict

### Amaç

Aynı counter iki cihazda değiştiğinde veri kaybını önlemek.

### Başlangıç Koşulları

- Aynı counter birden fazla cihazda offline değiştirilmiştir.
- Sync sırasında remote ve local state çakışmıştır.

### Ana Akış

1. Sync engine conflict tespit eder.
2. Counter `syncStatus = conflict` yapılır.
3. Kullanıcıya conflict state gösterilir.
4. Kullanıcıya seçenek sunulur:
   - Local value kullan
   - Remote value kullan
   - Manuel value gir
5. Kullanıcı seçim yapar.
6. Sistem seçimi lokal kaydeder.
7. Corrective sync operation oluşturulur.
8. Conflict çözülür.

### V1 Notu

Operation-based merge uygulanabiliyorsa kullanıcıya conflict göstermeden doğru sonuç üretilebilir. Ancak emin olunamıyorsa kullanıcı seçimi daha güvenlidir.

---

# 12. Analytics Akışları

---

## FLOW-RC-023 — Counter Create Analytics

### Ana Akış

1. Kullanıcı counter create başlatır.
2. Sistem `counter_create_started` event gönderir.
3. Counter başarıyla oluşursa `counter_create_completed` event gönderir.
4. Hata olursa `counter_create_failed` event gönderir.

### Privacy

Analytics payload içinde şunlar bulunmamalıdır:

- Counter name
- Notes
- Project name
- Part name

---

## FLOW-RC-024 — Increment Analytics

### Amaç

Counter kullanım yoğunluğunu event spam oluşturmadan ölçmek.

### V1 Önerisi

Her increment için analytics event göndermek yerine:

- Session bazlı aggregate
- Counter detail kapanınca summary
- Belirli eşiklerde event
- Günlük usage metric

kullanılabilir.

### Güvenli Payload

- Counter type
- Has target
- Increment count bucket
- Is offline
- Source screen

Counter adı gönderilmez.

---

# 13. User Journey Özetleri

## 13.1 Yeni Kullanıcı Basit Counter

```text
Project Detail
→ Empty Counter Section
→ Add Counter
→ Quick Create
→ Counter Detail
→ Increment
```

## 13.2 Target Kullanan Kullanıcı

```text
Project Detail
→ Add Counter
→ Detailed Form
→ Target gir
→ Save
→ Counter Detail
→ Progress gör
```

## 13.3 Offline Kullanıcı

```text
Counter Detail
→ Offline Increment
→ Local Save
→ Pending Sync
→ Connectivity Restored
→ Sync Completed
```

## 13.4 Yanlış Tap Düzeltme

```text
Increment
→ Undo Snackbar
→ Undo
→ Previous Value Restored
```

## 13.5 Broken Part

```text
Open Counter
→ Linked Part Missing
→ Broken Part State
→ Remove Link or Select New Part
```

---

# 14. Flow ve Requirement Eşleştirmesi

| Flow ID | İlgili Requirements |
|---|---|
| FLOW-RC-001 | RC-FR-001, RC-FR-002, RC-FR-004, RC-FR-005 |
| FLOW-RC-002 | RC-FR-001, RC-FR-006, RC-FR-010, RC-FR-012, RC-FR-024 |
| FLOW-RC-003 | RC-FR-003 |
| FLOW-RC-004 | RC-FR-035 |
| FLOW-RC-005 | RC-FR-008, RC-FR-028, RC-FR-039 |
| FLOW-RC-006 | RC-FR-009, RC-FR-029 |
| FLOW-RC-007 | RC-FR-011, RC-FR-030 |
| FLOW-RC-008 | RC-FR-017 |
| FLOW-RC-009 | RC-FR-012 |
| FLOW-RC-010 | RC-FR-013, RC-FR-015 |
| FLOW-RC-011 | RC-FR-014 |
| FLOW-RC-012 | RC-FR-016 |
| FLOW-RC-013 | RC-FR-018 |
| FLOW-RC-014 | RC-FR-038 |
| FLOW-RC-015 | RC-FR-010 |
| FLOW-RC-016 | RC-FR-024 |
| FLOW-RC-017 | RC-FR-024 |
| FLOW-RC-018 | RC-FR-040 |
| FLOW-RC-019 | RC-FR-028 |
| FLOW-RC-020 | RC-FR-028, RC-FR-034 |
| FLOW-RC-021 | RC-FR-032 |
| FLOW-RC-022 | RC-FR-033 |
| FLOW-RC-023 | RC-FR-001 |
| FLOW-RC-024 | RC-FR-008, RC-FR-009 |

---

# 15. Açık Akış Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-FLOW-OD-001 | Quick create sonrası Counter Detail mi Project Detail mi? | Counter Detail | Open |
| RC-FLOW-OD-002 | Bağımsız counter ekranı V1'de var mı? | Ertelenebilir | Open |
| RC-FLOW-OD-003 | Undo süresi kaç saniye? | 3-5 saniye | Open |
| RC-FLOW-OD-004 | Reset undo destekler mi? | Evet, mümkünse | Open |
| RC-FLOW-OD-005 | Increment analytics nasıl ölçülecek? | Aggregate | Open |
| RC-FLOW-OD-006 | Conflict UI V1'de gösterilecek mi? | Gerekirse basit seçim | Open |
| RC-FLOW-OD-007 | Project archived iken counter editlenebilir mi? | Önce restore | Open |

---

# 16. Codex İçin Uygulama Notları

Codex implementasyonunda:

- Increment/decrement işlemleri UI içinde raw state mutasyonu olarak bırakılmamalıdır.
- Counter operation domain/use case katmanından geçmelidir.
- Hızlı tap durumunda race condition oluşmamalıdır.
- Counter local save remote sync'ten önce yapılmalıdır.
- Sync operation idempotent olmalıdır.
- Counter name ve notes analytics'e gönderilmemelidir.
- Project ve part relationship owner scope ile doğrulanmalıdır.
- Deleted counter normal listelerde görünmemelidir.
- Project soft delete counterları normal project context'ten gizlemelidir.
- Reset ve delete confirmation gerektirmelidir.
- Increment confirmation istememelidir.
- Decrement minimum altına düşmemelidir.

---

# 17. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/data-model.md
```

`data-model.md` içinde counter entity, operation log, sync model, conflict modeli, indexler ve repository yapısı detaylandırılacaktır.
