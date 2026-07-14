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

## Verdict
PASS (after fixes)
