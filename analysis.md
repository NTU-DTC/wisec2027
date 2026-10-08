# WiSec 2027 Website Migration & Refactoring Analysis Report

> Project: ACM WiSec 2027 conference website (forked from the WiSec 2026 website, unmodified)
> Analysis date: 2026-10-08
> Deployment target: https://www.ntu.edu.sg/dtc/wisec2027

---

## 1. `hugo server` Error Fix

### 1.1 Error Symptom

Running `hugo server` fails the build and exits:

```
ERROR You have the value 'taxonomy' in the disabledKinds list.
In Hugo 0.73.0 we fixed these to be what most people expect (taxonomy and term).
Error: Error building site: logged 1 error(s)
```

### 1.2 Root Cause

[config.toml](config.toml:9) contains `disableKinds = ["taxonomy"]`.
The original site was built on Hugo < 0.73, where `disableKinds = ["taxonomy"]` disables both
taxonomy and term page kinds. The current environment has `hugo v0.92.2+extended`. Since
Hugo 0.73.0, `taxonomy` and `term` are two separate page kinds; writing only `taxonomy` is
treated as ambiguous configuration, and in 0.92 this is escalated to an **ERROR that aborts
the build**.

### 1.3 Fix (Applied)

The site has no tags/categories content at all; the original intent was to disable both
taxonomy kinds. Therefore we adopt the "fully express intent + explicitly suppress the notice"
double-safety approach, changing [config.toml](config.toml:9) to:

```toml
disableKinds = ["taxonomy", "term"]
ignoreErrors = ["error-disable-taxonomy"]
```

- `disableKinds = ["taxonomy", "term"]`: explicitly disables both taxonomy page kinds,
  matching the old-version semantics;
- `ignoreErrors = ["error-disable-taxonomy"]`: suppresses Hugo's informational ERROR so the
  build no longer aborts, while remaining compatible with future Hugo upgrades.

### 1.4 Verification

After the fix, `hugo server` builds successfully (0 errors), the local site
`http://localhost:1313/` returns HTTP 200, and static assets (Bootstrap / FontAwesome /
theme.css / site.css) all load normally.

Note: the README's mentioned URL `/wisec21/` sub-path is because the old site's baseURL
carried a sub-path; the 2026 baseURL is a domain root (`https://wisec26.events.cispa.de/`),
so the local site is served from the root path.

---

## 2. Required Code Changes for WiSec 2027 (Excluding Paper Submission & Payment)

The refactoring checklist below is ordered by priority. Overall approach:
**global config → brand visuals → content pages → clean up 2026 leftovers**.

### 2.1 Global Configuration (Mandatory)

Modify [config.toml](config.toml):

| Config item | Current value (2026) | Suggested value (2027) |
|---|---|---|
| `baseURL` | `https://wisec26.events.cispa.de/` | `https://www.ntu.edu.sg/dtc/wisec2027/` (see §3; sub-path deployment **requires the trailing slash**) |
| `title` | `ACM WiSec 2026` | `ACM WiSec 2027` |
| `banner_title` | `19th ACM Conference ...` | `20<sup>th</sup> ACM Conference on Security and Privacy in Wireless and Mobile Networks` |
| `banner_subtitle` | `Saarbrücken, Germany<br>June 30 - July 3, 2026` | New host city & dates (e.g. `Singapore<br><new dates>`, hosted by NTU) |
| `banner_images` | `images/saarschleife.png`, `images/sb2.jpg` | Replace with Singapore/NTU campus images |
| `Copyright` | `ACM` | Keep `ACM` |

Menus (`[[menu.main]]`):
- Rename `wiseml2026` to `wiseml2027`, pointing to the new workshop page;
- Domain in the commented-out Proceedings link URLs must be synced too;
- Homepage `Program` / `Call for` links stay unchanged (page paths unchanged, content updated only).

### 2.2 Brand Visual Assets

