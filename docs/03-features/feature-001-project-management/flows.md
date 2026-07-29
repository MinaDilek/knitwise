# Proje Yönetimi — Akışlar

| Alan | Değer |
|---|---|
| Ürün | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Adı | Project Management |
| Dosya | `03-features/feature-001-project-management/flows.md` |
| Öncelik | P0 |
| Planlanan Sürüm | V1 |
| Doküman Durumu | Draft |
| Versiyon | 1.0 |
| Dil | Türkçe |
| Teknik Sabitler | İngilizce korunur |

---

## 1. Amaç

Bu doküman, Knitwise uygulamasındaki **Project Management** özelliği için kullanıcı akışlarını, sistem davranışlarını, alternatif yolları, hata durumlarını ve ekranlar arası geçişleri tanımlar.

Bu dosya aşağıdaki sorulara cevap verir:

- Kullanıcı bir projeyi nasıl oluşturur?
- Proje listesi hangi durumlarda nasıl davranır?
- Proje detay ekranında hangi aksiyonlar bulunur?
- Proje nasıl düzenlenir?
- Proje nasıl duraklatılır, devam ettirilir, tamamlanır, arşivlenir veya silinir?
- Free plan aktif proje limitine ulaşıldığında kullanıcı ne görür?
- Offline durumda proje oluşturma ve düzenleme nasıl çalışır?
- Eksik pattern, yarn, tool veya counter ilişkileri nasıl yönetilir?
- Sync conflict olduğunda kullanıcı deneyimi nasıl olmalıdır?

Bu doküman `requirements.md` içindeki gereksinimlerin kullanıcı yolculuklarına dönüştürülmüş halidir.

---

## 2. Genel Akış Prensipleri

Project Management akışları şu prensiplere göre tasarlanmalıdır:

1. Kullanıcı en kısa yoldan proje oluşturabilmelidir.
2. Proje oluşturmak için pattern, yarn veya tool seçimi zorunlu olmamalıdır.
3. Lokal kayıt remote sync işleminden önce gelmelidir.
4. Kullanıcı geçerli verisini kaybetmemelidir.
5. Arşivleme ve silme kullanıcıya net şekilde ayrıştırılmalıdır.
6. Premium limitler mevcut veriyi silmemelidir.
7. Offline durum normal bir kullanım senaryosu olarak ele alınmalıdır.
8. Eksik optional ilişkiler proje ekranını bozmamalıdır.
9. Status değişiklikleri kullanıcı tarafından anlaşılır olmalıdır.
10. Her kritik aksiyon geri alınabilir veya onaylı olmalıdır.

---

## 3. İlgili Ekranlar

Project Management aşağıdaki ekranları veya UI bölümlerini kullanır:

| Ekran / Bileşen | Açıklama |
|---|---|
| Project List Screen | Kullanıcının projelerini listelediği ana ekran |
| Project Card | Liste içinde tek bir projeyi gösteren kart |
| Empty Project State | Kullanıcının hiç projesi olmadığında görülen durum |
| No Search Result State | Arama sonucu bulunamadığında görülen durum |
| Quick Create Project Sheet | Hızlı proje oluşturma alanı |
| Detailed Create Project Screen | Detaylı proje oluşturma ekranı |
| Edit Project Screen | Proje düzenleme ekranı |
| Project Detail Screen | Proje detay ekranı |
| Project Status Selector | Status değiştirme bileşeni |
| Archive Confirmation Dialog | Arşivleme onayı |
| Delete Confirmation Dialog | Silme onayı |
| Recovery Screen | Silinen projeyi kurtarma ekranı |
| Limit Reached Screen | Free plan limit ekranı |
| Sync Status Indicator | Pending, failed veya conflict sync bilgisini gösteren bileşen |
| Broken Relationship State | Eksik pattern, yarn veya tool ilişkisi için güvenli durum |

---

## 4. Aktörler

| Aktör | Açıklama |
|---|---|
| Free User | Ücretsiz plan kullanıcısı |
| Premium User | Premium plan kullanıcısı |
| Downgraded User | Premium'dan Free plana dönmüş kullanıcı |
| Offline User | İnternet bağlantısı olmayan kullanıcı |
| Returning User | Daha önce proje oluşturmuş kullanıcı |
| New User | Henüz proje oluşturmamış kullanıcı |

---

## 5. Proje Oluşturma Akışları

---

### FLOW-PM-001 — İlk Proje Oluşturma Akışı

#### Amaç

Yeni kullanıcının ilk projesini hızlı ve düşük sürtünmeyle oluşturmasını sağlamak.

#### Başlangıç Koşulları

- Kullanıcı uygulamayı açmıştır.
- Kullanıcının henüz projesi yoktur.
- Kullanıcı Project List ekranındadır.

#### Ana Akış

1. Sistem Project List ekranını açar.
2. Sistem kullanıcının hiç projesi olmadığını tespit eder.
3. Empty Project State gösterilir.
4. Kullanıcı `Create Project` aksiyonuna basar.
5. Sistem Quick Create Project Sheet açar.
6. Kullanıcı proje adını girer.
7. Kullanıcı `Create` aksiyonuna basar.
8. Sistem proje adını validate eder.
9. Sistem aktif proje limiti kontrolü yapar.
10. Sistem lokal proje kaydını oluşturur.
11. Sistem projeyi `active` status ile kaydeder.
12. Sistem sync status değerini bağlantı durumuna göre belirler.
13. Sistem Project Detail ekranına yönlendirir.
14. Project List cache veya state güncellenir.

