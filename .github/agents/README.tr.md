# GitHub Agent'ları — Kullanım Kılavuzu

Bu proje, küçük bağlam pencereleriyle çalışan **dört uzman agent** ve hafif bir orkestratör kullanır.

## Agent'lar

| Agent | Ne zaman kullanılır… |
|-------|----------------------|
| **`playwright-bdd-orchestrator`** | Yeni bir QA görevine başlarken (her zaman buradan başlayın) |
| `playwright-test-planner` | Gereksinim var, test planı gerekiyor |
| `playwright-test-generator` | Plan onaylandı — spec karşılaştır ve test üret |
| `playwright-test-healer` | Testler başarısız |

## Önerilen Akış

```
1. @playwright-bdd-orchestrator  →  gereksinimi kaydet
2. @playwright-test-planner      →  uygulamayı keşfet + specs/plans/ altına plan yaz
3. Planı manuel incele
4. @playwright-test-generator    →  mevcut planlarla karşılaştır, kod üret
5. Çalıştır: npm run test:dev
6. @playwright-test-healer       →  hataları düzelt (gerekirse)
```

## Specs Klasörü

Test üretmeden önce gereksinimler ve planlar `specs/` altında saklanır:

- `specs/requirements/` — ne test edilecek (paydaşlardan)
- `specs/plans/` — nasıl test edilecek (Gherkin + framework eşlemesi)

Generator, kod yazmadan önce **yeni gereksinimleri mevcut planlarla karşılaştırır**. Detaylar: [specs/README.md](../../specs/README.md).

## Örnek Promptlar

### Yeni özellik (orkestratör ile)

```
@playwright-bdd-orchestrator

Yeni gereksinim: dev ortamında stok sipariş oluşturma formunu doğrula.
Adımlar:
1. Admin olarak giriş yap
2. Create Resources'a git
3. Zorunlu alanları doldur ve gönder
```

### Mevcut plandan test üret

```
@playwright-test-generator

specs/plans/login-dashboard.plan.md planını karşılaştır ve uygula
```

### Hataları düzelt

```
@playwright-test-healer

Login senaryosu Keycloak yönlendirme zaman aşımı ile başarısız. @smoke testlerini düzelt ve tekrar çalıştır.
```

## Test Komutları

```bash
npm run test:dev          # Tüm testler, dev ortamı
npm run test:test         # Test ortamı
npm run test:smoke        # Sadece @smoke etiketi
cross-env TAGS=@login npm run test:dev
cross-env WORKERS=1 npm run test:dev   # Hata ayıklama modu
```

## Dokümantasyon

- [Çoklu agent iş akışı](WORKFLOW.md)
- [Specs klasörü rehberi](../../specs/README.md)
- [Kodlama standartları](../../docs/CODING_STANDARDS.md)
- [English guide](README.md)
