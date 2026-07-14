# Build Brief

Keep this compact. Add only sourced facts and assets actually used or deliberately rejected.

## Contact
- Email: brankleyfarm@yahoo.co.uk
- Email source URL: http://www.brankleyfarm.co.uk/ (Contact section, `mailto:` link; renders live via curl HTTP)
- Rechecked date: 2026-07-14 (curl HTTP GET, HTTP 200, email present in page body)
- Phone: 01604 743900
- Address: Naseby Road, Haselbech, Northampton, NN6 9LH

## Business State Check
- Status: still open at logged address
- Checked sources:
  - `curl -sk https://brankleyfarm.co.uk/` → connection failure (SSL handshake, exit 35).
  - `curl -I http://brankleyfarm.co.uk/` → HTTP 302 → `http://www.brankleyfarm.co.uk/`.
  - `curl http://www.brankleyfarm.co.uk/` → HTTP 200, full page renders with current contact details, opening hours, licence line, footer copyright "© Brankley Farm 2025".
  - `curl http://www.brankleyfarm.co.uk/robots.txt` → returns the site's own custom 404 template (not a real robots.txt), which itself displays an *older* cached state: "© Brankley Farm 2019" and licence number `18/00010/EHABCM` (vs. the live homepage's `LN/200500044` and "© Brankley Farm 2025"). This confirms the site has been actively updated since 2019 and the 2025-copyright homepage is the current live version, not a stale cache.
  - `curl https://nominatim.openstreetmap.org/search?q=NN6+9LH&format=json` → independently confirms NN6 9LH/Haselbech sits in "West Northamptonshire" per OpenStreetMap's own administrative data, corroborating LEADS.md's council-name claim.
