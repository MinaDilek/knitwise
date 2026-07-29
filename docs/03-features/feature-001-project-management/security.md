
# Proje Yönetimi — Security

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/security.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliği için güvenlik, gizlilik, yetkilendirme, veri izolasyonu, medya güvenliği, soft delete güvenliği, offline veri güvenliği ve abuse prevention kurallarını tanımlar.

Project Management, kullanıcının kişisel proje bilgilerini, notlarını, görsellerini, pattern ilişkilerini, malzeme kullanımını ve proje ilerleme bilgilerini içerdiği için gizlilik ve güvenlik açısından temel modüllerden biridir.

Bu doküman aşağıdaki sorulara cevap verir:

- Bir kullanıcı yalnızca kendi projelerine nasıl erişir?
- `projectId` manipülasyonu ile başka projeler görüntülenebilir mi?
- Local-first mimaride güvenlik nasıl korunur?
- Soft deleted projeler normal sorgulardan nasıl hariç tutulur?
- Project media private olarak nasıl saklanır?
- Analytics ve logs içinde PII nasıl engellenir?
- Offline sync sırasında ownership nasıl korunur?
- Premium limit güvenlik açısından nasıl bypass edilemez?
- Broken relationship ve stale deep link durumları nasıl güvenli yönetilir?

---

## 2. Güvenlik Prensipleri

Project Management güvenlik tasarımı şu prensiplere göre yapılmalıdır:

1. Her project kaydı bir `ownerId` ile kullanıcıya scope edilmelidir.
2. Client tarafındaki kontroller yeterli kabul edilmemelidir.
3. Backend veya remote database seviyesinde owner isolation zorunlu olmalıdır.
4. Kullanıcı başka kullanıcının `projectId` değerini bilse bile veriye erişememelidir.
5. Soft deleted veriler normal user-facing sorgularda dönmemelidir.
6. Archived ve deleted kavramları karıştırılmamalıdır.
7. Project media private storage içinde tutulmalıdır.
8. Analytics ve log sistemlerine özel kullanıcı içeriği gönderilmemelidir.
9. Offline kayıtlar sync sırasında tekrar ownership validasyonundan geçmelidir.
10. Entitlement ve Premium limitler client tarafında bypass edilebilir kabul edilmeli, server tarafı kontrollerle desteklenmelidir.
11. Hata mesajları başka kullanıcıya ait veri varlığını sızdırmamalıdır.
12. Security kontrolleri test edilebilir ve merkezi olmalıdır.

---

## 3. Korunacak Veri Türleri

Project Management aşağıdaki veri türlerini korumalıdır:

| Veri Türü | Hassasiyet | Açıklama |
|---|---|---|
| Project name | Orta | Kullanıcının özel proje adı olabilir |
| Description | Orta / Yüksek | Kişisel açıklama içerebilir |
| Notes | Yüksek | Ölçü, kişisel not, özel bilgi içerebilir |
| Cover image | Yüksek | Kullanıcının evi, yüzü veya özel alanı görünebilir |
| Pattern relationship | Orta | Kullanıcının tercihlerini gösterir |
| Yarn relationship | Düşük / Orta | Kullanıcı tercihlerini gösterir |
| Tool relationship | Düşük | Araç tercihi |
| Progress | Düşük / Orta | Kullanım davranışı |
| Status history | Orta | Kullanıcı davranış geçmişi |
| Sync metadata | Orta | Teknik davranış ve cihaz durumu |
| Local file paths | Yüksek | Cihaz yapısı hakkında bilgi sızdırabilir |

---

## 4. Veri Sahipliği Modeli

### 4.1 Temel Kural

Her project kaydı tam olarak bir kullanıcıya ait olmalıdır.

Ana sahiplik alanı:

```text
ownerId
```

Her user-owned tabloda veya entity'de `ownerId` bulunmalıdır:

- `Project`
- `ProjectPatternLink`
- `ProjectYarnLink`
- `ProjectToolLink`
- `ProjectMedia`
- `ProjectStatusHistory`
- `ProjectNote`, kullanılırsa
- `ProjectSyncState`, kullanılırsa

### 4.2 Ownership Kontrolü

Aşağıdaki işlemlerden önce ownership kontrolü yapılmalıdır:

- Project detail görüntüleme
- Project update
- Project status change
- Project archive
- Project restore
- Project soft delete
- Project recovery
- Pattern link / unlink
- Yarn link / unlink
- Tool link / unlink
- Cover image upload / remove
- Counter create from project
- Part create from project

### 4.3 Client Kontrolü Yeterli Değildir

UI tarafında buton gizlemek güvenlik kontrolü sayılmaz.

Örnek:

- Kullanıcı edit butonunu görmese bile API ile update isteği gönderebilir.
- Kullanıcı route içindeki `projectId` değerini değiştirebilir.
- Kullanıcı stale deep link ile eski kayda ulaşmayı deneyebilir.

Bu nedenle ownership kontrolü domain/service ve backend seviyesinde uygulanmalıdır.

---

## 5. Authorization Kuralları

### 5.1 Project Read Authorization

Bir kullanıcı project okuyabilmek için şu koşulları sağlamalıdır:

```text
project.ownerId == currentUser.id
project.deletedAt == null
```

Recovery ekranı gibi özel durumlarda `deletedAt != null` kayıtlar ayrıca kontrollü şekilde okunabilir.

### 5.2 Project Write Authorization

Bir kullanıcı project güncelleyebilmek için:

```text
project.ownerId == currentUser.id
project.deletedAt == null
```

koşullarını sağlamalıdır.

Archived project için V1 önerisi:

```text
Archived project doğrudan editlenmemeli, önce restore edilmelidir.
```

Bu karar değişirse `requirements.md`, `flows.md` ve bu security dokümanı birlikte güncellenmelidir.

### 5.3 Relationship Authorization

Project ile başka bir entity ilişkilendirilirken iki taraf da kullanıcıya ait veya kullanıcı tarafından erişilebilir olmalıdır.

Örnek pattern link kontrolü:

```text
project.ownerId == currentUser.id
pattern.ownerId == currentUser.id OR pattern.isStarterPattern == true
```

Örnek yarn link kontrolü:

```text
project.ownerId == currentUser.id
yarn.ownerId == currentUser.id
```

Örnek tool link kontrolü:

```text
project.ownerId == currentUser.id
tool.ownerId == currentUser.id
```

### 5.4 Media Authorization

Project media işlemleri için:

```text
project.ownerId == currentUser.id
media.ownerId == currentUser.id
media.projectId == project.projectId
```

kontrol edilmelidir.

---

## 6. Row Level Security / Backend Policy

Eğer Supabase veya benzeri backend kullanılıyorsa RLS zorunlu olmalıdır.

### 6.1 Projects RLS

`projects` tablosu için temel policy mantığı:

```sql
owner_id = auth.uid()
```

Read policy:

```sql
owner_id = auth.uid()
AND deleted_at IS NULL
```

Recovery veya account export gibi özel işlemler ayrı policy veya server-side privileged function ile yapılmalıdır.

### 6.2 Relationship RLS

Relationship tabloları için temel policy:

```sql
owner_id = auth.uid()
```

Ayrıca ilişkilendirilen `project_id` gerçekten aynı kullanıcıya ait olmalıdır.

Bu kontrol:

- Foreign key
- Trigger
- Server-side validation
- RPC function

ile güçlendirilebilir.

### 6.3 Media RLS

Project media tablosu için:

```sql
owner_id = auth.uid()
```

Storage path policy de aynı mantıkla korunmalıdır.

Önerilen path yapısı:

```text
users/{ownerId}/projects/{projectId}/media/{mediaId}
```