- **Banner images**: add Singapore/NTU-themed images under `static/images/` (1200px wide banner);
- **favicon/icons**: current favicons in `static/` were generated for the CISPA era
  (`static/favicon-*.png` etc.) — keep the ACM WiSec style or regenerate;
  also check `static/browserconfig.xml` and `static/site.webmanifest`;
- **Logos**: the CCS Saarbrücken sponsor-logo in [content/sidebar/hosts.md](content/sidebar/hosts.md)
  must be replaced with the NTU / Digital Trust Centre (DTC) logo; add `static/images/logos/ntu.png`,
  `dtc.png`, etc.;
- **Conference year images**: 2026 award ceremony photos in
  [content/best-paper-award.md](content/best-paper-award.md)
  (`static/images/best-papers/*.jpg`) are 2026 content — for 2027 remove or hide that page.

### 2.3 Content Page Checklist

**Homepage [content/_index.md](content/_index.md)**
- `ACM WiSec 2026` → `ACM WiSec 2027` in title/body, edition `19th` → `20th`;
- Host description: supported by CISPA → supported by NTU Singapore (DTC);
- `Registration` paragraph: before 2027 registration opens, change to "Registration will open in ..."
  (payment is explicitly out of scope, so simplify to a contact-email pointer or placeholder);
- `Travel Grants for PhD-Holders` paragraph: DFG (German Research Foundation) funding is
  specific to the 2026 host — for NTU-hosted 2027, remove or replace with the new grant policy;
- `Call for Papers` red callout about "2026 ACM Conferences": the ACM Open Access policy note
  may still apply for 2027, but update the wording per ACM's latest policy;
- Add `WiSec 2026 / WiseML 2026` to the top of the `Past conferences` list (link to the 2026 archive).

**[content/sidebar/important-dates.md](content/sidebar/important-dates.md)**
- All dates are the 2026 cycle; until 2027 dates are set, replace with TBA placeholders or remove;
- This sidebar shows on every page — one of the first files to update.

**Call for series ([content/call-for-papers.md](content/call-for-papers.md),
[content/call-for-artifacts.md](content/call-for-artifacts.md),
[content/call-for-posters-and-demos.md](content/call-for-posters-and-demos.md),
[content/call-for-workshops.md](content/call-for-workshops.md))**
- Update edition, year, dates throughout;
- HotCRP submission links at [call-for-papers.md:78](content/call-for-papers.md:78)
  (`wisec26.hotcrp.com`, `wisec26-cycle2.hotcrp.com`) → `wisec27...` (if HotCRP is retained;
  since submission is out of scope, the "Submission Guidelines" subsection can be hidden first);
- The "ACM Open Access publishing model for 2026" section at
  [call-for-papers.md:116](content/call-for-papers.md:116): update for 2027 applicability or remove;
- If not yet soliciting papers, set the whole page to "Call for Papers coming soon".

**Attending series**
- [content/attending-venue.md](content/attending-venue.md): Saarbrücken Congresshalle →
  NTU Singapore venue (travel guide fully rewritten);
- [content/attending-Area-Hotels.md](content/attending-Area-Hotels.md): hotel list → Singapore hotels;
- [content/attending-dcAttractions.md](content/attending-dcAttractions.md): currently Seoul
  attractions (image references `/images/travel/*.png` don't exist in static — broken links
  already); replace with Singapore attractions directly;
- [content/attending-visas.md](content/attending-visas.md): German visa → Singapore entry/visa requirements;
- [content/attending-Registration.md](content/attending-Registration.md): 2026 Cvent registration
  link and EUR price table are payment scope — explicitly out of scope, so set `hidden = true`
  (the switch already exists in front matter) or replace with "Registration details coming soon";
- [content/attending-grants.md](content/attending-grants.md): DFG-related; set `hidden = true` or delete.

