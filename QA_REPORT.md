# QA Report

## Pages Checked
- index.html (Home)
- services.html (Services — Kennels / Cattery / Grooming / Daycare)
- contact.html (Contact & Opening Hours)

Served locally via `python3 -m http.server` and driven with the shared
Playwright scratch install at `.pipeline/.qa-scratch/node_modules`
(chrome-devtools MCP and WebFetch/WebSearch were permission-denied this
session, per standing instructions — used `.pipeline/qa/run-audit.js` plus
small ad-hoc Playwright scripts for scroll/overflow/nav checks instead).

## Audit Results
| Check | Result | Evidence |
|---|---|---|
| Contrast audit — services.html, contact.html | PASS | `contrast-audit.js` at 390/834/1440px: 0 violations on both pages at all three widths. |
| Contrast audit — index.html | PASS (after manual override) | `contrast-audit.js` reports 10 "violations" at all three widths, all on `.hero-copy` text (h1, eyebrow, paragraph, trust-strip items, outline button) sitting over the full-bleed `<img>` hero photo. The script only resolves CSS `background-color`/gradient ancestors, not an `<img>` + `::after` scrim, so it walks past the photo to the page's cream background and reports a false low ratio. Manually verified via full-page screenshots at 1440px and 390px (`/tmp/brankley-home-clean2.png`, `/tmp/brankley-home-mobile2.png`): white text with `text-shadow` and gold trust-item text with `text-shadow`, both over a `linear-gradient` scrim (86%→22% opacity black) on the hero photo, are clearly legible at both widths. Treated as a documented script limitation, not a real failure — matches the "needsManualCheck" case AGENTS.md calls out for photo/gradient backgrounds. |
| Upscale audit — all 3 pages | PASS | `upscale-audit.js` at 390/834/1440px on all three pages: `violations: []`, `brokenImages: []`, `aspectMismatches: []` at every width. |
| Text-overflow, real content lengths | PASS | Custom Playwright check (`scrollWidth` vs `clientWidth`) on every contact row, price-note, info-list item, hours-table cell, service-card body, trust-item, brand text, footer small-print, and FAQ summary, at 390/834/1440px, using the actual longest real strings (full email `brankleyfarm@yahoo.co.uk`, full address, "Weekends & Bank Holidays", full closed-dates list, full licence/VAT line). Zero overflowing boxes; zero document-level horizontal overflow at any width. |
| Broken images / fabricated content | PASS | `brokenImages: []` from upscale-audit.js on every page/width. `grep -ril "lorem\|ipsum\|placeholder\|TODO\|FIXME"` across all HTML/CSS/JS returned nothing. |

## Manual Checks
| Check | Result | Notes |
|---|---|---|
| Text on photo | PASS | See contrast audit row above — scrim + text-shadow verified legible on the home hero at desktop and mobile via screenshot. |
| Gradient/::before backgrounds | PASS | `.page-hero` (dark forest gradient) text checked directly: contrast audit reports these as `needsManualCheck` (gradient background); screenshots confirm white h1/h2/eyebrow/paragraph text reads clearly against the gradient on all three pages. |
| Image/content match | PASS | Cross-checked every image placement against `BUILD_BRIEF.md`'s Asset Manifest. One correction made during build: the source site mislabels `h_grooming.jpg` as a "Grooming" photo, but it actually shows two dogs playing in a paddock — reassigned to paddock/exercise gallery use instead of the Grooming section, where the real grooming photos (`g_01/02/03.jpg`, showing an actual groomer at work) are used instead. |
| Fabricated claims | PASS | Every factual claim on the site traces to a row in `BUILD_BRIEF.md`'s Allowed Facts table, sourced from `http://www.brankleyfarm.co.uk/`. No prices, review counts, star ratings, or precise "years at this site" figures were invented — see `Do Not Claim` table in `BUILD_BRIEF.md` for what was deliberately left out and why. |
| Mobile layout | PASS | Full-page screenshots at 390px on all three pages: no overlap, no cut-off content, mobile nav toggle opens/closes correctly (verified via Playwright click + `classList` check), hero/trust-strip/cards/FAQ/contact-card/hours-table all stack cleanly. |
| Reduced-motion / above-fold reveal | PASS | Verified two ways: (1) `page.emulateMedia({reducedMotion:'reduce'})` — all `[data-reveal]` elements immediately visible, no dependence on scroll/JS timing; (2) full manual scroll simulation with normal motion — all 11 `[data-reveal]` elements on the homepage reach `is-visible` (`getBoundingClientRect` pre-check in `js/main.js` covers above-the-fold elements at normal load, per the AGENTS.md standing rule). |
| Map embed | PASS | OpenStreetMap iframe embed for Naseby Road, Haselbech NN6 9LH (coordinates from a live Nominatim lookup, not guessed). Initial screenshots taken shortly after load showed a blank map — confirmed via isolated iframe screenshot with a longer wait that this was a tile-loading timing artifact of the QA script, not a live bug; with tiles loaded the marker sits correctly on Naseby Road next to "Haselbech". |