Bu path kullanıcıya açık şekilde paylaşılmamalıdır.

### 6.4 Public Bucket Kullanılmamalı

Project cover image veya progress image public bucket içinde saklanmamalıdır.

Kullanıcı görselleri için:

- Private bucket
- Signed URL
- Kısa süreli erişim linki
- Owner doğrulaması

kullanılmalıdır.

---

## 7. Deep Link Güvenliği

### 7.1 Deep Link Read Kontrolü

Deep link ile gelen her `projectId` için:

1. Project var mı?
2. `ownerId` current user ile eşleşiyor mu?
3. `deletedAt` null mı?
4. Status archived ise hangi ekran gösterilecek?
5. Recovery durumunda kullanıcı yetkili mi?

kontrolleri yapılmalıdır.

### 7.2 Unauthorized Deep Link

Kullanıcı yetkisiz bir project deep link'i açarsa:

- Project verisi gösterilmemelidir.
- Projenin var olup olmadığı açıkça sızdırılmamalıdır.
- Generic not-found veya access-denied state gösterilebilir.

Önerilen kullanıcı mesajı:

```text
Bu proje bulunamadı veya erişim izniniz yok.
```

### 7.3 Deleted Project Deep Link

`deletedAt` dolu project için normal detail ekranı gösterilmemelidir.

Recovery süresi aktifse ve kullanıcı owner ise recovery state gösterilebilir.

Aksi halde generic not-found gösterilmelidir.

### 7.4 Archived Project Deep Link

Archived project owner tarafından açılırsa:

- Project detail gösterilebilir.
- Archived badge görünmelidir.
- Edit veya çalışma aksiyonları restore gerektirebilir.

---

## 8. Soft Delete Güvenliği

### 8.1 Deleted State Ayrımı

Deleted state `status` değildir.

Silme için:

```text
deletedAt
```

kullanılmalıdır.

Archived project ve deleted project farklıdır.

### 8.2 Normal Query Filtreleri

Normal kullanıcı sorguları şu filtreyi içermelidir:

```text
deletedAt == null
```

Bu kural aşağıdaki ekranlarda zorunludur:

- Project List
- Search
- Filter
- Sort
- Project Detail
- Pattern relation picker
- Yarn relation picker
- Tool relation picker
- Counter/part entry points

### 8.3 Recovery Query

Recovery ekranı özel bir query kullanmalıdır:

```text
ownerId == currentUser.id
deletedAt != null
recoverableUntil >= now
```

### 8.4 Permanent Delete

Permanent delete işlemi:

- Kullanıcı yanlışlıkla tetikleyememelidir.
- Background cleanup veya account deletion süreciyle yapılmalıdır.
- Media cleanup ile uyumlu olmalıdır.
- Backup retention politikalarını dikkate almalıdır.

### 8.5 Soft Deleted Data Exposure

Soft deleted project:

- Normal analytics eventlerinde aktif veri gibi işlenmemelidir.
- Search sonuçlarında dönmemelidir.
- Normal deep linkte gösterilmemelidir.
- Liste count hesaplarına dahil edilmemelidir.

---

## 9. Media Security

### 9.1 Private Storage

Project görselleri private storage içinde saklanmalıdır.

Public URL kalıcı olarak üretilmemelidir.

### 9.2 Signed URL

Görsel gösterimi için signed URL kullanılacaksa:

- Kısa süreli olmalıdır.
- Sadece owner için üretilebilir olmalıdır.
- Loglarda URL'nin tamamı yazılmamalıdır.
- Analytics payload içinde gönderilmemelidir.

### 9.3 Local Media

Local media path güvenli kabul edilmemelidir.

Aşağıdaki alanlar analytics/log sistemine gönderilmemelidir:

```text
localPath
remotePath
thumbnailLocalPath
thumbnailRemotePath
fileName
```

### 9.4 Upload Validation

Görsel upload öncesi:

