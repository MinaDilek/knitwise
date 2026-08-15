# Row Counter — Security

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-002 |
| Feature Adı | Row Counter |
| Dosya | `03-features/feature-002-row-counter/security.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Row Counter** özelliği için güvenlik, gizlilik, yetkilendirme, ownership, offline data security, sync güvenliği, operation idempotency, analytics privacy ve abuse prevention kurallarını tanımlar.

Row Counter ilk bakışta yalnızca sayısal bir sayaç gibi görünse de aşağıdaki nedenlerle güvenlik açısından önemlidir:

- Counter genellikle bir project'e bağlıdır.
- Project kullanıcıya özel içerik barındırır.
- Counter name ve notes kişisel bilgi içerebilir.
- Counter value kullanıcının proje ilerlemesini gösterir.
- Offline update ve sync mekanizması veri tutarlılığı gerektirir.
- Operation log yanlış tasarlanırsa duplicate increment veya veri kaybı oluşabilir.
- Başka kullanıcıya ait counter'a erişim kesinlikle engellenmelidir.

Bu doküman, Row Counter'ın hem kullanıcı verisini korumasını hem de offline-first yapıda güvenilir çalışmasını hedefler.

---

## 2. Güvenlik Prensipleri

Row Counter güvenliği şu prensiplere dayanmalıdır:

1. Her counter bir `ownerId` ile kullanıcıya scope edilmelidir.
2. Counter read/write işlemlerinde `ownerId` zorunlu filtre olmalıdır.
3. `counterId` tek başına güvenlik için yeterli değildir.
4. Project veya part ilişkisi kurulurken iki tarafın ownership'i doğrulanmalıdır.
5. Counter name ve notes analytics/log sistemlerine gönderilmemelidir.
6. Offline operation'lar sync sırasında yeniden yetkilendirilmelidir.
7. Operation idempotency duplicate increment riskini engellemelidir.
8. Sync failure lokal veriyi silmemelidir.
9. Conflict durumunda lokal veri sessizce kaybolmamalıdır.
10. Soft deleted counter normal listelerde görünmemelidir.
11. Hata mesajları başka kullanıcıya ait counter varlığını sızdırmamalıdır.
12. Increment/decrement performansı korunurken güvenlik kuralları atlanmamalıdır.

---

## 3. Korunacak Veri Türleri

| Veri Türü | Hassasiyet | Açıklama |
|---|---|---|
| Counter name | Orta | Kullanıcı özel proje/parça adı yazabilir |
| Counter notes | Yüksek | Kişisel not, pattern açıklaması veya özel bilgi içerebilir |
| Current value | Düşük / Orta | Kullanıcının proje ilerlemesini gösterir |
| Target value | Düşük / Orta | Kullanıcının hedefini gösterir |
| Project relationship | Orta | Counter'ın hangi project'e ait olduğunu gösterir |
| Part relationship | Orta | Proje parçası bilgisini gösterir |
| Operation log | Orta | Kullanım davranışını ve zaman bilgisini gösterir |
| Sync metadata | Orta | Cihaz/sync davranışı hakkında bilgi verir |
| Conflict payload | Orta / Yüksek | Local/remote değer ve metadata içerebilir |

---

## 4. Ownership Modeli

### 4.1 Temel Kural

Her Row Counter kaydı tam olarak bir kullanıcıya ait olmalıdır.

Ana alan:

```text
ownerId
```

Aşağıdaki entity'lerde `ownerId` bulunmalıdır:

- `RowCounter`
- `CounterOperation`
- `CounterConflict`, kullanılırsa
- `CounterSnapshot`, kullanılırsa

### 4.2 Owner Scope

Counter sorguları şu pattern ile yapılmalıdır:

Yanlış:

```text
getCounterById(counterId)
```

Doğru:

```text
getCounterById(ownerId, counterId)
```

Aynı kural update, delete, increment ve sync işlemleri için de geçerlidir.

---

## 5. Authorization Kuralları

### 5.1 Counter Read Authorization

Bir kullanıcı counter okuyabilmek için şu koşulları sağlamalıdır:

```text
counter.ownerId == currentUser.id
counter.deletedAt == null
```

Deleted counter recovery veya debug/reconciliation dışında normal UI'da okunmamalıdır.

### 5.2 Counter Write Authorization

Bir kullanıcı counter değiştirebilmek için:

```text
counter.ownerId == currentUser.id
counter.deletedAt == null
```

koşullarını sağlamalıdır.

Write işlemleri:

- Increment
- Decrement
- Manual set
- Reset
- Undo
- Metadata update
- Target update
- Part link/unlink
- Soft delete

### 5.3 Project Relationship Authorization

Counter project'e bağlanırken:

```text
counter.ownerId == currentUser.id
project.ownerId == currentUser.id
project.deletedAt == null
```

kontrol edilmelidir.

Project archived ise ürün policy uygulanmalıdır. V1 önerisi:

```text
Archived project içinde counter edit için önce restore gerekebilir.
```

### 5.4 Part Relationship Authorization

Counter part'a bağlanırken:

```text
counter.ownerId == currentUser.id
part.ownerId == currentUser.id
part.projectId == counter.projectId
part.deletedAt == null
```

kontrol edilmelidir.

### 5.5 Operation Authorization

Bir `CounterOperation` yalnızca aynı owner'a ait counter için uygulanabilir.

```text
operation.ownerId == currentUser.id
operation.counterId belongs to currentUser.id
```

Client tarafından gönderilen `ownerId` güvenilir kabul edilmemelidir; auth context ile doğrulanmalıdır.

---

## 6. Row Level Security / Backend Policy

Supabase veya benzeri backend kullanılıyorsa RLS zorunludur.

### 6.1 `row_counters` Policy

Temel policy:

```sql
owner_id = auth.uid()
```

Read policy:

```sql
owner_id = auth.uid()
AND deleted_at IS NULL
```

Special recovery veya account export işlemleri ayrı policy veya server-side function ile yapılmalıdır.

### 6.2 `counter_operations` Policy

Operation tablosu için:

```sql
owner_id = auth.uid()
```

Ek doğrulama:

- `counter_id` gerçekten aynı owner'a ait olmalıdır.
- Operation başka kullanıcının counter'ına uygulanmamalıdır.
- Duplicate `operation_id` tekrar işlenmemelidir.

### 6.3 Relationship Policy

Counter create/update sırasında `project_id` ve `part_id` değerleri için backend doğrulama yapılmalıdır.

Sadece client UI'da project listesi filtrelemek yeterli değildir.

---

## 7. Operation Idempotency Güvenliği

### 7.1 Problem

Counter increment operation'ı retry sırasında iki kez uygulanırsa sayaç yanlış artabilir.

Örnek:

```text
currentValue = 10
increment operation retry iki kez işlendi
Yanlış sonuç = 12
Doğru sonuç = 11
```

### 7.2 Çözüm

Her operation benzersiz `operationId` taşımalıdır.

Remote tarafı aynı `operationId` değerini ikinci kez aldığında:

- Operation'ı tekrar uygulamamalıdır.
- Önceki sonucu döndürebilir.
- Duplicate operation olarak işaretleyebilir.

### 7.3 Idempotency Kuralı

```text
operationId unique
counterId + operationId unique
```

constraint veya server-side kontrol ile korunmalıdır.

### 7.4 Retry Güvenliği

Retry operation'ları:

- Aynı `operationId` ile gönderilmelidir.
- Yeni operation gibi üretilmemelidir.
- `attemptCount` artırılmalıdır.
- Duplicate value change üretmemelidir.

---

## 8. Offline Security

### 8.1 Offline Increment

Offline increment sırasında:

- Counter local owner scope içinde bulunmalıdır.
- Local update yalnızca current user local context'ine yapılmalıdır.
- Operation `ownerId` ile kaydedilmelidir.
- Sync sırasında server tekrar auth user ile doğrulama yapmalıdır.

### 8.2 Offline Multi User Riski

Aynı cihazda farklı kullanıcılar login/logout yapıyorsa:

- Local DB user scope ile ayrılmalıdır.
- User logout sonrası başka kullanıcının counterları görünmemelidir.
- Pending operations yanlış user altında sync edilmemelidir.

### 8.3 Sync Revalidation

Offline operation remote'a gönderildiğinde server şu kontrolleri yapmalıdır:

```text
auth.uid() == operation.ownerId
counter.ownerId == auth.uid()
project.ownerId == auth.uid(), varsa
part.ownerId == auth.uid(), varsa
```

### 8.4 Local Cache Security

Local cache cihaz seviyesinde güvenli kabul edilmemelidir.

Gerekenler:

- Sensitive content logs içine yazılmamalıdır.
- Logout sonrası cache policy net olmalıdır.
- Çok kullanıcılı cihaz senaryosu test edilmelidir.
- Counter notes gibi kullanıcı metinleri debug ekranlarında sızdırılmamalıdır.

---

## 9. Sync ve Conflict Güvenliği

### 9.1 Sync Failure Veri Silmemelidir

Remote sync başarısız olursa:

- Lokal `currentValue` korunmalıdır.
- Operation log korunmalıdır.
- `syncStatus = failed` veya `pending` olmalıdır.
- Kullanıcı çalışmaya devam edebilmelidir.

### 9.2 Conflict Durumunda Veri Koruma

Conflict oluştuğunda sistem:

- Lokal veriyi sessizce remote ile ezmemelidir.
- Remote veriyi sessizce local ile ezmemelidir.
- Kullanıcıya seçim sunabilir.
- Operation-based merge güvenliyse otomatik çözebilir.

### 9.3 Conflict Payload Privacy

Conflict payload içinde counter name veya notes bulunabilir.

Bu nedenle:

- Production logs conflict payload yazmamalıdır.
- Analytics conflict eventleri yalnızca conflict type göndermelidir.
- Debug export kullanıcı rızası olmadan paylaşılmamalıdır.

### 9.4 Remote Deleted Conflict

Remote counter deleted iken local increment varsa:

- Local operation sessizce silinmemelidir.
- Counter `conflict` state'e alınabilir.
- Kullanıcıya recover veya discard seçenekleri sunulabilir.
- Owner doğrulaması korunmalıdır.

---

## 10. Soft Delete Güvenliği

### 10.1 Counter Soft Delete

Counter silindiğinde:

```text
deletedAt = now
```

Normal listelerde:

```text
deletedAt IS NULL
```

filtresi zorunludur.

### 10.2 Deleted Counter Update

`deletedAt != null` olan counter üzerinde şu işlemler engellenmelidir:

- Increment
- Decrement
- Manual set
- Reset
- Metadata update
- Part link/unlink

Recovery özel bir işlem olarak tasarlanırsa ayrı use case olmalıdır.

### 10.3 Project Soft Delete Etkisi

Project soft delete olduğunda bağlı counterlar normal project context'te görünmemelidir.

Ancak counterlar hemen permanent delete yapılmamalıdır.

### 10.4 Permanent Delete

Permanent delete sadece şu durumlarda yapılmalıdır:

- Account deletion
- Project permanent delete
- Recovery süresi sonrası cleanup
- Explicit data deletion policy

---

## 11. Input Validation Güvenliği

### 11.1 Counter Name

Validation:

```text
trimmed length >= 1
trimmed length <= 80
```

### 11.2 Notes

Validation:

```text
length <= 1000
```

Notes analytics veya production logs içine gönderilmemelidir.

### 11.3 Numeric Fields

| Alan | Kural |
|---|---|
| `currentValue` | integer, `>= 0` |
| `initialValue` | integer, `>= 0` |
| `stepValue` | integer, `>= 1`, önerilen max `100` |
| `targetValue` | null veya integer `>= 1` |

### 11.4 Counter Type Allowlist

Counter type kullanıcı input olarak arbitrary string kabul etmemelidir.

Geçerli değerler:

```text
row
round
repeat
increase
decrease
custom
```

### 11.5 Operation Type Allowlist

Operation type allowlist ile sınırlandırılmalıdır.

```text
increment
decrement
manual_set
reset
undo
create
update_metadata
soft_delete
recover
```

### 11.6 Metadata Patch Güvenliği

`metadataPatch` arbitrary field update'e izin vermemelidir.

Allowlist:

- `name`
- `counterType`
- `stepValue`
- `initialValue`
- `targetValue`
- `notes`
- `partId`
- `isPrimary`

yasak alanlar:

- `ownerId`
- `counterId`
- `createdAt`
- `remoteVersion`
- `schemaVersion`
- `deletedAt`, özel use case dışında

---

## 12. Analytics ve Logging Güvenliği

### 12.1 Analytics Yasak Alanlar

Analytics payload içinde bulunmamalıdır:

```text
counterName
name
notes
projectName
partName
patternText
rawUserText
localPath
remotePath
```

### 12.2 Güvenli Analytics Alanları

Kullanılabilir:

```text
counterType
hasTarget
hasPart
isPrimary
syncStatus
operationType
errorCode
durationMs
valueBucket
countBucket
```

### 12.3 Production Logs

Production logs içine yazılmamalıdır:

- Counter name
- Notes
- Full operation payload
- Conflict payload
- Raw user text
- Access token
- Refresh token

### 12.4 Error Reporting

Crash reporting için:

- Raw counter data gönderilmemelidir.
- `counterIdHash` kullanılabilir.
- `errorCode` kullanılabilir.
- Operation type kullanılabilir.
- Notes ve name maskelenmelidir.

---

## 13. API / Repository Güvenliği

### 13.1 Scope'suz Method Kullanılmamalı

Yanlış:

```text
incrementCounter(counterId)
```

Doğru:

```text
incrementCounter(ownerId, counterId)
```

### 13.2 Atomic Update

Counter update atomic olmalıdır.

Increment sırasında:

```text
read current value
calculate new value
write new value
insert operation
```

işlemleri transaction içinde yapılmalıdır.

### 13.3 Race Condition Önleme

Hızlı tap durumunda aynı current value üzerinden birden fazla update yapılmamalıdır.

Öneriler:

- DB atomic increment
- transaction
- local operation queue
- mutex/lock
- sequential state update

### 13.4 Update Scope

Update query şu scope'u içermelidir:

```text
counterId = input.counterId
ownerId = currentUser.id
deletedAt IS NULL
```

---

## 14. Abuse Prevention

### 14.1 Çok Fazla Increment

Kullanıcı çok hızlı increment yapabilir.

Bu normal kullanım da olabilir, abuse da olabilir.

Önlemler:

- UI performans optimizasyonu
- Operation compaction
- Sync batching
- Rate limit remote tarafında
- Local queue size monitoring

### 14.2 Operation Queue Şişmesi

Offline uzun kullanımda operation queue büyüyebilir.

Önlemler:

- Compaction
- Max queue warning
- Background sync
- Retry backoff
- Failed operation cleanup policy

### 14.3 Counter Spam

Kullanıcı çok sayıda counter oluşturabilir.

Önlemler:

- Soft product limits
- Premium limits, ileride
- Local performance guard
- Abuse monitoring

### 14.4 Notes Abuse

Notes alanı çok uzun veya zararlı input içerebilir.

Önlemler:

- Length limit
- Sanitization, rich text gelirse
- Log masking
- Export policy

---

## 15. Error Message Güvenliği

### 15.1 Unauthorized Counter

Önerilen mesaj:

```text
Bu sayaç bulunamadı veya erişim izniniz yok.
```

Sakınılması gereken mesaj:

```text
Bu sayaç başka bir kullanıcıya ait.
```

### 15.2 Deleted Counter

Önerilen mesaj:

```text
Bu sayaç artık kullanılamıyor.
```

### 15.3 Sync Error

Önerilen mesaj:

```text
Değişiklikler cihazınızda saklandı. Bağlantı geldiğinde senkronize edilecek.
```

### 15.4 Conflict

Önerilen mesaj:

```text
Bu sayaç başka bir cihazda da değiştirilmiş. Hangi değeri kullanmak istediğinizi seçin.
```

---

## 16. Security Test Senaryoları

### 16.1 Ownership Testleri

- Kullanıcı kendi counter'ını okuyabilir.
- Kullanıcı başka kullanıcının counter'ını okuyamaz.
- Kullanıcı başka kullanıcının counter'ını increment edemez.
- Kullanıcı başka kullanıcının counter'ını soft delete edemez.
- Kullanıcı başka kullanıcının counter operation'ını sync edemez.

### 16.2 Relationship Testleri

- Kullanıcı kendi project'ine counter bağlayabilir.
- Kullanıcı başka kullanıcının project'ine counter bağlayamaz.
- Kullanıcı kendi counter'ını kendi part'ına bağlayabilir.
- Kullanıcı başka kullanıcının part'ına counter bağlayamaz.
- Part aynı project'e ait değilse link reddedilir.

### 16.3 Operation Idempotency Testleri

- Aynı `operationId` iki kez gönderilirse value bir kez değişir.
- Retry duplicate increment üretmez.
- Failed operation tekrar denenirse aynı operation korunur.
- Operation order bozulursa conflict veya safe error oluşur.

### 16.4 Offline Security Testleri

- Offline increment owner scope içinde kalır.
- Logout sonrası başka kullanıcı pending counterları görmez.
- Sync sırasında operation auth user ile doğrulanır.
- Pending operation yanlış kullanıcı altında sync edilmez.

### 16.5 Soft Delete Testleri

- Deleted counter normal listede görünmez.
- Deleted counter increment edilemez.
- Deleted counter deep link normal detail açmaz.
- Project soft delete counterları project context'ten gizler.

### 16.6 Analytics Privacy Testleri

- Counter name analytics payload içinde yoktur.
- Notes analytics payload içinde yoktur.
- Project name analytics payload içinde yoktur.
- Part name analytics payload içinde yoktur.
- Raw conflict payload analytics'e gitmez.

---

## 17. Security Checklist

Release öncesi şu kontroller tamamlanmalıdır:

- [ ] Tüm counter query'leri `ownerId` scope içeriyor.
- [ ] Increment/decrement owner check yapıyor.
- [ ] Operation log owner scope içeriyor.
- [ ] Duplicate `operationId` duplicate increment üretmiyor.
- [ ] Deleted counter normal listelerde görünmüyor.
- [ ] Deleted counter update edilemiyor.
- [ ] Project/part relationship ownership doğrulanıyor.
- [ ] Offline sync server tarafında revalidation yapıyor.
- [ ] Analytics counter name ve notes içermiyor.
- [ ] Production logs raw operation payload yazmıyor.
- [ ] Conflict payload loglanmıyor.
- [ ] Error mesajları veri varlığını sızdırmıyor.
- [ ] Race condition testleri geçiyor.
- [ ] Operation queue abuse riskleri yönetiliyor.

---

## 18. Codex İçin Uygulama Beklentileri

Codex implementasyonunda:

- `counterId` ile scope'suz repository method yazma.
- Increment/decrement işlemlerini transaction veya sequential queue ile güvenli hale getir.
- Operation log oluşturmadan value update yapma.
- Retry sırasında yeni `operationId` üretme.
- Duplicate operation idempotency kuralını uygula.
- Analytics'e counter name veya notes gönderme.
- Conflict payload'ı production loglara yazma.
- Project ve part link işlemlerinde ownership doğrulaması yap.
- Deleted counterları normal query'lerden filtrele.
- Sync failure durumunda lokal değeri geri alma veya silme.
- Security testlerini implementation ile birlikte yaz.

---

## 19. Açık Güvenlik Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| RC-SEC-OD-001 | Counter remote sync operation-based mi olacak? | V1 local operation log, remote latest value olabilir | Open |
| RC-SEC-OD-002 | Operation compaction güvenlik seviyesi | Sync öncesi kontrollü compaction | Open |
| RC-SEC-OD-003 | Logout sonrası local counter cache policy | Güvenli default belirlenmeli | Open |
| RC-SEC-OD-004 | Counter recovery UI olacak mı? | V1 için zorunlu değil | Open |
| RC-SEC-OD-005 | Conflict payload localde nasıl saklanacak? | Maskelenmiş/local secure yaklaşım | Open |
| RC-SEC-OD-006 | Increment rate limit olacak mı? | Remote sync tarafında değerlendirilmeli | Open |
| RC-SEC-OD-007 | Notes encryption gerekli mi? | Genel app security kararına bağlı | Open |

---

## 20. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-002-row-counter/implementation-notes.md
```

`implementation-notes.md` içinde Row Counter için Flutter/Riverpod mimarisi, atomic increment yaklaşımı, operation log, repository yapısı, local DB, sync engine ve test edilebilir domain servisleri tanımlanacaktır.
