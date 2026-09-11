# Typography lessons for local-business rebuilds

Use this when a local-business site feels polished but the user says the type looks generic, "AI slop", too boutique, or mismatched to the trade.

## Durable lesson
Do not treat the first pretty display/body pairing as final. Typography is part of the trade fit. A Maler-, Raumdesign-, Handwerker-, Sanierungs- or material-heavy business often needs type that feels like craft, catalog, workshop, material sample, or local trust — not a generic premium landing page.

## Avoid as defaults
- Do not default to `Playfair Display + DM Sans` for every premium local-business redesign. It can read as AI-premium-template: glossy, tasteful, but too familiar.
- Do not reach for startup defaults (`Inter`, `Poppins`, `Montserrat`, `Roboto`, generic grotesks) when the user asked for no AI slop.
- Do not defend the original font choice. Change it, build it, visually verify it, and redeploy.

## Better replacement pattern
Pick a pairing whose personality matches the business material:

| Business feel | Display direction | Body/UI direction |
|---|---|---|
| Interior surfaces, plaster, limewash, editorial craft | Newsreader, Fraunces, Cormorant Garamond with restraint | Source Sans 3, IBM Plex Sans, Atkinson Hyperlegible |
| Industrial / workshop / metal / roofing | Archivo, Roboto Slab alternatives, Besley, Barlow Condensed with care | IBM Plex Sans, Source Sans 3 |
| Premium but quiet local service | Literata, Newsreader, Libre Baskerville | Source Sans 3, Work Sans if not overused |
| Traditional craft / heritage | Cormorant Garamond, Fraunces, Vollkorn | Source Sans 3, IBM Plex Sans |

## Verification steps
1. Update font packages/imports and global CSS.
2. Update `DESIGN.md` with the typography revision and rationale.
3. Run `npm run build`.
4. Start/refresh local preview and inspect computed fonts in the browser:
   ```js
   ({
     fontH1: getComputedStyle(document.querySelector('h1')).fontFamily,
     fontBody: getComputedStyle(document.body).fontFamily,
   })
   ```
5. Visually inspect the first viewport. Ask: would this look plausible in a real material catalog, workshop brochure, or local trades portfolio?
6. Commit, push, and redeploy. Verify the public URL, not only localhost.

## Example from RaumDesign Rudek
The first build used `Playfair Display + DM Sans`. The user said the font did not fit and asked for "keinen ki slop". The better fit was:
- Display: `Newsreader Variable`
- Body/UI: `Source Sans 3 Variable`

This made the site feel more like Raumhandwerk / material catalog and less like a generic AI-premium template.