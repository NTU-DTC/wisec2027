# Content Engineer Work Checklist

> **Scope**: `content/` directory, visual assets in `static/images/`, page copy.
> **Do NOT touch**: baseURL/build-related items in `config.toml`, theme templates (`themes/`), deployment scripts.
> Background: ACM WiSec 2027 website (forked from WiSec 2026), deployment target
> https://www.ntu.edu.sg/dtc/wisec2027 , hosted by NTU Singapore (DTC).
> **Out of scope**: paper submission and payment related requirements.
> Full background: [analysis.md](analysis.md); path conventions: [README.md](README.md) "Content Editing Conventions".

**Core convention: all new `<img src>` / `<a href>` must never start with a leading `/`**
(write `images/foo.png`, `program-keynote-speakers/` — not `/images/foo.png`).

---

## A. Branding & Homepage (P0)

- [ ] [content/_index.md](content/_index.md)
  - `ACM WiSec 2026` → `ACM WiSec 2027`, edition `19<sup>th</sup>` → `20<sup>th</sup>`;
  - Host description: CISPA Helmholtz Center → NTU Singapore (Digital Trust Centre, DTC);
  - `Registration` paragraph: change to "Registration will open in ..." placeholder before 2027 registration opens;
  - `Travel Grants for PhD-Holders` paragraph: remove or rewrite (DFG funding is specific to the 2026 German host);
  - `Call for Papers` paragraph: rewrite the red callout per ACM's latest Open Access policy wording;
  - Add to the top of the `Past conferences` list:
    `- WiSec 2026 / WiseML 2026` (linking to https://wisec26.events.cispa.de/ ).

## B. Sidebar Modules (P0 — shown on every page)

- [ ] [content/sidebar/important-dates.md](content/sidebar/important-dates.md)
  - All 2026 submission cycles → TBA placeholders (until 2027 dates are set);
- [ ] [content/sidebar/hosts.md](content/sidebar/hosts.md)
  - CCS Saarbrücken `sponsor-logo` → NTU/DTC logo
    (requires new `static/images/logos/ntu.png`, `dtc.png`, see item F);
- [ ] [content/sidebar/sponsors.md](content/sidebar/sponsors.md)
  - Clear 2026 sponsors (ACM/SIGSAC may stay, DFG/CISPA removed), set "Sponsorship opportunities coming soon";
- [ ] [content/sidebar/follow-us.md](content/sidebar/follow-us.md)
  - Fix hardcoded `href="/livestreams"` → relative path or remove (no livestream for 2027 yet);
- [ ] [content/sidebar/important-dates-past.md](content/sidebar/important-dates-past.md)
  - Confirm it stays hidden (the 2026 version already uses menu/hidden control).

## C. Call for Series (P1)

- [ ] [content/call-for-papers.md](content/call-for-papers.md)
  - Edition `19th` → `20th`, year 2026 → 2027, Important dates updated or TBA;
  - Submission Guidelines subsection (HotCRP links `wisec26.hotcrp.com` etc.):
    since submission is out of scope — hide the subsection or set links to TBA;
  - "Important update on ACM's new open access publishing model for 2026" section:
    rewrite for 2027 applicability or remove;
- [ ] [content/call-for-artifacts.md](content/call-for-artifacts.md): update year/dates or set "coming soon";
- [ ] [content/call-for-posters-and-demos.md](content/call-for-posters-and-demos.md): same;
- [ ] [content/call-for-workshops.md](content/call-for-workshops.md): same.

## D. Attending Series (P1)

- [ ] [content/attending-venue.md](content/attending-venue.md)
  - Saarbrücken Congresshalle → NTU Singapore venue (TBA placeholder until the 2027 venue is set);
  - Travel guide (Frankfurt/SCN airports, DB trains) → Singapore (Changi Airport, MRT) full rewrite;
  - Hardcoded `src="/images/ccs_small.png"` → relative path (or remove with the venue replacement);
- [ ] [content/attending-Area-Hotels.md](content/attending-Area-Hotels.md)
  - Saarbrücken hotel list → Singapore hotel list (or TBA placeholder);
- [ ] [content/attending-dcAttractions.md](content/attending-dcAttractions.md)
  - Currently Seoul attractions, and its images reference `/images/travel/*.png` which never existed
    (broken links) — replace with Singapore attractions or delete the page;
- [ ] [content/attending-visas.md](content/attending-visas.md)
  - German visa requirements → Singapore entry/visa requirements (most attendees are visa-exempt;
    state ICA requirements);
- [ ] [content/attending-Registration.md](content/attending-Registration.md) (payment scope)
  - **Payment is explicitly out of scope**: set `hidden = true` in the front matter,
    or replace with "Registration details coming soon";
- [ ] [content/attending-grants.md](content/attending-grants.md)
  - DFG-related → set `hidden = true` or delete (2027 grant policy undecided).

## E. Program / Workshop / Organization (P2)

- [ ] Full Program series (2026 conference artifacts; clear to placeholders or `hidden = true`):
  - [content/program-accepted-papers.md](content/program-accepted-papers.md)
  - [content/program-at-a-glance.md](content/program-at-a-glance.md) (contains several hardcoded
    `href="/program-keynote-speakers/"` etc. → convert to relative paths or clear with placeholders)
  - [content/program-detailed-program.md](content/program-detailed-program.md)
  - [content/program-keynote-speakers.md](content/program-keynote-speakers.md)
  - [content/program-vision-talks.md](content/program-vision-talks.md)
  - [content/program-banquet.md](content/program-banquet.md)
  - [content/best-paper-award.md](content/best-paper-award.md)
- [ ] WiseML Workshop pages (convert to 2027 or placeholders; **renaming files requires a change
    request to the Infra Engineer to sync config.toml menu URLs — do NOT rename yourself**):
  - [content/workshop-wiseml-2026.md](content/workshop-wiseml-2026.md)
  - [content/workshop-wiseml-2026-keynote-speaker.md](content/workshop-wiseml-2026-keynote-speaker.md)
  - [content/workshop-wiseml-program.md](content/workshop-wiseml-program.md)
- [ ] Organization pages:
  - [content/organization-committee.md](content/organization-committee.md)
    - 2027 committee roster (replace all General Chairs/Program Chairs/... entries);
    - Mailing lists `wisec26-general-chairs@googlegroups.com` etc. → `wisec27-*`;
    - Add headshots for new members in `static/images/persons/`; clean up photos of outgoing members;
  - [content/organizations-program-committee.md](content/organizations-program-committee.md):
    2026 PC roster → clear to placeholder (PC not yet determined);
  - [content/organizations-steering-committee.md](content/organizations-steering-committee.md):
    steering committee is usually stable year-over-year; verify and update.
- [ ] [content/sponsorship.md](content/sponsorship.md)
  - 2026 sponsors (GMU/Virginia Tech/CCI etc.) → 2027 sponsorship framework + placeholders;
  - Fix hardcoded `src="/images/logos/..."` → relative paths.

## F. Visual Assets (P1)

- [ ] `static/images/`:
  - Add Singapore/NTU-themed banner images (1200px wide, referenced by `banner_images` in `config.toml`);
  - Add NTU logo, DTC logo (`static/images/logos/ntu.png`, `dtc.png`);
  - Add 2027 committee member headshots (`static/images/persons/`);
  - Remove 2026 award photos `static/images/best-papers/*.jpg` (along with best-paper-award page reset);
  - Clean up no-longer-referenced old images (saarschleife.png, sb2.jpg, ccs_small.png etc.,
    along with banner/venue updates).

---

## Suggested Execution Order

1. B Sidebar (first — visible on every page) → 2. A Homepage → 3. F banner/logo assets →
4. C Call for → 5. D Attending → 6. E Program/Workshop/Organization.

## Items Requiring Infra Engineer Support (submit change requests)

- `config.toml` menu copy/structure changes, WiseML page renames + menu URL sync;
- Hiding/adjusting the `Program` menu entry ([config.toml:26](config.toml:26)).