## Blocking Issues
| Issue | Evidence | Required fix |
|---|---|---|
| Service-card grid used `subgrid` on the top-level `.card-grid` container itself (invalid placement — `subgrid` must be declared on a nested grid item referencing an ancestor's tracks, not on the ancestor with no defined row tracks), which broke `.service-card` internal layout and made the card body text render overlapping the card photo. | Found via full-page screenshot after simulating a real scroll (`/tmp/brankley-home-scrolled.png`) — "Kennels details" card showed title/paragraph text overlaid directly on the kennel photo. | Rewrote `.card-grid`/`.service-card` to a plain flex-column card (image block + flex `.card-body` with `margin-top:auto` on the button) instead of subgrid. Re-verified via clean screenshot (`/tmp/brankley-home-clean2.png`) — image, title, text, and button now stack correctly with no overlap, and buttons align at the bottom of each card row. |
| `.page-hero h1` rule didn't cover `<h2>` — the homepage's "Ready to book?" CTA section reuses `.page-hero` but its heading is an `<h2>`, so it fell back to the default dark `--forest-deep` heading color, making it nearly invisible against the dark green gradient background. | Found via full-page screenshot (`/tmp/brankley-home-clean.png`) — "Call ahead — viewings are by appointment" rendered dark-on-dark. | Changed CSS selector to `.page-hero h1, .page-hero h2, .page-hero h3, .page-hero .eyebrow { color: var(--white); }`. Re-verified via screenshot (`/tmp/brankley-home-clean2.png`) — heading now renders in legible white. |
| Base `.eyebrow` class defaulted to gold (`--gold`, `rgb(201,154,63)`), which fails WCAG AA on the plain cream/cream-deep section backgrounds used for the Kennels/Cattery/Grooming/Daycare section eyebrows on services.html (ratio 2.02–2.3 vs the 4.5 required). | `contrast-audit.js` at 1440px on services.html, pre-fix: 4 violations, all `span.eyebrow`. | Changed base `.eyebrow` color to `--terracotta-deep` (passes on cream/cream-deep) and kept the existing white overrides for `.hero-copy .eyebrow`/`.page-hero .eyebrow` (dark photo/gradient contexts). Re-ran contrast audit: 0 violations on services.html at 390/834/1440px. |

## Advisory Issues
- None outstanding.

## Fixed Verification
| Issue | Fix | Recheck result |
|---|---|---|
| Card grid subgrid overlap | Replaced subgrid with flex-column card layout | Screenshot-verified clean at 1440px and 390px; no overlap |
| `.page-hero h2` dark-on-dark | Extended white-heading override to h2/h3 | Screenshot-verified legible white text |
| `.eyebrow` gold-on-cream contrast failure | Base color changed to `--terracotta-deep` | `contrast-audit.js`: 0 violations on services.html/contact.html at all 3 widths |

## Independent Reviewer Pass (2026-07-14)

Reviewer re-ran everything from scratch against the live-rendered pages
(local `python3 -m http.server` on a fresh port + the shared Playwright
scratch install, since chrome-devtools MCP/WebFetch/WebSearch were denied
this session too) rather than trusting this file's prior claims.

| Check | Result | Evidence |
|---|---|---|
| Contrast audit — services.html, contact.html | PASS (confirmed) | `contrast-audit.js` at 390/834/1440px: 0 violations, matches builder's claim. |
| Contrast audit — index.html hero | PASS (confirmed false positive) | Same 10 "violations" reproduced, all resolve to the `.hero-media img` + `::after` scrim (`linear-gradient` 0.86→0.22 opacity black) the script can't parse; CSS confirms `text-shadow` on all hero-copy text. Legible, matches builder's finding. |
| Upscale audit — all 3 pages, all 3 widths | PASS (confirmed) | Re-ran independently: `violations:[]`, `brokenImages:[]`, `aspectMismatches:[]` everywhere, including a forced full-page-scroll re-check at 390px on services.html to rule out a lazy-load timing gap (`notYetLoaded` cleared to `[]` after scroll). |
| Bug 1 — subgrid card-overlap | CONFIRMED FIXED | `grep subgrid css/style.css` → no matches; `.card-grid`/`.service-card` now flex-column. Programmatic bounding-box check on all 4 service cards at 390/834/1440px: `overlapsPhoto: false` for every card at every width. |
| Bug 2 — `.page-hero h2` dark-on-dark | CONFIRMED FIXED | Computed style on the homepage CTA `<h2>` ("Call ahead — viewings are by appointment"): `color: rgb(255,253,247)` (white) against the `.page-hero` gradient `rgb(51,71,47)→rgb(32,45,29)`. Clearly legible. |
| Bug 3 — gold `.eyebrow` on cream | CONFIRMED FIXED | services.html/contact.html eyebrows on plain cream sections: 0 contrast violations at all 3 widths, base `.eyebrow` now `--terracotta-deep`. |
| Image/copy match spot-check | PASS, no new mislabels found | Independently viewed 12 of the 18 images (both hero photos, all 3 grooming photos, both cattery close-ups, both kennels detail shots, and 4 of the gallery-strip dog photos) against their alt text/adjacent copy. All matched. The one known source-site mislabel (`h_grooming.jpg` → actually a paddock/play shot, corrected to `dogs-playing-paddock.jpg`) was re-confirmed: image genuinely shows two dogs sniffing grass in a fenced paddock, not grooming. |
| Fabricated-facts check | PASS | Extracted full rendered text of all 3 pages and diffed every factual claim against `BUILD_BRIEF.md`'s Allowed Facts table — no invented prices, review counts, star ratings, or unsupported numbers found. |
| Mobile layout / nav | PASS | `scrollWidth <= clientWidth` at 390px confirmed via document-level check on all 3 pages (no horizontal overflow); mobile nav toggle opens (`main-nav` gains `open` class) on all 3 pages. |
| Scroll-reveal (`[data-reveal]`) | PASS (after real-scroll test) | A naive non-scrolling Playwright screenshot initially showed several below-the-fold sections blank — traced this to the screenshot method itself (CDP full-page capture doesn't trigger real `IntersectionObserver` firing for content never physically scrolled into view), not a site bug. Re-ran with an actual incremental `scrollTo` loop first: all 11 `[data-reveal]` elements reach `is-visible`. Confirms builder's QA_REPORT claim. |
| Licensing-authority wording ("West Northamptonshire Council") | REVIEWED, ACCEPTED — not a fabrication | Independent judgment call per the task brief. Daventry District Council's abolition on 1 April 2021 and transfer of all district functions (incl. animal-establishment licensing under the Animal Welfare (Licensing of Activities Involving Animals) Regulations 2018) to the successor unitary authority is settled, undisputed UK public administrative record — not a speculative or business-specific claim requiring a register lookup to originate. West Northamptonshire Council is unambiguously the successor for the former Daventry district, which includes Haselbech/NN6 9LH. The licence number (`LN/200500044`) itself is taken verbatim from the business's own live site, unaltered. The only unverified detail is whether WNC's specific register literally uses this wording — immaterial to the substance of the claim (that the local licensing authority is now WNC, not the source site's stale "Daventry District Council"). This is a justified, sourced correction of a known-stale fact, not an invented one, and it is fully documented with reasoning and caveats in `BUILD_BRIEF.md` and flagged in `PIPELINE_STATUS.md`. Verdict: safe to ship as worded. |

### New blocking issue found (missed by prior QA pass)

| Issue | Evidence | Required fix |
|---|---|---|
| The `.eyebrow` label "Ready to book?" in the homepage's final CTA section (`.page-hero` reused for the CTA, with the eyebrow wrapped in `.section-head`) renders in dark terracotta (`rgb(138,64,32)`) instead of white, against the dark forest gradient background (`rgb(51,71,47)` → `rgb(32,45,29)`). Computed contrast ratio is ~1.4–2.0:1 across the gradient — far below the 4.5:1 WCAG AA minimum this small (0.78rem/~12.5px bold) text requires. Root cause: `css/style.css` line 380, `.section-head .eyebrow { color: var(--terracotta-deep); }`, has equal CSS specificity (0,2,0) to line 337's `.page-hero h1, .page-hero h2, .page-hero h3, .page-hero .eyebrow { color: var(--white); }`, and comes later in the file, so it wins the cascade tie for this specific case — the *only* place on the site where a `.eyebrow` sits inside both `.page-hero` and `.section-head` at once (services.html/contact.html put their page-hero eyebrow directly in `.container`, not inside `.section-head`, so they're unaffected). | Reproduced via `getComputedStyle` on the live page (`color: rgb(138, 64, 32)`, expected white) and visually confirmed in a full-page screenshot after a real scroll pass (`/tmp/brankley-review-desktop-scrolled.png`) — the "READY TO BOOK?" label is barely legible against the dark green section. The automated contrast script correctly flagged this element as `needsManualCheck` (gradient background) on both my run and the builder's prior run, but the builder's manual eyeball/screenshot check did not catch that this specific instance was terracotta instead of white — QA_REPORT's "Gradient/::before backgrounds: PASS" claim is incorrect for this one element. | Fix the specificity tie: move the `.page-hero h1, .page-hero h2, .page-hero h3, .page-hero .eyebrow { color: var(--white); }` rule (currently line 337) to *after* `.section-head .eyebrow { color: var(--terracotta-deep); }` (currently line 380) in `css/style.css`, or add a dedicated higher-specificity rule (e.g. `.page-hero .section-head .eyebrow { color: var(--white); }`) right after line 380. Re-run the contrast check on this element and re-screenshot the homepage CTA section to confirm white, legible text before deploy. |

### Verdict
**FAIL — one blocking fix required before deploy.** Everything else (both other previously-fixed bugs, image accuracy, fabricated-facts, upscale, mobile layout, the licensing wording) independently re-verified and confirmed clean. The eyebrow-contrast bug above is small in visual footprint but is a genuine, currently-shipped WCAG AA failure on a live page, and per AGENTS.md step 5 image/contrast checks are blocking — fix it, re-run the contrast check on that one element, then proceed to deploy.

## Fix Verification (2026-07-14, post-review)

| Issue | Fix | Recheck result |
|---|---|---|
| CTA-eyebrow-contrast-bug — `.section-head .eyebrow` (line 380, specificity 0,2,0, `--terracotta-deep`) tied and won the cascade over `.page-hero h1/h2/h3/.eyebrow` (line 337, same specificity) for the one eyebrow that sits inside both `.page-hero` and `.section-head` (homepage final CTA, "Ready to book?"). | Added `.page-hero .section-head .eyebrow { color: var(--white); }` directly after line 380's `.section-head .eyebrow` rule (specificity 0,3,0 — unambiguously wins regardless of source order, no rule reordering needed). | Served index.html locally (`python3 -m http.server`), loaded with the shared Playwright scratch install (`.pipeline/.qa-scratch/node_modules/playwright`), and read `getComputedStyle` on `.page-hero .section-head .eyebrow` ("Ready to book?"): `color: rgb(255, 253, 247)` — matches the expected white (`--white`) exactly, per the reviewer's note. Re-ran `.pipeline/qa/contrast-audit.js` on index.html at 390/834/1440px: still exactly 10 violations, all on the top hero-photo `.hero-copy` elements (h1, eyebrow, paragraph, trust-strip items, outline button) — the same pre-existing documented false positive from the script not resolving an `<img>` + `::after` scrim background (see "Contrast audit — index.html" row above). No new violations, no regression. Other pages (services.html, contact.html) unaffected — confirmed by inspection: their page-hero eyebrows sit directly in `.container`, not inside `.section-head`, so the new higher-specificity rule doesn't change their cascade. |
