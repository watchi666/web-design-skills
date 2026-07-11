---
name: local-business-visual-scorer
description: Use when auditing, scoring, comparing, or QA-checking built local-business websites in WebsiteUpgrade. Orchestrates the website-quality-scorer CLI plus Impeccable/local-business/web-quality rules to produce a 100-point score, feature matrix, blockers, screenshots, Markdown and JSON reports.
---

# Local Business Visual Scorer

Use this skill when the user wants to analyze, score, compare, or QA a finished website or preview, especially WebsiteUpgrade local-business builds.

## Runtime reality

This skill is the operating procedure. The actual measurable checks are done by the CLI:

```bash
/home/hermes/projekte/website-upgrade/tools/website-quality-scorer/bin/website-quality-scorer.mjs
```

Run from the tool directory unless you pass absolute paths:

```bash
cd /home/hermes/projekte/website-upgrade/tools/website-quality-scorer
node bin/website-quality-scorer.mjs audit <url-or-path> --out <report-dir> --name <label>
node bin/website-quality-scorer.mjs audit <url> --out <report-dir> --name <label> --lighthouse
node bin/website-quality-scorer.mjs audit <url-or-path> --out <report-dir> --project <project-name> --history ./quality-history.jsonl
node bin/website-quality-scorer.mjs history ./quality-history.jsonl --limit 10
node bin/website-quality-scorer.mjs compare <old-url-or-path> <new-url-or-path> --out <report-dir>
```

The CLI uses local Chromium through DevTools Protocol. It has no npm dependencies.

## What it checks

The v2 CLI checks:

- Desktop, tablet, and mobile screenshots
- H1/H2 structure
- local/location cues
- CTA in first viewport
- `tel:`, `mailto:`, WhatsApp links
- forms, labels, mailto/FormData fallback signals
- FAQ / gallery / project signals
- JSON-LD and LocalBusiness-like schema
- broken images
- dead or empty links / `#` links
- HTTP linkcheck for HTTP(S) targets, with internal broken HTTP links treated as blockers
- mobile horizontal scroll
- mobile tap target sizing
- reduced-motion support
- keyframe count / motion presence
- Impeccable CSS bans: gradient text, `transition: all`, side-stripe borders
- AI/placeholder tells: Lorem Ipsum, Direction A/B, internal labels, AI image filenames, em dash
- console messages
- optional Lighthouse via `--lighthouse` when the local `lighthouse` command is installed
- V2.1 Lighthouse performance diagnosis: critical metrics, top image/JS/main-thread causes, heavy assets, render-blocking hints
- V3.0 optional benchmark history via `--history <jsonl>` and `history <jsonl>` for score trends, blockers, Lighthouse performance and project comparisons
- `vision-brief.md` review pack with screenshot paths and a concrete Hermes/Vision review prompt

## Score model

| Bereich | Max |
|---|---:|
| Hero / First Impression | 15 |
| Leistungen / Substanz | 10 |
| Vertrauen / Proof | 15 |
| Conversion / CTA | 20 |
| Mobile UX | 15 |
| Bilder / Authentizität | 10 |
| Copy / Natürlichkeit | 10 |
| Technik / Hygiene | 5 |
| **Gesamt** | **100** |

## Hard blockers

Regardless of score, treat these as not deliverable until fixed:

- no H1
- no contact path at all
- broken images
- dead or empty links, especially `#` CTAs
- mobile horizontal scroll
- form without fallback or alternate contact method
- placeholders or internal labels visible to visitors
- AI-generated image filenames visible in source

## Required workflow

1. Identify target:
   - live URL, e.g. Vercel preview or production alias
   - local path, e.g. `/home/hermes/projekte/website-upgrade/hasseler-bloemkes`
   - comparison pair, e.g. old site vs rebuild
2. For static local directories, prefer serving them over HTTP before auditing:
   - `cd <project> && python3 -m http.server <free-port> --bind 127.0.0.1`
   - audit `http://127.0.0.1:<free-port>/`, not only the raw directory path
   - raw `file://`/directory audits can produce browser error pages or miss relative assets, which creates false low scores.
3. Run the CLI.
4. Read the generated `report.md` and `report.json`.
5. If blockers exist, fix the site before calling it ready.
6. If score is below 80, verify the screenshot/report is actually the target page before editing the site. A sudden score collapse with H1 like “Your file couldn’t be accessed” means the audit target is wrong, not that the site became terrible.
7. If score is below 80, use the feature matrix to prioritize fixes.
8. For subjective visual judgement, inspect screenshots or run `vision_analyze` on the screenshots. The MVP report explicitly does not replace human/vision review of atmosphere and taste.
9. After deployment, rerun the scorer against the live production URL and report the live score, blockers, and screenshot paths.

Reference: `references/static-http-audit.md` documents the static-local-directory audit pitfall and recovery pattern.

## Output locations

For project-local runs, prefer:

```bash
<project>/website-quality-report/
```

For comparisons, prefer:

```bash
<project>/website-quality-compare/
```

Reports contain:

- `report.md`
- `report.json`
- `desktop-1440.png`
- `tablet-768.png`
- `mobile-375.png`

Compare mode additionally writes `compare.md`.

## Interpretation

| Score | Meaning |
|---:|---|
| 90–100 | sehr stark, likely presentable if no blockers |
| 80–89 | gut, minor corrections |
| 70–79 | brauchbar, improve before client preview |
| 55–69 | schwach, visible gaps |
| <55 | neu bauen / major rework |

Blockers override score. A visually strong site with a dead CTA is still not deliverable. Ein toter Button bleibt tot, auch mit schönerem Radius.

## Existing rule sources

This scorer intentionally consolidates rules from:

- `impeccable`: anti-slop, design bans, typography, layout, motion rules
- `local-business-rebuild`: local conversion, image strategy, contact fallback, thin-page trap, mobile CTA
- `web-quality-audit`: accessibility, performance, SEO, best practices framing
- `website-visual-scorer`: score/report model inspiration

## Current limitations

Current limitations:

- Lighthouse is optional via `--lighthouse` and reported separately; it does not yet alter the 100-point local-business score. V2.1 extracts performance causes from Lighthouse so low scores point to concrete assets/scripts instead of just saying “slow”.
- Vision is implemented as a `vision-brief.md` review pack, not as an automatic in-CLI model call. Run Hermes `vision_analyze` on screenshots when taste/AI-slop judgement matters.
- Linkcheck covers HTTP(S) URLs from the rendered page; `file://` targets skip HTTP link validation.
- Benchmark history is JSONL and append-only; it is good for trends and comparisons, not a full dashboard/database yet.
- Copy semantics are still mostly pattern-based.

Use it as a TÜV checklist and scorecard, not as a final creative director.
