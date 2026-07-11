# Static local directory audit via HTTP

## Why this exists

When auditing a static site from a local directory, Chromium/CDP can end up loading a browser error page instead of the real page, or relative assets can resolve differently than they will on a deployed URL. The visible symptom is a nonsense score collapse: the report may show an H1 such as `Your file couldn’t be accessed`, no tel/mail links, missing schema, and only a couple of browser-error images.

That is not a design failure. It is an audit-target failure.

## Reliable pattern

Serve the static directory first, then audit the localhost URL:

```bash
cd <project-root>
python3 -m http.server 8097 --bind 127.0.0.1
```

In another command:

```bash
node /home/hermes/projekte/website-upgrade/tools/website-quality-scorer/bin/website-quality-scorer.mjs \
  audit http://127.0.0.1:8097/ \
  --out <project-root>/website-quality-report \
  --name <label>
```

If the server is long-running, start it as a tracked background process and clean it up when done.

## Verification checklist

Before trusting the score:

- `report.md` URL is the intended `http://127.0.0.1:<port>/` or live URL.
- H1 in the feature matrix is the actual site H1, not a browser error.
- Screenshots show the real website.
- After Vercel/Git deployment, repeat the audit against the production alias, not only localhost.

## Typical fix loop

1. Run local HTTP audit.
2. Fix blockers: copy tells, schema, `tel:`/WhatsApp/contact fallback, dead links, broken images, mobile tap targets.
3. Re-run local HTTP audit and visually inspect at least the mobile screenshot.
4. Commit and push.
5. Deploy to Vercel.
6. Re-run audit against the live production URL and report only the live verified result as delivery status.