- Notes: Live site's footer text still says "Daventry District Council" for the animal boarding licence, but Daventry District Council was abolished 1 April 2021 and its functions (including animal-establishment licensing) transferred to West Northamptonshire Council — a unitary authority created by the 2021 Northamptonshire local government reorganisation. The site's footer simply hasn't been updated to the new council name; the licence number itself (`LN/200500044`) is displayed on the business's own live page and is newer-format than the old `18/00010/EHABCM` seen in the cached 404 template, indicating the licence record was refreshed after the 2021 reorganisation. Could not locate a West Northamptonshire Council public licence-register page via direct curl (guessed URL paths all 404'd; WebSearch unavailable this session) — the licensing claim is therefore sourced from the business's own site plus independent geographic corroboration (Nominatim), not a council register page directly. Demo copy says "licensed by the local authority (West Northamptonshire Council)" rather than repeating the outdated "Daventry District Council" wording.
- Build decision: proceed

## Page Plan
- Scope: 3-page default
- Pages: index.html (Home), services.html (Services — Kennels/Cattery/Grooming/Daycare), contact.html (Contact & Opening Hours)
- Reason for any extra page: none — single-page source site has exactly four service categories, which fit comfortably as sections on one Services page rather than needing individual pages.

## Pitch Hook
- Verified observation: The business's own site (brankleyfarm.co.uk) is unreachable over HTTPS on both bare and www domains (SSL handshake failure) — most visitors on modern browsers/mobile will see a security warning or fail to connect outright before ever reaching the HTTP fallback. The pitch is "your current site is actively turning visitors away with a broken padlock, even though the underlying content and real farm photos are solid."
- Source URL: https://brankleyfarm.co.uk/ (SSL failure, confirmed via `curl -sk`, exit code 35) vs. http://www.brankleyfarm.co.uk/ (HTTP 200, confirmed working fallback)

## Allowed Facts
| Fact | Source URL | Used where |
|---|---|---|
| Business name "Brankley Farm Kennels & Cattery" | http://www.brankleyfarm.co.uk/ (title, header) | All pages |
| Address: Naseby Road, Haselbech, Northampton, NN6 9LH | http://www.brankleyfarm.co.uk/ (Contact section) | contact.html, footer all pages |
| Phone: 01604 743900 | http://www.brankleyfarm.co.uk/ (Contact section, tel: links) | All pages |
| Email: brankleyfarm@yahoo.co.uk | http://www.brankleyfarm.co.uk/ (Contact section, mailto: link) | All pages |
| Established 2005 at Kelmash Hall, later moved ~2 miles to Brankley Farm | http://www.brankleyfarm.co.uk/ (Home hero paragraph) | index.html |
| Licensed by the local authority; licence no. LN/200500044 | http://www.brankleyfarm.co.uk/ (Home hero paragraph + footer) | index.html, contact.html footer |
| Accredited as a 5-star boarding establishment | http://www.brankleyfarm.co.uk/ (Home hero + Contact section) | index.html, services.html |
| Prices include heating, bedding, administration of medication & their own food; special-diet pets should bring their own food | http://www.brankleyfarm.co.uk/ (Home hero, Daycare, Cattery sections) | services.html |
| Kennels: 1.5-acre secure paddock with wooded area; daily routine — 8am wake + walk + breakfast, midday room clean/check, 3-4pm walk + feed, runs open till 10pm in summer, heat lamps + evening treats in winter | http://www.brankleyfarm.co.uk/ (Kennels section) | services.html |
| Kennels/Cattery T&Cs: dogs on leads on arrival, report to office, valid vaccination certificate with yearly boosters (+ kennel cough for dogs) required; viewings by appointment only | http://www.brankleyfarm.co.uk/ (Kennels, Cattery sections) | services.html |
| Pick-up & drop-off service available on request | http://www.brankleyfarm.co.uk/ (Kennels, Cattery, Grooming, Daycare sections) | services.html |
| Cattery overlooks the farm; cats given breakfast + cuddle, pens cleaned, medication administered, afternoon feed/check, individually heated pens in cold weather | http://www.brankleyfarm.co.uk/ (Cattery section) | services.html |
| Grooming Parlour open to resident & non-resident dogs; services: bath, nail clipping, ear cleaning, styled to breed standard & customer preference, hand stripping, bath+brush+nail clipping, nail-clipping only | http://www.brankleyfarm.co.uk/ (Grooming section) | services.html |
| Daycare: individual kennels with bedding & heating, two walks a day plus feeding, treats offered around feeding time if owner feeds at home | http://www.brankleyfarm.co.uk/ (Daycare section) | services.html |
| Opening hours: Weekdays 8:30-12:00 & 14:00-17:00; Weekends & Bank Holidays 9:00-12:00 & 14:00-16:00; closed Christmas Day, Boxing Day, New Year's Eve & New Year's Day | http://www.brankleyfarm.co.uk/ (Contact section) | contact.html |
| "Grown primarily through recommendation" | http://www.brankleyfarm.co.uk/ (Contact section intro) | index.html |
| VAT registered 448 0713 91 | http://www.brankleyfarm.co.uk/ (footer) | contact.html footer (small print) |

## Do Not Claim
| Claim or uncertainty | Reason |
|---|---|
| "Over 11 years at Brankley Farm" / any precise years-at-this-site figure | Source text's duration claim is relative to an unknown/stale write date (site footer shows a 2019 cached variant elsewhere); can't verify current accuracy of a specific year count. Used only the verifiable fact (established 2005, since relocated to Brankley Farm) without a number. |
| "Daventry District Council" as current licensing authority | Daventry DC was abolished 1 April 2021, merged into West Northamptonshire Council. Site footer text is outdated; used "the local authority (West Northamptonshire Council)" instead, per LEADS.md and independent Nominatim corroboration. |
| Owner's personal name (Mr Duncan Mark Smith) | Publicly on the source site's licence line, but not load-bearing for the pitch; omitted from demo copy for scope, not because it's inaccurate. |
| Any Google/Facebook review count or star rating | No such review count is published anywhere on the business's own site; only the self-described "5 star boarding establishment accreditation" (a formal accreditation, not a review score) is used, worded exactly as sourced. |
| Any social media presence/links | Not verified reachable this session (WebFetch/WebSearch denied; a blind Facebook URL guess returned a redirect, not confirmation) — no social links included. |
| Specific prices for any service | Never published on the source site — every service section there says "for prices please ring." Demo repeats that pattern; no invented numbers. |

## Asset Manifest
| File | Source URL | Native size | License/credit | Watermark checked | Intended section | Copy match |
|---|---|---|---|---|---|---|
| assets/img/hero-farm.jpg (from h_home.jpg) | http://www.brankleyfarm.co.uk/library/h_home.jpg | 1200x700 (recompressed, dimensions unchanged) | Business's own site | yes/no watermark: no watermark found | index.html hero | Farm buildings/yard establishing shot — matches "welcome to Brankley Farm" intro |
| assets/img/hero-cattery.jpg (from h_cattery.jpg) | http://www.brankleyfarm.co.uk/library/h_cattery.jpg | 1200x700 | Business's own site | no watermark | services.html Cattery section | Cat close-up portrait — matches Cattery copy |
| assets/img/dogs-sniffing-grass.jpg (from h_kennels.jpg) | http://www.brankleyfarm.co.uk/library/h_kennels.jpg | 1200x700 | Business's own site | no watermark | services.html Kennels section | Multiple dogs together outdoors — matches Kennels "space to feel at home" copy |
| assets/img/dogs-playing-paddock.jpg (from h_grooming.jpg) | http://www.brankleyfarm.co.uk/library/h_grooming.jpg | 1200x700 | Business's own site | no watermark | index.html gallery / services.html Daycare intro | NOTE: source site labelled this image "Grooming" but it actually shows two dogs playing in a fenced paddock, not a grooming scene — reassigned here to paddock/exercise context to match actual image content, not the original mislabel, per AGENTS.md image/copy-match rule |
| assets/img/golden-retriever-stick.jpg (from h_daycare.jpg) | http://www.brankleyfarm.co.uk/library/h_daycare.jpg | 1200x700 | Business's own site | no watermark | services.html Daycare section hero | Relaxed dog chewing a stick on grass — matches Daycare copy |
| assets/img/kennels-exterior.jpg (from k_01.jpg) | http://www.brankleyfarm.co.uk/library/k_01.jpg | 400x400 | Business's own site | no watermark | services.html Kennels gallery | Brick kennel block exterior — matches Kennels copy |
| assets/img/kennels-corridor.jpg (from k_03.jpg) | http://www.brankleyfarm.co.uk/library/k_03.jpg | 400x400 | Business's own site | no watermark | services.html Kennels gallery | Covered kennel run corridor — matches Kennels copy |
| assets/img/cat-green-bed.jpg (from c_01.jpg) | http://www.brankleyfarm.co.uk/library/c_01.jpg | 400x400 | Business's own site | no watermark | services.html Cattery gallery | Cat in bed — matches Cattery copy (used in same section on source site) |
| assets/img/cat-cattery-pen.jpg (from c_03.jpg) | http://www.brankleyfarm.co.uk/library/c_03.jpg | 400x400 | Business's own site | no watermark | services.html Cattery gallery | Cat inside wooden cattery pen — matches Cattery copy |
| assets/img/grooming-table-black-dog.jpg (from g_01.jpg) | http://www.brankleyfarm.co.uk/library/g_01.jpg | 400x500 | Business's own site | no watermark | services.html Grooming section hero | Groomer working on dog on grooming table — matches Grooming copy |
| assets/img/grooming-bath.jpg (from g_02.jpg) | http://www.brankleyfarm.co.uk/library/g_02.jpg | 400x500 | Business's own site | no watermark | services.html Grooming gallery | Dog grooming bath tub setup — matches Grooming copy |
| assets/img/grooming-brushing.jpg (from g_03.jpg) | http://www.brankleyfarm.co.uk/library/g_03.jpg | 400x500 | Business's own site | no watermark | services.html Grooming gallery | Groomer brushing a dog — matches Grooming copy |
| assets/img/jack-russell-daycare.jpg (from d_01.jpg) | http://www.brankleyfarm.co.uk/library/d_01.jpg | 400x400 | Business's own site | no watermark | services.html Daycare gallery | Happy dog outdoors — matches Daycare copy |
| assets/img/golden-retriever-daycare.jpg (from d_02.jpg) | http://www.brankleyfarm.co.uk/library/d_02.jpg | 400x400 | Business's own site | no watermark | services.html Daycare gallery / index.html service card | Happy dog outdoors — matches Daycare copy |
| assets/img/dogs-playing-daycare.jpg (from d_03.jpg) | http://www.brankleyfarm.co.uk/library/d_03.jpg | 400x400 | Business's own site | no watermark | services.html Daycare gallery | Two dogs playing outdoors — matches Daycare copy |
| assets/img/dog-with-ball.jpg (from k_07.jpg) | http://www.brankleyfarm.co.uk/library/k_07.jpg | 400x400 | Business's own site | no watermark | index.html "Life at the farm" gallery strip | Dog playing with a ball in the exercise field |
| assets/img/yorkie-running.jpg (from k_08.jpg) | http://www.brankleyfarm.co.uk/library/k_08.jpg | 400x400 | Business's own site | no watermark | index.html gallery strip | Small dog running on grass |
| assets/img/dogs-playing-snow.jpg (from k_09.jpg) | http://www.brankleyfarm.co.uk/library/k_09.jpg | 400x400 | Business's own site | no watermark | index.html gallery strip | Dogs playing in snow |

Note on recompression: the five 1200x700 hero images were re-encoded with `sips -s formatOptions 78` to cut file size (~1.3-1.5MB → ~0.3-0.6MB) for page-weight reasons; pixel dimensions were verified unchanged before and after (`sips -g pixelWidth -g pixelHeight`), so this is a JPEG-quality compression pass only, not an upscale or crop.

## Design Notes
- Palette: forest green (`--forest #33472f`) + terracotta/brick (`--terracotta #b1592f`) + warm cream (`--cream #f8f2e2`) + gold accent — distinct from other repos in this workspace (e.g. Christine's Dog Grooming's rose/plum palette), and drawn from the real brick-and-slate kennel buildings and green paddocks in the sourced photos.
- Typography: system serif stack (`Rockwell/Iowan Old Style/Georgia`) for headings (farmhouse-signage feel), system sans-serif for body. No external font CDN — avoids a runtime dependency on fonts.googleapis.com.
- Image layout pattern: wrapper `aspect-ratio` + `object-fit: cover` throughout (card media, gallery strip, detail-photo grids), except the full-bleed hero which sizes the `<img>` itself to `width:100%; height:100%; object-fit:cover` inside an absolutely-positioned wrapper.
- Risk notes: `h_grooming.jpg` mislabeled by the source site (see Asset Manifest) — corrected placement, not copied blindly.

## Builder QA
- Contrast: PASS on services.html/contact.html (0 violations at 390/834/1440px); index.html hero flagged by the script as a false positive (photo background, not a CSS gradient) — manually verified legible via screenshot. Full detail in QA_REPORT.md.
- Upscale mobile: PASS — 0 violations/brokenImages/aspectMismatches at 390px, all 3 pages
- Upscale tablet: PASS — 0 violations/brokenImages/aspectMismatches at 834px, all 3 pages
- Upscale desktop: PASS — 0 violations/brokenImages/aspectMismatches at 1440px, all 3 pages
- Broken images: none found
- Manual checks: see QA_REPORT.md — 2 real bugs found and fixed during build QA (subgrid card-overlap bug, `.page-hero h2` dark-on-dark contrast bug), both re-verified fixed
