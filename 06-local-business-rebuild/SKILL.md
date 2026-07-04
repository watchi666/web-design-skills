---
name: local-business-rebuild
description: >
  Build a stunning new website for a local business prospect. Adapted from the
  full website-rebuild skill but optimized for speculative local business pitches.
  10-phase process: brand extraction, SEO audit, design audit, rebuild plan, full
  build with design system, copy optimization, combined QA, pre-deploy checklist,
  and output to monorepo. Uses scraped content from prospect-scrape as input.
  No tools/calculator research phase. Single combined QA round.
  Triggers on: "rebuild prospect", "local business rebuild", "build prospect site",
  "rebuild their site", "prospect rebuild", or any request to build a new website
  for a qualified prospect.
---

# Local Business Website Rebuild

Build a world-class website for a local business prospect using their scraped content and branding. This is the core value delivery — the rebuilt site must be so dramatically better than their current one that the business owner immediately sees the value.

**Register:** This skill always targets **Brand register** (design IS the product — distinctiveness is the bar), never Product register (app UI/dashboards, where earned familiarity matters more than standing out). If a prospect request secretly asks for an internal tool instead of a marketing site, flag it — this skill's rules don't apply there.

## How This Skill Works

This is an adapted version of the full `/website-rebuild` skill, streamlined for speculative local business pitches:
- **Removed:** Phases 11a/11b (Competitive Tools Research & Build), Phase 12 (Tools QA)
- **Consolidated:** 3 QA rounds → 1 combined QA round
- **Kept full depth:** Rebuild plan, copy optimization, all design rules, accessibility
- **Output location:** `~/prospect-pipeline/sites/<slug>/` (monorepo structure)

## Input

Read from `~/prospect-pipeline/prospects/<slug>/`:
- `info.json` — business details, category, contact info
- `audit-report.md` — what's wrong with their current site
- `original-site/content-inventory.json` — all scraped content
- `original-site/assets/` — logo, images, etc.

## Standard Tech Stack — Static First

This skill has a default stack. Use it unless the task gives a concrete reason not to.

### Our Standard

```txt
Static HTML/CSS/JS
No framework
No Tailwind
No React
No build step
No package manager required
```

This is the default for local-business previews, handwerker one-pagers, speculative redesigns, and public preview links.

Why:
- fastest to build and verify
- easiest to deploy as a public preview
- no `node_modules` tax
- no framework lock-in
- easiest to hand off, mirror, zip, host, or convert later
- matches watchi's normal workflow: direct files, direct preview, direct deployment

### Target Matrix

| Final use case | Default technical target |
|---|---|
| Quick visual preview / prospect pitch | Static HTML/CSS/JS |
| Simple one-page local-business site | Static HTML/CSS/JS |
| Multi-page static website with reusable sections | Astro or another static site generator, only if component reuse justifies it |
| Config-driven template system | Astro/Next/static generator depending on the existing system |
| Final site must be editable by the client | WordPress/ACF or Elementor workflow, **exception only** |
| Existing WordPress site must be repaired/extended | Use the relevant WordPress skill, not this generic rebuild path |

### Hard Rules

- Do **not** add Astro, Tailwind, React, Next, animation libraries, or build tooling if plain HTML/CSS/JS is enough.
- Do **not** default to WordPress or Elementor. Watchi uses WordPress/Elementor only for exceptions.
- If the final platform is unknown, build a static preview first. Decide production implementation after visual approval.
- If a framework is chosen, write the reason in `REBUILD_PLAN.md` under **Technical Target**.
- If WordPress/Elementor is chosen, write why static output is insufficient and load the relevant WordPress workflow skill.

### Static HTML/CSS/JS Baseline

Recommended structure for the default path:

```txt
<slug>/
  index.html
  assets/
    css/
      style.css
    js/
      main.js
    images/
      ...
  README.md
```

Rules:
- Keep CSS custom properties at the top of `style.css`.
- Keep JS minimal and purposeful: mobile nav, FAQ accordion, form mailto fallback, lightbox if needed.
- Use local images only. No hotlinked Unsplash or source-site assets in production.
- Use semantic HTML, JSON-LD, proper meta tags, and accessible forms exactly as in the framework path.

### Optional Astro Path

Use Astro only when the project benefits from:
- multiple pages with shared layouts
- repeated section components
- image optimization through a build step
- sitemap generation through the deployment pipeline
- an existing Astro/static-site project structure

If Astro is used, keep the stack lean. Do not add React unless there is real interactive state that cannot be done with small vanilla JS.

### Font Loading

For static HTML/CSS:
- Prefer self-hosted font files when available.
- Google Fonts `<link>` is acceptable for previews.
- Avoid adding package-managed font dependencies unless a build system already exists.

For framework builds:
- Use the framework's existing font pattern, or `@fontsource` if already part of the project.


## Design Philosophy — MAKE IT UNFORGETTABLE

**The AI Slop Test:** Before you ship, ask yourself: "If someone saw this site and was told 'AI made this,' would they believe it immediately?" If yes, you have failed. Redesign until that answer is no.

Read the [design-rules.md](references/design-rules.md) for full specification. Below are NON-NEGOTIABLE rules with specific alternatives.

### Pre-Code Design Commitment (Phase 4.5 — MANDATORY)

