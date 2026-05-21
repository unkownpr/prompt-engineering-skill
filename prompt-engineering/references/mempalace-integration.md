# MemPalace Entegrasyonu — Self-Improving Prompt Memory

[MemPalace](https://github.com/MemPalace/mempalace) = yerel, verbatim AI hafıza sistemi. %96.6 R@5 LongMemEval skoruyla en iyi open-source memory backend.

Bu skill ile birlikte kullanıldığında: **başarılı prompt pattern'leri kaydedersin, yeni prompt yazarken otomatik recall edilir**. Hem token verimi hem kalite artar.

## Neden MemPalace?

| Özellik | Fayda |
|---------|-------|
| Verbatim storage | Prompt'un orijinal hali, özet değil |
| Local-first | Hassas iç prompt'lar dışarı çıkmaz |
| Semantic search | "RAG promptu örneği" sorgusu ilgili geçmişi getirir |
| Wing/Room/Drawer yapısı | Proje/kategori bazında ayır |
| MCP server | Claude Code'dan direkt erişim |

## Kurulum (One-Time)

```bash
# uv ile (önerilen)
uv tool install mempalace

# pip ile alternatif
pip install mempalace

# Init — prompt geçmişi için ayrı palace
mempalace init ~/.claude/prompt-memory

# MCP server'ı Claude Code'a tanıt (claude_desktop_config.json)
# https://mempalaceofficial.com/reference/mcp-tools
```

## Klasör Stratejisi (Wings)

```
~/.claude/prompt-memory/
├── wings/
│   ├── general-prompts/     # her proje için ortak pattern
│   ├── rag-prompts/         # RAG-spesifik kazanan prompt'lar
│   ├── agent-prompts/       # tool-using agent prompt'ları
│   ├── cot-prompts/         # CoT şablonları
│   └── eval-prompts/        # LLM-as-judge prompt'ları
```

Her wing içinde **drawer = bir prompt pattern**.

## Workflow: Yeni Prompt Yazarken

### Adım 1: Geçmişi Recall Et

```bash
# Komut satırı
mempalace search "RAG promptu evidence-first format"

# Veya MCP üzerinden (Claude Code içinde)
# mempalace search tool'unu çağır → top 5 ilgili pattern
```

### Adım 2: Pattern'i Uyarla

Çıkan pattern'i yeni göreve uyarla. Sıfırdan yazmadığın için:
- Token tasarrufu (200-500 token kaybetmiyorsun)
- Test edilmiş yapı kullanıyorsun
- Mantık tutarlılığı yüksek

### Adım 3: Yeni Pattern'i Kaydet

Prompt başarılı çalıştıysa kaydet:

```bash
mempalace add \
  --wing rag-prompts \
  --drawer "evidence-first-with-conflict-detection" \
  --content "$(cat my_winning_prompt.md)" \
  --metadata "domain=legal,model=claude-opus,score=4.6/5"
```

## Workflow: Self-Improving Loop

```
1. Prompt yaz (sıfırdan veya recall ile)
   ↓
2. Test et (golden dataset, LLM-as-judge)
   ↓
3. Skor ≥4.0?
   - Evet → MemPalace'a kaydet (metadata: skor, model, domain)
   - Hayır → Critique-refine döngüsü, tekrar test
   ↓
4. Aynı domain'de yeni prompt gerekirse:
   mempalace search "{{domain}} {{task type}}" → recall
```

**Net etki:** Her başarılı prompt gelecekteki promptları besler. Bilgi birikmeli, kayıp yok.

## MCP Tool Kullanımı (Claude Code İçinde)

MemPalace MCP server kurulduysa, Claude Code direkt erişir. 29 tool var, kritikler:

| Tool | Kullanım |
|------|----------|
| `mempalace_search` | Semantic + keyword arama |
| `mempalace_add` | Yeni drawer (prompt) ekle |
| `mempalace_get` | Spesifik drawer'ı getir |
| `mempalace_list_wings` | Mevcut kategorileri listele |
| `mempalace_diary` | Agent'ın kendi notlarını tut |

## Pattern Metadata Standardı

Her prompt drawer'ı için tutarlı metadata kullan:

```yaml
domain: rag | agent | extraction | summarization | classification | ...
model: claude-opus | gpt-4 | qwen-max | ...
score: 4.6/5
golden_dataset: customer_support_v3.json
created: 2026-05-17
updated: 2026-05-17
deprecated: false
notes: |
  Conflict detection ekledim, %15 daha az halüsinasyon
  Edge: 50K+ token bağlamda performans düşer
```

Bu metadata sayesinde:
- `mempalace search --filter "model=claude-opus AND score>4"` çalışır
- Eski/deprecated pattern'leri filtreler
- Karşılaştırmalı analiz yapabilirsin

## Knowledge Graph Kullanımı

MemPalace temporal entity-relationship graph içerir. Prompt evrimi için ideal:

```
Entity: "evidence-first-pattern"
Relations:
  - evolved_from: "basic-rag-prompt"
  - works_with_model: "claude-opus"
  - tested_on_dataset: "legal-qa-v2"
  - replaced_by: "evidence-first-v3"
  - valid_period: 2025-01-01 to 2025-12-31
```

`mempalace_kg_query` ile sorgulayabilirsin: "evidence-first-pattern'in geçmişi ne?"

## Token Verimliliği — Sayılar

Tipik bir RAG prompt yazımı:
- **Sıfırdan**: 500-800 token (talimatlar) + test → ~3000 token
- **Recall ile**: 100 token (recall query) + adapt → ~600 token

**Tasarruf: ~%80**, üstelik daha iyi kalitede (test edilmiş pattern).

## Anti-Pattern

- ❌ Her prompt'u kaydet → palace'ı çöplüğe çevirir
  - ✅ Sadece skor ≥4.0 olanlar
- ❌ Generic metadata ("good prompt") → arama isabetsiz
  - ✅ Spesifik metadata (domain, model, score, dataset)
- ❌ Pattern'i recall edip körü körüne kopyala → yeni context'e uymayabilir
  - ✅ Recall + adapt + re-test
- ❌ Deprecated pattern'leri silme
  - ✅ `deprecated: true` etiketi + neden notu

## Diğer Memory Sistemleri ile İlişki

MemPalace'a alternatif:
- **Mem0** — bulut tabanlı, RAG odaklı
- **Zep** — agent memory, knowledge graph
- **Letta (eski MemGPT)** — recursive summarization

Bu skill MemPalace varsayar ama yapı benzer. Başka backend kullanırsan aynı metadata standardını uygula.

## Hızlı Başlangıç Checklist

- [ ] `uv tool install mempalace`
- [ ] `mempalace init ~/.claude/prompt-memory`
- [ ] Wings oluştur: general-prompts, rag-prompts, agent-prompts, cot-prompts, eval-prompts
- [ ] MCP config (Claude Code için)
- [ ] İlk başarılı prompt'u kaydet
- [ ] Bir hafta sonra recall yap, ne kadar tasarruf ettin?
