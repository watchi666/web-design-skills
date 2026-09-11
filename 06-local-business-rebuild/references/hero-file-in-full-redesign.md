# Hero/prototype file in a full local-business redesign

Use this when the user provides a standalone hero file, WebGL/canvas animation, image carousel, before/after demo, or says “use this as the hero”.

## Core rule
The supplied hero is an opening section, not the site architecture and not automatically the full visual language.

Build the complete homepage first-class:
- hero
- brand/value intro
- services
- process/proof/trust
- project/gallery teaser(s)
- testimonials/reviews
- contact/CTA
- footer

## Preserve animation, restyle wrapper
If the user says the animation matters, preserve the animation mechanics before touching aesthetics:
- keep canvas/WebGL scene and renderer setup
- keep interaction handlers (drag, click, focus, before/after reveal)
- keep image arrays/material logic unless paths need correction
- keep reduced-motion fallbacks if present

Only restyle the surrounding presentation:
- typography: match the final design system, not the prototype’s font
- colors/accent: match the full site palette
- copy: make it customer-facing and consistent with the rest of the page
- navigation: link to final sections/subpages
- CTAs: clear action + relevant secondary link
- overlays: improve contrast while keeping the animation visible
- spacing: ensure CTAs are visible in the first viewport

## Anti-patterns caught in review
- Treating the supplied hero file as the entire homepage.
- Applying the hero prototype’s style to the whole redesign when the user only wanted the animation.
- Preserving the animation but leaving old prototype typefaces/colors/copy, making the hero feel like a foreign module.
- Deploying after body sections are added but before confirming the hero wrapper is aligned with the new design system.

## Verification checklist
Run these checks before deploy:
- homepage contains real body sections below the hero
- `document.querySelector('#stage')` or equivalent animation root exists
- animation root reaches the expected ready state/class if available
- H1 font matches the design system
- body/UI font matches the design system
- hero CTAs are visible in the first viewport
- console has no JavaScript errors
- primary project/gallery subpage opens and has no broken images

## Good outcome
Same animation, new website-native wrapper: the user can still say “that animation is the thing I wanted”, while the hero’s text, type, color, and CTA feel like part of the redesigned website.