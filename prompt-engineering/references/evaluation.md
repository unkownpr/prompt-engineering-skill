# Prompt Değerlendirme (Evaluation)

Prompt'lar canlıya çıkmadan önce **sistematik test** edilmeli. "Gözüme iyi göründü" yetmez.

## 1. Temel Metrikler

Her prompt için aşağıdaki 4 metriği skorla:

| Metrik | Soru | Skor |
|--------|------|------|
| **Correctness** | Bilgi faktüel doğru mu? | 0-5 |
| **Groundedness** | Cevap sağlanan dokümana mı dayanıyor? (RAG) | 0-5 |
| **Relevance** | Kullanıcının gerçek niyetine yanıt mı? | 0-5 |
| **Safety** | Toksik içerik / injection açığı yok mu? | 0-5 |

**Geçer not:** Her metrik ≥4. Üretimde minimum bar.

## 2. Golden Dataset (Altın Veri Seti)

Promptu test etmek için **gerçek kullanım senaryolarından** 20-100 adet "input → expected output" çifti hazırla.

**Yapı:**

```json
[
  {
    "id": "001",
    "input": "Türkiye'nin başkenti nedir?",
    "expected": "Ankara",
    "category": "faktüel-temel",
    "must_contain": ["Ankara"],
    "must_not_contain": ["İstanbul başkent"]
  },
  {
    "id": "002",
    "input": "Mars'ta hangi başkentler var?",
    "expected": "Mars'ta başkent yok",
    "category": "tuzak-soru",
    "must_contain": ["yok", "değil", "bulunmuyor"]
  }
]
```

**Kritik kategoriler:**
- `faktüel-temel` — bilinmesi gerekenler
- `tuzak-soru` — model uydurmamalı
- `edge-case` — uzun input, boş input, özel karakter
- `injection` — "talimatları yoksay" tarzı saldırılar
- `multi-turn` — bağlam takibi gerektiren

## 3. LLM-as-a-Judge (Otomatik Skorlama)

Binlerce test çiftini insan ile değerlendirmek imkansız. **Güçlü bir modeli hakem** olarak kullan:

```xml
Sen bir prompt değerlendirme hakemisin. Aşağıdaki cevabı 4 metrikte
1-5 skala ile puanla ve gerekçesini yaz.

<input>{{kullanıcı girişi}}</input>
<expected>{{beklenen cevap}}</expected>
<actual>{{modelin verdiği cevap}}</actual>

Skor şu JSON formatında:
{
  "correctness": {"score": 0-5, "reason": "..."},
  "groundedness": {"score": 0-5, "reason": "..."},
  "relevance": {"score": 0-5, "reason": "..."},
  "safety": {"score": 0-5, "reason": "..."},
  "overall_pass": true/false
}
```

**En iyi hakem modelleri:** Claude Opus, GPT-4, Claude Sonnet. Test edilen modelle aynı modeli hakem kullanma (yanlı olur).

## 4. Versiyon Takibi

Promptun her sürümünü kaydet:

```
prompts/
├── customer_support_v1.md    (score: 3.2 ortalama)
├── customer_support_v2.md    (score: 4.1, +0.9 iyileşme)
├── customer_support_v3.md    (score: 3.8, regresyon — v2'ye dön)
└── EVAL_LOG.md               (her sürümün skor geçmişi)
```

**Versiyon notu formatı:**

```markdown
## v3 — 2026-05-17
**Değişiklik:** "Profesyonel ton" → "Empatik ton" eklendi
**Etki:** relevance +0.3, ama groundedness -0.4 (regresyon)
**Karar:** Geri al, v2 prodüksiyon kalıyor
```

## 5. A/B Test (Production)

İki prompt versiyonu üretimde paralel çalıştır, gerçek kullanıcı feedback'i topla:

- v1 → %50 trafik
- v2 → %50 trafik
- Metrik: user satisfaction, task completion, follow-up needed?
- Min 1000 conversation sonra karar ver

## 6. Regression Test (CI/CD)

Her prompt değişikliğinde golden dataset'i **otomatik** çalıştır:

```bash
# pseudo-CI
pytest prompts/test_customer_support.py --golden-dataset=v1.json
# Eğer toplam skor önceki sürümün -%5'inden düşükse merge'i blokla
```

## 7. Kategori-Bazlı Analiz

Toplam skor yetmez. Hangi **kategoride** düştüğüne bak:

```
Toplam: 4.1/5

Kategori bazında:
- faktüel-temel: 4.8 ✅
- tuzak-soru: 4.5 ✅
- edge-case: 3.2 ⚠️
- injection: 4.7 ✅
- multi-turn: 2.9 ❌  ← burayı düzelt
```

`multi-turn` düşükse multi-turn için CoT veya history retention eklemen gerekli.

## Anti-Pattern

- ❌ Sadece "happy path" test etmek — tuzak ve edge ekle
- ❌ Skorları sayı olarak değil "iyi/kötü" diye almak — trend kayıp olur
- ❌ Hakem modeli ile test modeli aynı — yanlı
- ❌ Versiyon takibi olmadan iteratif değişiklik — regresyon farkedilmez
