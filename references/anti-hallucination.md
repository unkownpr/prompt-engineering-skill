# Halüsinasyon Önleme Stratejileri

Halüsinasyon = model kaynağı olmayan/yanlış bilgiyi uydurarak verir. Gerçekliğin kritik olduğu projelerde (RAG, hukuk, sağlık, finans) kontrol şart.

## 1. Evidence-First Extraction (Kanıt-Odaklı Çıkarım)

Modele önce **kaynaktan alıntı**, sonra cevap yazdır:

```xml
Görev: Aşağıdaki dokümandan {{soru}} sorusunu cevapla.

<document>
{{kaynak metin}}
</document>

Adımlar:
1. <evidence> içinde dokümandan **birebir alıntı** yap (en fazla 3 alıntı).
   Her alıntının dokümandaki yerini (paragraf/satır) belirt.

2. <answer> içinde cevabı **sadece bu alıntılara** dayanarak yaz.
   Alıntı dışında bilgi ekleme.

KURAL: Eğer ilgili alıntı yoksa, <answer>Kaynakta belirtilmemiş</answer> yaz.
```

**Neden çalışır:** Model "önce kanıt göster, sonra konuş" diye zorlanır. Uydurma riski düşer.

## 2. Abstention (Bilmiyorum Demeyi Öğretme)

Promptu açıkça "uydurma yetkisi yok" diyerek sınırla:

```
KESİN KURAL:
- Cevabı sadece <document> içinden çıkar.
- Eğer bilgi <document> içinde yoksa, kendi eğitim verini kullanma.
- Bunun yerine tam olarak şunu yaz: "Kaynak metinde belirtilmemiş."
- Tahmin yürütme, çıkarım yapma. Sadece doğrudan ifade edilen bilgiyi al.
```

Bu kuralı promptun **sonunda** tekrar et (recency etkisi).

## 3. Çelişki Bildirimi (Conflict Detection)

Çoklu doküman varsa modelden çelişkileri açıkça raporla:

```xml
<documents>
<doc1>...</doc1>
<doc2>...</doc2>
</documents>

Eğer belgeler arasında **çelişen bilgi** varsa:
1. <conflicts> içinde çelişen ifadeleri yan yana listele.
2. Hangi belgenin daha güncel/güvenilir olduğunu belirleyemiyorsan
   "çelişki var, karar verilemedi" yaz.
3. Tek bir cevap **uydurma**.
```

## 4. Faktüel Sıkılama — "Hangi Tarihte?" Sorusu

Tarihsel iddialar için modelden **kaynak yılını** iste:

```
Her faktüel iddia için:
- İddia
- Kaynak (doküman + sayfa)
- Bu bilginin geçerli olduğu zaman aralığı

Eğer zaman bilgisi yoksa, "tarihsiz" işareti koy ve iddiayı
"tarihsiz" olarak etiketle.
```

## 5. Structured Output ile Sınırlandırma

JSON şeması zorla — modelin uydurabileceği alanı daralt:

```xml
Cevabı **sadece** bu JSON şemasında ver:

{
  "answer": string,
  "confidence": "high" | "medium" | "low",
  "source_quotes": [string, ...],  // boş array OK
  "missing_info": [string, ...]    // hangi bilgi yoktu
}

Eğer cevap yoksa:
{
  "answer": null,
  "confidence": "low",
  "source_quotes": [],
  "missing_info": ["sorulan bilgi kaynakta yok"]
}
```

## 6. Multi-Turn Verification (RAG için)

Tek geçişte bitirme — iki aşama yap:

```
Aşama 1: Cevap taslağı oluştur (draft)
Aşama 2: Aynı modele şunu sor:
  "Aşağıdaki cevap, sadece verilen kaynaklardan mı çıkarılmış?
   Her iddiayı kaynakla eşle. Eşleştirilemeyenleri işaretle."
```

İkinci geçişte fact-check otomatik yapılır.

## 7. Temperature ve Sampling Ayarları

- **Faktüel görevler:** `temperature=0` veya `temperature=0.1`
- **Yaratıcı görevler:** `temperature=0.7+`
- Halüsinasyon şüphesi → temperature düşür, top_p daralt

## Anti-Pattern: "Doğru cevap ver" Demek

❌ "Lütfen doğru bilgi ver" → ölçülemez, etkisiz
✅ "Kaynakta yoksa 'belirtilmemiş' yaz" → spesifik, doğrulanabilir

## Test Stratejisi

Halüsinasyon testi için:
1. **Tuzak soru ekle:** dokümanda olmayan bir konu sor → model "belirtilmemiş" demeli
2. **Çelişki injection:** iki çelişen ifade ekle → model çelişkiyi raporlamalı
3. **Tarih kayması:** dokümanın eski olduğunu söyle → model güncellik uyarısı vermeli

Detay için `evaluation.md`.
