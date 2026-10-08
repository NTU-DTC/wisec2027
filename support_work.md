# Infra / Deployment Engineer Work Checklist

> **Scope**: build configuration (`config.toml`), theme templates (`themes/`), static asset
> infrastructure (`static/`), server deployment, compliance.
> **NOT responsible for**: `content/` page content and copy (see [content_work.md](content_work.md)).
> Background: ACM WiSec 2027 website (Hugo static site, forked from WiSec 2026),
> deployment target https://www.ntu.edu.sg/dtc/wisec2027 (sub-path under the NTU main site).
> Full analysis: [analysis.md](analysis.md).

---

## A. Build Fix (P0 — Completed)

- [x] [config.toml:9](config.toml:9) Hugo 0.92 compatibility fix:

  ```diff
  -disableKinds = ["taxonomy"]
  +disableKinds = ["taxonomy", "term"]
  +ignoreErrors = ["error-disable-taxonomy"]
  ```

  Verified: `hugo server` builds with 0 errors, local site returns 200, static assets load fine.

## B. URL / Deployment Configuration (P0)

- [ ] [config.toml:1](config.toml:1)

  ```toml
  baseURL = "https://www.ntu.edu.sg/dtc/wisec2027/"
  ```

  **The trailing slash is mandatory** (otherwise Hugo's joined relative links lose the
  `/dtc/wisec2027` sub-path). Note: this change makes `hugo server` preview serve under the
  sub-path; for local preview use:
  `hugo server --baseURL http://localhost:1313/dtc/wisec2027/ --appendPort=false`.
  Recommend executing after content work is mostly done, to avoid blocking Content Engineers' preview;
- [ ] [config.toml](config.toml) menu structure:
  - Rename `wiseml2026` menu item to `wiseml2027` + sync URL (**depends on Content Engineer's
    WiseML page naming decision in [content_work.md](content_work.md) item E**);
  - Sync domain in the commented-out Proceedings link URLs (verify when enabling);
  - `Program` entry: hide or point to a placeholder page per Content Engineer's decision
    ([config.toml:26](config.toml:26));
  - After the baseURL change, menu URLs automatically carry the sub-path via the template's
    `absLangURL` — no per-item manual edits needed;
- [ ] Theme template hardcoded fixes:
  - [themes/conference/layouts/partials/footer.html:47](themes/conference/layouts/partials/footer.html:47)
    `<a href="https://cispa.de/de/impressum">Impress</a>` (CISPA German legal requirement)
    → remove, or replace with NTU privacy statement (`https://www.ntu.edu.sg/footer/ntu-privacy-statement`);
  - [themes/conference/layouts/partials/footer.html:51](themes/conference/layouts/partials/footer.html:51)
    hardcoded copyright year `Copyright © 2025` → `Copyright © {{ now.Format "2006" }}`
    (the commented-out code in the file already has the correct form; restore it and delete the hardcoded version);
- [ ] Delete 0-byte empty layout leftovers (zip packaging leftovers; safe to delete — Hugo falls
  back to `_default/` layouts):
  - `themes/conference/layouts/sidebar/baseof.html`
  - `themes/conference/layouts/sidebar/list.html`
  - `themes/conference/layouts/sidebar/single.html`

## C. Site-Wide Root-Relative Path Fix (P0 — deployment-critical; recommend the Infra Engineer does this in one batch)

All root-relative paths starting with `/` in content files will point to
`ntu.edu.sg/images/...` (nonexistent) after sub-path deployment, causing 404s. Fix: convert to
relative paths (drop the leading `/`) or Hugo `relURL`/`relref`. Checklist (from analysis.md §3.2.3):

- [ ] [content/attending-venue.md:16](content/attending-venue.md:16) `src="/images/ccs_small.png"`
- [ ] [content/sponsorship.md](content/sponsorship.md) several `src="/images/logos/..."`
- [ ] [content/best-paper-award.md](content/best-paper-award.md) several `src="/images/best-papers/..."`
- [ ] [content/program-at-a-glance.md](content/program-at-a-glance.md) several `href="/program-keynote-speakers/"`, `href="/workshop-wiseml-2026-keynote-speaker/"`
- [ ] [content/sidebar/follow-us.md:15](content/sidebar/follow-us.md:15) `href="/livestreams"`
- [ ] [content/attending-dcAttractions.md](content/attending-dcAttractions.md) several `src="/images/travel/..."`