- MIME type kontrolü
- Dosya boyutu kontrolü
- Görsel decode testi
- Desteklenen format kontrolü
- Gerekirse compression

yapılmalıdır.

### 9.5 Malicious File Risk

Kullanıcı görsel olarak zararlı dosya yüklemeye çalışabilir.

Bu nedenle sadece uzantıya güvenilmemelidir.

Kontrol edilmesi gerekenler:

- MIME type
- Magic bytes, mümkünse
- Görsel decode edilebilir mi?
- Boyut limitleri
- Storage path traversal önlemleri

### 9.6 Orphan Media Cleanup

Project silinirken veya cover image değiştirilirken eski medya orphan kalabilir.

Orphan media cleanup:

- Güvenli background job ile yapılmalıdır.
- Başka project tarafından kullanılan dosya silinmemelidir.
- Soft delete recovery süresi dikkate alınmalıdır.

---

## 10. Offline Security

### 10.1 Offline Project Create

Offline oluşturulan project için `ownerId` lokal authenticated session'dan alınmalıdır.

Kullanıcı oturumu yoksa:

- Project create engellenebilir, veya
- Anonymous/local-only mode açıkça tanımlanmalıdır.

V1 önerisi:

```text
Authentication veya locally authorized user gereklidir.
```

### 10.2 Offline Update

Offline update sadece lokal owner'a ait kayıtlarda yapılmalıdır.

Local DB içinde farklı kullanıcıya ait veri varsa session switch sırasında izolasyon korunmalıdır.

### 10.3 Device Shared Usage

Aynı cihazda farklı kullanıcılar uygulamayı kullanabilirse:

- User logout sırasında local cache policy net olmalıdır.
- Başka kullanıcının project verisi görünmemelidir.
- Local DB user scope ile query edilmelidir.

### 10.4 Sync Sırasında Revalidation

Offline oluşturulan veya düzenlenen kayıt remote'a sync edilirken server tekrar şu kontrolleri yapmalıdır:

- Current authenticated user kim?
- Payload içindeki `ownerId` auth user ile uyumlu mu?
- Project gerçekten bu kullanıcıya mı ait?
- Relationship target aynı kullanıcıya mı ait?
- Deleted veya archived state kuralları geçerli mi?

Client payload içindeki `ownerId` tek başına güvenilir değildir.

---

## 11. Sync ve Conflict Security

### 11.1 Sync Payload Güvenliği

Sync payload içinde kullanıcı özel metinleri gerekmedikçe loglanmamalıdır.

Özellikle:

- Notes
- Description
- Project name
- Media path

debug loglarda maskelenmelidir.

### 11.2 Conflict State

Conflict durumunda kullanıcıya gösterilen bilgiler sadece kendi verisine ait olmalıdır.

Başka kullanıcıya ait remote payload asla gösterilmemelidir.

### 11.3 Deleted Remote Conflict

Remote tarafta project deleted ise local update güvenli şekilde ele alınmalıdır.

Sistem:

- Local veriyi hemen silmemelidir.
- Kullanıcıya conflict veya recovery state gösterebilir.
- Ownership doğrulamasını korumalıdır.

### 11.4 Replay Risk

Sync operations tekrar gönderilebilir.

Bu nedenle sync operasyonları idempotent olmalıdır.

Öneri:

```text
operationId
entityId
localVersion
remoteVersion
```

kullanılarak duplicate update riski azaltılmalıdır.

---

## 12. Entitlement ve Premium Güvenliği

### 12.1 Client Bypass Riski

Free plan aktif proje limiti yalnızca client UI ile uygulanmamalıdır.

Kullanıcı client tarafında:

- Create butonunu manipüle edebilir.
- Local DB'ye kayıt ekleyebilir.
- API çağrısını doğrudan gönderebilir.
- Entitlement response'u spoof etmeye çalışabilir.

Bu nedenle server-side veya sync-side kontrol gereklidir.

