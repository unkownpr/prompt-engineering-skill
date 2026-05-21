# RAG (Retrieval-Augmented Generation) İpuçları

Kurumsal projelerde **doğru doküman → doğru cevap** zinciri için 6 katmanlı optimizasyon.

## 1. Akıllı Chunking (Metin Bölme)

Yanlış: Sabit 1000 karakter blokları → cümle ortasından bölünür, semantik bütünlük gider.

Doğru:
- **Recursive splitter** — önce paragrafa, sonra cümleye, son çare karaktere böl
- **Chunk boyutu:** 300-800 token (model context'ine göre ayarla)
- **Overlap:** %10-20 (önceki chunk'tan biraz taşır, kayıp önler)
- **Header ekle** — her chunk'ın başına dosya adı + section başlığı

```python
# pseudo
chunk = {
    "text": "...",
    "metadata": {
        "source": "policy.pdf",
        "section": "Bölüm 3.2 - İade",
        "page": 12,
        "prev_chunk_id": "...",
        "next_chunk_id": "..."
    }
}
```

## 2. Metadata Stratejisi

Her chunk'a sorgu sırasında filtre olarak kullanılacak etiketler ekle:

| Metadata | Kullanım |
|----------|----------|
| `source` | Hangi dokümandan | "Sadece 2024 raporlarından getir" |
| `date` | Doküman tarihi | Eski versiyonları ele |
| `category` | Konu etiketi | "Sadece HR politikaları" |
| `permission` | Erişim seviyesi | Kullanıcının yetkisi olmayanları filtrele |
| `language` | Dil | "Sadece TR" |

## 3. Hybrid Search (Semantic + Keyword)

Tek başına semantic search bazı durumlarda kötü:
- Spesifik isimler ("Müşteri X", "Hata 401")
- Kısa sorgular
- Sayılar / kodlar

**Çözüm:** İkisini birleştir.

```
Semantic score (embedding cosine) * 0.7
+
BM25 keyword score * 0.3
= Final rank
```

Reciprocal Rank Fusion (RRF) daha gelişmiş bir birleştirme.

## 4. Query Rewriting (Multi-Round)

Kullanıcı "Özellikleri neler?" dediğinde sistem **bağlam ekleyerek** sorguyu yeniden yaz:

```xml
<conversation_history>
User: iPhone 15 hakkında bilgi
Bot: iPhone 15 Apple'ın 2023 modeli...
User: Özellikleri neler?
</conversation_history>

<rewrite_task>
Son kullanıcı sorusunu, geçmişteki bağlamı kullanarak **bağımsız**
bir arama sorgusuna dönüştür.

Çıktı: "iPhone 15 teknik özellikleri (kamera, pil, işlemci)"
</rewrite_task>
```

## 5. Re-Ranking (İkinci Aşama Eleme)

Embedding araması ile top-20 al, sonra **cross-encoder** veya LLM ile top-5'e in:

```
Aşama 1: Embedding search → 20 chunk
Aşama 2: Re-ranker → her chunk'ı sorguyla birlikte skorla → top 5
Aşama 3: Top 5'i LLM'e bağlam olarak ver
```

**Neden:** Embedding search "yakınlık" der, re-ranker "alakanlılık" der. İkincisi daha doğru.

## 6. RAG Promptu — Tam Şablon

```xml
Sen şirket dokümanlarına dayalı cevap veren bir asistansın.

<documents>
{{retrieved chunks, her biri <doc id="N" source="..." page="N">...</doc>}}
</documents>

<user_question>
{{soru}}
</user_question>

Adımlar:
1. <evidence> içinde her iddianı destekleyen alıntıları ver
   ([doc 2, page 12] formatında).
2. <answer> içinde cevabı yaz.
3. <sources> içinde kullandığın dokümanları listele.

KESİN KURALLAR:
- Sadece <documents> içindeki bilgiyi kullan.
- Eğer cevap dokümanlarda yoksa: "Bu konuda dokümanlarda
  bilgi bulunmuyor" yaz.
- Çelişen bilgi varsa <conflict> içinde işaretle.
- Tahmin yürütme, sentez ederken bile kaynağa bağlı kal.
```

## 7. Yaygın RAG Hataları

| Hata | Belirti | Çözüm |
|------|---------|-------|
| Çok büyük chunk | Modelde alaka kayıp | Chunk küçült 300-500 token |
| Çok küçük chunk | Bağlam kayıp | Overlap artır, parent retrieval |
| Sadece semantic | İsim/kod kaçırır | Hybrid search |
| Re-ranker yok | Top-K kalitesi düşük | Cross-encoder ekle |
| Metadata yok | İlgisiz doc gelir | Filter ekle |
| Kaynak göstermez | Trust düşük | Promptta `[doc N]` zorunlu yap |
| Çelişki yutar | Hatalı cevap | Conflict detection |

## 8. Değerlendirme — RAG-Özel Metrikler

| Metrik | Soru |
|--------|------|
| **Context Precision** | Getirilen chunk'lar ne kadar alakalı? |
| **Context Recall** | Cevap için gereken bilgi getirildi mi? |
| **Answer Faithfulness** | Cevap getirilen bağlama dayanıyor mu? |
| **Answer Relevance** | Cevap soruyu yanıtlıyor mu? |

**Tool:** Ragas, TruLens, ya da kendi LLM-as-judge setup'ın (`evaluation.md`).

## 9. Parent Document Retrieval

Küçük chunk ara, **büyük parent'ı** bağlam olarak ver:

```
1. Doc'u 200 token "child" chunk'lara böl (arama için)
2. Doc'u 2000 token "parent" chunk'lara böl (bağlam için)
3. Search → child match → parent'ı LLM'e ver
```

Sonuç: hassas arama + zengin bağlam.

## 10. Konuşma Geçmişi Yönetimi

Multi-turn RAG'da geçmişi her seferinde **özetleyerek** ver, yoksa context patlar:

```
Eski mesajlar (n > 5): tek satır özet
Son 3 mesaj: birebir
Sistem prompt'u: sabit, kısa
RAG chunks: dynamic, en üstte
```