Batch detection command:

```bash
grep -rn 'src="/\|href="/' content/ --include="*.md" | grep -v 'http'
```

Verification after fix: with the new baseURL set, `hugo` build, then crawl all HTML links in
`public/` — no 404s, no URLs pointing to `ntu.edu.sg/images/`.

## D. Static Asset Infrastructure & Compliance (P1)

- [ ] **Remove Google Analytics** (NTU PDPA compliance; the NTU main site has its own cookie
  consent management):
  - Empty or delete [layouts/partials/analytics.html](layouts/partials/analytics.html)
    (if deleted, the partial reference at
    [themes/conference/layouts/_default/baseof.html:4](themes/conference/layouts/_default/baseof.html:4)
    is skipped automatically; also check `layouts/index.html` if present);
  - Or option B: apply for a new GA4 ID for the 2027 site, approved by NTU, and replace;
- [ ] Delete the old-domain Search Console verification file: `static/google4d8437e7240e126e.html`;
- [ ] Clear 2026 leftover static content:
  - `static/proceedings/` (2026 TOC HTML)
  - `static/talks/` (2026 slide PDFs)
  - `static/artifacts/` (clear if these are 2026 leftovers)
- [ ] Self-host external dependencies (NTU may enforce a CSP):
  [themes/conference/layouts/_default/baseof.html:25](themes/conference/layouts/_default/baseof.html:25)
  references `https://code.iconify.design/1/1.0.7/iconify.min.js`;
  download to `static/libs/iconify/` and switch to a local reference;
- [ ] favicon/webmanifest consistency: verify `static/site.webmanifest`, `static/browserconfig.xml`
  against the new icons (`static/favicon-*.png` etc.); `static/safari-pinned-tab.svg` does not exist
  (referenced at [layouts/partials/favicon.html:5](layouts/partials/favicon.html:5)) —
  remove that line or add the file.

## E. NTU Server-Side Coordination & Go-Live (P0 — external coordination)

- [ ] Request the `/dtc/wisec2027` path from NTU Web/DTC administrators
  (verified: `https://www.ntu.edu.sg/dtc/wisec2027` currently returns 404 — not yet provisioned);
- [ ] Confirm the hosting method: the NTU main site runs Sitefinity (Telerik/ASP.NET), very likely
  IIS-hosted; confirm whether `/dtc/wisec2027` is an IIS virtual directory, static directory,
  or reverse proxy;
- [ ] Confirm server behavior:
  - default document includes `index.html` (Hugo generates `page/index.html` directory structure);
  - custom 404 points to `/dtc/wisec2027/404.html` (the theme generates a 404 page);
- [ ] Confirm NTU's CSP (Content-Security-Policy) / cookie policy constraints on this site's JS;
- [ ] HTTPS: inherits the NTU main site certificate, no extra configuration;
- [ ] Upload (after [rebuild.sh](rebuild.sh) generates `public/`):

  ```bash
  ./rebuild.sh
  rsync -chrvP --stats public/ WEB-SERVER:/path/to/ntu/dtc/wisec2027/
  ```

  If NTU does not provide rsync/SSH, upload the full `public/` content via SFTP (WinSCP/FileZilla);
- [ ] Post-deployment audit:
  - Homepage and key pages return 200;
  - Crawl all site links (focus: image relative links, no URLs that lost the sub-path);
  - No GA remnants, no CISPA leftover links;
  - Mobile (Bootstrap responsive) rendering and interlinks with the NTU main site work.

---

## Suggested Execution Order

1. A Build fix (completed) → 2. C root-relative path batch fix → 3. D compliance cleanup →
4. B baseURL/menu switch after content work is done → 5. E NTU path provisioning → go-live → audit.

## Interface with Content Engineers

- Content Engineers submit menu copy/structure change requests; this role executes `config.toml` changes;
- WiseML 2027 page file renames are synced to menu URLs by this role;
- Content conventions (no leading `/`) are in [README.md](README.md) "Content Editing Conventions";
  this role verifies with grep before go-live.
