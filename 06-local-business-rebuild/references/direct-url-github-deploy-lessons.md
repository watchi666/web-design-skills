# Direct URL → Astro rebuild → GitHub/Vercel lessons

Use when applying `local-business-rebuild` outside its original prospect-pipeline shape, especially when the user gives a live URL plus a concrete workdir/GitHub repo.

## Adapted input shape
When no `~/prospect-pipeline/prospects/<slug>/` exists:
- Treat the user-provided workdir as the site root.
- Create `original-site/assets/` for downloaded images.
- Create `original-site/asset-manifest.json` with source URL, local filename, bytes, and any download errors.
- Create `original-site/content-extract.json` for cleaned text used in the rebuild.
- Keep `REBUILD_PLAN.md` and `DESIGN.md` at project root.

## Do not commit raw mirrored HTML
Mirrored website-builder HTML can contain third-party service tokens (example: Mapbox token embedded in copied page HTML). GitHub Push Protection may block the push.

Preferred pattern:
- Use raw HTML only as temporary scrape input.
- Do not commit `original-site/html/`.
- Add to `.gitignore`:
  ```gitignore
  # mirrored source html can contain third-party tokens
  original-site/html/
  ```
- Commit extracted text/manifest and downloaded public assets, not raw builder HTML.

## Lazy-load image verification
A first DOM check can report lazy-loaded images as incomplete before the page has scrolled. Before declaring broken images:
1. Load the page.
2. Scroll through the full document to trigger lazy loading.
3. Re-run:
   ```js
   Array.from(document.querySelectorAll('img'))
     .filter(img => img.complete && img.naturalWidth === 0)
     .map(img => img.src)
   ```
4. Treat `!img.complete` as pending, not broken, until after scroll/wait.

## Vercel/GitHub note
Vercel linking may add `.vercel` to `.gitignore` after a successful CLI deploy. Commit that `.gitignore` change and push it so future runs do not accidentally expose local project metadata.

### Preview-only first deployment: always set the target explicitly

Do not assume that omitting `--prod` guarantees a preview on a newly created Vercel project. A first deployment can be classified as `production` and receive the project's default aliases even when the command was only `vercel deploy --yes`.

For a user-requested preview-only deployment, use the explicit target from the first write:

```bash
vercel deploy --target preview --yes --scope <team>
```

Then verify the result rather than trusting the command wording:

```bash
vercel inspect <deployment-url> --scope <team>
```

Required evidence before reporting success:
- `target` is `preview`
- `status` is `Ready`
- the reported URL is the immutable preview deployment URL
- no production/custom domain was promoted unintentionally

If an accidental production deployment already exists:
1. Create and verify a replacement `--target preview` deployment first.
2. Inspect the accidental deployment and record its exact deployment ID plus active aliases.
3. Remove or unalias it only after the preview is `Ready`, using the exact ID and intended scope.
4. Fetch both the preview URL and every former production alias afterward; do not claim cleanup from CLI output alone.

This ordering preserves a working artifact while correcting the target. Never delete the only ready deployment first.

### Git-linked projects can recreate Production after a correct preview

An explicit preview deployment does not prevent later Git pushes from creating Production deployments when the Vercel project is connected to the repository and `main` is treated as the production branch. For a strict preview-only review project:

1. Push the repository and create the explicit preview.
2. Run `vercel ls <project> --scope <team>` after every push, not only after the manual deploy.
3. If no Git-triggered deployment is wanted, disconnect the repository from this Vercel project with `vercel git disconnect --scope <team>`. This leaves GitHub intact; it only stops automatic Vercel builds.
4. Remove accidental deployments by their exact immutable deployment URL only after the intended preview is `Ready`:
   ```bash
   vercel rm <exact-deployment-url> --yes --scope <team>
   ```
5. Verify the final deployment list contains only the intended `Preview` entry.

A preview URL may also return `302` to Vercel SSO even when the deployment is healthy. For a user-requested public review link, inspect project protection first:

```bash
vercel project protection <project> --format json --scope <team>
```

If SSO is the only blocker and public sharing is intended, disable it for that project only:

```bash
vercel project protection disable <project> --sso --scope <team>
```

Keep preview indexing protection independent of access protection: verify HTTP `200`, `X-Robots-Tag`, `robots.txt`, a unique changed string in HTML, and at least one newly added asset returning `200`. SSO-off does not mean indexable when those noindex controls remain active.

If the user requests a specific new GitHub repository name but the local project already has an `origin` remote, avoid `gh repo create --remote origin`: it can create the repo and then fail because `origin` already exists. Use this safer sequence:

```bash
# Create only if missing. Omit --remote when origin already exists.
gh repo view <owner>/<new-repo> >/dev/null 2>&1 \
  || gh repo create <owner>/<new-repo> --public --source=. --description "<description>"

# Retarget the existing remote explicitly.
git remote set-url origin https://github.com/<owner>/<new-repo>.git

git add .
git commit -m "Build <site name> website"
git push -u origin main
```

After `vercel deploy --no-wait`, run `vercel inspect <preview-url> --scope <team>` and report the `Ready` status plus preview URL.