# Mobile-First Checklist — Local Business

**Rule:** At least 2/3 of visitors come from mobile. The site must work perfectly at 375px before it works at 1440px.

---

## Touch Targets

| Element | Minimum Size | Preferred |
|---|---|---|
| Buttons | 44×44px | 48×48px |
| Navigation links | 44×44px | 48px height with generous padding |
| Form inputs | 44px height | 48px height |
| Floating CTA | 60×60px | 60×60px |

**Check:** Can you tap every interactive element with your thumb without hitting the neighbour?

---

## Typography on Mobile

| Element | Minimum | Reason |
|---|---|---|
| Body text | 16px | Prevents iOS auto-zoom on form focus |
| Small/caption | 14px | <14px is unreadable on 375px screens |
| Form labels | 16px | Same zoom-prevention |
| Input placeholders | 16px | Same zoom-prevention |

**Never** go below 16px for anything the user might need to read or tap.

---

## Layout

- [ ] No horizontal scroll at any width ≥ 375px
- [ ] Images use `max-width: 100%` or `width: 100%` with `height: auto`
- [ ] Containers have `padding-inline: 1rem` minimum on mobile (not flush to edges)
- [ ] Grid columns collapse to 1 on mobile; no 2-column text grids below 640px
- [ ] Hero text fits within first viewport without scrolling (H1 + subline + CTA visible)

---

## Navigation

- [ ] Hamburger menu works (opens, closes, links work)
- [ ] Menu closes automatically when a link is tapped
- [ ] Phone number is **visible without opening the menu**
- [ ] No more than 5 items in the mobile menu
- [ ] Sticky header does not hide content when jumping to anchors

---

## Contact & CTA

- [ ] Phone number visible as `tel:` link in the first viewport
- [ ] Floating CTA present on mobile (call or WhatsApp)
- [ ] Contact form fits on one mobile screen without zooming
- [ ] Form submit button is ≥ 48px tall and full-width on mobile
- [ ] Success/error states are visible without scrolling

---

## Forms

- [ ] All inputs have visible labels (not placeholders-only)
- [ ] Placeholder text is 16px or larger
- [ ] Date/phone inputs use appropriate `type` attributes
- [ ] Checkbox/radio groups have tap targets ≥ 44×44px
- [ ] Error messages appear inline, not as `alert()`

---

## Performance

- [ ] No layout shift when images load (aspect-ratio or explicit dimensions)
- [ ] Fonts do not cause FOUT/FOIT that pushes text down
- [ ] Hero image is ≤ 150KB on mobile (use `srcset` or responsive image)
- [ ] No JavaScript that blocks rendering for > 500ms on slow 3G

---

## Self-Check

Before declaring a site mobile-ready, answer these honestly:

1. Was the page **first designed at 375px**, then expanded to desktop?
2. Can you **complete the primary goal** (call / form / WhatsApp) with one hand?
3. Is there **anything** that requires pinch-zoom to read or tap?
4. Would you **show this to your mother** on her phone without explaining?

If any answer is "no", go back to mobile view and fix it.
