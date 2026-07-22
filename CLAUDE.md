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
- Two setups (Current / New) with live inputs — no "Calculate" button.
- Live sliders on New: rim width, offset (ET), profile.
- Verdict readout: poke change, inner clearance, ride height, fitment badge
  (Mild/Moderate/Aggressive/Extreme by |poke delta|).
- Cross-section diagram (SVG, looking along the axle): both wheels overlaid,
  dimension lines, a faint coilover (spring → shock → lower eye) + hub/brake, tread ticks.
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
- Type: **quiet system-sans site title** ("Wheel Fitment Calculator" + a small wheel
  glyph — de-branded from the old condensed-uppercase STANCE wordmark, 2026-07-22);
  condensed uppercase for section labels/badges; **monospace tabular** for all numbers;
  system sans for UI. Fonts are system stacks (Artifact CSP blocks font CDNs).

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
- [ ] Analytics: GoatCounter snippet is in `index.html` (commented out) — sign up, add site code, uncomment. Plausible is an alternative.
- [ ] Optional: make the share link remember the metric/imperial choice.
- [ ] Fitment verdict thresholds are a placeholder heuristic; a real "will it rub"
      needs fender/suspension data (the data-moat problem).
- [~] Visualizer endgame ("upload a photo of your car") — **shelved** by owner decision (2026-07-22); scope is deliberately just the calculator. Don't resume without a fresh ask.
