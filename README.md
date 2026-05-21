# prompt-engineering

[![skills.sh](https://skills.sh/b/unkownpr/prompt-engineering-skill)](https://skills.sh/unkownpr/prompt-engineering-skill)

Yapay zeka ve prompt mühendisliği için Agent Skill. LLM'ler için yüksek kaliteli prompt yazma, halüsinasyon azaltma, Chain-of-Thought, RAG, model seçimi, prompt değerlendirme ve self-improving prompt döngüleri.

## Kurulum

```bash
# Tüm desteklenen agent'lara yükle (Claude Code, Cursor, Codex, Gemini CLI, ...)
npx skills add unkownpr/prompt-engineering-skill

# Sadece Claude Code'a global yükle
npx skills add unkownpr/prompt-engineering-skill -g -a claude-code

# Skill içeriğini önce listele
npx skills add unkownpr/prompt-engineering-skill --list
```

## Ne Zaman Devreye Girer

- LLM için prompt yazma veya iyileştirme
- Halüsinasyon, format hatası, eksik bilgi gibi beklenmedik çıktılar
- RAG sistemi kurma veya optimize etme
- AI agent / autonomous workflow tasarımı
- Few-shot örnekler, system prompt, role assignment
- Prompt değerlendirme veya golden dataset oluşturma
- Belirli bir model için optimizasyon (Claude, GPT, Qwen, Kimi, MiniMax)

## Tetikleyici Kelimeler

`prompt yaz`, `prompt iyileştir`, `LLM çıktı kalitesi`, `halüsinasyon`, `Chain of Thought`, `CoT`, `RAG`, `COSTAR`, `few-shot`, `prompt evaluation`, `model seçimi`, `agent prompt`, `system prompt`, `prompt injection`

## Yapı

```
prompt-engineering/
├── SKILL.md          # Skill metadata + ana talimatlar
├── references/       # Detaylı referans dökümanları
└── templates/        # Hazır prompt şablonları
```

## Lisans

MIT
