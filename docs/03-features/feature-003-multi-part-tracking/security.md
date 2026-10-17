# Multi-Part Tracking — Security

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-003 |
| Feature Adı | Multi-Part Tracking |
| Dosya | `03-features/feature-003-multi-part-tracking/security.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Multi-Part Tracking özelliği için security, ownership, authorization, privacy, offline sync güvenliği ve abuse prevention kurallarını tanımlar.

Part bilgileri kullanıcı projesinin detaylarını, notlarını ve ilerleme bilgisini içerdiği için kişisel veri kabul edilmelidir.

---

## 2. Güvenlik Prensipleri

1. Her part `ownerId` ile scope edilmelidir.
2. `partId` tek başına güvenlik için yeterli değildir.
3. Part create/update/delete işlemleri owner check yapmalıdır.
4. Part project’e bağlıysa project owner doğrulanmalıdır.
5. Part-counter link için iki tarafın owner ve project ilişkisi doğrulanmalıdır.
6. Part name ve notes analytics/log içine gönderilmemelidir.
7. Offline changes sync sırasında yeniden doğrulanmalıdır.
8. Deleted part normal listelerde görünmemelidir.
9. Error mesajları başka kullanıcıya ait veri varlığını sızdırmamalıdır.

---

## 3. Korunacak Veri Türleri

| Veri | Hassasiyet | Açıklama |
|---|---|---|
| Part name | Orta | Kullanıcı özel isim verebilir |
| Notes | Yüksek | Kişisel not/pattern bilgisi içerebilir |
| Progress | Orta | Proje ilerlemesi |
| Status | Orta | Kullanıcı davranışı |
| Project relationship | Orta | Project bağlamı |
| Counter relationship | Orta | Kullanım davranışı |
| Sync metadata | Orta | Cihaz/sync bilgisi |

---

## 4. Authorization Kuralları

### Part Read

```text
part.ownerId == currentUser.id
part.deletedAt == null
```

### Part Write

```text
part.ownerId == currentUser.id
part.deletedAt == null
```

### Project Relationship

```text
project.ownerId == currentUser.id
project.deletedAt == null
```

### Counter Relationship

```text
part.ownerId == currentUser.id
counter.ownerId == currentUser.id
part.projectId == counter.projectId
```

---

## 5. RLS / Backend Policy

Supabase kullanılırsa tüm part tablolarında:

```sql
owner_id = auth.uid()
```

policy zorunludur.

`project_parts` read policy:

```sql
owner_id = auth.uid()
AND deleted_at IS NULL
```

`part_counter_links` policy:

```sql
owner_id = auth.uid()
```

Ek backend doğrulama:

- `project_id` current user’a ait mi?
- `counter_id` aynı user ve project içinde mi?
- `part_id` aynı user ve project içinde mi?

---

## 6. Offline Security

Offline part create/update:

- Local user context ile `ownerId` atanır.
- Sync sırasında server auth user ile doğrulanır.
- Pending part başka kullanıcı altında sync edilmemelidir.
- Logout sonrası başka kullanıcı local partları görmemelidir.

---

## 7. Soft Delete Security

Part silindiğinde:

```text
deletedAt = now
```

Normal query:

```text
deletedAt == null
```

Deleted part üzerinde şu işlemler engellenmelidir:

- Edit
- Status change
- Progress update
- Counter link
- Reorder

---

## 8. Reorder Security

Part reorder sırasında:

- Tüm partlar aynı owner/project içinde olmalıdır.
- Kullanıcı başka project’in partOrder değerini değiştirememelidir.
- Batch update transaction içinde yapılmalıdır.

---

## 9. Analytics ve Logging Privacy

Analytics/log içine gönderilmez:

```text
partName
name
notes
projectName
counterName
patternText
rawUserText
```

Güvenli alanlar:

```text
partType
partStatus
progressBucket
hasLinkedCounter
syncStatus
errorCode
durationMs
```

---

## 10. Error Message Güvenliği

Unauthorized veya not found için önerilen mesaj:

```text
Bu parça bulunamadı veya erişim izniniz yok.
```

Başka kullanıcıya ait olduğunu açıkça söyleme.

---

## 11. Abuse Prevention

Riskler:

- Çok fazla part oluşturma
- Çok hızlı reorder
- Çok büyük notes
- Offline queue şişmesi
- Broken relationship spam

Önlemler:

- Notes length limit
- Soft limits
- Batch sync
- Reorder debounce
- Operation retry backoff
- Queue size monitoring

---

## 12. Security Testleri

- Kullanıcı kendi part’ını okuyabilir.
- Başka kullanıcının part’ını okuyamaz.
- Başka kullanıcının part’ını update edemez.
- Başka kullanıcının project’ine part oluşturamaz.
- Başka kullanıcının counter’ını part’a bağlayamaz.
- Deleted part update edilemez.
- Reorder sadece aynı owner/project içinde çalışır.
- Analytics part name ve notes içermez.
- Offline sync revalidation yapar.

---

## 13. Security Checklist

- [ ] Tüm query’ler `ownerId` scope içeriyor.
- [ ] Project relationship owner check yapıyor.
- [ ] Counter relationship owner/project check yapıyor.
- [ ] Deleted part normal listede görünmüyor.
- [ ] Reorder transaction owner/project scope ile çalışıyor.
- [ ] Analytics PII içermiyor.
- [ ] Logs raw notes içermiyor.
- [ ] Sync revalidation yapıyor.
- [ ] Unauthorized error generic.

---

## 14. Sonraki Dosya

```text
03-features/feature-003-multi-part-tracking/implementation-notes.md
```