**Program series (2026 conference artifacts)**
- [content/program-accepted-papers.md](content/program-accepted-papers.md),
  [content/program-at-a-glance.md](content/program-at-a-glance.md),
  [content/program-detailed-program.md](content/program-detailed-program.md),
  [content/program-keynote-speakers.md](content/program-keynote-speakers.md),
  [content/program-vision-talks.md](content/program-vision-talks.md),
  [content/program-banquet.md](content/program-banquet.md),
  [content/best-paper-award.md](content/best-paper-award.md):
  all are the 2026 program; for 2027 clear to placeholders ("Program coming soon") or `hidden = true`,
  to be filled once the 2027 program is set;
- The `Program` menu entry in [config.toml:26](config.toml:26) can be hidden temporarily or point
  to a placeholder page.

**Organization series**
- [content/organization-committee.md](content/organization-committee.md),
  [content/organizations-program-committee.md](content/organizations-program-committee.md),
  [content/organizations-steering-committee.md](content/organizations-steering-committee.md):
  replace with the 2027 committee roster (General Chairs etc.), mailing-list names
  `wisec26-*@googlegroups.com` → `wisec27-*`; new member photos go in `static/images/persons/`,
  clean up photos of outgoing members;
- [content/sidebar/hosts.md](content/sidebar/hosts.md): CCS logo → NTU/DTC logo.

**Workshop (WiseML) series**
- [content/workshop-wiseml-2026.md](content/workshop-wiseml-2026.md),
  [content/workshop-wiseml-2026-keynote-speaker.md](content/workshop-wiseml-2026-keynote-speaker.md),
  [content/workshop-wiseml-program.md](content/workshop-wiseml-program.md):
  rename/create as `workshop-wiseml-2027*` (sync the menu URL at [config.toml:48](config.toml:48)),
  or set to placeholder pages first.

**Sponsorship**
- [content/sponsorship.md](content/sponsorship.md): 2026 sponsors (GMU, Virginia Tech etc.) →
  2027 sponsorship plan placeholder; DFG/CISPA logos in
  [content/sidebar/sponsors.md](content/sidebar/sponsors.md) → NTU-related or clear;
- Payment is out of scope, but sponsorship (solicitation) is usually kept — keep the framework,
  clear 2026 content as needed.

### 2.4 Code/Engineering Cleanup (Non-Content)

1. **Google Analytics**: [layouts/partials/analytics.html](layouts/partials/analytics.html:2)
   uses the 2026 site's GA ID `G-6Q2XKE1T87`. For the NTU deployment:
   - Option A: replace with a new GA4 ID for the 2027 site;
   - Option B (recommended): empty or delete this partial — the NTU site has its own analytics,
     and its privacy policy requires cookie consent management; third-party GA may violate
     NTU's privacy terms (see §3.4).
2. **CISPA leftovers**: [themes/conference/layouts/partials/footer.html:47](themes/conference/layouts/partials/footer.html:47)
   hardcodes `<a href="https://cispa.de/de/impressum">Impress</a>` (a German legal Impressum
   requirement) — remove for the Singapore deployment or replace with the NTU equivalent;
   the footer copyright year is hardcoded to `2025`
   ([footer.html:51](themes/conference/layouts/partials/footer.html:51)); recommend
   `{{ now.Format "2006" }}` (the commented-out code already has the correct form);
3. **Verification file**: `static/google4d8437e7240e126e.html` is a Google Search Console
   verification file for the CISPA domain — delete after migration;
4. **proceedings/talks static files**: `static/proceedings/` and `static/talks/` hold 2026 TOCs
   and slide PDFs — clear for 2027 (`rebuild.sh` copies them as-is);
5. **scripts**: [scripts/](scripts/) (`gen-papers.py`, `parse-pc.py`) are 2026 paper-data
   processing tools — can be kept for reference; the Archive subdirectory does not affect the build;
6. **Empty layout leftovers**: the three 0-byte files under `themes/conference/layouts/sidebar/`
   (`baseof.html`, `list.html`, `single.html`) are zip packaging leftovers — recommend deleting
   to avoid confusion (Hugo falls back to `_default/` layouts; deletion has no side effects);
