# Model Seçimi ve Modele Özel Taktikler

Farklı modeller farklı görevler için optimize edilmiştir. Doğru modeli seçmek prompt kalitesinden bağımsız %30-50 performans farkı yaratabilir.

## Hızlı Karar Tablosu

| Görev | Önerilen Model | Neden |
|-------|----------------|-------|
| Uzun doküman analizi | **Claude Opus / Sonnet** | XML + reasoning + long context |
| Frontend/tasarım | **Claude Opus** | İyi görsel akıl yürütme, kod kalitesi |
| Kurumsal RAG | **Qwen Max / Plus** | RAG için optimize, çok dilli |
| Kodlama (uzun) | **Qwen Coder / Claude Opus** | Coder fine-tune var |
| Çok uzun bağlam (>200K) | **Kimi K2 / Claude 1M** | 1M+ token context |
| Otonom ajan (tool use) | **MiniMax M2 / Claude Opus** | Plan-Act-Reflect eğitimli |
| Hızlı/ucuz tek soru | **Claude Haiku / GPT-4o-mini** | Düşük latency, düşük maliyet |
| Çince + İngilizce | **Qwen** | İki dilde dengeli güçlü |

## Claude (Anthropic)

**Güçlü yönleri:**
- XML etiketlerini en iyi anlayan model
- `<thinking>` ile scratchpad reasoning kusursuz
- Uzun bağlam takibi (Sonnet 1M token)
- Frontend kodu ve karmaşık doküman analizi

**Taktikler:**
- XML her zaman: `<instructions>`, `<context>`, `<example>`, `<thinking>`
- Sistem prompt'u kısa tut, asıl talimatları user message'a koy
- `<thinking>` etiketi otomatik çağrılır — açıkça izin ver
- Few-shot örneklere `<thinking>` ekle (modelin tarzını alır)
- Constitutional prompting iyi çalışır ("Bu cevap zararlı mı? Önce kontrol et")

**Örnek prompt başı:**

```xml
Sen kıdemli bir yazılım mimarısın.

<task>
{{görev}}
</task>

<context>
{{bağlam}}
</context>

Önce <thinking> içinde mimari kararları gerekçele.
Sonra <answer> içinde kodu yaz.
```

## Qwen (Alibaba)

**Güçlü yönleri:**
- Kurumsal RAG için fine-tune edilmiş
- Çince-İngilizce iki yönlü mükemmel
- Qwen-Coder: kod görevlerinde rekabetçi
- COSTAR çerçevesi ile çok iyi çalışır

**Taktikler:**
- COSTAR şablonunu birebir uygula (`frameworks.md`)
- RAG'da kaynakları açık `<doc id="1">...</doc>` formatında ver
- Türkçe görevlerde olası — Llama/Mistral'dan iyi sonuç

## Kimi (Moonshot)

**Güçlü yönleri:**
- Çok uzun bağlam (1M+ token)
- Devasa metin özetleme
- Long-form analiz

**Taktikler:**
- Devasa metni tek seferde verme — **recursive summarization** kullan:
  ```
  Adım 1: Metni 50K token bloklarına böl
  Adım 2: Her blok için 1-sayfa özet
  Adım 3: Özetleri birleştir, tekrar özet (recursion)
  Adım 4: Nihai cevabı en üst özet seviyesinden üret
  ```
- Çok uzun bağlamda "needle in haystack" testi yap — model orta bölümleri kaçırabilir

## MiniMax (ABAB / M-serisi)

**Güçlü yönleri:**
- Plan-Act-Reflect döngüsü üzerine eğitilmiş
- Tool use ve agentic workflow için ideal
- Kodlama agent görevlerinde rekabetçi

**Taktikler:**
- Agentic prompt'larda `<plan>`, `<act>`, `<reflect>` etiketlerini kullan
- Hata onarma döngüleri (compile → test → fix) için optimize
- Çok aşamalı görevde `<thinking>` yerine `<plan>` tercih et

## GPT-4 / GPT-4o (OpenAI)

**Güçlü yönleri:**
- Genel amaçlı, geniş ekosistem
- Function calling olgun
- JSON mode garantili yapısal çıktı

**Taktikler:**
- `response_format={"type": "json_object"}` ile JSON garantile
- System message kısa, user message detaylı
- Markdown başlık yerine `### Section` net çalışır
- `temperature=0` faktüel görevlerde

## Open-Source Modeller (Llama, Mistral, DeepSeek)

**Genel kural:**
- Daha fazla **few-shot örnek** ekle (3-5 yerine 5-10)
- Daha kısa talimatlar — uzun system prompt'u kaybedebilirler
- `<thinking>` etiketi daha az güvenilir — açık adım numarası kullan
- Yerel deployment için **quantization seviyesi** etkili (Q4 ile Q8 farkı %15-25)

## Maliyet vs Kalite Trade-off

```
Yüksek maliyet, yüksek kalite:
  Claude Opus, GPT-4 → kritik üretim, agentic, kompleks RAG

Orta maliyet:
  Claude Sonnet, Qwen Max → çoğu üretim görevi

Düşük maliyet:
  Claude Haiku, GPT-4o-mini, Qwen Plus → yüksek hacim, basit görev

Ücretsiz/local:
  Llama, Qwen-open, DeepSeek → privacy gerekli, kontrol gerekli
```

**Pratik kural:** Önce Sonnet/Qwen Max ile prototip. Skor yetersizse Opus'a çık. Yeterliyse Haiku'ya in.

## Routing Pattern (Multi-Model)

Tek model yerine **görev tipine göre routing**:

```python
def route(task):
    if task.type == "faktüel-lookup":
        return "haiku"  # ucuz
    elif task.type == "uzun-analiz":
        return "sonnet-1m"  # uzun bağlam
    elif task.type == "agent":
        return "minimax-m2"  # plan-act-reflect
    else:
        return "sonnet"  # default
```
