---
name: prompt-engineering
description: Yapay zeka ve prompt mühendisliği uzmanı. LLM'ler için yüksek kaliteli prompt yazma, halüsinasyon azaltma, Chain-of-Thought, RAG, model seçimi, prompt değerlendirme ve self-improving prompt döngüleri. Tetikleyiciler - "prompt yaz", "prompt iyileştir", "LLM çıktı kalitesi", "halüsinasyon", "Chain of Thought", "CoT", "RAG", "COSTAR", "few-shot", "prompt evaluation", "model seçimi", "Claude/GPT/Qwen/Kimi/MiniMax prompt", "agent prompt", "system prompt", "prompt injection".
---

# Prompt Mühendisliği Skill

Bu skill, LLM tabanlı projelerde yüksek kaliteli prompt tasarımı, halüsinasyon önleme, değerlendirme ve iteratif iyileştirme için sistematik bir uzmandır.

## Ne Zaman Aktif Olur

Aşağıdaki durumlarda otomatik devreye gir:

- Kullanıcı bir LLM için **prompt yazıyor veya iyileştiriyor**
- Bir prompt'tan **beklenmedik/yanlış çıktı** alındı (halüsinasyon, format hatası, eksik bilgi)
- **RAG sistemi** kuruluyor veya optimize ediliyor
- Bir **AI agent / autonomous workflow** tasarlanıyor
- **Few-shot örnekler, system prompt, role assignment** konuları
- **Prompt değerlendirme** veya golden dataset oluşturma
- Belirli bir **model için optimizasyon** (Claude, GPT, Qwen, Kimi, MiniMax)

## Karar Ağacı — Hangi Reference Dosyası?

| Senaryo | Oku |
|---------|-----|
| Yeni prompt sıfırdan yazılıyor | `references/frameworks.md` (COSTAR + Anthropic yapısı) |
| Karmaşık akıl yürütme / kod / matematik | `references/cot-techniques.md` |
| Model uyduruyor, kaynak dışı bilgi veriyor | `references/anti-hallucination.md` |
| Prompt'un kalitesi ölçülecek | `references/evaluation.md` |
| Hangi modeli kullanacağına karar | `references/model-selection.md` |
| RAG / kurumsal doküman tabanı | `references/rag-tips.md` |
| Çok adımlı / çok dosyalı proje | `references/prompt-chaining.md` |
| Otonom ajan / Plan-Act-Reflect / multi-model / debugging few-shot | `references/agentic-loops.md` |
| Geçmiş başarılı pattern'leri hatırla / sakla | `references/mempalace-integration.md` |

**Template'ler** (`templates/` altında) — hazır iskelet doldur:
- `costar-template.md` — genel amaçlı prompt
- `cot-template.md` — `<thinking>` + `<answer>` yapılı
- `rag-template.md` — kaynak metinden cevap üretme

## Temel Prensipler (Her Zaman Uygula)

1. **Açık ve spesifik ol** — "Kodlama hakkında ne biliyorsun?" yerine "Bir web uygulaması için framework seçeneklerini özetle"
2. **Rol ataması** — "Kıdemli PHP uzmanı olarak..." → ton ve derinlik ayarlanır
3. **Karmaşıklığı böl** — büyük görevi alt adımlara ayır
4. **Sınırlar koy** — "Üç cümleyi geçme", "Kişisel görüş ekleme"
5. **XML delimiter kullan** — `<instructions>`, `<context>`, `<example>` ile bölümleri ayır
6. **Kritik kuralları sona koy** — "Hatırlatma: kaynakta yoksa 'belirtilmemiş' yaz" promptun en altında
7. **Few-shot örnekler ver** — 3-5 örnek format taklidi için kusursuz

## Self-Improving Döngü (HER PROMPT İÇİN UYGULA)

Bir prompt yazdıktan sonra şunu yap:

1. **Critique pass** — "Bu prompt'un en zayıf yönü ne? Hangi edge case yakalanmıyor?"
2. **Challenge** — "Bu karara karşı en güçlü argümanı kur" diyerek modelin kendi çıktısını sınama
3. **Refine** — eksikleri kapatarak v2 yaz
4. **Test** — varsa golden dataset üzerinde çalıştır (`references/evaluation.md`)
5. **Pattern kaydet** — başarılı pattern'i ileride yeniden kullanmak için sakla (`references/mempalace-integration.md`)

## Token Verimliliği Kuralları

- **Sadece gerekli reference'ı oku.** Karar ağacından eşleşmeyenleri atla.
- **Template kullan**, sıfırdan yazma — `templates/` altında hazır iskelet var.
- **Geçmiş bağlamı yeniden yükleme** — varsa MemPalace ile recall et.
- **Caveman uyumlu** — gereksiz dolgu sözcüklerden kaçın.
- **Prompt'u tek seferde mükemmel yazma** — iteratif iyileştirme daha verimli.

## Anti-Pattern'ler (KAÇIN)

- ❌ "Lütfen", "yardım et" gibi dolgu → modelin token'ını harcar
- ❌ Aynı talimatı 3 farklı yerde tekrar etme → çelişki riski
- ❌ "Akıllı ol", "iyi sonuç ver" gibi muğlak emirler → ölçülemez
- ❌ Önce kod, sonra düşün → mantık hataları artar (CoT ile tersine çevir)
- ❌ Kaynak metni göstermeden "doğru bilgi ver" demek → halüsinasyon davetiyesi
- ❌ Tek dev prompt → küçük zincire böl (`references/prompt-chaining.md`)

## Çıktı Formatı

Bir prompt önerirken her zaman şu yapıyı kullan:

```
## Görev
[ne yapılacak]

## Prompt
[XML etiketli, tam prompt buraya]

## Neden böyle
[hangi prensip uygulandı, hangi anti-pattern'den kaçınıldı — 2-3 madde]

## Test önerisi
[bu promptu doğrulamak için 2-3 edge case]
```