#### Başarılı Sonuç

- Yeni proje oluşturulur.
- Proje lokal olarak saklanır.
- Kullanıcı proje detay ekranını görür.
- Proje varsayılan listede görünür.

#### Alternatif Akışlar

##### A1 — Kullanıcı Detaylı Oluşturma İster

1. Kullanıcı Quick Create yerine `Detailed Create` seçer.
2. Sistem Detailed Create Project Screen açar.
3. Kullanıcı ek alanları doldurur.
4. Ana akış validasyon adımından devam eder.

##### A2 — Kullanıcı Oluşturmayı İptal Eder

1. Kullanıcı formu kapatır.
2. Sistem girilen veri varsa onay ister.
3. Kullanıcı vazgeçerse form kapanır.
4. Proje oluşturulmaz.

#### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Proje adı boş | Validasyon mesajı gösterilir |
| Lokal kayıt başarısız | Proje oluşturuldu gibi gösterilmez |
| Remote sync başarısız | Proje lokal kalır, sync status `pending` veya `failed` olur |
| Free limit dolu | Limit Reached akışı başlar |

---

### FLOW-PM-002 — Hızlı Proje Oluşturma Akışı

#### Amaç

Kullanıcının yalnızca proje adı girerek hızlıca proje başlatmasını sağlamak.

#### Başlangıç Koşulları

- Kullanıcı Project List veya Project Detail dışındaki uygun bir ekrandadır.
- Kullanıcı proje oluşturma aksiyonuna erişebilir.

#### Ana Akış

1. Kullanıcı `+` veya `Create Project` aksiyonuna basar.
2. Quick Create Project Sheet açılır.
3. Sistem yalnızca proje adı alanını ön plana çıkarır.
4. Kullanıcı proje adını girer.
5. Kullanıcı oluşturmayı onaylar.
6. Sistem whitespace trim uygular.
7. Sistem proje adının boş olmadığını kontrol eder.
8. Sistem entitlement kontrolü yapar.
9. Sistem lokal kayıt oluşturur.
10. Sistem Project Detail ekranına yönlendirir.

#### UI Notları

- Form sade olmalıdır.
- Pattern, yarn ve tool seçimi gösterilmek zorunda değildir.
- Gelişmiş ayarlar ikincil link veya butonla açılabilir.
- Kullanıcıya "Daha sonra detay ekleyebilirsin" mesajı verilebilir.

#### Çıkış Noktaları

- Başarılı oluşturma
- İptal
- Limit reached
- Validasyon hatası
- Lokal kayıt hatası

---

### FLOW-PM-003 — Detaylı Proje Oluşturma Akışı

#### Amaç

Kullanıcının proje bilgilerini başlangıçta daha kapsamlı girmesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı proje oluşturmak istemiştir.
- Kullanıcı detaylı formu seçmiştir.

#### Ana Akış

1. Detailed Create Project Screen açılır.
2. Kullanıcı proje adını girer.
3. Kullanıcı isteğe bağlı açıklama ekler.
4. Kullanıcı teknik seçer veya boş bırakır.
5. Kullanıcı kategori seçer veya boş bırakır.
6. Kullanıcı başlangıç tarihi seçer veya boş bırakır.
7. Kullanıcı hedef bitiş tarihi seçer veya boş bırakır.
8. Kullanıcı pattern seçer veya boş bırakır.
9. Kullanıcı yarn seçer veya boş bırakır.
10. Kullanıcı hook / needle seçer veya boş bırakır.
11. Kullanıcı kapak görseli seçer veya boş bırakır.
12. Kullanıcı oluşturmayı onaylar.
13. Sistem validasyonları çalıştırır.
14. Sistem entitlement kontrolü yapar.
15. Sistem core project kaydını lokal oluşturur.
16. Sistem ilişkileri lokal oluşturur.
17. Sistem görseli optimize eder.
18. Sistem remote sync için kayıtları sıraya alır.
19. Sistem Project Detail ekranına yönlendirir.

#### Alternatif Akışlar

##### A1 — Pattern Seçimi Sonradan Yapılır

- Kullanıcı pattern seçmeden proje oluşturabilir.
- Project Detail ekranında Pattern boş state gösterilir.
- Kullanıcı daha sonra pattern ekleyebilir.

##### A2 — Yarn Seçimi Sonradan Yapılır

- Kullanıcı yarn seçmeden proje oluşturabilir.
- Project Detail ekranında Material boş state gösterilir.

##### A3 — Görsel Upload Başarısız

- Core project kaydı korunur.
- Görsel için retry yapılabilir.
- Kullanıcıya non-blocking uyarı gösterilir.

#### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Target date start date'den önce | Validasyon mesajı |
| Pattern erişilemez | Pattern ilişkilendirilmez, proje kaydı devam eder |
| Yarn erişilemez | Yarn ilişkilendirilmez, proje kaydı devam eder |
| Görsel formatı desteklenmez | Görsel alanı reddedilir, proje oluşturma devam edebilir |
| Lokal core kayıt başarısız | Proje oluşturulmaz |

