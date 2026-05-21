# Agentic Loops — Plan-Act-Reflect + Multi-Model Mimarisi

Otonom ajanlar, tool-using sistemler ve karmaşık çok-dosyalı projeler için. Modelin kendi kendini düzelten (self-correcting) iş akışı.

## 1. Few-Shot CoT ile Debugging Mantığı Öğretme

**Amaç:** Modele sadece "hatalı kod → düzeltilmiş kod" değil, **arada nasıl düşündüğünü** öğret.

### Şablon

```xml
<example>
<input>
Hatalı kod:
def get_user(uid):
    user = users.get(uid)
    return user.email

Hata:
Traceback (most recent call last):
  File "app.py", line 5, in get_user
    return user.email
AttributeError: 'NoneType' object has no attribute 'email'
</input>

<thinking>
1. Hata mesajı: `AttributeError: 'NoneType' object has no attribute 'email'` —
   line 5'te `user.email` çağrısı yapılırken `user` None'mış.

2. Neden? `users.get(uid)` — dict.get() bulunamazsa None döner.
   Yani `uid` users dict'inde yok.

3. Çözüm seçenekleri:
   a) Default value: `users.get(uid, {}).get("email")`
   b) Explicit check: `if user is None: raise NotFoundError`
   c) KeyError fırlat: `users[uid]` ve except'le yakala

4. En iyi seçim: (b) — caller'a açık sinyal verir, sessiz hata yok.

5. Edge case:
   - users dict'i hiç initialize edilmemişse? AttributeError gelir.
     Bu kaynaklı problem, ayrı.
   - user objesi var ama email attribute'u yoksa? AttributeError gelir.
     Bu da ayrı, scope dışı.
</thinking>

<answer>
def get_user(uid):
    user = users.get(uid)
    if user is None:
        raise UserNotFoundError(f"User {uid} not found")
    return user.email
</answer>

<self_check>
- Orijinal hata çözüldü mü? Evet — None kontrolü eklendi
- Yeni edge case yarattı mı?
  - UserNotFoundError henüz tanımlı değil → import veya tanımlama gerekli
  - Caller bu exception'ı handle ediyor mu? Test gerekli
- Performans değişti mi? Hayır
</self_check>
</example>
```

### Neden Çalışır

Model `<thinking>` bloğunun **stilini** taklit eder. Bir sonraki problem geldiğinde:
- Önce hata mesajını parse eder
- Kök neden analizi yapar
- Alternatif çözümleri tartar
- En iyisini seçer ve gerekçe yazar
- Edge case taraması yapar

Sadece "girdi → çıktı" örneği bunu öğretmez.

## 2. Plan-Act-Reflect Döngüsü (Detaylı)

Otonom kodlama, debugging, multi-step görevler için. **Interleaved Thinking** olarak da bilinir (MiniMax M2, Claude extended thinking).

### Aşama 0 — Hedef ve Tool Tanımı

```xml
<goal>
{{Nihai hedef. Tek cümle, ölçülebilir.}}
Örnek: "FastAPI uygulamasını test suite ile birlikte hatasız çalışır hale getir."
</goal>

<available_tools>
- run_bash(cmd: str) → stdout, stderr, exit_code
- read_file(path: str) → content
- write_file(path: str, content: str) → ok
- run_tests() → pass_count, fail_count, failures: [Failure]
</available_tools>

<success_criteria>
- pytest exit code 0
- Tüm endpoint'ler 200 döner
- Code coverage >80%
</success_criteria>
```

### Aşama 1 — Plan (Düşünme Bloğu)

```xml
<plan>
Strateji:
1. Mevcut hata listesini al → run_tests()
2. İlk failing test için ilgili dosyayı oku
3. Root cause analiz et
4. Düzeltme uygula
5. Sadece o testi yeniden çalıştır (hızlı feedback)
6. Geçtiyse → sonraki failure'a geç
7. Hepsi geçtiyse → full suite çalıştır

Risk:
- Bir düzeltme başka testi bozarsa, ne yapmalıyım?
  → Her düzeltmeden sonra son N geçen testi de tekrar koş (regression check)
</plan>
```

### Aşama 2 — Act (Tool Çağrısı)

```xml
<act>
run_tests()
</act>

<observation>
{
  "pass": 12,
  "fail": 3,
  "failures": [
    {"test": "test_login", "error": "AssertionError: expected 200 got 401"},
    ...
  ]
}
</observation>
```

