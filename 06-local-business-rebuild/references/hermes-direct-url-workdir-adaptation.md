# Hermes direct URL/workdir adaptation

Use this when `local-business-rebuild` is run inside Hermes for a user-provided source URL and explicit work path instead of the original `~/prospect-pipeline/prospects/<slug>/` intake.

## Adapted input/output shape
- Treat the user-provided work path as the project root. Do not force the prospect-pipeline monorepo layout.
- Preserve the skill's planning discipline by creating `REBUILD_PLAN.md` and `DESIGN.md` in the project root.
- Store source evidence inside the project, e.g.:
  - `original-site/html/` — mirrored/extracted source pages
  - `original-site/assets/` — downloaded original images/assets
  - `original-site/asset-manifest.json` — source URL → local filename mapping
  - `original-site/content-extract.json` — text extracted from mirrored pages
- Copy production-usable assets into `public/images/` and reference them locally. Never leave hotlinked CDN/Unsplash/original-site image URLs in the final build.

## Crawling/image capture pattern
- A simple `wget --mirror --page-requisites` may miss builder/lazy/background images.
- After mirroring, extract image URLs from:
  - HTML URLs
  - CSS `url(...)`
  - DOM attributes (`src`, `data-src`, `data-image`, etc.)
  - computed `background-image`
- For 1&1 / website-editor style sites, many useful images may be hidden in signed `le-cdn.website-editor.net/.../dms3rep/multi/opt/...` URLs.

## QA pitfall: lazy images look broken before scroll
The naive check:

```js
Array.from(document.querySelectorAll('img')).filter(img => !img.complete || img.naturalWidth === 0)
```

can falsely report below-the-fold lazy images as broken because they are still pending.

Use this pattern instead:

```js
await (async () => {
  for (const y of [0, 700, 1400, 2200, 3000, 4000, document.body.scrollHeight]) {
    scrollTo(0, y);
    await new Promise(r => setTimeout(r, 250));
  }
})();

({
  images: document.querySelectorAll('img').length,
  broken: Array.from(document.querySelectorAll('img'))
    .filter(img => img.complete && img.naturalWidth === 0)
    .map(img => img.src),
  pending: Array.from(document.querySelectorAll('img')).filter(img => !img.complete).length,
});
```

Only treat images as broken after scroll/lazy-load has had a chance to resolve. Pending images are a load-state, not proof of a broken path.

## Push/deploy note
If the user asks for GitHub push and Vercel deploy, still build and commit locally, then attempt push/deploy. If auth is missing, report the exact credential blocker and the ready commands; do not treat missing credentials as a design/build failure.