---

## 6. Proje Listeleme Akışları

---

### FLOW-PM-004 — Varsayılan Proje Listesi Akışı

#### Amaç

Kullanıcının aktif olarak üzerinde çalıştığı projeleri hızlıca görmesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı Project List ekranını açar.
- Kullanıcının lokal veya remote proje kayıtları vardır.

#### Ana Akış

1. Sistem lokal proje kayıtlarını okur.
2. Sistem `deletedAt` dolu projeleri hariç tutar.
3. Sistem varsayılan status filtresini uygular:
   - `active`
   - `paused`
4. Sistem projeleri `updatedAt descending` sıralar.
5. Sistem proje kartlarını gösterir.
6. Sistem remote sync başlatabilir.
7. Remote veri geldiyse liste güncellenir.

#### Kartta Gösterilecek Minimum Alanlar

- Proje adı
- Status
- Kapak görseli veya placeholder
- Son güncellenme bilgisi

#### Alternatif Akışlar

##### A1 — Hiç Proje Yok

- Empty Project State gösterilir.
- Kullanıcıya proje oluşturma aksiyonu sunulur.

##### A2 — Sadece Completed / Archived Proje Var

- Varsayılan listede boş state gösterilebilir.
- Sistem kullanıcıya "Completed veya Archived projelerini görüntüle" aksiyonu sunabilir.

##### A3 — Sync Pending Projeler Var

- Kart üzerinde küçük sync indicator gösterilebilir.
- Kullanıcı projeyi açabilir.

---

### FLOW-PM-005 — Proje Arama Akışı

#### Amaç

Kullanıcının proje adı üzerinden proje bulmasını sağlamak.

#### Başlangıç Koşulları

- Kullanıcı Project List ekranındadır.
- Kullanıcının bir veya daha fazla projesi vardır.

#### Ana Akış

1. Kullanıcı arama alanına metin girer.
2. Sistem input üzerinde normalize işlemi yapar:
   - Trim
   - Case-insensitive karşılaştırma
   - Türkçe karakter desteği
3. Sistem mevcut liste kapsamındaki projelerde arama yapar.
4. Eşleşen projeler gösterilir.
5. Kullanıcı projeye tıklarsa Project Detail açılır.

#### Alternatif Akışlar

##### A1 — Sonuç Yok

- No Search Result State gösterilir.
- Kullanıcıya aramayı temizleme aksiyonu sunulur.

##### A2 — Kullanıcı Aramayı Temizler

- Varsayılan liste geri gelir.

#### Özel Not

Arama, başka kullanıcıya ait projeleri asla döndürmemelidir.

---

### FLOW-PM-006 — Filtreleme Akışı

#### Amaç

Kullanıcının projeleri status, teknik, kategori veya ilişki durumuna göre daraltmasını sağlamak.

#### Ana Akış

1. Kullanıcı filtre panelini açar.
2. Sistem desteklenen filtreleri gösterir.
3. Kullanıcı bir veya daha fazla filtre seçer.
4. Sistem filtreleri uygular.
5. Aktif filtreler UI üzerinde gösterilir.
6. Kullanıcı proje kartlarını görüntüler.

#### Desteklenen V1 Filtreleri

- Status
- Teknik
- Kategori
- Pattern bağlı mı?
- Counter var mı?
- Yakın zamanda güncellendi mi?

#### Alternatif Akışlar

##### A1 — Filtre Sonucu Yok

- Empty Filter Result State gösterilir.
- `Clear Filters` aksiyonu sunulur.

##### A2 — Tek Filtre Kaldırma

- Kullanıcı aktif filtre chip'ini kaldırır.
- Liste yeniden hesaplanır.

##### A3 — Tüm Filtreleri Temizleme

- Kullanıcı `Clear All` aksiyonunu seçer.
- Varsayılan liste geri yüklenir.

---

### FLOW-PM-007 — Sıralama Akışı

#### Amaç

Kullanıcının proje listesini ihtiyacına göre sıralamasını sağlamak.

#### Ana Akış

1. Kullanıcı sıralama menüsünü açar.
2. Sistem desteklenen sıralama seçeneklerini gösterir.
3. Kullanıcı sıralama seçer.
4. Sistem listeyi yeniden sıralar.
5. Seçili sıralama UI üzerinde gösterilir.

#### Desteklenen Sıralamalar

- Son güncellenen
- İlk güncellenen
- Yeni oluşturulan
- Eski oluşturulan
- Ada göre A-Z
- Ada göre Z-A

#### Varsayılan

```text
updatedAt descending
```

---

## 7. Proje Detay Akışları

---

### FLOW-PM-008 — Proje Detayına Girme Akışı

#### Amaç

Kullanıcının seçtiği projeye ait tüm ilgili bilgileri güvenli şekilde görüntülemesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı proje kartına tıklar.
- Proje mevcut kullanıcıya ait olmalıdır.

#### Ana Akış

1. Kullanıcı Project Card seçer.
2. Sistem `projectId` ile lokal kayıt arar.
3. Sistem `ownerId` kontrolü yapar.
4. Sistem `deletedAt` kontrolü yapar.
5. Sistem Project Detail ekranını açar.
6. Core project bilgileri gösterilir.
7. Optional ilişkiler kademeli yüklenir:
   - Pattern
   - Yarn
   - Tool
   - Counter
   - Part
