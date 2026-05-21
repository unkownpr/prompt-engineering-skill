# Prompt Chaining (İstem Zincirleme)

Tek dev prompt yerine **ardışık küçük prompt'lar**. Karmaşık/çok dosyalı projelerde tutarlılık artar, hata düşer.

## Neden Zincirleme?

**Tek prompt sorunları:**
- Çok büyük → model bağlamı kaybeder
- Hata bulması zor → hangi adım yanlıştı?
- Yeniden çalıştırması pahalı → tek değişiklik için tümünü yeniden üret

**Zincir avantajları:**
- Her adım tek sorumluluk → debug kolay
- Adım çıktısı insan veya başka model tarafından doğrulanabilir
- Başarısız adımı tek başına yeniden çalıştırırsın
- Paralel adımlar → toplam latency düşer

## Klasik 3-Adım Pattern (Yazılım Projesi)

```
Adım 1: Yüksek seviyeli planlama
  Input: Kullanıcı isterleri
  Output: Mimari blueprint + requirements.txt + dosya listesi

Adım 2: Dosya dosya implementasyon (paralel olabilir)
  Input: Adım 1 çıktısı + tek dosyanın spec'i
  Output: O dosyanın tam kodu

Adım 3: Tutarlılık denetimi
  Input: Tüm üretilen dosyalar
  Output: Import uyumsuzlukları, isim tutarsızlıkları, eksik fonksiyonlar
```

### Adım 1 Prompt Örneği

```xml
Sen kıdemli bir yazılım mimarısın. Aşağıdaki istek için
**sadece mimari blueprint** üret, kod yazma.

<requirements>
{{kullanıcının isterleri}}
</requirements>

<output_format>
1. Yüksek seviye mimari (3-5 cümle)
2. Klasör/dosya yapısı (ascii tree)
3. requirements.txt içeriği
4. Her dosya için 2-3 cümlelik sorumluluk tanımı
5. Dosyalar arası bağımlılık grafiği
</output_format>

NOT: Kod yazma, sadece plan. Detayları sonraki adımda alacaksın.
```

### Adım 2 Prompt Örneği

```xml
Aşağıdaki blueprint'i baz al ve **sadece `app.py`** dosyasını yaz.

<blueprint>
{{Adım 1 çıktısı}}
</blueprint>

<target_file>
app.py — FastAPI router setup + middleware
</target_file>

Diğer dosyalara (db.py, models.py) referans verebilirsin ama
o dosyaların kodunu yazma. Sadece app.py.

<output_format>
```python
# app.py
{{kod buraya}}
```
</output_format>
```

### Adım 3 Prompt Örneği

```xml
Aşağıdaki dosyaları **tutarlılık** açısından denetle.

<files>
{{tüm üretilen dosyalar buraya}}
</files>

Kontrol et:
- Bir dosyada import edilen fonksiyon başka dosyada tanımlı mı?
- İsimler tutarlı mı? (camelCase / snake_case)
- requirements.txt'de eksik bağımlılık var mı?
- Aynı işi yapan tekrarlı fonksiyon var mı?

<output_format>
{
  "ok": false,
  "issues": [
    {"file": "app.py", "line": 12, "issue": "...", "fix": "..."}
  ]
}
</output_format>
```

## Pattern Tipleri

### A. Sequential (Sıralı)

```
A → B → C
```
B, A'nın çıktısını kullanır. Klasik pipeline.

### B. Parallel-Fan-Out

```
       ┌─→ B1 ─┐
A ─────┼─→ B2 ─┼─→ C
       └─→ B3 ─┘
```
A tek başına çalışır, sonuçtan paralel B1/B2/B3, hepsini C birleştirir.

**Use case:** 10 dosya yaz → her dosya paralel → birleştirip tutarlılık denetle.

### C. Loop (Plan-Act-Reflect)

```
A → B → C → (if not ok) → B → C → ...
```

Test geçene kadar yeniden çalıştır.

**Use case:** Kod yaz → test çalıştır → hatayı düzelt → tekrar test.

### D. Routing (Conditional)

```
A ─→ classifier ─→ if x: branch1
                  └─ if y: branch2
```

A bir sınıflandırıcı, çıktıya göre farklı zincir.

**Use case:** Kullanıcı sorusu "soru tipi nedir?" → faktüel ise RAG, yaratıcı ise temperature artır.

## Anti-Pattern'ler

| Hata | Sorun | Çözüm |
|------|-------|-------|
| Zincirin sonunda tek dev özet adımı | Bağlam patlar | Özyineli özet (chain içinde özet) |
| Her adım için sıfırdan bağlam | Token israfı | Sadece ilgili çıktıyı taşı |
| Adımlar arası format değişkenliği | Parsing hatası | Her adımda kesin JSON şeması zorla |
| Hata yutma | Sessiz başarısızlık | Her adımda success/failure raporla |
| Aşırı uzun zincir (>10 adım) | Compounding error | Mantıklı yerden böl, 2 zincir yap |

## Tool / Framework

- **LangChain** — Sequential, RouterChain, MultiPromptChain
- **LangGraph** — state machine, loop, conditional
- **Anthropic SDK** — manual chaining + prompt caching
- **CrewAI / AutoGen** — multi-agent chain

## Maliyet Optimizasyonu

Zincir = daha çok API çağrısı = daha pahalı. Hafifletme:

1. **Prompt caching** — sabit bölümler (sistem prompt, blueprint) önbellekle
2. **Küçük modele basit adımlar** — sınıflandırıcı = Haiku, üretici = Opus
3. **Batch API** — toplu çalıştır (paralel adımlar için)
4. **Erken çıkış** — adım 2'de gerek kalmadıysa adım 3'ü atla

## Self-Improving Loop

Zincirin sonuna **kendini iyileştirme** adımı ekle:

```
Adım N: "Bu zincirin hangi adımı zayıf çalıştı? Hangi prompt'u
nasıl iyileştirirdin? JSON output:"
{
  "weakest_step": 2,
  "suggested_fix": "Adım 2'deki blueprint çok kısa, sistem prompt'una
                    '5+ örnek dosya yapısı ekle' kuralı koy"
}
```

Bu çıktıyı prompt versiyonlama sistemine besle (`evaluation.md`).
