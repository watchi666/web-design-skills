# Hero animation integration in full local-business redesigns

Use this when the user provides a standalone hero animation/prototype and asks for a full website redesign.

## Core rule
- Preserve the requested animation/interaction as a technical asset.
- Redesign the hero wrapper around it: logo, typography, colors, copy, CTAs, and overlay must match the new site design system.
- Do **not** copy the hero prototype's original design language across the whole site unless the user explicitly asks for that.
- If the user says the animation is important, treat it as a visible hero object, not background decoration behind text.

## Workflow
1. Build or keep the full homepage body first-class: services, trust/proof, project teaser/gallery, testimonials, contact, footer.
2. Integrate the hero animation as the first section only.
3. Replace prototype text/nav/buttons with site copy and conversion CTAs.
4. Use the original business logo if available; do not leave a hand-built text logo when a real logo asset exists.
5. Keep the animation code isolated. Change visual wrapper CSS and shader/photo grading only when needed; do not rewrite interaction logic casually.
6. Verify in browser:
   - canvas/animation is `ready` or visibly active
   - H1/body fonts match the site design system
   - CTAs are visible in the first viewport
   - logo loads as an image with non-zero natural dimensions
   - no broken images or JS errors

## Composition rule: text left, animation right
When the hero has large copy on the left and a WebGL/canvas ring/cylinder on the right, do not let animated images sit underneath the text unless the user explicitly wants a background-video feel.

Preferred fix pattern:
- shrink the ring/cylinder radius/diameter first
- reduce tile size if needed
- move the animation group/object to the right
- keep a responsive mobile fallback that recenters the animation on narrow screens
- in focus/selected mode, let the selected item recenter if that improves usability
- re-check CTA visibility after resizing the hero; shrinking/moving animation can expose typography/spacing regressions

Concrete WebGL-style knobs from the Sydney Sanierung hero fix:
```js
const PLANE_W = 2.15, PLANE_H = 1.62;
const R0 = 1.95;
const RADIUS_GAIN = 0.16;
const RING_X = 3.75;

group.position.x = RING_X;

// During resize/tick:
group.position.x = innerWidth < 720 ? 0 : (innerWidth < 1100 ? 2.45 : RING_X);
// Optional: interpolate toward center during focus mode.
group.position.x = baseRingX * (1 - focusAmount);
```

The visual acceptance test is simple: the complete spinning animation should read as a separate right-side feature before the left headline starts; the headline must not cover project cards.

### Iterative centering after the no-overlap fix
After shrinking/right-aligning a hero ring, the next likely issue is an overcorrection: a visible empty gap between the left headline and the right-side animation. Fix that by moving the ring back toward the center in small steps, not by increasing the radius first.

Safe pattern:
- keep the already-approved radius/tile size if the animation scale feels right
- reduce the horizontal group offset (`RING_X`) incrementally, e.g. `3.95 → 3.35 → 3.05`
- adjust the tablet offset in parallel, e.g. `2.45 → 2.10 → 1.85`
- verify visually after each step that the gap is smaller and no project card visibly covers the H1 or CTA area
- keep focus interpolation (`group.position.x = baseRingX * (1 - focusAmount)`) so a selected image can recenter cleanly

Do not treat the first no-overlap position as final if it leaves the animation looking parked at the far right. The target is balanced: text left, animation right/middle, no dead canyon between them.

## Click/focus mode: selected image must be unobstructed
When a hero ring/cylinder supports clicking a project card/image, the selected item must become the only foreground object. Do not leave nav, hero text, captions, hint pills, back buttons, or overlay gradients above the focused image unless the user explicitly asks for UI chrome.

Implementation pattern:
```css
body.hero-focus #stage{z-index:30!important;cursor:zoom-out}
body.hero-focus .hero::after{opacity:0!important}
body.hero-focus .overlay,
body.hero-focus .caption,
body.hero-focus .hint,
body.hero-focus .backbtn{
  opacity:0!important;
  pointer-events:none!important;
  visibility:hidden!important;
}
```

```js
// on select/focus
document.body.classList.add('hero-focus');

// on deselect/escape/canvas click
document.body.classList.remove('hero-focus');
```

Verification:
- trigger focus mode in the browser after a real click or by adding the focus class for layer testing
- confirm selected canvas/image z-index is above the hero overlay
- confirm text/nav/hints/captions/back button are hidden and non-interactive
- confirm there are no JS errors

## If animated photos look washed out
Common causes in WebGL/canvas heroes:
- shader depth dimming too aggressive
- global overlay too dark
- warm tint/desaturation applied after sampling
- dark vignette/edge multiplier too strong
- motion filters such as speed blur, chromatic aberration, afterimage trails, or strong bloom

### User asks for stronger/natural photos
Use subtle fixes first:
- increase base depth brightness instead of removing depth entirely
- add only modest contrast/saturation if the brief allows grading
- reduce edge/vignette darkness
- slightly lighten the CSS overlay that sits above the canvas

### User explicitly asks to remove filters
Remove grading and motion filters rather than tuning them. Disable:
- depth/tone dimming in the fragment shader
- luma-based saturation boosts
- warm tint multipliers
- dark edge masks/vignettes
- `uAberration` / chromatic offsets
- speed blur
- afterimage damp/trails
- strong bloom

Example raw-photo shader tail:
```glsl
// Photos raw: no depth dimming, warm tint, saturation/contrast filter, or edge mask.
// uDim is kept only for focus mode so non-selected cards can step back.
col *= uDim;
col = clamp(col, 0.0, 1.0);
gl_FragColor = vec4(col, 1.0);
```

Example motion-filter disable:
```js
const aberration = 0;
const blur = 0;
bloom.strength = 0.08;
if (!REDUCED) after.uniforms['damp'].value = 0;
```

Keep the result natural: clear real project photos, not an Instagram filter and not a fog machine.