8. Eksik ilişkiler varsa safe state gösterilir.
9. Sync status varsa gösterilir.

#### Hata Durumları

| Durum | Beklenen Davranış |
|---|---|
| Proje bulunamadı | Safe not-found state |
| Kullanıcı owner değil | Unauthorized veya generic not-found |
| Proje deleted | Normal detay gösterilmez |
| Pattern eksik | Pattern unavailable state |
| Yarn eksik | Material unavailable state |
| Counter bozuk | Counter section safe fallback |

---

### FLOW-PM-009 — Proje Detayından İlgili Modüle Geçiş

#### Amaç

Project Detail ekranının diğer modüllere merkez giriş noktası olmasını sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranındadır.
2. Kullanıcı ilgili modül aksiyonunu seçer:
   - Open Pattern
   - Open Row Counter
   - Open Parts
   - Open Yarn
   - Open Tools
   - Open Notes
3. Sistem ilgili relationship var mı kontrol eder.
4. İlgili ekran açılır.
5. Açılan ekran `projectId` context ile çalışır.

#### Alternatifler

##### A1 — İlişki Yok

- Sistem boş state gösterir.
- Kullanıcıya yeni ilişki oluşturma veya bağlama aksiyonu sunulur.

##### A2 — İlişki Bozuk

- Sistem broken relationship state gösterir.
- Kullanıcı kaldırabilir veya değiştirebilir.

---

## 8. Proje Düzenleme Akışları

---

### FLOW-PM-010 — Proje Bilgilerini Düzenleme

#### Amaç

Kullanıcının proje bilgilerini güvenli şekilde değiştirmesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı proje sahibidir.
- Proje soft deleted değildir.

#### Ana Akış

1. Kullanıcı Project Detail ekranında `Edit` aksiyonuna basar.
2. Edit Project Screen açılır.
3. Mevcut proje bilgileri forma doldurulur.
4. Kullanıcı alanları düzenler.
5. Kullanıcı `Save` aksiyonuna basar.
6. Sistem validasyonları çalıştırır.
7. Sistem lokal kaydı günceller.
8. `updatedAt` yenilenir.
9. Sync status bağlantıya göre belirlenir.
10. Kullanıcı Project Detail ekranına geri döner.
11. Güncel bilgiler gösterilir.

#### Düzenlenebilir Alanlar

- Proje adı
- Açıklama
- Teknik
- Kategori
- Kapak görseli
- Tarihler
- Pattern ilişkisi
- Yarn ilişkileri
- Tool ilişkileri
- Manual progress
- Notlar

#### Alternatif Akışlar

##### A1 — Kullanıcı Değişiklikleri İptal Eder

1. Kullanıcı geri gider veya cancel seçer.
2. Sistem değişiklik var mı kontrol eder.
3. Değişiklik varsa confirmation gösterir.
4. Kullanıcı discard seçerse değişiklikler atılır.
5. Kullanıcı continue editing seçerse forma döner.

##### A2 — Auto-save Notes

- Notes alanı auto-save destekliyorsa ayrı kaydetme gerekmeden lokal save yapılabilir.
- UI save status göstermelidir.

#### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Proje adı boş | Validasyon |
| Tarih uyumsuz | Validasyon |
| Lokal save başarısız | Kullanıcı verisi korunur |
| Remote sync başarısız | Lokal veri korunur, pending/failed sync |

---

### FLOW-PM-011 — Kapak Görseli Değiştirme

#### Amaç

Kullanıcının proje kapak görselini eklemesini, değiştirmesini veya kaldırmasını sağlamak.

#### Ana Akış — Görsel Ekleme

1. Kullanıcı cover image alanına basar.
2. Sistem kaynak seçeneklerini gösterir:
   - Photo Library
   - Camera
3. Kullanıcı görsel seçer.
4. Sistem format ve boyut kontrolü yapar.
5. Sistem görseli optimize eder.
6. Lokal referans kaydedilir.
7. Upload gerekiyorsa sync queue'ya alınır.
8. Project Detail güncellenir.

#### Ana Akış — Görsel Kaldırma

1. Kullanıcı mevcut görseli kaldır seçer.
2. Sistem confirmation gösterebilir.
3. Görsel ilişkisi kaldırılır.
4. Placeholder gösterilir.
5. Eski remote dosya cleanup için işaretlenebilir.

#### Hata Durumları

| Hata | Beklenen Davranış |
|---|---|
| Format desteklenmiyor | Kullanıcı uyarılır |
| Görsel çok büyük | Sıkıştırılır veya reddedilir |
| Upload başarısız | Project kaydı korunur |
| Lokal dosya kayboldu | Placeholder gösterilir |

---

## 9. Status Yönetimi Akışları

---

### FLOW-PM-012 — Projeyi Duraklatma

#### Amaç

Kullanıcının aktif projeyi geçici olarak durdurmasını sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranındadır.
2. Kullanıcı status selector veya action menu açar.
3. `Pause Project` seçer.
4. Sistem status geçişinin geçerli olduğunu kontrol eder.
5. Proje status `paused` yapılır.
6. `updatedAt` güncellenir.
7. Proje verileri korunur.
8. Kullanıcıya success feedback gösterilir.

