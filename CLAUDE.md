# STANCE — project context

A live alloy wheel & tyre fitment calculator. Single self-contained `index.html`
(HTML + inline CSS + inline JS, no build step, no dependencies). Runs by opening
the file in a browser.

**Origin:** built to be a better-looking, more useful version of
`willtheyfit.com` (Driftworks' fitment calculator). Long-term product idea is a
wheel *visualizer* ("see these wheels on my car"); this calculator is the first
shippable piece and the geometry engine the visualizer will reuse.

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
- Type: condensed uppercase for wordmark/labels, **monospace tabular** for all
  numbers, system sans for UI. Fonts are system stacks (Artifact CSP blocks font CDNs).

## Deploy
- GitHub: https://github.com/smnmnssn/stance.git  (branch `main`)
- Static site — no build. Host = drag folder to Netlify, or connect the GitHub
  repo to Netlify/Cloudflare Pages/Vercel for auto-deploy on push (publish dir = root).

## Deploy checklist — MANUAL STEP REQUIRED
Before/after first deploy, find-and-replace the token **`REPLACE-WITH-YOUR-DOMAIN`**
across the repo (`index.html`, `robots.txt`, `sitemap.xml`) with the real domain
(no trailing slash, e.g. `stance.example.com`). Until then, canonical/OG/sitemap
URLs are placeholders.

## Roadmap / open items
- [x] Favicon (`favicon.svg`), `robots.txt`, `sitemap.xml`, OG/Twitter meta added.
- [ ] Replace `REPLACE-WITH-YOUR-DOMAIN` token once the domain is live.
- [ ] Submit the site to Google Search Console after deploy.
- [ ] `og-image.svg` exists; convert to a 1200×630 **PNG** for best social-preview compatibility (some scrapers ignore SVG).
- [ ] Analytics: GoatCounter snippet is in `index.html` (commented out) — sign up, add site code, uncomment. Plausible is an alternative.
- [ ] Optional: make the share link remember the metric/imperial choice.
- [ ] Fitment verdict thresholds are a placeholder heuristic; a real "will it rub"
      needs fender/suspension data (the data-moat problem).
- [ ] Bigger picture: the "upload a photo of your car" visualizer, built on this engine.
