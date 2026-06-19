# Submission — FirstMove

## Team

- Team name: FirstMove
- Team members: _<Doga Mentese, Agnıeszka Ambrozewıcz, Alyona Pukhova>_
- GitHub fork URL: _<https://github.com/dogamentese/nova-first-move>_
- Demo URL, if any: https://nova-first-move.lovable.app


## Summary

FirstMove is an opportunity-detection system for retail buyers, shown on the Swiss/DACH outdoor market. Its agent, Nova, turns a set of market signals into one clear answer per trend: where it sits on the trend-maturity curve (early vs. late), whether it will transfer to Switzerland/DACH, and what the buyer should do next. We improve the scoring-and-decision layer: staging trends on a lifecycle curve and validating local transferability before recommending an action — then drafting that first action (supplier shortlist + outreach email).


## Inputs

- Market: Switzerland / DACH (Germany, Austria, Switzerland)
- Category: Outdoor / hiking gear, materials, footwear, apparel
- Sources: 25 market-research signals (Mordor Intelligence, Grand View Research, Market Research Future, Technavio, DataIntelo, et al.) in `data/signals.xlsx`
- External datasets/APIs: `data/signals.xlsx`; optional Anthropic API for live action drafting
- Configurable: market geography, themes, scoring weights, and DACH rules in `engine/config.py`

## Outputs

- **Dashboard**: React + Vite + Tailwind SPA — hero lifecycle curve, morning briefing, ranked cards, opportunity detail, geographic lead/lag strip
- **Structured data**: `web/public/analysis.json` (25 opportunities, full score decomposition, verdicts, drafted actions)
- **API endpoint** (optional): `/api/generate` local middleware for live Claude regeneration

## Ranked Opportunities (real engine output)

25 signals ranked. **4 to act on now · 3 regulation-forced mandatory adopts · 3 rejected (redirected).**

### Act now — Move

| Score | Opportunity | Stage | Verdict | Why |
| --- | --- | --- | --- | --- |
| 89 | Sustainability & Eco-Conscious Materials ★ | market_proof | TRANSFER | EU Single-Use Plastics Directive bans plastic soles from 2027 — regulation-forced |
| 86 | Brand–NGO Sustainability Partnerships ★ | market_proof | TRANSFER | Recycled-material collections; regulatory floor guarantees demand |
| 86 | PFAS-Free Waterproofing ★ | market_proof | TRANSFER | EU PFAS restriction + German Umweltbundesamt pressure — mandatory by 2027 |
| 70 | Hiking-Fashion Crossover ('Gorpcore') | early_adoption | TRANSFER | Proven in South Korea, arriving via DE/FR/IT crossover footwear — **the sweet spot** |

★ = regulation-forced **mandatory adopt**.

### Too late — Watch (representative)

| Score | Opportunity | Stage | Why |
| --- | --- | --- | --- |
| 93 | Ultralight Equipment Dominance | market_proof | ~60% of EU hikers already prefer lightweight gear — you are not early |
| 88 | Modular & Customisable Pack Systems | market_proof | Deuter (DE) already shipping at Outdoor Retailer 2025 |

### Reject — with redirect

| Score | Opportunity | Verdict | Redirect to |
| --- | --- | --- | --- |
| 24 | Desert & Wadi Hiking Gear (UAE/Saudi) | REJECT | Summer Alpine Hydration & Lightweight Cooling Gear |
| 41 | Adventure Tourism Boom in Latin America | REJECT | Premium Technical Gear with Sustainability Story |
| 35 | Adventure Tourism Budget Gear (Mexico) | REJECT | Premium Technical Gear with Sustainability Story |

## Evidence Trail

- Sustainability / EU SUP Directive — https://www.globalgrowthinsights.com/market-reports/hiking-gear-and-equipment-market-106059
- PFAS-free / Umweltbundesamt — https://markwideresearch.com/outdoor-gear-market
- Gorpcore (South Korea) — https://dataintelo.com/report/hiking-gear-market
- Ultralight dominance (~60% EU) — https://www.marketresearchfuture.com/reports/hiking-gear-equipment-market-33349
- Desert & Wadi (rejected) — https://www.verifiedmarketreports.com/product/hiking-market/
- Reference arc: Gorpcore is flagged as the completed diffusion arc (South Korea → EU footwear) and drawn faintly behind the live curve.

## Reusability

Everything market-specific lives in `engine/config.py`: `INPUT_FILE`, `WEIGHTS`, `ADVANCED_MARKETS`, `THEME_KEYWORDS`, `COMMERCIAL_HEURISTICS`, and `DACH_RULES`. To run for another retailer/category, swap `data/signals.xlsx` (same columns), adjust the rules and weights, and re-run the engine — the dashboard consumes the new `analysis.json` unchanged. The lifecycle curve, transferability layer, and action drafting are all category-agnostic.

## Known Limitations

- `observed_at` is coarse (quarters / year ranges) — used only as a "since when" label, so the curve is driven by **geographic diffusion**, not dates.
- No `price` data — the commercial score is a labeled **assumption**, never presented as fact.
- All signals are `web` (market-research reports), so no first-party sales/search data.
- Parts of DACH transferability reasoning use built-in market knowledge rather than the scraped row; these are explicitly marked as reasoning in the UI (honesty over false precision).

