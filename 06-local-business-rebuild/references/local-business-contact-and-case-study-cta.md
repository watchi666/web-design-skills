# Contact parity + WhatsApp + project CTA pattern

Use this when rebuilding or polishing a static local-business website and the user asks to copy contact behavior from the original site, add WhatsApp, or make a project/case-study page easier to find.

## Contact source parity

When the user says to take the contact changes from the original site, inspect the source page first and mirror the actual visible contact structure:

- phone number and `tel:` link
- email and `mailto:` link
- street address + postal code/city exactly as shown
- opening hours
- form fields, service checkboxes, dropdowns, and message textarea
- any existing WhatsApp/quick-contact affordance

Do not keep stale draft contact data. In the Sydney Sanierung case the draft had an older simplified address while the source had:

```txt
Telefon: 0152 34346248
E-Mail: info@sydney-sanierung.de
Adresse: Altenbrückstraße 113, 40599 Düsseldorf
Öffnungszeiten: Mo–Fr: 08:00–18:00 Uhr
```

## Static form reliability

If there is no backend, implement a small submit handler that composes a `mailto:` URL from `FormData` and shows a visible status message before redirecting:

```js
const form = document.querySelector('#contactForm');
const status = document.querySelector('#contactStatus');
form.addEventListener('submit', event => {
  event.preventDefault();
  const data = new FormData(form);
  const workTypes = data.getAll('Art der Arbeit').join(', ') || 'Nicht ausgewählt';
  const rows = [
    ['Name', data.get('Name') || ''],
    ['Telefon', data.get('Telefon') || ''],
    ['E-Mail', data.get('E-Mail') || ''],
    ['Art der Arbeit', workTypes],
    ['Ungefähre Fläche', data.get('Ungefähre Fläche') || 'Nicht ausgewählt'],
    ['Wann gewünscht', data.get('Wann gewünscht') || 'Nicht ausgewählt'],
    ['PLZ / Ort', data.get('PLZ / Ort') || ''],
    ['Projektbeschreibung', data.get('Projektbeschreibung') || '']
  ];
  const body = rows.map(([key, value]) => `${key}: ${value}`).join('\n');
  const mailto = `mailto:info@example.de?subject=${encodeURIComponent('Sanierungsanfrage')}&body=${encodeURIComponent(body)}`;
  status.textContent = 'E-Mail-Programm wird geöffnet. Falls nichts passiert: bitte direkt per E-Mail oder WhatsApp melden.';
  window.location.href = mailto;
});
```

## WhatsApp CTA

Use `wa.me` with country code and no punctuation in the phone number:

```html
<a class="soft-btn whatsapp-btn"
   href="https://wa.me/4915234346248?text=Hallo%20Sydney%20Sanierung%2C%20ich%20m%C3%B6chte%20ein%20Sanierungsprojekt%20besprechen."
   target="_blank" rel="noopener">
  WhatsApp schreiben
</a>
```

Verification:

- source contains `https://wa.me/<number>`
- source contains the same number in `tel:+<number>`
- visible button text is clear: `WhatsApp schreiben`
- link uses `target="_blank" rel="noopener"`

## Project/case-study discovery CTAs

If a project page exists, make its path obvious in at least three places:

1. hero secondary CTA
2. project section CTA
3. footer link

For stronger discovery, make the featured project card itself clickable and add a small label such as `Projekt ansehen`.

Tasteful motion pattern:

```css
.project-cta {
  position: relative;
  overflow: hidden;
  animation: project-float 3.2s cubic-bezier(.22,1,.36,1) infinite;
}
.project-cta::before {
  content: '';
  position: absolute;
  inset: -2px;
  background: linear-gradient(110deg, transparent 0 34%, rgba(255,255,255,.42) 45%, transparent 58%);
  transform: translateX(-120%);
  animation: project-sheen 3.2s cubic-bezier(.22,1,.36,1) infinite;
}
.project-cta::after {
  content: '→';
  animation: project-arrow 1.25s cubic-bezier(.22,1,.36,1) infinite;
}
@keyframes project-float { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-5px)} }
@keyframes project-sheen { 0%,46%{transform:translateX(-120%)} 72%,100%{transform:translateX(120%)} }
@keyframes project-arrow { 0%,100%{transform:translateX(0)} 50%{transform:translateX(5px)} }
@media (prefers-reduced-motion: reduce) {
  .project-cta, .project-cta::before, .project-cta::after { animation: none !important; }
}
```

Keep the movement subtle and premium. The point is discoverability, not a blinking fairground sign.

## QA checklist

- Contact facts match the original/source page exactly.
- No stale draft address/phone remains in source.
- WhatsApp link opens via `wa.me` with the correct verified number.
- `tel:` link uses the same verified number.
- Static form has a submit handler, a visible status message, and no silent no-op path.
- Project/case-study links exist in hero, body, and footer.
- Featured project card is clickable if it looks like a card users would try to click.
- Animation uses transform/opacity only and respects `prefers-reduced-motion`.