Before writing ANY code, you must describe in plain English:
1. **Aesthetic direction** — Pick ONE: dark/moody editorial, warm/organic handcrafted, clean/luxury minimal, bold/industrial, retro-modern, or something unique to this business. Commit to it.
2. **For each homepage section** — describe: layout structure (NOT "3-column grid"), column ratios, visual devices (overlap? angle? texture? full-bleed?), and how it differs from the section above it.
3. **The "wow" moment** — every great site has one thing that makes you stop scrolling. What is it for this site? (parallax hero? animated counter? overlapping testimonial cards? a diagonal section break?)

Write this into `REBUILD_PLAN.md` before touching any implementation file (`index.html`, CSS, JS, Astro, or otherwise).

### Typography
- Beautiful, unique fonts from Google Fonts links or self-hosted font files. Use `@fontsource` only when a framework/build system was explicitly selected.
- **BANNED:** Inter, Roboto, Arial, Helvetica, system fonts, Space Grotesk, Lato, Open Sans, Source Sans Pro
- **INSTEAD:** Distinctive display font + refined body font. Fraunces, Playfair Display, Clash Display, Cabinet Grotesk, General Sans, Satoshi, Plus Jakarta Sans.
- Use the FULL typographic range: hero headings should be 56-80px at minimum, clamp()'d fluidly, but capped at a **96px ceiling** — after writing the clamp(), compute its maximum value in px and confirm it does not exceed 96px. A shipped build hit 118px with no ceiling check catching it; past 96px the page is shouting, not designing. Extreme weight contrast (300 vs 800, not 400 vs 600)
- Body minimum 16px, line-height 1.5-1.65
- `text-wrap: balance` on headings, `font-variant-numeric: tabular-nums` on numbers

