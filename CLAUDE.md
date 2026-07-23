# STANCE — project context

A live alloy wheel & tire fitment calculator. Single self-contained `index.html`
(HTML + inline CSS + inline JS, no build step, no dependencies). Runs by opening
the file in a browser.

**Origin:** built to be a better-looking, more useful version of
`willtheyfit.com` (Driftworks' fitment calculator).

**Naming / direction (updated 2026-07-22):** the public site is now plainly named
**"Wheel Fitment Calculator"** (matches the domain) — the old flashy "STANCE"
wordmark was **removed on purpose**. "stance" survives only as the repo/folder name
and codename. The earlier wheel-*visualizer* endgame ("see these wheels on my car")
is **shelved by owner decision**: scope is deliberately just this calculator, kept
utilitarian rather than a brand. Don't reintroduce STANCE branding or the visualizer
without a fresh ask.

## Current state (done)
- Two setups (Old / New) with live inputs — no "Calculate" button.
- Live compact slider under every New-setup input (Ø, width, ET, tyre, profile).
- Output order: graph (cross-section) is the top block, then the verdict readout.
- Verdict readout: poke change, inner clearance, ride height, fitment badge
  (Mild/Moderate/Aggressive/Extreme by |poke delta|).
- Cross-section diagram (SVG, looking along the axle): both wheels overlaid — the **new**
  wheel has a gradient tire fill + one subtle rim outline + tread; the **old**
  wheel is a plain dashed silhouette (no inner detail — kept deliberately minimal to avoid
  center clutter); ghosted coilover + metal-gradient hub/brake; dimension lines; ROAD label.
  Subtle depth comes from SVG `<defs>` tire/metal gradients (no glow) built from the runtime
  theme colors. `wheel()` still returns the same
  geometry object — don't change the returns or the hover highlights break.
- Results table: Current / New / Δ.
- Glossary ("what the numbers mean") + **hover-to-locate**: hovering a glossary
  term, a table row, or a New-setup input highlights that exact measurement on
  the diagram in the `--hl` gold colour.
- Metric/imperial toggle (mm↔in, km/h↔mph, speed test points 50/100 vs 30/60).
- Light/dark themes (token-based, `prefers-color-scheme` + `data-theme` override).
- Share link (encodes all inputs in the URL hash) + copy-to-clipboard toast.

## SEO & discoverability (done)
- **Domain:** `wheelfitmentcalculator.com` (set in canonical, OG/Twitter, robots,
  sitemap — the `REPLACE-WITH-YOUR-DOMAIN` token is gone).
- **Spelling:** primary voice is **American English** ("tire", "center", "speedometer");
  British "tyre" is woven into the FAQ/prose so the page also ranks for UK/AU searches.
  `lang="en"` stays neutral. NOTE: internal code identifiers keep the `tyre` spelling
  (`id="e-tyre"`, JS keys) on purpose — do **not** rename them; change visible text only.
- **Content for SEO/AEO:** full-width `#how` ("How it works" — every formula written out
  in crawlable text) and `#faq` (10 Q&As via native `<details>`, zero JS) sections below
  the tool. These are the main indexable/citable copy — keep key text in static HTML,
  never move it into JS.
- **Structured data:** JSON-LD `WebApplication` + `FAQPage` (before the main `<script>`).
  FAQPage answer text mirrors the visible FAQ — if you edit one, edit both.
- **Headings:** H1 is the literal keyword "Wheel Fitment Calculator" (the site title
  itself), visible H2s on the setup cards + glossary, `sr-only` H2s on the
  verdict/diagram/results panels for a clean outline.
- **Meta:** keyword-rich `<title>`, `robots` meta (`max-image-preview:large`),
  OG/Twitter with `og-image.png` + width/height/alt.
- **robots.txt:** explicitly allows search + AI/answer-engine crawlers (GPTBot, ClaudeBot,
  PerplexityBot, Google-Extended, etc.) so the page is eligible to be cited.
- **llms.txt:** AEO summary (formulas + terms) at the repo root, per llmstxt.org.

### Regenerating og-image.png
`og-image.png` is a real 1200×630 PNG rasterized from `og-image.svg` with
`@resvg/resvg-js` (NOT a repo dependency — installed in a throwaway scratch dir to keep
the zero-dependency promise). Rebuild it after editing the SVG:
```
npm i @resvg/resvg-js        # in any scratch dir, not the repo
node -e "const{Resvg}=require('@resvg/resvg-js');const fs=require('fs');const p=new Resvg(fs.readFileSync('og-image.svg'),{fitTo:{mode:'width',value:1200}}).render().asPng();fs.writeFileSync('og-image.png',p)"
```

## Content pages (guides) — multi-page since 2026-07-23
The site is no longer a single file. `index.html` is still the self-contained calculator,
but there are now static **guide pages** under `/guides/` for SEO (long-tail search +
internal links + AI citations):
- `wheel-offset-et-explained`, `what-is-wheel-poke`, `tire-size-and-speedometer-error`,
  `how-to-read-a-tire-size`. (A `willtheyfit-alternative` competitor-capture page was
  tried and removed 2026-07-24 — owner didn't want that framing on the site.)
- They share **`/assets/site.css`** (brand tokens + the Space Grotesk font file at
  `/assets/space-grotesk-700.woff2`). The calculator keeps its own inline CSS; the guides
  do NOT reuse the calculator CSS.
- Each guide has its own meta/canonical/OG + `Article` + `BreadcrumbList` JSON-LD, a CTA
  back to the calculator, and cross-links. `index.html` links them from its `#guides`
  section; it also carries `WebSite` + `Organization` JSON-LD.
- **Clean URLs** via `vercel.json` (`cleanUrls: true`) — reference `/guides/slug` with NO
  `.html`. Canonicals + sitemap use the extensionless form.
- **To add a guide:** copy an existing one, swap content + meta + canonical + schema, then
  add it to `sitemap.xml`, the `#guides` links in `index.html`, and the related-links grid
  of the sibling guides.
- **IndexNow** key file: `/7a3f9c2e14b86d05f9a1c3e7b2d4a8b1.txt` (POST changed URLs to
  api.indexnow.org to nudge Bing).

## Writing style rules for site copy (2026-07-24, owner preference)
- **No em dashes ("—") anywhere in on-page copy** (index.html or any /guides page) — owner
  considers it a tell-tale sign of AI-written text. Rewrite with a period, comma, colon, or
  semicolon instead. Middle dots ("·", used for unit separators like "mm · km/h") and math
  minus signs in formulas ("−") are fine; only the em dash is banned. Code comments aren't
  copy, so this doesn't apply there, but keep them dash-free too since it's zero effort.
- **Guide names are Title Case wherever used as a link/label**: nav labels, breadcrumbs,
  related-guide grids, the homepage `#guides` section. Example: "What Is Wheel Poke?", not
  "What is wheel poke?". (The `<h1>`s were already written this way; only the nav-style
  labels needed fixing.) Body prose keeps normal sentence case.
- **Guide page structure order:** prose → `.note` (disclaimer/caveat box) → `.cta` (button
  back to the calculator) → `.related` (Related guides grid). The CTA always sits directly
  above Related guides, never above the note.
- **`.cta` button** (in `assets/site.css`, updated 2026-07-24): deliberately low-key, NOT a
  bright gradient block. A single line of text ending in a `<span class="cta-arrow">→</span>`,
  muted tinted background (`color-mix(... var(--new) 8%, var(--surface-2))`), thin accent
  border, ~half the height of the original design. All 4 guides use the identical copy
  "Compare your new setup to your old one with our free wheel fitment calculator →" — say
  "compare", not "visualize" (the tool shows a labeled diagram, not a photo-real render;
  "visualize" overclaims and belongs to the shelved visualizer idea, see
  [[product-direction]]).
- **Homepage header lockup**: `.brand-lockup` groups the wheel-mark SVG + the stacked
  `.site-title` (one word per line, left-aligned, "Fitment" in `--new`) inline as one unit.
  The `.tagline` sits beside that lockup (not below it) as a second flex child of `.brand`;
  `.brand` wraps on narrow screens so the tagline drops under the lockup instead of
  overflowing. Don't move the tagline back inside a `brand-text` column under the title.
  The small nav-bar name on `/guides/*` pages stays inline (compact utility label, not the
  logo).
- **FAQ → guide cross-links**: where an FAQ question has a directly matching guide, its
  `.faq-a` div ends with `<p class="faq-more">Read more: <a href="...">Title Case
  Name</a></p>` (poke, offset, offset-vs-inset, speedometer, and rolling-diameter-calc
  currently link out). Don't add one where there's no real match; three FAQ questions
  (fit-my-car, is-it-free, metric/imperial) intentionally have none.
- **Known duplicate disclaimer content (flagged, not yet resolved):** the "How it works"
  `.note-inline` in index.html and the `.note` in
  `guides/tire-size-and-speedometer-error.html` both restate the "rolling diameter is the
  standard formula, not tire-stretch-adjusted" point in slightly different words. A third,
  near-identical copy in the footer was removed 2026-07-24. Ask the owner before touching
  the remaining two; they were told about the overlap and haven't decided yet.

## The math — VERIFIED, do not guess or "improve" without data
Reverse-engineered from willtheyfit.com screenshots and matched to the exact
decimals (incl. their purple summary box). `IN = 25.4`.
- **Poke** = (rimWidth_in × 25.4)/2 − ET
- **Inset** = (rimWidth_in × 25.4)/2 + ET
- **Poke delta** = new.poke − old.poke   (purple box "pokes out X more")
- **Inner/strut delta** = old.inset − new.inset ("Y closer to strut")
- **Ride height gain** = (new.D − old.D)/2   (also shown as "arch gap loss")
- **Circumference** = D × π
- **Speedo error %** = (old.C − new.C)/new.C × 100
- **Reads at speed** = speed × old.C / new.C

### Known caveat — rolling diameter is NOT WTF-matched (intentional)
Overall Ø uses the **standard formula**: `D = rim_in×25.4 + 2×(tyre×profile/100)`.
WTF applies an undocumented **tyre-stretch** adjustment (e.g. 225/45 on a 6J
shows 595mm vs the naive 609mm). We could not pin their exact coefficient/design-rim
table from 4 data points, so we deliberately use the documented standard formula
and flag it in the footer. To ever match WTF exactly, run a controlled sweep on
their site (hold tyre fixed, step rim width 6→10J; then step profile) and refit.

## Design system (tokens in `:root`, both themes)
- Ground `#0b0e12` / panels `#141922` / hairline `#28303b` (cool steel bias).
- Semantic split: **`--existing` signal orange `#ff7a33` = current setup**,
  **`--new` electric cyan `#31c8ff` = new setup**. `--hl` gold `#ffcf40` = the
  measurement-highlight colour.
- Verdict semantics separate: `--ok/--warn/--bad`.
- Type: site title in **Space Grotesk 700**, embedded as a base64 woff2 `@font-face` at
  the top of the `<style>` (self-contained — no font CDN request), with the word
  "Fitment" in `--new` for a two-tone pop. Condensed uppercase for section labels/badges;
  **monospace tabular** for all numbers; system sans for UI/body.
- **Branding (2026 redesign):** `favicon.svg` and the header `.brand-mark` are the same
  alloy-wheel mark (faint tire ring + rim + 5 spokes + orange hub center = old/new colors).
  The favicon uses fixed hex on a dark rounded square; the brand-mark uses theme tokens and
  no square (sits on the page bg). `og-image.svg` → `og-image.png` (1200×630) is that wheel
  (with a cyan glow) + the two-tone title. Theme toggle = sun/moon SVGs (`#theme .ic-sun`
  shows in dark → switch to light; `.ic-moon` in light → switch to dark). The favicon
  ships in **multiple formats** for Google Search + browser coverage: `favicon.ico`
  (16/32/48, Google's default fallback), `favicon.svg`, `favicon-96.png`,
  `apple-touch-icon.png` — all rasterized from `favicon.svg` (resvg + png-to-ico). If the
  wheel mark changes, regenerate them.
- **Accessibility (WCAG AA) — do not undo:** light-theme accents (`--new` `#0e7490`,
  `--existing` `#c2410c`, `--ok` `#15803d`, `--warn` `#b45309`, `--bad` `#b91c1c`) are
  intentionally *darker* than the bright dark-theme versions so colored text clears
  4.5:1 on white — don't lighten them back. `--ink-faint` was bumped in both themes
  for the same reason. Every number input has `<label for=…>`; main content is wrapped
  in `<main>`. Fitment badges use a solid `--surface-2` fill (NOT a color tint) so the
  colored badge text stays ≥4.5:1. Lighthouse: Perf/Best-Practices/SEO 100, Accessibility
  fixed to pass.
- **Desktop column widths (2026-07-24) — keep `.grid` and `.content` in sync:** `.grid`
  (setup cards + output) and `.content` (How it works / FAQ / Guides) share the identical
  `grid-template-columns: 290px minmax(0, 1fr)` rule (same selector, one declaration) so
  the output column and the sections below it are always the exact same width — no
  visible "jump" when scrolling from the calculator into the prose sections. The input
  column is 290px (down from 340px, ~15% thinner, freeing that width to the output side).
  The 3 prose `<section>`s live inside `.content-inner` (`grid-column: 2`), which resets to
  `grid-column: 1` at the same ≤860px breakpoint where `.grid`/`.content` collapse to one
  column. If you ever change the 290px value, it only needs to change in one place because
  both grids share the rule — don't split them back into separate declarations.
- **Responsive (mobile) — do not undo:** the grid uses `minmax(0, 1fr)` and grid/flex
  children get `min-width: 0` so the nowrap results table and diagram header can't force
  horizontal **page** overflow — they scroll/wrap inside their own box instead. Mobile
  block at `≤600px` (graph-first order, 2-col verdict, tightened padding); `.input-row
  input` is bumped to 16px on mobile to stop iOS zoom-on-focus. `@media (pointer: coarse)`
  enlarges slider thumbs + tap targets and hides the "hover" glossary hint (meaningless on
  touch). Verified overflow-free 320–414px via headless render.

## Deploy
- GitHub: https://github.com/smnmnssn/stance.git  (branch `main`)
- Static site — no build. Host = drag folder to Netlify, or connect the GitHub
  repo to Netlify/Cloudflare Pages/Vercel for auto-deploy on push (publish dir = root).

## Deploy checklist
- **Domain is set** to `wheelfitmentcalculator.com` across `index.html`, `robots.txt`,
  `sitemap.xml` (canonical, OG/Twitter, sitemap `<loc>`). If the domain ever changes,
  find-and-replace `wheelfitmentcalculator.com` across the repo (no trailing slash).
- After first deploy: submit `sitemap.xml` in **Google Search Console** and verify the
  domain (manual — needs the owner's account).
- Sanity-check the social preview in a card validator once live (OG image is a PNG now).

## Roadmap / open items
- [x] Favicon (`favicon.svg`), `robots.txt`, `sitemap.xml`, OG/Twitter meta added.
- [x] Domain set to `wheelfitmentcalculator.com` (placeholder token removed).
- [x] `og-image.png` — real 1200×630 PNG rendered from the SVG (with width/height/alt meta).
- [x] JSON-LD (`WebApplication` + `FAQPage`), semantic headings, `#how` + `#faq` copy, `llms.txt`, AI-crawler allows in `robots.txt`.
- [x] American-English primary spelling with British "tyre" woven in for UK/AU search.
- [x] De-branded (2026-07-22): removed the STANCE wordmark; site is now plainly "Wheel Fitment Calculator" across the header, `<title>`, OG image and JSON-LD.
- [ ] Submit `sitemap.xml` to Google Search Console after deploy (manual — owner's account).
- [ ] Off-page: backlinks from car forums / Reddit / enthusiast communities (the real ranking lever vs willtheyfit.com).
- [x] Analytics: GoatCounter live at `wheelfitmentcalculator.goatcounter.com` — snippet active at the bottom of `index.html`.
- [x] Accessibility: Lighthouse a11y fixes — associated form labels, `<main>` landmark, WCAG AA contrast (light accents darkened, faint ink bumped). Perf/Best-Practices/SEO already 100.
- [x] Mobile/responsive pass — no horizontal clipping 320–414px, graph-first + 2-col verdict, touch targets, iOS zoom fix.
- [x] Header/branding redesign — embedded Space Grotesk title (two-tone), new alloy-wheel favicon + brand-mark + OG image, sun/moon theme toggle.
- [ ] Optional: make the share link remember the metric/imperial choice.
- [ ] Fitment verdict thresholds are a placeholder heuristic; a real "will it rub"
      needs fender/suspension data (the data-moat problem).
- [~] Visualizer endgame ("upload a photo of your car") — **shelved** by owner decision (2026-07-22); scope is deliberately just the calculator. Don't resume without a fresh ask.
