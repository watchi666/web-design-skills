# Conversion Patterns — Local Business

Proven UI/UX patterns that increase conversions, trust, and lead capture on small service-business websites. Use these as copy-paste building blocks. Do not reinvent them.

---

## Pattern 1: Trust Bar with Data Metrics

**Where:** Immediately after hero. First thing the eye hits after the headline.

**What it does:** Replaces empty space with credibility numbers. Four items max — more is noise.

```html
<div class="trust-strip" aria-label="Leistungsversprechen">
  <div class="trust-item" data-metric="20+">
    <strong>Jahre Erfahrung</strong>
    <p>Saubere Handschrift in jedem Projekt seit 2005.</p>
  </div>
  <div class="trust-item" data-metric="400+">
    <strong>Fertige Projekte</strong>
    <p>Zufriedene Kunden und hochwertige Sanierungen.</p>
  </div>
  <div class="trust-item" data-metric="4.9★">
    <strong>Kundenbewertung</strong>
    <p>28 Bewertungen auf Google – durchschnittlich 4.9/5.</p>
  </div>
  <div class="trust-item" data-metric="48h">
    <strong>Respons-Zeit</strong>
    <p>Neue Anfragen werden innerhalb von 48h bearbeitet.</p>
  </div>
</div>
```

```css
.trust-strip {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 2rem;
  padding: 3rem 0;
}

.trust-item::before {
  content: attr(data-metric);
  display: block;
  font-size: 2.2rem;
  font-weight: 800;
  font-variant-numeric: tabular-nums;
  color: var(--accent);
  margin-bottom: 8px;
}

@media (max-width: 1080px) { .trust-strip { grid-template-columns: repeat(2, 1fr); } }
@media (max-width: 720px)  { .trust-strip { grid-template-columns: 1fr; } }
```

