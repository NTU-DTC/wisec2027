# ACM WiSec 2027 Conference Website

Conference website for WiSec 2027, built with Hugo (customized Hugo Conference Theme), forked from the ACM WiSec 2026 website.

- **Conference**: The 20th ACM Conference on Security and Privacy in Wireless and Mobile Networks (ACM WiSec 2027)
- **Host**: Nanyang Technological University Singapore (Digital Trust Centre, DTC)
- **Deployment target**: https://www.ntu.edu.sg/dtc/wisec2027 (sub-path under the NTU main site)
- **Role assignments**: see [content_work.md](content_work.md) (Content Engineer) and [support_work.md](support_work.md) (Infra/Deployment Engineer)
- **Migration analysis report**: see [analysis.md](analysis.md)

# Project Structure

```
.
├── config.toml          # Site configuration (baseURL/title/banner/menus)
├── content/             # Page content (Markdown + HTML)
│   └── sidebar/         # Sidebar modules (shown on every page)
├── layouts/             # Site-level layout overrides (analytics/favicon/shortcodes)
├── themes/conference/   # Hugo theme (customized Hugo Conference Theme)
├── static/              # Static assets (images/css/libs/proceedings)
├── scripts/             # Paper data generation tools (gen-papers.py etc.)
├── rebuild.sh           # Production build script
└── resources/           # Hugo build cache
```

# Requirements

- [Hugo](https://gohugo.io/) (**extended edition**; current environment uses `hugo v0.92.2+extended`;
  the site configuration is already compatible with the taxonomy/term kind split since Hugo 0.73)
- No external database or services needed for local preview; Bootstrap/jQuery/FontAwesome are
  self-hosted under `static/libs/` (injected via the theme at build time)

# Local Development

1. Clone the repository and start the dev server (with hot reload):

	hugo server

2. Visit `http://localhost:1313/`.

	> Note: the local preview path depends on `baseURL` in `config.toml`.
	> While baseURL is a domain root, the site is served from `/`. Once baseURL is changed to the
	> NTU sub-path `https://www.ntu.edu.sg/dtc/wisec2027/`, preview with:
	>
	> 	hugo server --baseURL http://localhost:1313/dtc/wisec2027/ --appendPort=false
	>
	> or simply visit `http://localhost:1313/dtc/wisec2027/`.

3. Local test of the production build (optional):

	hugo

	The generated site goes to the `public/` directory.

# Production Build

Run the build script (wipes `public/` and regenerates):

	./rebuild.sh

The script is equivalent to:

	rm -rf public/ && hugo

# Deployment (NTU Sub-Path)

The target is a sub-path under the NTU main site, `/dtc/wisec2027` (not a dedicated sub-domain),
deployed as **pure static files**.

1. **Prerequisites (Infra/Deployment Engineer)**:
   - `baseURL` in `config.toml` must be `https://www.ntu.edu.sg/dtc/wisec2027/` (**the trailing slash is mandatory**);
   - The path has been requested from the NTU Web/DTC administrators, with the hosting method confirmed
     (IIS virtual directory / static directory / reverse proxy);
   - Confirm IIS default document includes `index.html`, and custom 404 points to `/dtc/wisec2027/404.html`.

2. **Generate and upload**:

	./rebuild.sh
	rsync -chrvP --stats public/ WEB-SERVER:/path/to/ntu/dtc/wisec2027/

	If NTU does not provide rsync/SSH access, upload the full `public/` content via SFTP (WinSCP/FileZilla).

3. **Post-deployment checks**:
   - Homepage and key pages return 200;
   - Full-site link audit (focus: image/page relative links, no URLs like `ntu.edu.sg/images/...`
     that lost the sub-path);
   - Confirm no Google Analytics remnants (required by NTU PDPA compliance).

# Deployment Compatibility Notes (see analysis.md §3)

| Aspect | Conclusion |
|---|---|
| CSS/JS conflicts | Essentially none (page document isolation; Bootstrap/jQuery self-hosted) |
| URL/paths | baseURL trailing slash is mandatory; root-relative paths (`/images/...`) in content must be converted to relative paths |
| Privacy compliance | Remove GA, replace the CISPA Impressum link, follow the NTU PDPA cookie policy |
| HTTPS | Inherits the NTU main site certificate, no extra configuration needed |

# Content Editing Conventions (Important)

- All `<img src>` / `<a href>` in content must **never** start with a leading `/`
  (write `images/foo.png`, `program-keynote-speakers/` — not `/images/foo.png`);
  the templates' `relURL`/`absLangURL` will prepend the baseURL automatically;
- `baseURL`/menu structure in `config.toml` is owned by the Infra/Deployment Engineer;
  Content Engineers should submit change requests for menu copy (see content_work.md / support_work.md).

# History

Build & deployment instructions for the original site (WiSec 2026, Saarbrücken, hosted by CISPA)
are in git history; the `wisec21` path mentioned in the old README is a leftover description
from the earlier (2021 NYUAD) site.