### Aşama 3 — Reflect (Analiz + Yeniden Plan)

```xml
<reflect>
test_login 401 dönüyor. Bu auth middleware sorunu olabilir.

Hipotezler:
1. Token validation kodu yanlış
2. Test'te kullanılan mock user veritabanında yok
3. Auth header formatı değişmiş

Bir sonraki adım: auth.py'ı oku, sonra test_login'in setup'ını oku.
</reflect>
```

### Aşama 4 — Döngü

Reflect → yeni Plan → yeni Act → yeni Reflect ... testler geçene kadar.

### Kritik Kural: Mesaj Geçmişini Koru

API çağrılarında **her** `<thinking>`, `<plan>`, `<act>`, `<observation>`, `<reflect>` bloğunu **birikimli** olarak mesaj geçmişine ekle.

```python
messages = [
    {"role": "user", "content": "<goal>...</goal>"},
    {"role": "assistant", "content": "<plan>...</plan>"},
    {"role": "user", "content": "<observation>...</observation>"},
    {"role": "assistant", "content": "<reflect>...</reflect>"},
    {"role": "assistant", "content": "<plan>...</plan>"},
    # ... biriktirerek devam et
]
```

**Aksi takdirde:** Model her döngüde sıfırdan başlar, ne planladığını unutur, aynı hatayı tekrar yapar.

Anthropic SDK için: `reasoning_details` objesini sonraki çağrıda da gönder.

## 3. Compile-Run-Test-Fix Simülasyonu

Plan-Act-Reflect'in en yaygın uygulaması: kod üretirken kendi kendini test etme.

```
Plan: "Şu özellikleri olan fonksiyonu yazacağım: ..."
Act: write_file("solution.py", code)
Act: run_bash("python -m pytest test_solution.py -v")
Observation: "1 failed: test_edge_case"
Reflect: "Boş input case'ini handle etmemişim. Düzelteceğim."
Plan: "if not input: return [] satırını ekle"
Act: write_file (updated)
Act: run_bash (re-run)
Observation: "all passed"
Done.
```

Promptta açık emir:

```
Her tool çağrısından sonra:
1. Sonucu <reflect> içinde analiz et
2. Hata varsa kök nedenini belirle
3. Önceki yaklaşımın neden yetmediğini açıkla
4. Test edilmiş bir yama planla
5. Yamayı uygula ve testi yeniden çalıştır

Testler geçene kadar bu döngüyü sürdür. Maksimum {{N}} iterasyon.
```

## 4. Multi-Model Mimarisi (Görev Bölüşümü)

Tek modelle her şey yapmaya çalışmak yerine **uzmanlaşma** kullan.

### Klasik Pattern: Planner + Executor

```
┌──────────────────────────────┐
│  Claude Opus (Planner)       │
│  - Yüksek seviye mimari      │
│  - Blueprint                 │
│  - Best-practice review      │
│  - Güvenlik denetimi         │
└─────────────┬────────────────┘
              │ (blueprint)
              ▼
┌──────────────────────────────┐
│  MiniMax M2 (Executor)       │
│  - Plan-Act-Reflect döngüsü  │
│  - Spesifik kod bileşenleri  │
│  - Test + debug              │
└─────────────┬────────────────┘
              │ (üretilen kod)
              ▼
┌──────────────────────────────┐
│  Claude Opus (Reviewer)      │
│  - Güvenlik incelemesi       │
│  - Tutarlılık denetimi       │
│  - Final approve             │
└──────────────────────────────┘
```

### Neden Çalışır

| Model | Güçlü Yönü | Görev |
|-------|------------|-------|
| Claude Opus | Reasoning, mimari, XML, denetim | Planner + Reviewer |
| MiniMax M2 | Agentic loop, tool use, hata onarma | Executor |
| Qwen Coder | Spesifik kod kalitesi, çok dilli | Code generation |
| Haiku | Hızlı/ucuz sınıflandırma | Router, classifier |

### Pratik Örnek — SaaS Backend Oluşturma