**Rules:**
- Only use **verified data**. Real Google review count, real project tally.
- If the business has <3 years experience, use a different metric (e.g. „Ausbildung + Meisterprüfung").
- Never invent numbers. Fake metrics destroy trust faster than no metrics.

---

## Pattern 2: Floating CTA — Mobile Click-to-Call

**Where:** Fixed bottom-right, mobile only.

**What it does:** The phone is always one tap away. No scrolling, no searching.

```html
<a href="tel:+49-152-3434-6248" class="floating-cta" title="Sydney Sanierung anrufen" aria-label="Anrufen">
  <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
    <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"/>
  </svg>
</a>
```

```css
.floating-cta {
  display: none;
  position: fixed;
  bottom: 24px;
  right: 24px;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: var(--accent);
  color: var(--bg);
  align-items: center;
  justify-content: center;
  z-index: 40;
  box-shadow: 0 8px 24px rgba(0,0,0,0.3);
}

@media (max-width: 720px) {
  .floating-cta { display: flex; }
}
```

**Rules:**
- Minimum 60×60px tap target. Google says 48px; we say 60px for accidental-tap safety.
- Always use `tel:` with **country code** (`+49-...` not `02054/...`).
- Hide strictly on desktop. This is a mobile-only pattern.

---

## Pattern 3: FAQ Accordion (Accessible)

**Where:** Before contact section. Catches objections before the visitor decides not to call.

**What it does:** Keeps the page compact while answering the 5-7 questions that stop people from contacting.

```html
<div class="faq-list" role="region" aria-label="Häufig gestellte Fragen">
  <details class="faq-item">
    <summary class="faq-trigger">
      <span>Wie lange dauert eine durchschnittliche Sanierung?</span>
    </summary>
    <div class="faq-content">
      <p>Je nach Umfang zwischen 2 und 6 Wochen. Wir erstellen vor Projektbeginn einen detaillierten Zeitplan.</p>
    </div>
  </details>
</div>
```

```css
.faq-item {
  border-bottom: 1px solid rgba(255,255,255,0.08);
}

.faq-trigger {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.2rem 0;
  cursor: pointer;
  list-style: none;
  font-weight: 500;
}

.faq-trigger::-webkit-details-marker { display: none; }

.faq-trigger::after {
  content: "✚";
  transition: transform 0.25s ease;
  font-size: 0.9rem;
}

.faq-item[open] .faq-trigger::after { transform: rotate(45deg); }

.faq-content {
  padding-bottom: 1.2rem;
  color: var(--muted);
  line-height: 1.65;
  animation: faqOpen 0.3s ease;
}

@keyframes faqOpen {
  from { opacity: 0; transform: translateY(-6px); }
  to   { opacity: 1; transform: translateY(0); }
}
```

**Rules:**
- Max 7 questions. More = nobody reads them.
- Answers max 2-3 sentences. Long answers = skipped.
- Questions must be **real** — not marketing-speak. „Warum sollte ich ausgerechnet Sie wählen?" is fake. „Sind Sie auch in [Stadtteil] tätig?" is real.

---

## Pattern 4: Lead Magnet Section

**Where:** After services, before FAQ. The visitor has seen what you do — now give them a reason to stay in touch.

**What it does:** Captures email addresses for follow-up. Not every visitor calls today; some need nurturing.

```html
<section class="lead-magnet">
  <div class="container">
    <h2>Gratis Sanierungs-Checkliste</h2>
    <p>10 Punkte, die Sie vor jedem Projekt prüfen sollten – und bares Geld sparen.</p>
    <form class="lead-form">
      <input type="email" placeholder="Ihre E-Mail-Adresse" required aria-label="E-Mail">
      <button type="submit">Checkliste herunterladen</button>
      <small>Kostenlos, kein Spam. Jederzeit abmeldbar.</small>
    </form>
  </div>
</section>
```

```css
.lead-magnet {
  padding: 5rem 0;
  background: var(--surface);
  text-align: center;
}

.lead-magnet h2 { margin-bottom: 0.5rem; }
.lead-magnet > .container > p { color: var(--muted); margin-bottom: 2rem; max-width: 480px; margin-inline: auto; }

.lead-form {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  justify-content: center;
  max-width: 520px;
  margin-inline: auto;
}

.lead-form input {
  flex: 1 1 240px;
  padding: 0.85rem 1rem;
  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.12);
  border-radius: 6px;
  color: inherit;
}

.lead-form button {
  padding: 0.85rem 1.5rem;
  background: var(--accent);
  color: var(--bg);
  border: none;
  border-radius: 6px;
  font-weight: 700;
  cursor: pointer;
}

.lead-form small {
  width: 100%;
  color: var(--muted);
  font-size: 0.8rem;
}
```

**Rules:**
- Offer something **specific**. „Newsletter" is worthless. „Checkliste: 10 Fehler bei der Badsanierung" is valuable.
- The form must have a **real backend** (Resend, Make.com, Zapier, n8n). `alert("Danke!")` is a dead end.
- If no backend is available, skip this pattern entirely. A broken lead magnet hurts more than none.

---

## Pattern 5: JSON-LD with Reviews (Rich Snippets)

**Where:** In `<head>` on every page.

**What it does:** Makes stars appear in Google search results. Increases CTR significantly.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Sydney Sanierung",
  "telephone": "+49-152-34346248",
  "email": "info@sydney-sanierung.de",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "Altenbrückstraße 113",
    "postalCode": "40599",
    "addressLocality": "Düsseldorf"
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.9",
    "reviewCount": "28"
  },
  "areaServed": ["Düsseldorf", "Neuss", "Meerbusch", "Ratingen"]
}
</script>
```

**Rules:**
- `aggregateRating` only if **real reviews exist**. Fabricated stars = Google penalty.
- `areaServed` must be real service areas. Not „ganz NRW" if you only work in Düsseldorf.
- Include on **every page**, not just the homepage.

---

## Anti-Patterns (Do Not Build)

| Don't | Why |
|---|---|
| Fake metrics | Destroys trust if discovered. Better no trust bar than a lying one. |
| `mailto:` as only contact | Many browsers block it silently. Always offer phone + form. |
| Newsletter without backend | A form that goes nowhere is worse than no form. |
| More than 7 FAQ items | Nobody reads them. Pick the top objections only. |
| Floating CTA on desktop | Looks cheap and blocks content. |

---

## Inclusion Checklist

Before calling a local-business site complete, verify at least 3 of these 5 patterns are present:

- [ ] Trust Bar (verified data only)
- [ ] Floating CTA (mobile, `tel:` link)
- [ ] FAQ Accordion (≤7 real questions)
- [ ] Lead Magnet (only if backend exists)
- [ ] JSON-LD with reviews (only if real reviews)