### Color
- CSS custom properties for every color value in `global.css`
- 1-2 dominant colors + 1 sharp accent derived from their logo/branding
- **BANNED:** purple gradients on white, generic blue corporate, rainbow with no hierarchy, pure black (#000), pure white (#fff) as main backgrounds, cyan-on-dark "developer aesthetic", gradient text
- **INSTEAD:** Tint your neutrals toward the brand hue. Use `oklch()` or warm grays. Off-white backgrounds (like `#faf9f6`), near-black text (like `#1a1a2e`). Bold saturated accents that pop.
- Every combination must pass WCAG AA contrast (4.5:1 for body text, 3:1 for large text)

### Motion & Animation
- CSS animations and transitions only — no JavaScript animation libraries
- **BANNED:** bounce/elastic easing, `transition: all`, animating width/height/margins (layout thrashing), identical fadeInUp on every section
- **INSTEAD:** Exponential easing (`cubic-bezier(0.16, 1, 0.3, 1)` for ease-out-expo). Staggered reveals with `animation-delay`. One orchestrated page-load sequence. `transform` and `opacity` only (60fps).
- **MINIMUM 3 distinct animation types per site** — don't just use fadeInUp everywhere. Examples: slide-in-from-left, scale-reveal, clip-path-wipe, counter-count-up, text-split-reveal, parallax-subtle
- Scroll-triggered reveals required on at least 2 sections (use Intersection Observer in `assets/js/main.js` for the static standard)
- Respect `prefers-reduced-motion` with `@media` query
- Page transitions are optional. Do not add framework-specific transition systems unless the chosen target already supports them.

### Layout — BREAK THE GRID
- **BANNED:** Every section centered with `max-w-7xl mx-auto`. Identical 3-column card grids. Every section header as `text-center max-w-2xl mx-auto mb-14`. Uniform padding on every section. Everything wrapped in rounded cards with drop shadows.
- **INSTEAD:** Use these specific layout patterns (pick at least 4 different ones for the homepage):

  **Pattern A — Asymmetric Split:** `grid-cols-[2fr_1fr]` or `grid-cols-[1fr_2fr]` instead of equal columns. One side is a large image, the other is tight text. Not 50/50.

  **Pattern B — Full-Bleed Break:** At least one section must escape the container — a full-width image, a colored band that runs edge-to-edge, or a diagonal section divider using CSS `clip-path`.

  **Pattern C — Overlapping Elements:** Use negative margins (`-mt-16`, `-ml-8`) or `translate` to overlap images onto adjacent sections. Break the box model.

  **Pattern D — Bento/Masonry Grid:** Instead of equal cards, vary the sizes — one large featured card with smaller ones around it. `grid-row: span 2` on the featured item.

  **Pattern E — Staggered/Offset:** Alternate image-left/text-right and text-left/image-right between sections. Offset vertically so items don't align on a flat grid.

  **Pattern F — Pull Quote / Callout:** A testimonial or stat that breaks out of the text flow — oversized text, a different background, rotated slightly, or overlapping adjacent content.

- **RULE:** No two consecutive sections may share the same column structure or visual pattern.
- **VERIFY:** if template logic computes a variant class name (e.g. a `wide`/`tall`/`featured` modifier based on index, as in Pattern D), grep the stylesheet before considering the section done to confirm a matching CSS rule actually exists. A shipped build had exactly this gap — the markup computed the variant classes correctly, nothing in `global.css` styled them, and the grid rendered completely uniform despite looking varied in the source.

### Additional Generic-Tell Rules (do not duplicate the rules above — these close specific gaps)
- **BANNED:** the rounded-square icon tile sitting above every heading — the single most common generic-AI tell, more diagnostic than any individual color or font choice
- **BANNED:** cards nested inside cards
- **BANNED:** a decorative two-axis CSS grid overlay (hairline gradient lines in a repeating background) used as generic filler on a surface that isn't an actual canvas, map, blueprint, or measurement tool. This is different from the intentional background patterns already allowed above (dots, topographic lines, etc.) — the test is whether the pattern is doing real work for this specific business or just filling empty space because nothing else was designed.
- **BANNED:** gray body text sitting directly on a saturated/colored section background (distinct from the text-over-image contrast rule above — this applies to solid color fills, not photographs). Tint the text toward the section's own palette instead.
- **BANNED:** a small uppercase tracked label ("eyebrow"/kicker) placed above the heading of every section. One deliberate label used once as a genuine brand device is voice; the same treatment repeated on a third, fourth, fifth section is the tell — verified in a real shipped build where this pattern reached 11 of 11 sections despite the rules above already being in place. If you catch yourself adding the same label pattern a third time, stop and vary the section's hierarchy some other way instead.
- **BANNED:** a colored `border-left`/`border-right` stripe as a card or list-item accent, including on numbered process steps. Give a numeral visual weight through large/distinctive typography instead of a side border.
- **BANNED:** sequential numbering (01/02/03…) applied to a list that has no real order — a services list, a feature grid. Numbers earn their place only on a genuine sequence (an actual multi-step process, a timeline) where the order itself carries information.

### Visual Depth & Atmosphere
- **BANNED:** Flat solid-color section backgrounds with nothing else. Plain white sections. Sections that are just text floating in space.
- **INSTEAD:** Add visual depth with AT LEAST 2 of these per site:
  - Subtle noise/grain texture overlay (CSS `background-image: url("data:image/svg+xml,...")` or a tiny PNG)
  - Gradient meshes or radial gradients for section backgrounds
  - Decorative SVG shapes (blobs, waves, diagonal lines) as section dividers or background elements
  - `box-shadow` with colored tints (not just gray)
  - Subtle background patterns (dots, lines, topographic maps for outdoor businesses)
  - Layered z-index compositions where elements overlap intentionally

### Section Architecture
- **MANDATORY:** Each visually distinct homepage section must be designed as its own conceptual component, even in static HTML.
- In the static standard, separate sections clearly with semantic HTML, comments, and dedicated CSS blocks: Hero, Trust, Services, About, Process, Projects, Testimonials, FAQ, CTA, Footer.
- Do not copy-paste one card/grid pattern across the whole page. Each section needs its own layout logic.
- If a framework target is explicitly chosen, map these same conceptual sections to real components in that framework.

## Optional Intensity Pass — Bolder / Overdrive

Run this between Phase 5 (Build) and Phase 7 (QA) only when the brief explicitly asks for something beyond the standard bar above (e.g. "atemberaubend", "blow people away", "unlike anything else in the industry"). Two distinct modes — pick based on what's actually missing:

- **Bolder:** the design system in DESIGN.md is right but was executed timidly. Push harder on hierarchy, proportion, density, and copy tone using the *existing* palette/typography/components — don't introduce new colors or effects, just commit harder to what's already chosen.
- **Overdrive:** the brief wants technically ambitious effects the current design system doesn't have room for (WebGL/shader accents, cursor-reactive elements, elaborate scroll choreography beyond the "MINIMUM 3 distinct animation types" rule above). If DESIGN.md genuinely can't express the direction being asked for, say so and name the exact additions before building them — don't silently bolt effects onto a system that wasn't built for them.

Either mode: Lighthouse 95+ and WCAG AA from the rules above are not negotiable trade-offs for boldness. An effect that tanks performance or breaks contrast failed this pass regardless of how impressive it looks in isolation.

## The 10 Phases

Execute in strict order. Each phase matters.

### Phase 1: Brand Extraction
Using the scraped content and assets:
- Analyze the logo (already downloaded to `original-site/assets/`)
- Extract brand colors, typography personality, visual tone
- Determine light/dark background compatibility
**Output:** Internal notes (not a separate file — fold into REBUILD_PLAN.md)

### Phase 2: SEO Audit
Using the scraped meta data and the audit report:
- Document all SEO issues found
- Identify target keywords for this business + location (e.g., "plumber Coeur d'Alene")
- Plan local SEO strategy (Google Business schema, NAP consistency, local keywords)
- Plan content strategy (what pages need what keywords)
**Output:** SEO section of REBUILD_PLAN.md

### Phase 3: Design & UX Audit
Using the audit report already created by `/prospect-audit`:
- Summarize what's wrong with their current design
- Identify specific improvements we'll make
- Set the aesthetic direction (what style fits THIS business?)
**Output:** Design section of REBUILD_PLAN.md

### Phase 4: Rebuild Plan
Synthesize everything into a concrete plan:
1. **Site Architecture** — page list, nav structure, URL structure
2. **Design Direction** — color palette (with WCAG contrast pre-verified), typography selections, component style, overall personality
3. **Image Strategy** — KEEP / ENHANCE / REPLACE for each image
4. **Copy Strategy** — what to rewrite vs. keep, tone of voice, key messages
5. **SEO Plan** — target keywords per page, schema types, meta strategy
6. **Technical Plan** — any special integrations, forms, maps
7. **Accessibility Targets** — zero critical/serious violations
8. **IMAGE DOWNLOAD** — Follow the Image Selection Protocol below, then download ALL images before writing any component code.

   ```bash
   mkdir -p ~/prospect-pipeline/sites/<slug>/assets/images
   # Download each planned image:
   curl -sL -o ~/prospect-pipeline/sites/<slug>/assets/images/hero.jpg "https://images.unsplash.com/photo-XXXXX?auto=format&fit=crop&w=2400&q=85"
   ```

   Verify images exist before proceeding:
   ```bash
   ls -la ~/prospect-pipeline/sites/<slug>/assets/images/
   ```
   The directory must contain at minimum: hero image, about section image, at least one services image. If it doesn't, download them now.

**Generate DESIGN.md:** After defining the design direction above, formalize it as a structured `DESIGN.md` file using the `design-system-generator` skill.

1. Read `00-design-references/design-md-format.md` for the 9-section format.
2. Select 1-2 reference DESIGN.md files from `00-design-references/references/by-aesthetic/` matching the aesthetic direction.
3. Generate `DESIGN.md` with all 9 sections. The plain English design commitment becomes Section 1 (Visual Theme) and Section 7 (Do's and Don'ts).
4. REBUILD_PLAN.md Design Direction section should say "See DESIGN.md for the complete design system."

**Optional Shape Probes (when no clear direction from user):**
If the user has not given a specific aesthetic direction (no mood, no reference site, no "make it dark"), build 3 lightweight HTML probes before committing to DESIGN.md. Each probe is a single viewport (1440×900) showing one structural direction with real business photos:

- **Probe A:** Warm/local — editorial, generous whitespace, Playfair-style serif
- **Probe B:** Premium/minimal — dark, sparse, large type, magazine feel
- **Probe C:** Conversion-first — split layouts, prominent phone CTA, dense proof

Use the existing scraped photos, not stock. Each probe needs: headline + subline + 2 images + CTA. Present all three to the user and wait for explicit confirmation before building DESIGN.md.

**Why this matters:** Building without a confirmed direction produces template-like results — identical grids recolored. The probe phase prevents "Das ist nur ein Grundentwurf" rejections. See `references/canvas-direction-probes.md` from `impeccable` for the HTML pattern.

**Output:** `DESIGN.md` in the project root (alongside REBUILD_PLAN.md)

**Output:** `~/prospect-pipeline/prospects/<slug>/REBUILD_PLAN.md`

### Phase 5: Build the Website

**Read the project's `DESIGN.md` before writing any code.** Translate Section 2 into CSS custom properties, Section 3 into font loading and typographic scale, Section 4 into reusable HTML/CSS component patterns, and Section 6 into shadow tokens. The DESIGN.md is the source of truth for all visual implementation.

Before writing code, confirm images are downloaded in the selected static asset directory (`assets/images/` for the standard path). If images are missing, go back to Phase 4 step 8.

Build in `~/prospect-pipeline/sites/<slug>/` using the standard static stack unless a different target was explicitly justified in `REBUILD_PLAN.md`:

1. **Create the static project structure:**
   ```txt
   <slug>/
     index.html
     assets/
       css/style.css
       js/main.js
       images/
     README.md
   ```

2. **Set up the design system in CSS:**
   - Create `assets/css/style.css`
   - Put all CSS custom properties at the top: colors, type scale, spacing, radius, shadows
   - Add WCAG contrast ratios as comments next to critical color pairings
   - Load fonts via `<link>` in `index.html` for previews, or self-host font files when available
   - Do not use Tailwind unless the existing project already uses it and the user wants to keep it

3. **Build the HTML shell (`index.html`):**
   - Semantic `<html>`, `<head>`, `<main>`, `<section>`, `<footer>` structure
   - Unique `<title>` and `<meta name="description">`
   - Open Graph tags
   - JSON-LD structured data
   - Skip-to-content link as first focusable element
   - Header/nav, main content, footer
   - Local stylesheet and script references only

4. **Build the homepage as real sections:**
   - Make the page dramatically better than what the business currently has
   - Every hero section must use a real photograph, not CSS gradients
   - Use local image paths like `assets/images/hero.jpg`
   - Keep each visually distinct homepage section separated with clear comments in HTML/CSS
   - If the page grows large, split repeated or complex behavior into small JS functions in `assets/js/main.js`

   **CRITICAL — TEXT CONTRAST OVER IMAGES (this fails QA constantly):**
   When placing text over a hero image or any photograph:
   - ALWAYS add a dark overlay behind light text: `bg-black/50` or `bg-gradient-to-t from-black/70 to-black/30`
   - ALWAYS add a light overlay behind dark text: `bg-white/60` or similar
   - NEVER put white text directly on a light/bright photograph
   - NEVER put dark text directly on a dark photograph
   - Test mentally: if the image is mostly sky/clouds (bright), you need a dark overlay. If the image is mostly shadows/dark, you need the overlay even more for white text.
   - The overlay must cover the ENTIRE text area, not just a thin strip
   - Add `text-shadow: 0 2px 4px rgba(0,0,0,0.5)` as extra insurance on hero headings
   - Example pattern:
     ```html
     <section class="relative h-[80vh]">
       <img src="/images/hero.jpg" class="absolute inset-0 w-full h-full object-cover" />
       <div class="absolute inset-0 bg-black/50"></div> <!-- dark overlay -->
       <div class="relative z-10 flex items-center h-full">
         <h1 class="text-white text-5xl font-bold" style="text-shadow: 0 2px 4px rgba(0,0,0,0.5)">
           Heading Text Here
         </h1>
       </div>
     </section>
     ```
   This applies to EVERY section where text sits on top of a photograph — hero, about, testimonials, CTA banners, page headers. No exceptions.

   **CRITICAL — CONTACT FORM RELIABILITY (this silently fails in production):**
   If the site has no backend, a form using a bare `mailto:` action (`enctype="text/plain"`) is not enough by itself — many browsers block or silently no-op a form-triggered mailto handoff, leaving the visitor with zero indication anything happened.
   - Compose the `mailto:` link yourself in a small submit handler (subject + body built from the field values) and set `window.location.href` to it
   - Always show a visible status message after submit confirming what should happen next
   - Always include a manual fallback: a direct `mailto:` link with the same prefilled subject/body, plus a copy-to-clipboard option
   - Mirror the original site's contact facts when the user asks to "übernehmen": phone, email, street address, postal code/city, opening hours, form fields, service checkboxes, timing/area dropdowns. Do not silently keep stale contact facts from a draft.
   - Add a real WhatsApp CTA for German local-service sites when requested or when the original site already uses one: `https://wa.me/<countrycode-number>?text=<encoded message>`. Use the same verified phone number as the `tel:` link.
   - Never ship a contact form whose only feedback mechanism is "hope the browser handles it"

   See `references/local-business-contact-and-case-study-cta.md` for a compact static-site pattern covering source-parity contact sections, WhatsApp links, and animated case-study CTAs.

5. **Conversion Patterns — Apply at least 3 of 5:**
   The site must convert, not just look good. Read `references/conversion-patterns.md` for copy-paste code. Before shipping, verify at least 3 of these patterns are present:
   - **Trust Bar** with verified data metrics (years, projects, rating, response time) — immediately after hero
   - **Floating CTA** on mobile — `tel:` link, fixed bottom-right, 60×60px, hidden on desktop
   - **FAQ Accordion** — ≤7 real questions, answers ≤3 sentences, catches objections before contact
   - **Lead Magnet** — email capture with specific offer (checklist, guide), only if a real backend exists (Resend, Make, Zapier, n8n)
   - **JSON-LD with `aggregateRating`** — real Google review stars in search results, with `areaServed` for local SEO

   Do NOT invent metrics. Do NOT build a lead magnet without a backend. Fake trust destroys more than no trust at all. See `references/conversion-patterns.md`.

6. **Thin Page Trap — ELIMINATE before QA:**
   A service-business page with only a hero and a few cards is NOT enough. The visitor needs substance to trust and act. Minimum content structure:
   1. **Problem** — What pain does the visitor have?
   2. **Mechanism** — How do you solve it? (process, materials, guarantee)
   3. **Proof** — Why believe you? (projects, reviews, certifications)
   4. **Process** — What happens after contact? (reduces fear)
   5. **Objections** — FAQ answers "too expensive", "too slow", "not my area"
   6. **Examples** — Real projects with real photos
   7. **Final CTA** — One last chance to act

   **Check:** Does the homepage scroll for at least **2 full desktop viewports**? If not, it's too thin. Add content before QA. See `references/copy-discipline.md` for the thin page trap details.

7. **Images — every site MUST have at minimum:**
   - A hero image (full-width, atmospheric, sets the mood)
   - An about/story section image (business, workspace, or team)
   - Team photos (actual photos from their site)
   - At least 1 image per major content section
   - Reference by path: `assets/images/filename.jpg` for the static standard, or use the chosen framework's asset path if a framework target was explicitly selected
   - **A site with no images or only CSS gradients is UNACCEPTABLE and will not pass QA.**

8. **Comprehensive SEO:**
   - Unique `<title>` and `<meta name="description">`
   - Open Graph and Twitter Card meta tags
   - JSON-LD schema (LocalBusiness type) with NAP data, `areaServed`, and real `aggregateRating` if verified reviews exist
   - `robots.txt` when the preview is intended for indexing; `noindex` when it is only a private/prospect preview
   - Canonical URL if a final public URL exists
   - Semantic HTML throughout (proper heading hierarchy, landmarks, etc.)

9. **Generate favicon** — complete set from logo (at minimum: `favicon.svg` or `favicon.png` in project root/assets)

10. **Accessibility:**
   - WCAG AA contrast on all text/background combinations
   - **ESPECIALLY check text-over-image contrast** — every hero, banner, and CTA section with a background photo MUST have an overlay (dark overlay for light text, light overlay for dark text). This is the #1 accessibility failure in builds.
   - Full keyboard navigation with visible focus indicators
   - ARIA labels only where native HTML semantics are insufficient
   - `prefers-reduced-motion` media query wrapping all animations
   - Skip-to-main-content link

11. **Performance — static-first:**
   - No build step required for the standard path
   - Keep JavaScript small and purposeful
   - Optimize images manually: correct dimensions, WebP/AVIF when useful, no huge originals in hero
   - Use `loading="lazy"` for below-the-fold images and explicit `width`/`height` or `aspect-ratio` to prevent layout shift
   - Self-host critical assets when possible
   - Target: Lighthouse 95+

### Image Selection Protocol

**Why this matters:** Grabbing the first stock photo that matches a keyword produces embarrassing results. A salon site showed a man's beard trim when the business only serves women. An about page had a bright white interior for a brand with a dark industrial aesthetic. Think carefully about every image.

This protocol applies to ALL business types. It is not industry-specific.

**Step 1: Understand the business deeply before picking any images**

Read the scraped content, services, team info, Google reviews, and audit. Build a mental model:
- What does this business actually DO day-to-day?
- Who are their customers? (homeowners, other businesses, families, professionals, etc.)
- What's the vibe? (premium/luxury, blue-collar/hardworking, family-friendly, clinical/professional, creative/artistic)
- What does their physical space look like?
- What would the business owner be PROUD to show on their website?

A plumber's site needs clean professional work, neat uniforms, a friendly technician at a door. NOT a wrench on a white background.
An accountant's site needs trust and expertise. NOT a "handshake in front of a skyline" stock photo.
A construction company needs real job sites, finished projects, crews at work. NOT clip art of hard hats.

**Step 2: Read the copy, then choose the image**

For every image slot, read the surrounding copy FIRST. The image must reinforce what the text communicates.

- "Family-owned for 20 years" → warm, personal, team or workspace with character
- "Precision and attention to detail" → close-up work that demonstrates craft
- Customer service section → people interacting, not just equipment
- Testimonial → evoke the feeling the testimonial describes
- Contact section → use a welcoming, trust-building finished-room/work-quality image. Avoid toilets/WCs, demolition mess, exposed plumbing, or any image that makes the visitor think about unpleasant work at the exact moment they are deciding whether to call.

DO NOT just search Unsplash for the business category and grab the first result.

**Step 3: Use their actual photos first, stock as last resort**

Priority order:
1. **Their actual team photos** (from `original-site/assets/team/`). ALWAYS use for team/about sections. Never use stock people for named real humans.
2. **Their actual work/project photos** (from scrape). Office, shop, finished projects — use these.
3. **Their Google Maps/Business photos** (downloadable via Apify). Interior, exterior shots.
4. **Carefully chosen stock photos** for supporting/decorative slots ONLY.

**Dedupe by content, not filename:** when building the image manifest from downloaded/scraped assets, watch for the same source photo saved at multiple resolutions or crops (common with scraped asset pipelines) — these must resolve to ONE gallery entry, not two. A shipped gallery had the identical photo appear twice for exactly this reason.

**Step 4: Visual verification for every stock image**

Before using a stock image, verify:
- Does the color temperature match the design system?
- Does it feel like the same geographic/cultural context? (Manhattan skyline behind a rural Idaho plumber is absurd)
- Could the business owner say "yeah, that looks like us"?
- Right demographic for the actual customer base?
- Does it match the section's copy, not just vaguely "related to the industry"?

If you can't answer yes to all, pick a different image. A well-chosen abstract/texture is better than a wrong specific photo.

**Step 5: Create an image plan before building**

```
IMAGE PLAN for [Business Name]:
- Hero: [description, source, why it fits]
- About section: [description, source, why it fits]
- Team - [Name]: [their actual photo from assets/team/]
- Services section: [description, source, why it fits]
- Contact section: [description, source, why it fits]
```

**Step 6: Cross-check during QA**

During QA, visually inspect every page using `preview_screenshot`:
- Do team photos show the ACTUAL team members?
- Do images match the brand's color palette and mood?
- Would the business owner feel represented?
- Is there any image that feels "off" or generic? Replace it.
- Run the broken image check (see QA section).

**Visual quality bar:** This must look like a $20,000+ agency build. Stunning. Not good — stunning.

### Phase 6: Copy Audit & Optimization
Read through every word on the rebuilt site:
- Value prop clear within 5 seconds of landing?
- Every page has a benefit-driven CTA visible without scrolling?
- Lead with benefits, address objections, use specific numbers
- 8th-grade reading level, short sentences for impact
- Testimonials placed near CTAs and decision points
- Hook → Problem → Solution → Proof → Action flow
- **Reviews/testimonials are evidence, not decoration:** before writing generic testimonial copy, inspect the original site and public Google/Maps links for real reviews. If real Google reviews are visible, use the exact visible reviewer names, star ratings, review texts, aggregate rating, review count, and source link. Do not invent, paraphrase as if quoted, or silently downgrade them to vague “Kundenstimme” cards.

**Copy Discipline — Apply these rules (see `references/copy-discipline.md` for full details):**
- **No em dashes (`—`).** Use commas, colons, semicolons, periods instead.
- **No restated headings.** If a paragraph restates the heading, delete the paragraph.
- **No generic openers.** Ban: "Willkommen auf unserer Website", "Qualität steht bei uns an erster Stelle", "Kundenzufriedenheit ist unser oberstes Ziel", "Zögern Sie nicht, uns zu kontaktieren".
- **Active voice, specific numbers, local anchoring.** „247 Projekte in Düsseldorf seit 2020" beats „Viele zufriedene Kunden".
- **CTA copy must be direct.** "Jetzt anrufen: 0152 34 34 6248" beats "Kontaktieren Sie uns".
- **Read every heading out loud.** If it could be on any competitor's site, rewrite it.

Wordsmith every paragraph. This is a sales tool.

**Output:** `~/prospect-pipeline/prospects/<slug>/COPY_CHANGES.md`

### Phase 7: Combined QA
Single comprehensive pass covering functional, copy, and visual:

1. Run the site locally with `preview_start`
2. **Screenshot protocol — Capture ALL viewports:**
   - First viewport (hero) at 1440px — must show H1, subline, CTA without scrolling
   - Full page at 1440px — every section visible, footer visible
   - Mobile at 375px — text readable, tap targets visible, floating CTA if applicable
   - Tablet at 768px — layout intact, nav accessible
   Verify each output file exists and is > 0 bytes. See `references/screenshot-verification.md` for the full protocol.
3. **Images check (do this first):**
   - Count images on homepage: `document.querySelectorAll('img').length`
   - If fewer than 3 images, go back to Phase 5 and add images before continuing.
   - Verify: hero has a real photograph, about section has a real photograph, services has images.
   - If any are missing, download appropriate images and add them.
4. **Hero Clarity Check:**
   - Is the photo visible (not buried under overlay)? Overlay must not be darker than `rgba(0,0,0,0.5)` for light text.
   - Is text readable without squinting? White text on bright sky = fail.
   - Is CTA obvious within 3 seconds?
   - Does text overlap the busiest part of the photo?
   If any answer is no, fix before continuing. See `references/screenshot-verification.md`.
5. **Functional:** Check every link, image, form, interactive element at mobile + desktop
6. **Copy:** Read as a first-time customer. Understand in 5 seconds? Trust? Know what to do?
7. **Visual:** Animations smooth? Typography beautiful? Whitespace balanced? Colors cohesive? Photos beautiful and relevant?
8. **Accessibility:** Verify contrast ratios on all color pairings. Keyboard nav works. Focus indicators visible.
9. **Design system consistency:** Verify that every CSS custom property in `global.css` maps to a token defined in DESIGN.md. No orphan tokens, no missing tokens.
   - **Subpage/project-page consistency:** When a homepage redesign includes project pages, galleries, case studies, or other secondary pages, inspect those pages as first-class deliverables. They must inherit the homepage's typography, accent colors, button shapes, header/logo treatment, light/dark section rhythm, and footer style. Do not leave older prototype styling (different fonts like Bebas/Barlow, different accent colors like bright orange, square buttons, or foreign section backgrounds) on a subpage just because the homepage is polished.
   - **Case-study discovery paths:** Project/case-study pages only help conversion if visitors find them. Make every important project path obvious from the hero, the projects section, and the footer. Prefer a tasteful animated CTA (subtle float, sheen, arrow nudge; transform/opacity only, reduced-motion safe) plus a clickable project card over a quiet text link. Keep it premium, not carnival.
   - Mechanical check for static subpages: search the subpage source for obsolete fonts and colors from the previous design, verify computed `body`/heading font families and CTA accent color in the browser, then test galleries/lightboxes/filters after restyling.
10. Compare to best in industry — it should be BETTER.
11. **Harden pass (edge cases the happy path misses):**
    - Long business/owner names don't break the header or nav
    - Long address / opening-hours strings don't overflow their container
    - Empty or partially-filled form states look intentional, not broken
    - Every CTA has a real destination — no dead links left over from placeholder content
    - Re-check mobile viewport *after* any Bolder/Overdrive pass, not just after the initial build — effects that work at desktop width often break or lag on mobile
12. **Mobile-First pass — MANDATORY:**
    - Touch targets ≥ 44px (preferably 48px) for all interactive elements
    - Body text minimum 16px everywhere (prevents iOS auto-zoom)
    - Phone number visible in first viewport without scrolling
    - Floating CTA visible on mobile (if pattern is used)
    - No horizontal scroll at 375px
    - Can the primary goal (call / form / WhatsApp) be completed with one hand?
    - Hamburger menu opens, closes, links work
    Actually scroll the mobile viewport, not just load at scroll position 0. See `references/mobile-first-checklist.md`.
13. **Conversion-Patterns Check:**
    - Trust Bar present with verified data? If not, is there a good reason?
    - Floating CTA on mobile? If not, is phone prominently reachable?
    - FAQ Accordion present? Does it answer real objections?
    - Lead magnet only if backend exists — otherwise skip and note why.
    - JSON-LD with `aggregateRating` (if real reviews) + `areaServed`
    At least 3 of 5 patterns must be present. If fewer, add them or document why not.
14. **Mechanical self-checks (don't rely on memory of the rules above while deep in implementation):**
    - Count `@keyframes` blocks plus distinct IntersectionObserver-driven reveal patterns in the shipped code. Fewer than 3 distinct techniques means the "MINIMUM 3 distinct animation types" rule wasn't actually met — go back and add more.
    - Compute every heading `clamp()`'s maximum value in px. Any hero/section heading exceeding 96px → reduce the clamp max or rewrite the copy.
    - Grep the stylesheet for every variant class referenced in template logic (`wide`, `tall`, `featured`, etc.) — a class with no matching rule means the intended layout variety isn't actually rendering.
    - Actually scroll each page at the mobile viewport, not just load it at scroll position 0 — a header/nav that switches `position` at a breakpoint can lose persistent access to calls/navigation while scrolling, which checking the page only at the top will never reveal.

### Broken Image Check
Before deploying, verify ALL images load:
1. Start dev server with `preview_start`
2. Visit every page (/, /about, /services, /contact)
3. On each page, run via `preview_eval`:
   ```js
   Array.from(document.querySelectorAll('img')).filter(img => !img.complete || img.naturalWidth === 0).map(img => img.src)
   ```
4. If ANY broken images:
   - External URL → download locally to `assets/images/` and update src
   - Wrong path → fix the path
   - Corrupted → re-download
5. **NEVER hotlink images from the prospect's original website.** Always use local files.
6. **NEVER leave external Unsplash URLs in production.** Download all stock images locally.
7. Re-run check after fixes. Do not deploy until all images load.

**Output:** `~/prospect-pipeline/prospects/<slug>/QA_REPORT.md`

### Phase 8: Pre-Deploy Checklist
Verify every item:
- SEO: unique meta tags, OG tags, structured data, heading hierarchy, alt text, sitemap, robots.txt
- Performance: images optimized, fonts self-hosted, all pages static. Lighthouse 95+.
- Accessibility: all colors WCAG AA. Keyboard nav works. Focus indicators visible.
- Functionality: every link, form, element works. Mobile nav works. 404 page works.
- Content: no placeholders, no typos, all business info accurate.
- Visual: design consistent, polished at every viewport. No banned fonts.

### Phase 9: Prepare for Deployment
The site is built in `~/prospect-pipeline/sites/<slug>/`. Prepare it:
- Verify the static asset paths are relative and portable (`assets/...`, not local machine paths)
- Create a `README.md` with project overview and how to run locally (`python3 -m http.server`, PHP server, or chosen preview command)
- If the site is static, no package install/build should be required unless explicitly justified
- Mark the prospect `status` as `"built"` in `info.json`
- Add `built_date` to `info.json`
- Update `pipeline.json`: increment `total_built`

Note: Actual deployment to GitHub/Vercel/static hosting is handled separately. The standard output is a portable static folder.

### Phase 10: Summary
Output what was built:
```
LOCAL BUSINESS REBUILD COMPLETE
================================
Business:      Bob's Plumbing
Location:      Coeur d'Alene, Idaho
Category:      Plumber
Site Path:     ~/prospect-pipeline/sites/bobs-plumbing/
Framework:     Static HTML/CSS/JS (standard)
Pages Built:   7 (Home, About, Services, Gallery, Contact, FAQ, 404)
Design:        [brief description of aesthetic]
Key Features:  [list notable features]
SEO:           LocalBusiness schema, 8 target keywords, sitemap
Accessibility: WCAG AA, all contrast verified
Performance:   Static HTML, zero JS, Lighthouse 95+
Ready for:     /prospect-deploy
```

## Reference Files

This skill inherits the design philosophy from the main website-rebuild skill:
- [design-rules.md](references/design-rules.md) — Non-negotiable design rules
- [accessibility-spec.md](references/accessibility-spec.md) — WCAG AA specification

Hermes/WebsiteUpgrade adaptations that must be checked before a non-standard run:
- [hermes-direct-url-workdir-adaptation.md](references/hermes-direct-url-workdir-adaptation.md) — use when the user gives a live URL plus explicit workdir instead of the prospect-pipeline layout.
- [direct-url-github-deploy-lessons.md](references/direct-url-github-deploy-lessons.md) — safe direct URL → local rebuild → GitHub/Vercel pattern; avoid committing raw mirrored builder HTML.
- [local-business-contact-and-case-study-cta.md](references/local-business-contact-and-case-study-cta.md) — static contact-section parity, WhatsApp CTA, mailto form handler, and project/case-study discovery CTA pattern.
- [hero-file-in-full-redesign.md](references/hero-file-in-full-redesign.md) — supplied hero/prototype is the opening section, not the whole site.
- [hero-animation-redesign-integration.md](references/hero-animation-redesign-integration.md) — preserve hero animation mechanics, but restyle logo, typography, colors, copy, CTAs, overlay, and photo grading to match the final site.
- [typography-local-business.md](references/typography-local-business.md) — local craft/trades typography corrections when a design feels generic or like AI slop.

New reference files (added 2026-07-04):
- [conversion-patterns.md](references/conversion-patterns.md) — 5 conversion patterns with copy-paste code: Trust Bar, Floating CTA, FAQ Accordion, Lead Magnet, JSON-LD with reviews
- [mobile-first-checklist.md](references/mobile-first-checklist.md) — touch targets, typography, navigation, form, and performance checks for mobile-first builds
- [copy-discipline.md](references/copy-discipline.md) — banned patterns (em dashes, generic openers), thin page trap, writing rules, CTA copy
- [screenshot-verification.md](references/screenshot-verification.md) — capture protocol, hero clarity check, viewport verification, production URL check

## Key Differences from Full Website Rebuild

| Aspect | Full Rebuild | Local Business Rebuild |
|--------|-------------|----------------------|
| Phases | 14 | 10 |
| Tools research | Deep competitive research | None |
| Tools build | 2-5 interactive tools | None |
| QA rounds | 3 separate rounds | 1 combined round |
| Copy audit | Full | Full (kept per user request) |
| Rebuild plan | Full documentation | Full (kept per user request) |
| Deploy | Individual GitHub repo + Vercel | Monorepo path only (deploy is separate skill) |
| Input | URL provided by user | Scraped content from prospect-scrape |
| Output | Live deployed site | Built site in monorepo, ready for deploy |

## Credit

The Register check, the additional generic-tell rules (icon-tile, nested cards, decorative grid overlay, gray-on-color), and the optional Bolder/Overdrive intensity pass were adapted from the design-quality concepts in [`pbakaus/impeccable`](https://github.com/pbakaus/impeccable).

The eyebrow-kicker/side-stripe/numbered-scaffolding bans, the 96px heading ceiling, the variant-class verification step, the mailto form reliability callout, the image-dedupe note, and the Phase 7 mechanical self-checks were added after real defects were found in two sites shipped with this skill lineage (2026-07-04) — not ported from another skill, but field-tested against actual output.