### 12.2 Active Count Query

Aktif proje sayımı güvenli şekilde şu koşullarla yapılmalıdır:

```text
ownerId = currentUser.id
deletedAt IS NULL
status IN ('active', 'paused')
```

### 12.3 Downgrade Data Protection

Premium downgrade sonrası sistem mevcut projeleri silmemelidir.

Güvenlik kuralı:

- Entitlement mevcut veriye destructive işlem uygulamaz.
- Sadece yeni active project create/activation işlemini kısıtlayabilir.

### 12.4 Offline Limit

Offline durumda entitlement cache kullanılıyorsa:

- Cache süresi tanımlı olmalıdır.
- Online olduğunda reconciliation yapılmalıdır.
- Limit aşımı veri silmeyle çözülmemelidir.

---

## 13. Input Validation Güvenliği

### 13.1 Text Inputs

Aşağıdaki alanlar validate edilmelidir:

- `name`
- `description`
- `notes`
- `manualToolName`
- `colorRole`
- Custom category / technique alanları, varsa

### 13.2 Length Limits

Önerilen limitler:

| Alan | Limit |
|---|---:|
| `name` | 120 karakter |
| `description` | 2000 karakter |
| `notes` | 10000 karakter |
| `manualToolName` | 120 karakter |
| `colorRole` | 80 karakter |

### 13.3 Script Injection

Mobil uygulamada HTML render edilmiyorsa risk daha düşüktür, ancak ileride web veya rich text gelirse:

- User input escape edilmelidir.
- HTML render gerekiyorsa sanitizer kullanılmalıdır.
- Markdown preview varsa script injection engellenmelidir.

### 13.4 SQL Injection

ORM veya parameterized query kullanılmalıdır.

User input doğrudan SQL string içine eklenmemelidir.

Riskli alanlar:

- Search query
- Filter values
- Sort values
- Project name
- Notes

### 13.5 Sort ve Filter Allowlist

Sort ve filter değerleri allowlist ile sınırlandırılmalıdır.

Örnek geçerli sort değerleri:

```text
updatedAt_desc
updatedAt_asc
createdAt_desc
createdAt_asc
name_asc
name_desc
```

Kullanıcıdan gelen arbitrary field name ile query oluşturulmamalıdır.

---

## 14. Analytics ve Logging Güvenliği

### 14.1 Analytics Yasak Alanlar

Analytics eventlerinde aşağıdaki alanlar bulunmamalıdır:

```text
projectName
name
description
notes
patternText
searchQuery
imagePath
localPath
remotePath
fileName
```

### 14.2 Log Yasak Alanlar

Production loglarda aşağıdakiler yazılmamalıdır:

- Raw project name
- Notes
- Description
- Full media URL
- Signed URL
- Local file path
- Access token
- Refresh token
- Full sync payload

### 14.3 Güvenli Log Alanları

Kullanılabilecek alanlar:

- `projectIdHash`
- `status`
- `syncStatus`
- `errorCode`
- `operationType`
- `durationMs`
- `attemptCount`

### 14.4 Error Reporting

Crash/error reporting araçlarına gönderilen breadcrumb ve context alanları PII içermemelidir.

Project ile ilişkili hata gönderilecekse raw ID yerine hash kullanılmalıdır.

---

## 15. API / Repository Güvenliği

### 15.1 Repository Query Scope

Tüm repository query'leri `ownerId` scope ile çalışmalıdır.

Yanlış örnek:

```text
getProjectById(projectId)
```

Doğru örnek:

```text
getProjectById(ownerId, projectId)
```

### 15.2 Update Scope

Yanlış örnek:

```text
update projects set name = ? where project_id = ?
```

Doğru örnek:

```text
update projects set name = ? where project_id = ? and owner_id = ?
```

### 15.3 Delete Scope

Soft delete işlemi de owner scope ile yapılmalıdır:

```text
projectId == input.projectId
ownerId == currentUser.id
```

### 15.4 Relationship Query Scope

Relationship sorguları yalnızca `projectId` ile yapılmamalıdır.

Doğru sorgu:

```text
projectId + ownerId
```

---

## 16. Abuse Prevention

### 16.1 Project Spam

Kullanıcı çok sayıda project oluşturarak local veya remote storage tüketebilir.

Önlemler:

- Free active project limit
- Draft limit, gerekirse
- Rate limit
- Storage quota
- Media upload quota

### 16.2 Media Abuse

Görsel upload abuse riskleri:

- Çok büyük dosya yükleme
- Çok sık upload denemesi
- Desteklenmeyen dosya tipi
- Storage cost abuse

Önlemler:

- File size limit
- Upload rate limit
- Compression
- Private storage
- Cleanup job
- Quota monitoring

### 16.3 Sync Abuse

Offline sync queue çok büyüyebilir.

Önlemler:

- Max retry count
- Backoff policy
- Operation compaction
- Duplicate operation merge
- Queue size limit

### 16.4 Search Abuse

Search query çok sık tetiklenebilir.

Önlemler:

- Debounce
- Minimum query length
- Local search preference
- Server search rate limit, varsa

---

## 17. Error Message Güvenliği

### 17.1 Unauthorized Access

Hata mesajı başka kullanıcıya ait project varlığını sızdırmamalıdır.

Önerilen mesaj:

```text
Bu proje bulunamadı veya erişim izniniz yok.
```

Sakınılması gereken mesaj:

```text
Bu proje başka bir kullanıcıya ait.
```

### 17.2 Deleted Project

Önerilen mesaj:

```text
Bu proje artık kullanılamıyor.
```

Recovery mümkünse:

```text
Bu proje silinmiş. Kurtarma süresi devam ediyorsa geri alabilirsiniz.
```

### 17.3 Sync Error

Teknik detay yerine kullanıcı dostu mesaj gösterilmelidir:

```text
Değişiklikleriniz cihazınızda saklandı. Bağlantı geldiğinde senkronize edilecek.
```

---

## 18. Security Test Senaryoları

### 18.1 Ownership Testleri

- Kullanıcı kendi projesini okuyabilir.
- Kullanıcı başka kullanıcının projesini okuyamaz.
- Kullanıcı başka kullanıcının `projectId` değeriyle detail açamaz.
- Kullanıcı başka kullanıcının project'ini update edemez.
- Kullanıcı başka kullanıcının project'ini soft delete edemez.

### 18.2 Relationship Testleri

- Kullanıcı kendi project'ine kendi yarn kaydını bağlayabilir.
- Kullanıcı kendi project'ine başka kullanıcının yarn kaydını bağlayamaz.
- Kullanıcı başka kullanıcının pattern'ini izinsiz bağlayamaz.
- Starter pattern gibi public kaynaklar özel kurallarla bağlanabilir.
- Broken relationship project detail ekranını bozamaz.

### 18.3 Media Security Testleri

- Project media public URL ile erişilemez.
- Signed URL süresi dolunca erişim kapanır.
- Başka kullanıcı media URL üretemez.
- Unsupported file upload reddedilir.
- Çok büyük dosya reddedilir veya sıkıştırılır.
- Local path analytics payload'a girmez.

### 18.4 Soft Delete Testleri

- Soft deleted project normal listede görünmez.
- Soft deleted project search sonucunda görünmez.
- Soft deleted project normal detail ekranında açılmaz.
- Recovery query sadece owner'ın deleted projectlerini döndürür.
- Archived project deleted gibi davranmaz.

### 18.5 Offline Security Testleri

- Offline oluşturulan project doğru `ownerId` ile kaydedilir.
- Offline update sadece owner verisine uygulanır.
- Logout sonrası başka kullanıcı eski local projectleri görmez.
- Sync sırasında payload owner doğrulamasından geçer.