#### Sonuç

- Proje default listede görünmeye devam edebilir.
- Paused badge gösterilir.
- Counter ve parts verileri değişmez.
- `completedAt` oluşmaz.

---

### FLOW-PM-013 — Projeyi Devam Ettirme

#### Amaç

Paused projenin tekrar active hale gelmesini sağlamak.

#### Ana Akış

1. Kullanıcı paused proje detayını açar.
2. `Resume Project` aksiyonuna basar.
3. Sistem entitlement kontrolü yapar.
4. Limit uygunsa status `active` yapılır.
5. `updatedAt` güncellenir.
6. Kullanıcı proje üzerinde çalışmaya devam eder.

#### Alternatif

##### A1 — Limit Dolu

- Limit Reached akışı başlar.
- Status değişmez.

---

### FLOW-PM-014 — Projeyi Tamamlama

#### Amaç

Kullanıcının bitirdiği projeyi completed durumuna almasını sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranındadır.
2. Kullanıcı `Complete Project` aksiyonuna basar.
3. Sistem confirmation gösterir.
4. Kullanıcı onaylar.
5. Sistem status değerini `completed` yapar.
6. `completedAt` doldurulur.
7. `updatedAt` güncellenir.
8. Proje default active listeden çıkar.
9. Completed view içinde görünür.
10. Kullanıcıya success feedback gösterilir.

#### Alternatif Akışlar

##### A1 — Kullanıcı Completion Date Seçer

- Kullanıcının seçtiği tarih valid ise `completedAt` olarak kullanılır.

##### A2 — Kullanıcı Vazgeçer

- Status değişmez.
- Proje active veya paused kalır.

---

### FLOW-PM-015 — Completed Projeyi Yeniden Açma

#### Amaç

Kullanıcının tamamlanmış projeyi tekrar çalışma durumuna almasını sağlamak.

#### Ana Akış

1. Kullanıcı completed proje detayını açar.
2. `Reopen Project` aksiyonuna basar.
3. Sistem aktif proje limitini kontrol eder.
4. Limit uygunsa hedef status seçilir:
   - `active`
   - `paused`
5. Sistem status değerini günceller.
6. `completedAt` iş kuralına göre temizlenir veya geçmişe alınır.
7. Proje ilgili listede görünür.

#### Alternatif

##### A1 — Free Limit Dolu

- Reopen engellenir.
- Kullanıcıya Limit Reached ekranı gösterilir.
- Proje completed kalır.

---

### FLOW-PM-016 — Projeyi Arşivleme

#### Amaç

Kullanıcının projeyi silmeden aktif çalışma alanından kaldırmasını sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranında action menu açar.
2. `Archive Project` seçer.
3. Sistem Archive Confirmation Dialog gösterir.
4. Kullanıcı onaylar.
5. Status `archived` yapılır.
6. `archivedAt` doldurulur.
7. Proje default listeden çıkar.
8. Archived view içinde görünür.

#### Dialog Mesajı İçeriği

Dialog şu fikri net vermelidir:

- Proje silinmez.
- İstendiğinde geri alınabilir.
- Varsayılan aktif listeden kaldırılır.

---

### FLOW-PM-017 — Arşivden Geri Alma

#### Amaç

Kullanıcının archived projeyi yeniden görünür ve kullanılabilir hale getirmesini sağlamak.

#### Ana Akış

1. Kullanıcı Archived Projects görünümünü açar.
2. Arşivlenmiş projeyi seçer.
3. `Restore Project` aksiyonuna basar.
4. Sistem hedef status belirler veya kullanıcıdan seçmesini ister.
5. Hedef status active ise entitlement kontrolü yapılır.
6. Uygunsa status değiştirilir.
7. `archivedAt` temizlenir.
8. Proje ilgili listede görünür.

#### Alternatif

##### A1 — Limit Dolu

- Restore to active engellenir.
- Proje archived kalır.
- Kullanıcıya seçenekler sunulur:
  - Başka projeyi archive et
  - Başka projeyi complete et
  - Premium'a geç

---

## 10. Silme ve Kurtarma Akışları

---

### FLOW-PM-018 — Projeyi Silme

#### Amaç

Kullanıcının projeyi bilinçli ve güvenli şekilde silmesini sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranında action menu açar.
2. `Delete Project` seçer.
3. Sistem Delete Confirmation Dialog gösterir.
4. Dialog arşivleme ile silme farkını açıklar.
5. Kullanıcı silmeyi onaylar.
6. Sistem `deletedAt` doldurur.
7. Proje normal listelerden çıkar.
8. Proje recovery süresi boyunca kurtarılabilir kalır.
9. Kullanıcı Project List ekranına yönlendirilir.

#### Dialog İçeriği

Silme dialogu şu bilgileri içermelidir:

- Bu işlem projeyi aktif listelerden kaldırır.
- Belirli süre içinde geri alınabilir olabilir.
- Arşivlemek daha güvenli bir alternatif olabilir.
- Silme işlemi pattern, yarn veya inventory kayıtlarını otomatik silmez.

#### Alternatif Akış

##### A1 — Kullanıcı Archive Seçer

- Sistem silme yerine archive flow başlatır.

##### A2 — Kullanıcı Vazgeçer

