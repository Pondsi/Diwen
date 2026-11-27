# dsh-diwen — Diwen (迪文) Concept Coordinate System

> Release **6.15.2** — published to npm as `diwen@6.15.2` (registry API verified; tarball install smoke-tested). The registry remains authoritative: `npm view diwen version`.
>
> 中文概要见 [说明.md](说明.md)。

## What Diwen is: a communication language for machines

Diwen is a **machine-verifiable concept coordinate system** for agent-to-agent exchange: the deterministic content in natural-language↔machine interaction, and machine-to-machine interaction.

Natural language is ambiguous and drifts; Diwen anchors meaning to discrete concept coordinates (S3a semantic field × S3b construction), so machines can **align precisely**, and each concept renders back to a unique canonical Chinese definition. Diwen is **not a translator** and does not generate prose; it is a verifiable semantic intermediate representation above natural language.

## The two mapping directions (asymmetric by design)

- **Natural language → Diwen is NOT unique, and may be unreachable.** A word can carry several senses (polysemy), so mapping needs model word-sense disambiguation (WSD) — it is not mechanical (Axiom ②). When no concept fits, Diwen **does not guess or force a nearby concept**: the original text is preserved **verbatim** (Axiom ④). *Unreachable is a first-class, honest outcome.*
- **Diwen → natural language IS unique and deterministic.** Each concept renders to exactly one canonical Chinese definition (Axiom ①), machine-verifiable, zero drift. Determinism is the invariant.

## What's new
- **6.16.4 (2026-11-27)**: license relaxed to attribution-only (**redistribution now allowed**; commercial use, modification, mirroring and decryption are permitted — the only condition is crediting Pondsi). Candidate pool reached **197,009 verified content senses** (coverage 90.81%); npm package rebuilt and republished.


- **Full-lexicon curation completed**: 264,723 source words curated; concept inventory grew from 64,166 → **218,419** across **194** semantic fields. All growth enters the **bulk / `semanticApproval=false`** tier — *passing mechanical validation ≠ semantically approved*.
- **Matcher defect fixed (greedy consumption without backtracking)**: newly added non-word surfaces (e.g. 不退/我身/小女) could consume text, be dropped by the segmentation filter afterwards, and leave the covered word unreachable (worst case: a whole sentence mapping to nothing). Fixed by moving the compound/boundary checks into candidate selection so a rejected candidate falls back to shorter ones. Compound-level accuracy **0.9369 → 0.9660** (same original gold).
- **Simplified/Traditional surface ownership fixed**: 22 duplicate concepts created from variant spellings (進入/进入, 歡呼/欢呼, 以微知著/以微知着 …) were removed (Axiom ⑤: spelling variants do not create new concepts); 10 regionally distinct words (社群/社区, 影片/视频, 月台/站台 …) keep two concepts, with surface forms owned by the word itself. Regression invariants now enforce *“a newer concept may never take over an existing form”* and *“simplified/traditional packs must agree”*.
- **Field gate tightened** from `max(100000, 70%)` to `max(2000, 45%)` (documented re-specification, not a silent loosening). `abstract1` (the explicit *unverified field* pool) is **40.5%**.
- **Field-provenance tiers are published** (see Status) instead of a single “unverified” number.

## Use cases — and the measured benefit in each

Benefits are **measured**, not advertised: token counts use real `cl100k_base` BPE (`npm run benefits`); accuracy comes from blind WSD evaluation; `semanticApproval=false` throughout. **Token savings are relative to natural-language definitions / disambiguation, not bare Chinese words.**

