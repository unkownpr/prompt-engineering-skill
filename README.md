<div align="center">

# 🧠 Prompt Engineering Skill

### LLM'lerden tutarlı, doğru ve tekrar üretilebilir çıktılar almak için Agent Skill

[![skills.sh](https://skills.sh/b/unkownpr/prompt-engineering-skill)](https://skills.sh/unkownpr/prompt-engineering-skill)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Agent Skills Spec](https://img.shields.io/badge/spec-agentskills.io-7c3aed)](https://agentskills.io)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-compatible-ea580c)](https://code.claude.com)

**Halüsinasyon ↓ &nbsp;·&nbsp; Tutarlılık ↑ &nbsp;·&nbsp; Token verimi ↑**

Chain-of-Thought, RAG, COSTAR, self-improving loop'lar — hepsi tek skill içinde.
Claude Code, Cursor, Codex, Gemini CLI ve 50+ agent ile uyumlu.

```bash
npx skills add unkownpr/prompt-engineering-skill
```

</div>

---

## 🎯 Bu Skill Ne Yapar

Yapay zekâ projelerinde **prompt mühendisliğinin sistemli versiyonu**. Agent, aşağıdaki durumları yakaladığında otomatik devreye girer ve sana uzman bir prompt engineer gibi yaklaşır:

| Durum | Skill Ne Yapar |
|-------|----------------|
| LLM'den bozuk/eksik çıktı | Halüsinasyon kök sebebini bulur, anti-hallucination tekniği önerir |
| Yeni prompt yazıyorsun | COSTAR / Chain-of-Thought şablonuyla strüktürel başlangıç verir |
| RAG sistemi kuruyorsun | Retrieval kalitesi, chunking, re-ranking, citation pattern'leri önerir |
| Agent / autonomous workflow | Multi-step prompt zinciri, role assignment, guardrail tasarımı |
| Birden fazla model karşılaştırıyorsun | Claude / GPT / Qwen / Kimi / MiniMax için model-spesifik optimizasyon |
| Prompt çalışıyor mu bilmiyorsun | Golden dataset + evaluation framework kurar |

---

## ⚡ Hızlı Kurulum

**Tüm desteklenen agent'lara global yükle:**

```bash
npx skills add unkownpr/prompt-engineering-skill -g
```

**Sadece Claude Code'a:**

```bash
npx skills add unkownpr/prompt-engineering-skill -g -a claude-code
```

**Önce içeriği listele:**

```bash
npx skills add unkownpr/prompt-engineering-skill --list
```

> [!TIP]
> `-g` flag global kurulum (`~/.claude/skills/`). Flag'siz proje-local (`.claude/skills/`).

---

## 🔥 Tetikleyici Kelimeler

Agent şu anahtar kelimeleri yakaladığında skill aktive olur:

<table>
<tr>
<td><b>Yazma & İyileştirme</b></td>
<td>

`prompt yaz` · `prompt iyileştir` · `system prompt` · `agent prompt`

</td>
</tr>
<tr>
<td><b>Kalite & Hata</b></td>
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
prompt-engineering/
├── SKILL.md                    # Ana skill talimatları + frontmatter
├── references/
│   ├── agentic-loops.md        # Self-improving döngüler, ReAct, reflection
│   ├── anti-hallucination.md   # Halüsinasyonu önleme teknikleri
│   ├── cot-techniques.md       # Chain-of-Thought varyantları
│   ├── evaluation.md           # Prompt değerlendirme + golden dataset
│   ├── frameworks.md           # COSTAR, RTF, CRISPE, ICE
│   ├── mempalace-integration.md # Memory palace + long-context patterns
│   ├── model-selection.md      # Claude / GPT / Qwen / Kimi / MiniMax tradeoffs
│   ├── prompt-chaining.md      # Multi-step zincirleme
│   └── rag-tips.md             # Retrieval, chunking, re-ranking
└── templates/
    ├── costar-template.md      # COSTAR boilerplate
    ├── cot-template.md         # Chain-of-Thought boilerplate
    └── rag-template.md         # RAG system prompt boilerplate
```

---

## 🧩 Desteklenen Agent'lar

Skill, Agent Skills spec'ine uyduğu için **50+ agent**'ta çalışır:

Claude Code · Cursor · Codex · Gemini CLI · OpenCode · GitHub Copilot · Amp · Goose · Roo Code · Continue · Kiro · Crush · Windsurf · Factory · Junie · ve [diğerleri](https://agentskills.io/clients)

---

## 💡 Örnek Kullanım

Kurduktan sonra agent ile normal konuş — skill kendiliğinden tetiklenir:

```
Sen:  "Şu prompt'tan sürekli halüsinasyon alıyorum: '...'.
       Nedeni ne olabilir?"

Agent: [skill aktive olur]
       → Prompt'u analiz eder
       → 3 olası kök sebep listeler (kontext eksikliği, vague instruction, 
         konflikte instructions)
       → Her biri için somut fix önerir
       → Düzeltilmiş prompt + neden öyle yazıldığını açıklar
```

---

## 🛠️ Kendi Skill'ini Yazmak İster Misin

Bu skill `npx skills init` ile başlayıp adım adım inşa edildi. Şablon:

```bash
npx skills init my-skill
```

Detaylar → [agentskills.io/specification](https://agentskills.io/specification)

---

## 👤 Author

**ssilistre** · Prompt engineering & agent systems
🌐 [ssilistre.dev](https://ssilistre.dev) · 🐙 [@unkownpr](https://github.com/unkownpr)

Yeni özellik istek/öneri için → [Issues](https://github.com/unkownpr/prompt-engineering-skill/issues)
PR'lar açık → forkla, iyileştir, merge.

---

## 📄 Lisans

MIT © [ssilistre](https://ssilistre.dev)

<div align="center">

**Faydalı bulduysan ⭐ ver, install et, paylaş.**
skills.sh leaderboard'da yükselsin.

</div>
