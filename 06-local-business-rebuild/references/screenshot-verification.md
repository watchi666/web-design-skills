# Screenshot Verification — Local Business

Screenshots are acceptance evidence, not nice-to-haves. A site that looks good in your head but broken in a screenshot is a broken site.

---

## When to Capture

**Mandatory screenshots before calling a site done:**

1. **First viewport** — Hero must be legible, CTA visible, no broken images
2. **Full page** — Every section renders, no cutoff content, footer visible
3. **Mobile (375px)** — iPhone SE / similar narrow viewport
4. **Tablet (768px)** — iPad portrait
5. **Desktop (1440px)** — Primary design target

**Optional but recommended:**
- **Contact section** — Form visible, labels readable, no overflow
- **After scroll-reveal** — Trigger lazy-loaded images and animations before capture

---

## Capture Protocol

### Step 1: Start Local Server

```bash
cd ~/prospect-pipeline/sites/<slug>/
npm run start &
```

Or use the PHP built-in server if static HTML:

```bash
cd ~/prospect-pipeline/sites/<slug>/
php -S localhost:8124 &
```

### Step 2: Verify Before Capture

Open the page in a real browser. Check:

- [ ] No console errors
- [ ] All images load (`document.querySelectorAll('img')` → none with `naturalWidth === 0`)
- [ ] Hero text readable
- [ ] CTA clickable
- [ ] No horizontal scroll at any width

### Step 3: Capture Viewports

Use `browser_vision` or a screenshot tool for each breakpoint:

| Viewport | What to Check |
|---|---|
| 375×812 | Mobile layout, tap targets, text size, floating CTA |
| 768×1024 | Tablet layout, navigation, image sizing |
| 1440×900 | Desktop layout, spacing, typography scale |

### Step 4: Trigger Dynamic Content

Before the final capture, trigger:

- **Scroll to bottom** — lazy images load
- **Open FAQ items** — verify accordion content
- **Hover over buttons** — check hover states
- **Open mobile menu** — verify it renders correctly

```javascript
// Lazy-load trigger
window.scrollTo(0, document.body.scrollHeight);

// FAQ trigger
document.querySelectorAll('details').forEach(d => d.open = true);
```

### Step 5: Verify Output File

After capture:

```bash
ls -lh screenshot-*.png
# File must be > 0 bytes and reasonable size (> 50KB for full page)
```

A 0-byte screenshot means the capture failed silently.

---

## Hero Clarity Check

The hero is the first impression. It must pass this test:

1. **Photo visible?** Can you tell what the image shows? If the overlay is so dark that the photo disappears, reduce overlay or brighten image.
2. **Text readable?** Run a contrast check. White text on dark overlay = pass. White text on light sky = fail.
3. **CTA obvious?** Can a first-time visitor find the primary action in 3 seconds?
4. **No text/image overlap?** Headline doesn't sit on the busiest part of the photo.

**Rule:** If the hero looks like a gradient with text, it's failed. A hero needs a real photograph that communicates the business.

---

## Common Screenshot Failures

| Symptom | Cause | Fix |
|---|---|---|
| White band at bottom | Page shorter than viewport | Add more content or min-height |
| Cutoff text | Fixed height container | Use min-height, not height |
| Images missing | Lazy load not triggered | Scroll to bottom before capture |
| Broken fonts | Web font not loaded | Use @fontsource or self-hosted |
| Mobile menu visible | Capture at wrong state | Close menu before capture |
| Sticky header duplicated | Full-page stitching | Use `position: relative` for capture, not fixed |

---

## Production URL Verification

After deployment, verify the **live URL**, not just localhost:

```bash
curl -s -o /dev/null -w "%{http_code}" https://<site>.vercel.app/
# Expect 200
```

Then open in browser and repeat the visual checks from Step 2.

---

## Final Checklist

- [ ] First viewport screenshot captured and reviewed
- [ ] Full page screenshot captured (all sections visible)
- [ ] Mobile (375px) screenshot captured
- [ ] Tablet (768px) screenshot captured
- [ ] Desktop (1440px) screenshot captured
- [ ] Hero passes clarity check
- [ ] No broken images in any screenshot
- [ ] Live URL returns 200 and renders correctly