7. **`unsed_content/` directory**: typo of `unused_content`, contains 2024-and-earlier content;
   not built (directory name doesn't match Hugo conventions); recommend archiving or deleting;
8. **`content/sidebar/important-dates-past.md`**: correctly controlled via `hidden`/menu in the
   2026 version; for 2027 just clear its content.

### 2.5 Suggested Refactoring Order

1. `config.toml` (baseURL/title/banner/menus) → 2. Sidebar (important-dates, hosts, sponsors)
→ 3. Homepage `_index.md` → 4. Program/Workshop placeholders → 5. Call for series
→ 6. Attending series → 7. Organization → 8. Engineering cleanup (GA, Impressum, verification file)
→ 9. Visual assets.

---

## 3. Deployment to https://www.ntu.edu.sg/dtc/wisec2027 — Compatibility Research

### 3.1 NTU Main-Site Tech Stack Analysis (Measured)

Fetching `https://www.ntu.edu.sg/dtc` (Digital Trust Centre; DTC is an NTU centre living under
the main-site path `/dtc`) yields these facts:

| Item | NTU main site | This Hugo site |
|---|---|---|
| CMS | Sitefinity (Telerik, ASP.NET) — dynamic CMS | Hugo static site |
| Page framework | `/ResourcePackages/NTU/assets/styles/main.css` + `main.js` | Bundled `themes/conference` |
| CSS framework | In-house (custom `.container { max-width: 1440px }` etc.) | Bootstrap 4.0.0 (self-hosted in `static/libs/`) |
| JS libraries | jQuery (bundled in ResourcePackages) | jQuery 3.4.1 (self-hosted) |
| Theme color | `#d71440` (NTU red) | Bootstrap blue `#007bff` |
| Privacy compliance | Built-in cookie consent dialog (Privacy Notice) | Direct Google Analytics embed |

**Key conclusion: `/dtc/wisec2027` is a sub-path under the NTU main site, not a dedicated
sub-domain.** (Measured: `https://www.ntu.edu.sg/dtc/wisec2027` currently returns 404 —
the path is not provisioned yet and needs to be requested from the NTU web team.)

### 3.2 Style Conflict Assessment

**Conflict risk: low (thanks to sub-path isolation).** Details:

1. **CSS conflicts — low risk, theoretical only**:
   - The Hugo site's Bootstrap 4 CSS (with global selectors like `*`, `body`, `.container`,
     `.row`, `h1-h6`) only applies to the Hugo pages' own DOM. NTU's styles are not injected
     into Hugo pages (Hugo pages are standalone HTML documents, not sharing NTU's `main.css`),
     and vice versa;
   - The only "integration" need is visual appearance: NTU brand red `#d71440`, font
     `DM Serif Text`, etc. If the conference site should visually match NTU styling, override
     theme colors via [static/css/site.css](static/css/site.css) (that file exists exactly for
     site-level customization) — but this is optional;
   - If NTU requires mounting their unified header/footer (iframe or JS injection), the injection
     method needs to be confirmed with the NTU web team; only then do class-name collisions
     between Bootstrap and NTU CSS (`.container`, `.row`, `.btn`, etc.) become a real issue.
     **Mitigation**: namespace the conference site's HTML root and nest Bootstrap selectors
     under it (large change, usually unnecessary), or keep the independent style
     (academic conference site convention — past WiSec sites all had independent styles).

2. **JS conflicts — very low**: jQuery versions (NTU's bundled vs this site's 3.4.1) are never
   loaded together — no conflict. The site also references external `code.iconify.design` JS
   ([baseof.html:25](themes/conference/layouts/_default/baseof.html:25)) — a third-party CDN
   dependency; if NTU enforces a CSP (Content-Security-Policy), confirm it's allowed or
   self-host it in `static/libs/`.

3. **URL structure — the highest-risk item (most important)**:
   All page links are generated by Hugo relative to `baseURL`, so in theory changing baseURL
   migrates the whole site. But these hardcoded problems must be fixed:
   - [config.toml:1](config.toml:1) `baseURL` must become `https://www.ntu.edu.sg/dtc/wisec2027/`
     (**the trailing slash must be kept**, otherwise Hugo-generated absolute links become
     `ntu.edu.sg/dtc/page/`, losing `wisec2027`);
   - Content files contain **root-relative hardcoded paths** (starting with `/`) which, when
     deployed under a sub-path, resolve to `ntu.edu.sg/images/...` (nonexistent) and 404:
     - [content/attending-venue.md:16](content/attending-venue.md:16) `src="/images/ccs_small.png"`
     - [content/sponsorship.md:57](content/sponsorship.md:57) etc. `src="/images/logos/..."`
     - [content/best-paper-award.md:17](content/best-paper-award.md:17) `src="/images/best-papers/..."`
     - [content/program-at-a-glance.md:125](content/program-at-a-glance.md:125) `href="/program-keynote-speakers/"`
     - [content/sidebar/follow-us.md:15](content/sidebar/follow-us.md:15) `href="/livestreams"`
     - [content/attending-dcAttractions.md:73](content/attending-dcAttractions.md:73) and more
   - **Fix**: uniformly change to relative paths without the leading slash (e.g.
     `images/ccs_small.png`) or use Hugo's `{{< relURL >}}`/`relref` shortcodes;
     theme templates already use `relURL` correctly and need no changes;
   - Menu/footer links in theme templates render via `absLangURL` and correctly carry the new
     baseURL — no issue.

4. **Absolute page links**: the [`link.html`](themes/conference/layouts/shortcodes/link.html:17)
   shortcode applies `absURL` to scheme-less links, which stays correct under a sub-path
   (based on the new baseURL).

### 3.3 Server/Operations Actions

1. **Provision the path**: ask the NTU Web/DTC administrators to provision `/dtc/wisec2027` in
   Sitefinity or IIS (the NTU main site is ASP.NET, very likely IIS-hosted) as a static-file
   directory or reverse-proxy rule;
2. **Upload method**: per [README.md](README.md), after `./rebuild.sh` generates `public/`:
   ```bash
   rsync -chrvP --stats public/ WEB-SERVER:/path/to/ntu/dtc/wisec2027/
   ```
   If NTU provides no rsync/SSH (common in large institutions), alternatives: SFTP/WinSCP upload,
   or have NTU import the files via Sitefinity. `public/` is pure static files
   (HTML/CSS/JS/images) and works on any static hosting;
3. **URL rewriting (clean URLs)**: Hugo generates `page/index.html` directory structures and
   relies on the server resolving `index.html` for `/dtc/wisec2027/page/` (IIS supports default
   documents by default); configure IIS custom 404 to `/dtc/wisec2027/404.html`
   (the theme generates a 404 page);
4. **HTTPS**: the NTU main site is already HTTPS; the sub-path inherits it — no extra certificate.

### 3.4 Privacy & Compliance (NTU-Specific, Important)

1. **Cookie/GDPR/PDPA**: the NTU main site shows a cookie consent dialog (Singapore PDPA
   compliance). The directly embedded Google Analytics
   ([layouts/partials/analytics.html](layouts/partials/analytics.html)) should
   **strongly be removed** under the NTU domain (or replaced with an NTU-approved analytics
   solution), otherwise it may violate NTU's privacy policy;
2. **Impressum**: the CISPA Impressum link at
   [themes/conference/layouts/partials/footer.html:47](themes/conference/layouts/partials/footer.html:47)
   is a German legal requirement — not required in Singapore; replace it with an NTU/DTC
   contact page or NTU privacy statement (the NTU main site has `/footer/ntu-privacy-statement`);
3. **Search Console verification**: delete `static/google4d8437e7240e126e.html`
   (old-domain verification file).

### 3.5 Compatibility Conclusion

| Dimension | Conflict? | Notes |
|---|---|---|
| CSS styling | Essentially none | Page document isolation; only coordinate if NTU mandates a unified header injection |
| JS/libraries | None | Self-hosted on both sides, mutually independent |
| URL/paths | **Yes** | baseURL trailing slash + ~15 hardcoded root-relative paths in content must be fixed |
| Brand style | Optional | Can override theme color to NTU red via site.css (not mandatory) |
| Privacy compliance | **Yes** | Remove GA, replace the Impressum link, follow PDPA |
| Operations | Process issue | NTU must provision the sub-path; static files uploaded via SFTP/rsync |

Overall verdict: **no fundamental conflicts; this is a "controllable path-level migration"**.
The core workload is (a) changing baseURL, (b) fixing hardcoded root-relative paths,
(c) compliance cleanup (GA/Impressum).

---

## 4. Appendix: Applied Fix Diff

[config.toml](config.toml:9):

```diff
-disableKinds = ["taxonomy"]
+disableKinds = ["taxonomy", "term"]
+ignoreErrors = ["error-disable-taxonomy"]
```

Post-fix verification: `hugo server` builds with 0 errors, `http://localhost:1313/` returns 200,
static assets (bootstrap/fontawesome/theme.css/site.css) all return 200.

---

## 5. Reference File Index

- Build configuration: [config.toml](config.toml)
- Build script: [rebuild.sh](rebuild.sh)
- Theme base template: [themes/conference/layouts/_default/baseof.html](themes/conference/layouts/_default/baseof.html)
- Navigation/banner: [themes/conference/layouts/partials/header.html](themes/conference/layouts/partials/header.html)
- Footer (with the hardcoded Impressum): [themes/conference/layouts/partials/footer.html](themes/conference/layouts/partials/footer.html)
- Site-level style overrides: [static/css/site.css](static/css/site.css)
- GA injection: [layouts/partials/analytics.html](layouts/partials/analytics.html)

---

## 6. Role Assignments: Content Engineer vs Infra/Deployment Engineer

> **The checklists are split into standalone files**:
> - Content Engineer items: [content_work.md](content_work.md)
> - Infra/Deployment Engineer items: [support_work.md](support_work.md)
>
> The subsections below are the original analysis summary; treat the two standalone files as authoritative.

### 6.1 Content Engineer Items (Summary)

Scope: `content/` directory, visual assets in `static/images/`, menu copy. Details in
[content_work.md](content_work.md).

- Branding & homepage (`_index.md`, edition, host)
- Sidebar modules (important-dates, hosts, sponsors, follow-us)
- Call for series (edition/year/dates, HotCRP hide decision)
- Attending series (venue/hotels/attractions/visa → Singapore; hide Registration/grants)
- Program/Workshop/Organization → placeholders or hidden, 2027 committee
- Visual assets (banner/logo/headshots)

### 6.2 Infra/Deployment Engineer Items (Summary)

Scope: build configuration, theme templates, static asset infrastructure, server deployment,
compliance. Details in [support_work.md](support_work.md).

- Build fix (completed: disableKinds + ignoreErrors)
- baseURL switch (trailing slash mandatory) and menu structure
- Theme template hardcoded fixes (Impressum, copyright year)
- Site-wide root-relative path batch fix (§3.2.3 checklist)
- Static asset & compliance cleanup (GA, verification file, proceedings/talks, iconify self-hosting)
- NTU server-side coordination (path provisioning, IIS behavior, CSP, upload, go-live audit)

### 6.3 Cross-Role Interface Conventions

1. **Path convention**: all new `<img src>` / `<a href>` must never start with a leading `/`;
2. **Single ownership of `config.toml`**: only the Infra Engineer changes it (baseURL/menu structure);
3. **File-rename sync**: WiseML 2027 page renames must sync the menu URL, executed by the
   Infra Engineer;
4. **Suggested order**: content work (content_work.md) → infra fixes (support_work.md B/C/D)
   → integration preview → NTU path provisioning → go-live audit (support_work.md E).
