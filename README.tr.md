<div align="center">

<img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExbnNkbmZzYjFnYWR5N3p6Z2p4bWNlbnA1NnBxenBnZGVibWQ2OTY5biZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/e7CD41ekE5ZUSgTnkn/giphy.gif" alt="Prompt Engineering Skill" width="480" />

# 🧠 Prompt Engineering Skill

### LLM'lerden tutarlı, doğru ve tekrar üretilebilir çıktılar almak için bir Agent Skill

**🇹🇷 Türkçe** &nbsp;·&nbsp; [🇬🇧 English](./README.md)

[![skills.sh](https://skills.sh/b/unkownpr/prompt-engineering-skill)](https://skills.sh/unkownpr/prompt-engineering-skill)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Agent Skills Spec](https://img.shields.io/badge/spec-agentskills.io-7c3aed)](https://agentskills.io)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-compatible-ea580c)](https://code.claude.com)

**Halüsinasyon ↓ &nbsp;·&nbsp; Tutarlılık ↑ &nbsp;·&nbsp; Token verimi ↑**

Chain-of-Thought, RAG, COSTAR ve self-improving loop teknikleri tek bir skill içinde toplandı.
Claude Code, Cursor, Codex, Gemini CLI ve 50'den fazla agent ile uyumlu.

```bash
npx skills add unkownpr/prompt-engineering-skill
```

</div>

---

## 🎯 Bu Skill Ne Yapar

Yapay zekâ projelerinde prompt mühendisliğini sistematik hale getirir. Agent, aşağıdaki durumları yakaladığında otomatik devreye girer ve sana deneyimli bir prompt engineer gibi yaklaşır:

| Durum | Skill Nasıl Yardım Eder |
|-------|-------------------------|
| LLM'den bozuk veya eksik çıktı alıyorsun | Halüsinasyonun kök sebebini bulur, önleyici teknikler önerir |
| Sıfırdan yeni bir prompt yazıyorsun | COSTAR veya Chain-of-Thought şablonlarından uygun olanı kullanarak iskeleti hazır verir |
| RAG sistemi kuruyorsun | Retrieval kalitesi, chunking, re-ranking ve citation desenleri için somut öneriler sunar |
| Agent veya autonomous workflow tasarlıyorsun | Multi-step prompt zinciri, role assignment ve guardrail kurgusu çıkarır |
| Birden fazla model karşılaştırıyorsun | Claude, GPT, Qwen, Kimi ve MiniMax için modele özel optimizasyon yapar |
| Prompt'un çalışıp çalışmadığını bilmiyorsun | Golden dataset ve evaluation framework kurar, ölçülebilir hale getirir |

---

## ✨ Nasıl Kaliteli Prompt Üretiyor

Bu skill'in farkı, bir prompt yazarken tek bir şablonu körü körüne uygulamaması. Talebini aldığı anda **birden fazla referans dosyasından gelen bilgiyi birleştirerek** çalışıyor:

1. **Görevi sınıflandırır.** Senin ne istediğine bakar: kod üretimi mi, sınıflandırma mı, özetleme mi, agent workflow'u mu? Görev tipi şablon seçimini belirler.
2. **Uygun framework'ü seçer.** COSTAR, RTF, CRISPE, ICE arasından göreve en uygun olanı `references/frameworks.md` dosyasından çağırır. Sadece dökümanı kopyalamaz — senin bağlamına uyarlar.
3. **Reasoning tekniğini ekler.** Görev çok adımlı düşünme gerektiriyorsa `references/cot-techniques.md`'den Chain-of-Thought, Tree-of-Thought veya self-consistency desenini katar. Tek adımlık görevde bu adımı atlar.
4. **Anti-hallucination kurallarını uygular.** `references/anti-hallucination.md`'den belirsizlik durumunda "bilmiyorum" demeyi zorlayan ifadeler, kaynak gösterme zorunluluğu, citation pattern'leri ve grounding kuralları eklenir.
5. **Modele özel optimizasyon yapar.** Hedef model Claude ise XML tag kullanır, GPT ise markdown header, Qwen veya Kimi ise CJK-friendly desenler kullanır. Detay `references/model-selection.md` içinde.
6. **RAG veya retrieval varsa entegre eder.** Sistem RAG kullanıyorsa `references/rag-tips.md` kaynak alıntı formatı, chunk referansları ve fallback davranışı eklenir.
7. **Çıktıyı değerlendirilebilir hale getirir.** Prompt'un yanına 3-5 örnek girdi-çıktı çifti, golden dataset önerisi ve LLM-as-judge rubric'i koyar. Böylece "çalışıyor mu" sorusu tahmin değil ölçüm haline gelir.

Yani aldığın prompt, tek bir şablonun çıktısı değil — **görev tipine göre seçilmiş 4-6 farklı tekniğin sentezi**. Her bir öneri arkasındaki referans dosyasıyla birlikte gelir, istediğinde detaya inebilirsin.

---

## 🪶 Token Verimliliği Nasıl Sağlanıyor

Skill, **progressive disclosure** prensibiyle yazıldı. Yani her bilgi her zaman context'e yüklenmiyor:

1. **Discovery aşaması** — Agent açılışta sadece skill'in `name` ve `description` alanlarını okur. Tüm yük yaklaşık 250 token.
2. **Activation aşaması** — Konuşmada tetikleyici bir kelime geçtiğinde (örn. "halüsinasyon", "RAG", "COSTAR") tam `SKILL.md` yüklenir.
3. **Reference aşaması** — `references/` ve `templates/` altındaki dosyalar yalnızca o anki göreve gerektiği kadar okunur. Hepsi birden context'e girmez.

Bunun yanında skill içinde şu pratikler uygulandı:

- **Bölünmüş referans dosyaları** — Tek büyük dosya yerine konuya göre ayrılmış 9 küçük referans. Agent yalnızca ihtiyacı olanı yükler.
- **Hazır şablonlar** — Sık kullanılan COSTAR, CoT ve RAG iskeletleri `templates/` altında. Yeniden üretmek için token harcanmıyor.
- **Net tetikleyici kelimeler** — `description` alanındaki anahtar kelimeler agent'ın gereksiz yere skill'i yüklemesini engelliyor.

Sonuç: skill 10+ dosya ve binlerce satır içerse de tipik bir konuşmada eklediği yük çok düşük kalıyor.

---

## ⚡ Hızlı Kurulum

**Tüm desteklenen agent'lara global olarak yükle:**

```bash
npx skills add unkownpr/prompt-engineering-skill -g
```

**Sadece Claude Code'a yükle:**

```bash
npx skills add unkownpr/prompt-engineering-skill -g -a claude-code
```

**Önce içeriği listelemek istersen:**

```bash
npx skills add unkownpr/prompt-engineering-skill --list
```

> [!TIP]
> `-g` flag'i global kurulum yapar (`~/.claude/skills/`). Flag olmadan kurulum proje bazlı olur (`.claude/skills/`).

---

## 🔥 Tetikleyici Kelimeler

Agent aşağıdaki anahtar kelimelerden birini yakaladığında skill devreye girer:

<table>
<tr>
<td><b>Yazma ve İyileştirme</b></td>
<td>

`prompt yaz` · `prompt iyileştir` · `system prompt` · `agent prompt`

</td>
</tr>
<tr>
<td><b>Kalite ve Hata</b></td>
<td>

`halüsinasyon` · `LLM çıktı kalitesi` · `prompt injection` · `format hatası`

</td>
</tr>
<tr>
<td><b>Teknikler</b></td>
<td>

`Chain of Thought` · `CoT` · `RAG` · `COSTAR` · `few-shot` · `role assignment`

</td>
</tr>
<tr>
<td><b>Değerlendirme</b></td>
<td>

`prompt evaluation` · `golden dataset` · `LLM-as-judge` · `regression test`

</td>
</tr>
<tr>
<td><b>Model Seçimi</b></td>
<td>

`Claude prompt` · `GPT prompt` · `Qwen prompt` · `Kimi prompt` · `MiniMax prompt`

</td>
</tr>
</table>

---

## 📦 Skill İçeriği

```
prompt-engineering-skill/
├── README.md
└── prompt-engineering/
    ├── SKILL.md                     # Ana skill talimatları + frontmatter
    ├── references/
    │   ├── agentic-loops.md         # Self-improving döngüler, ReAct, reflection
    │   ├── anti-hallucination.md    # Halüsinasyonu önleme teknikleri
    │   ├── cot-techniques.md        # Chain-of-Thought varyantları
    │   ├── evaluation.md            # Prompt değerlendirme + golden dataset
    │   ├── frameworks.md            # COSTAR, RTF, CRISPE, ICE
    │   ├── mempalace-integration.md # Memory palace + long-context desenleri
    │   ├── model-selection.md       # Claude / GPT / Qwen / Kimi / MiniMax karşılaştırması
    │   ├── prompt-chaining.md       # Çok adımlı prompt zincirleme
    │   └── rag-tips.md              # Retrieval, chunking, re-ranking
    └── templates/
        ├── costar-template.md       # COSTAR boilerplate
        ├── cot-template.md          # Chain-of-Thought boilerplate
        └── rag-template.md          # RAG system prompt boilerplate
```

---

## 🧩 Desteklenen Agent'lar

Skill, Agent Skills spec'ine uyduğu için **50'den fazla agent** ile uyumlu:

Claude Code · Cursor · Codex · Gemini CLI · OpenCode · GitHub Copilot · Amp · Goose · Roo Code · Continue · Kiro · Crush · Windsurf · Factory · Junie · ve [diğerleri](https://agentskills.io/clients)

---

## 💡 Örnek Kullanım

Kurulumdan sonra agent ile normal konuşmaya devam et. Skill kendiliğinden tetiklenir.

**Senin talebin:**

> "E-ticaret destek e-postalarını şikayet, iade talebi, kargo sorusu, genel soru olarak sınıflandıran bir prompt yaz. Claude için olacak. Hatalı sınıflandırma yapmasın."

**Skill'in ürettiği prompt (özet):**

````markdown
You are an email triage assistant for an e-commerce company.
Classify the incoming customer email into exactly one of these categories:

<categories>
- complaint        — product quality, service, or experience issues
- refund_request   — explicit refund, return, or money-back asks
- shipping_query   — delivery status, ETA, tracking, address change
- general_question — anything that doesn't match above
</categories>

<rules>
1. If the email touches multiple topics, pick the PRIMARY intent —
   the action the customer is asking for.
2. If you cannot confidently classify, output "general_question".
   Do NOT invent a new category.
3. Output ONLY the category slug. No explanation, no greeting,
   no markdown.
</rules>

<examples>
<email>Order #4521 still hasn't arrived, been 2 weeks.</email>
<category>shipping_query</category>

<email>The headphones broke after 3 days. I want my money back.</email>
<category>refund_request</category>

<email>Do you ship to Turkey?</email>
<category>general_question</category>
</examples>

<email>
{{user_email}}
</email>
````

**Skill ayrıca şunları ekler:**
- 10 örnekten oluşan golden dataset önerisi (her kategoride 2-3 örnek)
- LLM-as-judge rubric'i (3 kriter: kategori doğru mu, gereksiz çıktı var mı, edge case'i hangi kategoriye attı)
- Edge case listesi (boş e-posta, birden fazla intent, sarkastik dil, başka dilde gelen e-posta)
- Production'a geçmeden önce yapılması gereken regression test plan'ı

---

## 📄 Lisans ve İletişim

MIT &copy; [ssilistre](https://ssilistre.dev) &nbsp;·&nbsp; 🐙 [@unkownpr](https://github.com/unkownpr)

Geri bildirim ve özellik önerileri için [Issues](https://github.com/unkownpr/prompt-engineering-skill/issues). PR'lara açığım.

<div align="center">

**Faydalı bulduysan ⭐ vermeyi unutma, kur ve paylaş.**
skills.sh leaderboard'unda yükselelim.

</div>

---

<div align="center">

### 💎 Sponsor

<a href="https://yapayzekapi.store" target="_blank">
  <img src="https://i.hizliresim.com/snpojv7.png" alt="yapayzekapi.store — One API Key, All AI Models" width="640" />
</a>

**[yapayzekapi.store](https://yapayzekapi.store) — Tek API Key, Tüm AI Modelleri**

Akıllı routing ve Çinli model aileleri tek bir API key ile.
Cursor, VS Code ve Cline içinde doğrudan kullanılabilir.

**[💰 Fiyatları Gör →](https://yapayzekapi.store)**

</div>