```python
# Aşama 1: Claude Opus → blueprint
blueprint = claude_opus.complete("""
<task>Multi-tenant SaaS backend mimarisi taslağı</task>
<requirements>{{user requirements}}</requirements>
<output>
- Klasör yapısı
- requirements.txt
- Her dosyanın sorumluluğu
- Auth/DB/API katmanları
</output>
""")

# Aşama 2: Her dosya için MiniMax M2 → plan-act-reflect ile kod + test
for file_spec in blueprint.files:
    code = minimax_m2.agentic_loop(
        goal=f"{file_spec.name} dosyasını yaz, testleri geçsin",
        tools=[write_file, run_tests, read_file],
        max_iterations=10,
        blueprint=blueprint
    )

# Aşama 3: Claude Opus → tutarlılık + güvenlik review
review = claude_opus.complete("""
<files>{{all generated code}}</files>
<task>
- Import tutarsızlıkları
- SQL injection / XSS açıkları
- N+1 query problemleri
- Auth bypass riskleri
</task>
""")

# Aşama 4: Eğer kritik issue varsa → MiniMax'e geri besle, döngü
```

### Maliyet/Kalite Trade-off

- Sadece Opus: pahalı, ama tek model = basit
- Sadece M2: ucuz, ama mimari kararlar zayıf olabilir
- **Multi-model:** orta maliyet, **en yüksek kalite**

## 5. Self-Correction Chain (Otonom Düzeltme)

Plan-Act-Reflect'i prompt chaining ile birleştir:

```
Prompt A: "Bu spec için kod taslağı üret"
   ↓ output: draft code
Prompt B: "Bu kodu şu kriterlere göre incele: [liste]. Issue listesi üret."
   ↓ output: issues
Prompt C: "Bu issue'lara göre kodu rafine et."
   ↓ output: refined code
Prompt D: "Bu rafine edilmiş kod issues'ı çözdü mü? Yeni issue var mı?"
   ↓ if new issues → loop back to C
   ↓ if no issues → done
```

**Neden chain:** Her aşama farklı sistem prompt'u kullanır → tek prompt'ta yapamayacağın derinlikte denetim.

## 6. Dikkat Edilmesi Gereken Kritik Teknikler

| Teknik | Neden Önemli |
|--------|--------------|
| **Mesaj geçmişini koru** | Model planını/hatalarını hatırlamalı |
| **`reasoning_details` ekle** | Extended thinking modellerde zorunlu |
| **Max iterasyon limiti** | Sonsuz döngü riski (model takılırsa) |
| **Erken çıkış kriteri** | "Sonuç bulundu" durumunda durmasını sağla |
| **Tool çıktısını birebir ver** | Özetleme = bilgi kaybı = yanlış reflect |
| **Hata mesajını trim etme** | Traceback'in tamamı root cause için kritik |
| **Tool error vs logic error ayır** | Farklı recovery stratejileri |

## 7. Anti-Pattern'ler

- ❌ Plan'sız direkt Act → model "savruluyor", hatalar zincirleme
- ❌ Reflect olmadan döngü → aynı hatayı tekrar yapar
- ❌ Mesaj geçmişi sıkıştırma → bağlam kaybı, sıfırdan başlama
- ❌ Tek model her şeyi yapsın → uzmanlık kaybı + maliyet
- ❌ Limitsiz iterasyon → maliyet patlaması, sonsuz loop
- ❌ Tool sonucunu yorumlayıp kısaltma → reflect yanlış input alır

## 8. Framework / Tool

| Tool | Use Case |
|------|----------|
| **LangGraph** | State machine, conditional loops |
| **CrewAI** | Multi-agent, role-based |
| **AutoGen** | Multi-model conversation |
| **Anthropic SDK + extended thinking** | Claude native Plan-Act-Reflect |
| **MiniMax M2 native loop** | Built-in interleaved thinking |

## 9. Pratik Checklist (Yeni Agentic Sistem Kurarken)

- [ ] Goal tek cümle ve ölçülebilir mi?
- [ ] Success criteria net mi?
- [ ] Tool listesi tam mı?
- [ ] Max iterasyon limiti var mı?
- [ ] Message history persistence stratejisi belirlendi mi?
- [ ] Multi-model bölüşümü maliyet açısından mantıklı mı?
- [ ] Self-check / regression check loop'ta var mı?
- [ ] Erken çıkış (success) ve abort (max iter) durumları handle edildi mi?
- [ ] Test edildi mi golden dataset üzerinde? (`evaluation.md`)
- [ ] Başarılı pattern MemPalace'a kaydedildi mi? (`mempalace-integration.md`)
