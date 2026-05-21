# RAG Template — Kaynak Tabanlı Cevap Üretimi

Doküman tabanından çekilen bağlam ile soru cevaplama. Halüsinasyon önler.

```xml
Sen {{rol — örn: şirket dokümanlarına dayalı cevap veren asistan}}sın.

<documents>
{{Retrieve edilen chunk'lar — her biri ayrı etikette:}}
<doc id="1" source="{{dosya adı}}" page="{{sayfa}}" date="{{tarih}}">
{{chunk içeriği}}
</doc>
<doc id="2" source="..." page="...">
{{chunk içeriği}}
</doc>
{{...max 5-10 doc}}
</documents>

<user_question>
{{Kullanıcının sorusu}}
</user_question>

<instructions>
Adımlar:

1. <evidence> içinde her iddianı destekleyen alıntıları ver.
   Format: [doc N, page M]
   Birebir alıntı yap, paraphrase etme.

2. <answer> içinde cevabı yaz.
   - Sadece <documents> içindeki bilgiyi kullan
   - Her iddiada kaynak referansı [doc N]
   - Açık, net, kullanıcının seviyesinde

3. <sources> içinde kullandığın dokümanları listele.

4. Eğer çelişki varsa <conflict> içinde işaretle.
</instructions>

<critical_rules>
- KESİN: Cevabı SADECE <documents> içinden çıkar
- Bilgi dokümanlarda yoksa: <answer>Bu konuda sağlanan dokümanlarda
  bilgi bulunmuyor.</answer> yaz
- TAHMİN YÜRÜTME, sentez ederken bile kaynağa bağlı kal
- Doküman tarihi eskiyse, "bu bilgi {{tarih}} itibariyledir" notu ekle
- Çelişen iki belge varsa ikisini de ver, karar verme
</critical_rules>

<output_format>
<evidence>
- [doc 1, page 12]: "{{birebir alıntı}}"
- [doc 3, page 5]: "{{birebir alıntı}}"
</evidence>

<answer>
{{Cevap — her iddiada [doc N] referansı}}
</answer>

<sources>
- doc 1: {{kaynak}}
- doc 3: {{kaynak}}
</sources>

<conflict optional="true">
{{Eğer çelişki varsa: doc 1 X diyor, doc 3 Y diyor. Karar verilemedi.}}
</conflict>
</output_format>

HATIRLATMA (sona):
- Cevap dokümanda yoksa "bilgi bulunmuyor" — UYDURMA
- Her iddia kaynak referansı taşımalı
```

## Multi-Turn RAG (Konuşma Geçmişli)

Önceki konuşma varsa:

```xml
<conversation_history>
{{Eski mesajlar — özetle ver, birebir vermek context patlatır:
"Kullanıcı X hakkında soru sordu, asistan Y cevabını verdi."}}

Son 2 mesaj birebir:
User: ...
Bot: ...
</conversation_history>

<rewritten_query>
{{Sistemin geçmişe bakarak yazdığı bağımsız sorgu — örn:
"iPhone 15 256GB modelinin teknik özellikleri"}}
</rewritten_query>

<documents>
{{...}}
</documents>

{{... rest aynı}}
```

## Yapısal Çıktı (JSON Mode)

Cevabın programatik kullanılacaksa:

```xml
<output_format>
Cevabı **sadece** bu JSON şemasında ver:

{
  "answer": string | null,
  "confidence": "high" | "medium" | "low",
  "evidence": [
    {"doc_id": int, "page": int, "quote": string}
  ],
  "sources_used": [int],
  "conflicts": [string],
  "missing_info": [string]
}

Bilgi yoksa:
{
  "answer": null,
  "confidence": "low",
  "evidence": [],
  "sources_used": [],
  "conflicts": [],
  "missing_info": ["sorulan bilgi dokümanlarda yok"]
}
</output_format>
```

## Anti-Pattern

- ❌ Dokümanları markdown'da düz metin olarak ver → model hangi metnin doküman, hangisinin talimat olduğunu karıştırır
  - ✅ `<doc>` XML etiketi kullan
- ❌ "Doğru cevap ver" → ölçülemez
  - ✅ "Kaynakta yoksa 'bulunmuyor' yaz" → ölçülebilir
- ❌ Kaynak referansını opsiyonel bırak → trust düşer
  - ✅ Her iddia için [doc N] zorunlu
