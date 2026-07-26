# Dokümantasyon Standartları

Her ana belgede aşağıdaki front matter bulunmalıdır:

```yaml
---
id:
title:
version: 0.1.0
status: Draft
owner:
priority:
release:
last_updated: 2026-07-26
dependencies: []
related_features: []
related_screens: []
---
```

## Durumlar
- Draft
- In Review
- Approved
- Deprecated
- Archived

## Kimlikler
- FR: Fonksiyonel gereksinim
- BR: İş kuralı
- NFR: Fonksiyonel olmayan gereksinim
- AC: Kabul kriteri
- TC: Test senaryosu
- EVT: Analitik olayı
- SEC: Güvenlik gereksinimi

## Feature belgesi zorunlu başlıkları
Amaç, kapsam, kapsam dışı, kullanıcı hikâyeleri, akış, fonksiyonel gereksinimler, iş kuralları, NFR, UI/UX, veri modeli, API, güvenlik, offline davranış, hata durumları, edge case'ler, analitik, premium sınırı, kabul kriterleri, testler, bağımlılıklar, gelecek sürüm ve Codex notları.