| Scenario | What Diwen adds | Measured benefit |
|---|---|---|
| **Multi-agent collaboration** | Agents exchange concept coordinates instead of re-describing meanings in ambiguous NL | A concept reference costs **2.86 tokens (coordinate) vs 10.98 (NL definition) = −73.9%**; by id **1.71 tokens = −84.5%** (852 mapped senses, real cl100k_base BPE). Sense alignment: model WSD **0.9923** (qwen4) / **0.9933** (deepseek) concept accuracy (frozen round136 subset) |
| **Sub-agent communication** | Lead ↔ sub-agents exchange tasks/results as deterministic concepts | Same compact reference; rendering **100% deterministic** (full round-trip over the whole inventory, **0 identity / 0 determinism failures**) |
| **Deterministic knowledge base** | Knowledge anchored to machine-verifiable concepts, not fuzzy text | Canonical concept index: id vs lemma **−35.3%** tokens; coordinate vs definition **−73.9%** |
| **Context summaries / prompts** | Compressed meaning carried as concept coordinates, stable across models | Compact concept references (**−78–88%** vs definitions); stable canonical rendering across turns/models |
| **Error correction (any NL→Diwen)** | Model WSD picks the right sense; honest abstention otherwise | Polysemous mapped words: mechanical **0.192 → ~0.99** with WSD (**≈+80pp**); verbatim abstention recall **0.9931** / precision **0.9983** → no fabrication |
| **Real-time / local** | Local decision models, sub-second; deterministic render instant | d3:4b **0.09s**, clef-flash **0.16s**, clef **0.45s** per example; cloud cascade routes easy cases to a small model |

**Honest caveats**: token savings are vs NL *definitions*, not raw sentences; coverage is <100% by design (Axiom ④); model accuracies are measured on frozen holdout / pressure subsets, not arbitrary real-world text; most of the 218k inventory is machine-curated and **not** semantically approved.

## Status: Chinese is deliverable (zh + zh-TW)

**Scale**: **218,419 concepts** / 194 fields; surface packs **zh 218,512 forms** and **zh-TW 218,595 forms**, each covering **all 218,419 concepts** (every concept reachable; simplified/traditional packs agree on every shared form). Frozen gold: **2889 sentences / 1024 senses**; frozen holdout 116. Private Diwen extension supported (user concepts, id ≥ 699051).

**Evaluation, stated at its proper strength**:

| Metric | Value | Basis |
|---|---|---|
| Compound-word mapping accuracy (C1) | **0.9660** | 206 real-domain compound cases, mechanical longest-match, original gold |
| Single-character baseline | below 0.766 | same cases; gate requires compound − single > 20pp and passes |
| Full-gold mechanical lower bound | 0.2111 (610 correct / 407 overMap / 483 underMap) | 2889-sentence gold; this is the **disclosed mechanical lower bound, not the production path**; it moved 0.2253 → 0.2125 → 0.2111 across the expansion and the matcher fix (Δ within the ±0.76pp binomial noise; the C1 gain is significant) |
| Blind WSD (qwen4 / deepseek) | 0.9923 / 0.9933 | frozen round136 polysemy subset, zero leakage; **unchanged lexicon-independent measurement** |
| Local decision models (d3 / clef-flash / clef) | 0.9267 / 0.9467 / 0.96 | same subset, `--ask --choice` |
| Dual-source confirmation | 82.4% | curated words ∩ chinese-xinhua dictionary |

**Field-tag provenance (published instead of a single “unverified” figure)** — `abstract1` pool = **88,515 (40.5%)** of the inventory:

| Tier | Count | Meaning |
|---|---|---|
| T1 two-family agreement (written) | 28,208 | local curator family + independent cloud family agreed (unambiguous) |
| T2 self-label confirmed by cloud | 9,606 | curation-time label confirmed by an independent cloud family re-check — **measured agreement 64.3%** |
| 2-of-3 majority (third-family vote) | 2,295 | two families disagreed, third vote broke the tie |
| `abstract1` = two families say abstract | 8,174 | “verified abstract” (kept in the pool, provenance known) |
| `abstract1` = families disagree / no second opinion | ~81k total pool | honestly marked unverified; not silently promoted |

**Regression**: `run-all` **720/0 + SELFTEST OK**; standalone suites **62/62**; MCP selftest **45/45**; dsh selftest ALL PASS; Diwen→Chinese round-trip over the full inventory, `identityFailures=0 / determinismFailures=0`.

Axiom ① (Diwen → unique Chinese definition) is 100% deterministic. When no concept exists, the original text is preserved verbatim (Axiom ④) — nothing is fabricated. **`semanticApproval=false` always holds.**

## Quick start