- Dialog kapanır.
- Proje değişmeden kalır.

---

### FLOW-PM-019 — Silinen Projeyi Kurtarma

#### Amaç

Kullanıcının recovery süresi içinde soft deleted projeyi geri almasını sağlamak.

#### Başlangıç Koşulları

- Projenin `deletedAt` değeri doludur.
- Recovery süresi dolmamıştır.

#### Ana Akış

1. Kullanıcı recovery görünümünü açar veya deleted deep link üzerinden recovery state görür.
2. Sistem silinen projeyi listeler.
3. Kullanıcı `Restore` aksiyonuna basar.
4. Sistem entitlement ve status kurallarını kontrol eder.
5. `deletedAt` temizlenir.
6. Proje uygun status ile geri döner.
7. Kullanıcıya success feedback gösterilir.

#### Alternatif

##### A1 — Recovery Süresi Dolmuş

- Restore aksiyonu gösterilmez.
- Proje kalıcı silinmiş veya silinmek üzere olabilir.

##### A2 — Restore Active Limit Doldu

- Restore active engellenir.
- Kullanıcı archived veya completed olarak restore edebiliyorsa seçenek sunulabilir.

---

## 11. Premium ve Limit Akışları

---

### FLOW-PM-020 — Free Plan Limit Doldu Akışı

#### Amaç

Free kullanıcının aktif proje limitine ulaştığında veri kaybı olmadan yönlendirilmesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı Free plandadır.
- Aktif proje sayısı limit değerine eşittir veya üzerindedir.
- Kullanıcı yeni active proje oluşturmak veya restore etmek ister.

#### Ana Akış

1. Kullanıcı active proje oluşturmayı veya aktif hale getirmeyi dener.
2. Sistem aktif proje sayısını hesaplar.
3. Sistem limitin dolduğunu tespit eder.
4. Limit Reached Screen gösterilir.
5. Kullanıcıya seçenekler sunulur:
   - Mevcut bir projeyi archive et
   - Mevcut bir projeyi complete et
   - Premium'a yükselt
   - İşlemi iptal et
6. Kullanıcı seçim yapar.
7. Seçime göre ilgili akış başlatılır.

#### Önemli Kural

Sistem mevcut projeleri otomatik olarak:

- Silmemeli
- Gizlememeli
- Archived yapmamalı
- Completed yapmamalıdır

---

### FLOW-PM-021 — Premium Downgrade Akışı

#### Amaç

Premium'dan Free plana dönen kullanıcının mevcut projelerini kaybetmemesini sağlamak.

#### Başlangıç Koşulları

- Kullanıcı daha önce Premium kullanmıştır.
- Kullanıcı Free plana dönmüştür.
- Aktif proje sayısı Free limitin üzerindedir.

#### Ana Akış

1. Sistem entitlement durumunu günceller.
2. Kullanıcının aktif proje sayısı hesaplanır.
3. Limit aşımı tespit edilir.
4. Kullanıcı Project List açar.
5. Tüm mevcut projeler görünür ve erişilebilir kalır.
6. Sistem yeni active proje oluşturmayı kısıtlayabilir.
7. Kullanıcıya açıklama gösterilir.

#### Kullanıcıya Gösterilecek Mesaj Mantığı

Mesaj şu fikri vermelidir:

- Mevcut projelerin korunuyor.
- Free plan limitinin üzerindesin.
- Yeni active proje oluşturmak için bazı projeleri tamamlaman, arşivlemen veya Premium'a geçmen gerekiyor.

---

## 12. Offline ve Sync Akışları

---

### FLOW-PM-022 — Offline Proje Oluşturma

#### Amaç

Kullanıcının internet bağlantısı olmadan da proje oluşturabilmesini sağlamak.

#### Ana Akış

1. Sistem bağlantının olmadığını tespit eder.
2. Kullanıcı proje oluşturma formunu açar.
3. Kullanıcı geçerli proje bilgilerini girer.
4. Sistem lokal kayıt oluşturur.
5. Sync status `pending` yapılır.
6. Proje listede görünür.
7. Kullanıcı proje detayını açabilir.
8. Bağlantı geri geldiğinde sync başlatılır.

#### Başarılı Sonuç

- Kullanıcı offline olmasına rağmen çalışmaya devam eder.
- Veri lokal olarak korunur.
- Remote sync sonradan yapılır.

---

### FLOW-PM-023 — Offline Proje Düzenleme

#### Amaç

Kullanıcının internet yokken mevcut projeyi düzenleyebilmesini sağlamak.

#### Ana Akış

1. Kullanıcı lokal mevcut projeyi açar.
2. Kullanıcı edit ekranına girer.
3. Kullanıcı değişiklik yapar.
4. Sistem lokal validasyon çalıştırır.
5. Sistem lokal kaydı günceller.
6. Sync status `pending` yapılır.
7. Kullanıcı güncel veriyi görür.
8. Bağlantı geldiğinde değişiklikler remote'a gönderilir.

---

### FLOW-PM-024 — Sync Retry Akışı

#### Amaç

Başarısız sync işlemlerinin güvenli şekilde yeniden denenmesini sağlamak.

#### Ana Akış

