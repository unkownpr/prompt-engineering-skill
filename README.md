<div align="center">

<img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExbnNkbmZzYjFnYWR5N3p6Z2p4bWNlbnA1NnBxenBnZGVibWQ2OTY5biZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/e7CD41ekE5ZUSgTnkn/giphy.gif" alt="Prompt Engineering Skill" width="480" />

# 🧠 Prompt Engineering Skill

### LLM'lerden tutarlı, doğru ve tekrar üretilebilir çıktılar almak için bir Agent Skill

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
prompt-engineering/
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

Kurulumdan sonra agent ile normal konuşmaya devam et. Skill kendiliğinden tetiklenir:

```
Sen:    "Şu prompt'tan sürekli halüsinasyon alıyorum: '...'.
         Nedeni ne olabilir?"

Agent:  [skill devreye girer]
        → Prompt'u analiz eder
        → Üç olası kök sebebi listeler (eksik context, belirsiz instruction,
          çelişen direktifler)
        → Her biri için somut çözüm önerir
        → Düzeltilmiş prompt'u ve bu kararların nedenini açıklar
```

---

## 👤 Hakkında

Bu skill, **ssilistre** tarafından gerçek dünyadaki prompt engineering deneyiminden damıtılarak yazıldı. Amaç, sık tekrar eden prompt tasarım kararlarını agent'a aktarmak ve aynı işi her seferinde sıfırdan düşünmek zorunda kalmamak.

🌐 [ssilistre.dev](https://ssilistre.dev) &nbsp;·&nbsp; 🐙 [@unkownpr](https://github.com/unkownpr)

Geri bildirim, hata raporu veya yeni özellik önerin varsa [Issues](https://github.com/unkownpr/prompt-engineering-skill/issues) sekmesinden ulaşabilirsin. Katkı sağlamak istersen repo'yu fork edip bir pull request açman yeterli — her katkı memnuniyetle değerlendirilir.

---

## 📄 Lisans

MIT &copy; [ssilistre](https://ssilistre.dev)

<div align="center">

**Faydalı bulduysan ⭐ vermeyi unutma, kur ve paylaş.**
skills.sh leaderboard'unda yükselelim.

</div>