This repo is Diwen's **open-core public distribution**: open loader shell + closed core (minified bundle) + encrypted concept data (AES-256-GCM). `npm install diwen` pulls the published version — currently **6.15.2**.

### npm package
```bash
npm install diwen
```
```js
import { mapChinese, describeConcept } from 'diwen';

// Natural language → Diwen fill contract (Axiom ②: model sense selection; Axiom ④: no concept → verbatim)
const r = await mapChinese('我吃饭', { model: 'deterministic' }); // 'auto'|'decision'|'off'
console.log(r.contract.status, r.contract.words, r.semanticApproval); // semanticApproval always false

// Diwen → unique Chinese definition (Axiom ①)
console.log(describeConcept(1223).lemma_zh); // 折
```
Or use from this repo: `cd packages/diwen && npm install`, then import locally.
- `model:'decision'` uses a local ollama decision model + calibrated abstention; `enhance:true` enables the enhancement layer.
- The runtime segmentation dependency `@node-rs/jieba` is installed automatically.

### Private Diwen (add your own concepts)
You can **add your own private concepts** without touching the public core; they merge into the lexicon and work across mapping / WSD / describe / render. Private concept IDs must be ≥ **699051**; glyph coordinates `(s3a,s3b)` must avoid base concepts (collision-checked at load).
```bash
diwen private template -o my-private.json    # scaffold
diwen private free-coords 20                 # list free coordinates
diwen private validate my-private.json       # validate partition / collisions / schema
export DIWEN_PRIVATE_LEXICON=my-private.json  # load
```
Set `DIWEN_PRIVATE_CANDIDATES` for private WSD senses. Your private file is your own data (plain JSON, not uploaded/redistributed).

### MCP server (core via npm `diwen`)
```bash
npm install diwen-mcp
npx diwen-mcp
```
Configure your MCP client with `command=node, args=[<absolute path>/diwen-mcp/server.mjs]`; **3 tools**: `diwen_map`, `diwen_describe`, `diwen_select`.

### DeepSeek Harness (dsh) plugin
Cordis shell + npm `diwen` core + deterministic force layer (diwen-dsh@0.3.2): registers `diwen_map`/`diwen_describe` + skill `diwen`; force-layer seams each have settings toggles (`forceSystemPrompt`/`forcePreStep`/`forcePostExecute`).

## Data artifact

The built encrypted data ships in this repo as `packages/diwen/dist/data.enc` (AES-256-GCM, gzip-then-encrypt); `env.cjs` decrypts it into a temp dir at load time, so there is no extra setup step. The npm package contains the same artifact.

Client-side encryption only raises the cost of extraction — it is **not** real secrecy. The data itself is not confidential; only the development process is kept private.

## Manuals
- [说明.md](说明.md): Chinese overview and usage.
- [issues.md](issues.md): how to report bugs or data corrections.

## Repository layout
```
packages/diwen/       npm package: index.mjs/env.cjs open shell + dist/core.mjs closed core + dist/data.enc encrypted data
packages/diwen-mcp/   MCP server (open shell, reuses closed core)
packages/diwen-dsh/   dsh plugin (Cordis shell + npm diwen core + force layer)
说明.md               Chinese overview
LICENSE               license
```

## License
**Commercial use permitted (free, no separate authorization), redistribution permitted, attribution required**: credit Diwen / 迪文 + author **Pondsi** + repository link. Full terms in [LICENSE](LICENSE).

## Limitations
- Deliverable scripts: **Simplified Chinese (zh) + Traditional Chinese (zh-TW)** sharing the same concept coordinates. The other 25 languages remain roadmap (Axiom ⑤: language-independent mapping, no pivot).
- Coverage is not 100% (by Axiom ④ design); words without a concept are preserved verbatim, never fabricated.
- Most of the 218k concepts are **machine-curated (`semanticApproval=false`)**: the mechanically enforced invariants (id contiguity, form ownership, cross-pack agreement, field-share gate, round-trip determinism) bound the data’s *consistency*, not its *semantic truth*. Semantic approval is a separate, slower process.

Author: Pondsi · https://github.com/Pondsi/dsh-diwen