1. Sistem sync status `failed` veya `pending` olan kayıtları tespit eder.
2. Bağlantı uygun hale gelir.
3. Sistem retry kuyruğunu işler.
4. Her işlem idempotent şekilde gönderilir.
5. Başarılı işlemler `synced` yapılır.
6. Başarısız işlemler retry policy'e göre bekletilir.
7. Kullanıcıya gerekiyorsa non-blocking sync bilgisi gösterilir.

---

### FLOW-PM-025 — Sync Conflict Akışı

#### Amaç

Aynı projenin birden fazla cihazda değiştirilmesi durumunda veri kaybını önlemek.

#### Başlangıç Koşulları

- Aynı proje iki cihazda düzenlenmiştir.
- En az bir cihaz offline çalışmıştır.
- Remote sync sırasında conflict tespit edilir.

#### Ana Akış

1. Sistem remote ve local versiyon arasında conflict tespit eder.
2. Sync status `conflict` yapılır.
3. Kullanıcıya conflict state gösterilir.
4. Kullanıcıya uygun seçenekler sunulur:
   - Local version kullan
   - Remote version kullan
   - Manuel birleştir
5. Kullanıcı seçim yapar.
6. Sistem seçilen stratejiyi uygular.
7. Sync yeniden denenir.
8. Conflict çözülürse status `synced` yapılır.

#### V1 Basitleştirme Önerisi

V1'de tüm alanlar için gelişmiş merge yerine şu yaklaşım uygulanabilir:

- Core metadata için last-write-wins
- Notes için manuel conflict
- Relationships için append-safe yaklaşım
- Media için en son başarılı upload

Detaylı teknik strateji `implementation-notes.md` içinde tanımlanmalıdır.

---

## 13. Broken Relationship Akışları

---

### FLOW-PM-026 — Eksik Pattern İlişkisi

#### Amaç

Pattern kaydı silinmiş veya erişilemez olsa bile projenin çalışmasını sağlamak.

#### Ana Akış

1. Kullanıcı Project Detail ekranını açar.
2. Sistem linked pattern kaydını yüklemeye çalışır.
3. Pattern bulunamaz veya erişilemez.
4. Sistem project detail ekranını açmaya devam eder.
5. Pattern alanında unavailable state gösterilir.
6. Kullanıcıya seçenek sunulur:
   - Pattern ilişkisinin kaldırılması
   - Başka pattern seçilmesi

#### Beklenen

- Proje açılır.
- Uygulama crash olmaz.
- Project core data korunur.

---

### FLOW-PM-027 — Eksik Yarn veya Tool İlişkisi

#### Amaç

Inventory ilişkisinin bozulması durumunda proje ekranının güvenli kalmasını sağlamak.

#### Ana Akış

1. Sistem linked yarn veya tool kaydını yüklemeye çalışır.
2. Kayıt bulunamaz.
3. UI unavailable item state gösterir.
4. Kullanıcı ilişkiyi kaldırabilir veya değiştirebilir.
5. Ana inventory kaydı yoksa yeniden oluşturma opsiyonu sunulabilir.

---

### FLOW-PM-028 — Eksik Counter veya Part İlişkisi

#### Amaç

Counter veya part verisi bozulsa bile proje detay ekranını korumak.

#### Ana Akış

1. Project Detail açılır.
2. Sistem counter veya part özetini yükler.
3. Kayıt eksik veya bozuksa section fallback state gösterilir.
4. Kullanıcı yeni counter veya part oluşturabilir.
5. Core project ekranı çalışmaya devam eder.

---

## 14. Progress Akışları

---

### FLOW-PM-029 — Proje Progress Hesaplama

#### Amaç

Proje ilerlemesinin doğru kaynaktan gösterilmesini sağlamak.

#### Ana Akış

1. Project Detail veya Project Card progress alanı yüklenir.
2. Sistem progress kaynaklarını kontrol eder.
3. Öncelik sırası uygulanır:
   1. Multi-part completion
   2. Target-based row counter progress
   3. Manual progress
   4. No progress
4. Geçerli kaynak bulunursa progress gösterilir.
5. Kaynak yoksa progress not set state gösterilir.

#### Kurallar

- Sistem ilgisiz progress kaynaklarını ortalamamalıdır.
- 100 üzeri değerler güvenli gösterilmelidir.
- Target zero ise progress hesaplanmamalıdır.

---

## 15. Deep Link ve Notification Akışları

---

### FLOW-PM-030 — Archived Projeye Deep Link

#### Amaç

Archived projeye eski link veya notification üzerinden gidildiğinde güvenli deneyim sağlamak.

#### Ana Akış

1. Kullanıcı deep link açar.
2. Sistem projectId ile proje arar.
3. Proje `archived` durumdadır.
4. Project Detail açılır.
5. Archived badge gösterilir.
6. Çalışma aksiyonları disabled olabilir veya restore gerektirebilir.

---

### FLOW-PM-031 — Deleted Projeye Deep Link

#### Amaç

Silinmiş projeye stale link üzerinden erişimde güvenli davranmak.

#### Ana Akış

1. Kullanıcı deep link açar.
2. Sistem projectId ile proje arar.
3. Proje `deletedAt` doludur veya bulunamaz.
4. Normal Project Detail gösterilmez.
5. Safe not-found veya recovery state gösterilir.

---

## 16. Kullanıcı Akışı Özetleri

### 16.1 Yeni Kullanıcı