### 18.6 Entitlement Testleri

- Free user limit üstünde active project oluşturamaz.
- Premium user limit üstünde project oluşturabilir, plana göre.
- Downgrade sonrası mevcut projeler silinmez.
- Client bypass denemesi server/sync aşamasında yakalanır.

### 18.7 Logging / Analytics Testleri

- Project name analytics payload içinde yoktur.
- Notes analytics payload içinde yoktur.
- Search query analytics payload içinde yoktur.
- Media path loglarda yoktur.
- Signed URL loglarda yoktur.
- Error reporting PII içermez.

---

## 19. Security Checklist

Release öncesi aşağıdaki checklist tamamlanmalıdır:

- [ ] Tüm project query'leri `ownerId` scope içeriyor.
- [ ] Project detail unauthorized erişimi engelliyor.
- [ ] Project update unauthorized erişimi engelliyor.
- [ ] Relationship link işlemleri iki taraf için ownership kontrolü yapıyor.
- [ ] Soft deleted kayıtlar normal listelerde görünmüyor.
- [ ] Archived ve deleted ayrı davranıyor.
- [ ] Project media private storage kullanıyor.
- [ ] Signed URL loglanmıyor.
- [ ] Analytics PII içermiyor.
- [ ] Production logs user content içermiyor.
- [ ] Offline sync owner revalidation yapıyor.
- [ ] Entitlement limit client-only değil.
- [ ] Search/sort/filter allowlist kullanıyor.
- [ ] Error mesajları veri varlığını sızdırmıyor.
- [ ] Security testleri geçiyor.

---

## 20. Codex İçin Uygulama Beklentileri

Codex implementasyonunda:

- `ownerId` parametresi repository metodlarından eksik bırakılmamalıdır.
- `getProjectById(projectId)` gibi scope'suz metodlar kullanılmamalıdır.
- Project ve relationship update işlemleri domain service üzerinden geçmelidir.
- Archive ve delete aynı method veya aynı status mantığıyla karıştırılmamalıdır.
- Media upload private storage hedeflemelidir.
- Analytics eventleri privacy allowlist ile üretilmelidir.
- Hata loglarında raw user input yazılmamalıdır.
- Sync payloadları production loglarda maskelenmelidir.
- Unit testlerde unauthorized scenario zorunlu olmalıdır.
- Integration testlerde RLS veya backend policy doğrulanmalıdır.

---

## 21. Açık Güvenlik Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-SEC-OD-001 | Backend Supabase mi olacak? | Supabase uygunsa RLS zorunlu | Open |
| PM-SEC-OD-002 | Project media storage private mı? | Evet | Open |
| PM-SEC-OD-003 | Signed URL süresi | Kısa süreli, örn. 15-60 dk | Open |
| PM-SEC-OD-004 | Logout sonrası local cache temizlenecek mi? | Kullanıcı seçimi veya güvenli default | Open |
| PM-SEC-OD-005 | Anonymous/local-only mode olacak mı? | V1 için önerilmez | Open |
| PM-SEC-OD-006 | Conflict resolution payload nerede tutulacak? | Local secure storage / local DB | Open |
| PM-SEC-OD-007 | Project ID analytics'te hash kullanılacak mı? | Evet, gerekiyorsa | Open |
| PM-SEC-OD-008 | Draft limit olacak mı? | Abuse riskine göre değerlendirilmeli | Open |
| PM-SEC-OD-009 | Permanent delete job ne zaman çalışacak? | Recovery süresi sonrası | Open |

---

## 22. Sonraki Dosya

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-001-project-management/implementation-notes.md
```

`implementation-notes.md` içinde Project Management için Flutter/Riverpod mimarisi, repository yapısı, domain service tasarımı, local database yaklaşımı, Supabase entegrasyonu, sync stratejisi ve Codex implementasyon yönlendirmeleri tanımlanacaktır.