```text
Open App
→ Project List
→ Empty State
→ Create Project
→ Quick Create
→ Project Detail
```

### 16.2 Aktif Kullanıcı

```text
Open App
→ Project List
→ Select Project
→ Project Detail
→ Open Counter / Edit / Update Status
```

### 16.3 Free Limit Dolmuş Kullanıcı

```text
Create Project
→ Entitlement Check
→ Limit Reached
→ Archive / Complete / Upgrade / Cancel
```

### 16.4 Offline Kullanıcı

```text
Create or Edit Project
→ Local Save
→ Sync Pending
→ Continue Working
→ Connectivity Restored
→ Retry Sync
```

### 16.5 Broken Relationship Durumu

```text
Open Project
→ Relationship Load Failed
→ Core Detail Still Visible
→ Unavailable State
→ Remove or Replace Relationship
```

---

## 17. Akış ve Gereksinim Eşleştirmesi

| Flow ID | İlgili Requirements |
|---|---|
| FLOW-PM-001 | PM-FR-001, PM-FR-002, PM-FR-005, PM-FR-037 |
| FLOW-PM-002 | PM-FR-001, PM-FR-002 |
| FLOW-PM-003 | PM-FR-003, PM-FR-027, PM-FR-028, PM-FR-029, PM-FR-030 |
| FLOW-PM-004 | PM-FR-007, PM-FR-008, PM-FR-025 |
| FLOW-PM-005 | PM-FR-023 |
| FLOW-PM-006 | PM-FR-024 |
| FLOW-PM-007 | PM-FR-025 |
| FLOW-PM-008 | PM-FR-010, PM-FR-011, PM-FR-040 |
| FLOW-PM-009 | PM-FR-028, PM-FR-029, PM-FR-030, PM-FR-031, PM-FR-032 |
| FLOW-PM-010 | PM-FR-012, PM-FR-013 |
| FLOW-PM-011 | PM-FR-027 |
| FLOW-PM-012 | PM-FR-015 |
| FLOW-PM-013 | PM-FR-016, PM-FR-037 |
| FLOW-PM-014 | PM-FR-017 |
| FLOW-PM-015 | PM-FR-018, PM-FR-037 |
| FLOW-PM-016 | PM-FR-019 |
| FLOW-PM-017 | PM-FR-020, PM-FR-037 |
| FLOW-PM-018 | PM-FR-021, PM-FR-022 |
| FLOW-PM-019 | PM-FR-021 |
| FLOW-PM-020 | PM-FR-037, PM-FR-038 |
| FLOW-PM-021 | PM-FR-039 |
| FLOW-PM-022 | PM-FR-034, PM-FR-036 |
| FLOW-PM-023 | PM-FR-035, PM-FR-036 |
| FLOW-PM-024 | PM-FR-036 |
| FLOW-PM-025 | PM-FR-036 |
| FLOW-PM-026 | PM-FR-028, PM-FR-040 |
| FLOW-PM-027 | PM-FR-029, PM-FR-030, PM-FR-040 |
| FLOW-PM-028 | PM-FR-031, PM-FR-032, PM-FR-040 |
| FLOW-PM-029 | PM-FR-033 |
| FLOW-PM-030 | PM-FR-019, PM-FR-020 |
| FLOW-PM-031 | PM-FR-021 |

---

## 18. Açık Akış Kararları

| ID | Karar | Öneri | Durum |
|---|---|---|---|
| PM-FLOW-OD-001 | Quick create sonrası direkt detail mi list mi? | Detail | Open |
| PM-FLOW-OD-002 | Archived proje detail ekranından editlenebilir mi? | Önce restore | Open |
| PM-FLOW-OD-003 | Deleted project recovery ekranı V1'de görünür mü? | Evet | Open |
| PM-FLOW-OD-004 | Conflict resolution UI V1'de ne kadar gelişmiş olacak? | Basit seçim ekranı | Open |
| PM-FLOW-OD-005 | Notes auto-save olacak mı? | Evet, mümkünse | Open |
| PM-FLOW-OD-006 | Limit reached ekranında Premium CTA ne kadar baskın olacak? | Dengeli | Open |

---

## 19. Codex İçin Uygulama Notları

Bu dosya akış davranışlarını tanımlar. Kod uygulamasında şu prensipler korunmalıdır:

- Flow logic UI içine dağınık yazılmamalıdır.
- Status transition kuralları domain/service katmanında merkezi olmalıdır.
- Entitlement check ayrı servis üzerinden yapılmalıdır.
- Local save ve remote sync birbirinden ayrılmalıdır.
- Project Detail ekranı optional relationship hatalarında crash olmamalıdır.
- Tüm critical aksiyonlar test edilebilir olmalıdır.
- Delete ve archive kesinlikle farklı aksiyonlar olarak modellenmelidir.
- Deep link erişimlerinde ownership ve deleted state kontrolü yapılmalıdır.

Detaylı teknik çözüm `implementation-notes.md` içinde yazılacaktır.

---

## 20. Sonraki Dosyalar

Bu dosyadan sonra hazırlanacak dosya:

```text
03-features/feature-001-project-management/data-model.md
```

`data-model.md` içinde bu akışları destekleyecek entity, field, relationship, enum, index, migration ve sync model detayları tanımlanacaktır.

