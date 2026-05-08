# 1Zpresso Q Air — Grinder Research

## TL;DR

- **Hardware spec:** 38 mm heptagonal stainless-steel conical burrs, internal adjustment, 30 clicks/rotation, 10 numbers/rotation (3 clicks/number), ~4 usable rotations (120 clicks total). 1Zpresso cites **25 µm/click** (vertical burr travel). HCG range is 0–1360 µm.
- **HCG back-fit says otherwise.** Solving against `METHOD_MICRON_RANGES` across espresso/moka/AeroPress/V60 gives **~12.5 µm/click** — exactly half the manufacturer figure, consistent with the "vertical travel ≠ effective gap" pattern documented for K-Ultra (20→7.6) and C2 (80→35).
- **Recommendation:** `micronsPerClick: 12.5, zeroOffset: 0, minClick: 0, maxClick: 120, dialNotation: 'numbered'`. No excluded methods. No coarse-end clamping within `METHOD_MICRON_RANGES` (cold max = click 96, within the 120-click range).
- **Dial notation:** `R.N.C` (rotation.number.click-within-number). Rotation = full 360°, number = 0–9 (each = 3 clicks), click-within-number = 0–2. Total clicks = R×30 + N×3 + C. Example: 1.2.0 = 36 clicks.
- **Variants:** Q2 (heptagonal) shares the same mechanism (30 clicks/rotation, 25 µm/click claim, identical HCG ranges). The inner cone geometry differs (Q Air has more aggressive pre-breakers) but the click-to-micron mapping is the same.

## Key findings

### Hardware

- **Burrs:** 38 mm heptagonal conical, 420 stainless steel. "Q-series heptagonal burrs" — 1Zpresso's comparison guide lists 38 mm for Q Air and Q2. Dual-bearing shaft. More aggressive pre-breaker geometry than the Q2.
- **Adjustment:** Internal ring (requires removing the grinds bin to adjust). 30 clicks per rotation, 10 numbers per rotation (3 clicks/number). ~4 full rotations of useful travel = 120 total clicks.
- **Manufacturer µm/click:** 25 µm/click (1Zpresso comparison guide, thecoffeefolk.com). "Every rotation moves the burr by 0.75 mm" per Q-series manual. 0.75 mm ÷ 30 clicks = 25 µm/click. At 120 clicks this implies 3000 µm total — contradicts HCG's 0–1360 µm range and is the standard vertical-travel-vs-gap discrepancy.
- **Zero-point:** Turn adjustment dial clockwise until the crank handle stops spinning freely. The numbered position at the dot is zero; it may not land on `#0`. No factory gap documented — treated as burrs-touching zero.

### HCG Q Air click ranges

Source: <https://honestcoffeeguide.com/1zpresso-q-air-grind-settings/>. Notation decoded as R.N.C → total clicks = R×30 + N×3 + C.

| Method | HCG notation | Total clicks |
|---|---|---|
| Turkish | 0.1.1 – 0.6.1 | 4 – 19 |
| Espresso | 0.5.1 – 1.1.0 | 16 – 33 |
| Moka Pot | 1.0.2 – 1.9.1 | 32 – 58 |
| AeroPress | 0.9.2 – 2.8.0 | 29 – 84 |
| V60 | 1.2.0 – 2.0.1 | 36 – 61 |
| Pour Over | 1.2.1 – 2.7.1 | 37 – 82 |
| Chemex | (not listed; Pour Over range used) | 37 – 82 |
| French Press | 2.0.1 – 3.8.0 | 61 – 114 |
| Cold Brew | 2.3.2 – 4.0.0 | 71 – 120 |

Notes: Identical to the 1Zpresso Q2 (heptagonal) HCG page — same mechanism, same community ranges. No methods excluded by HCG.

### Back-fit µm/click

Truth ranges from `METHOD_MICRON_RANGES` (zeroOffset = 0). Mid-point method: `µm/click = mid(METHOD µm range) / mid(HCG clicks)`.

| Method | µm range | Click range | Mid µm | Mid clicks | Implied µm/click |
|---|---|---|---|---|---|
| Espresso | 210–390 | 16–33 | 300 | 24.5 | **12.2** |
| Moka | 420–720 | 32–58 | 570 | 45.0 | **12.7** |
| AeroPress | 360–1050 | 29–84 | 705 | 56.5 | **12.5** |
| V60 | 450–750 | 36–61 | 600 | 48.5 | **12.4** |
| French | 780–1200 | 61–114 | 990 | 87.5 | 11.3 ← coarse-end divergence |
| Cold | 900–1200 | 71–120 | 1050 | 95.5 | 11.0 ← coarse-end divergence |

Core four (espresso/moka/AeroPress/V60) cluster at **12.4–12.7 µm/click**. French and cold diverge because HCG extends the dial to click 120 = 1360 µm, while `METHOD_MICRON_RANGES` tops out at 1200 µm — the Q Air simply has coarser range than the Comandante C40 truth max.

Picking `micronsPerClick = 12.5, zeroOffset = 0` and verifying:

| Method | Predicted clicks | HCG clicks | Δ |
|---|---|---|---|
| Espresso | 17–31 | 16–33 | within 2 |
| Moka | 34–58 | 32–58 | within 2 |
| AeroPress | 29–84 | 29–84 | exact |
| V60 | 36–60 | 36–61 | within 1 |
| Chemex | 60–82 | (Pour Over 37–82) | coarse half matches |
| French | 62–96 | 61–114 | lo within 1; hi capped at 96 (truth 1200 µm) |
| Cold | 72–96 | 71–120 | lo within 1; hi capped at 96 (truth 1200 µm) |

No excluded methods. Cold brew and the upper tail of French press compute within the 120-click range — the calibrator simply doesn't extend to HCG's most-extreme coarse settings (1200–1360 µm), which is consistent with `METHOD_MICRON_RANGES`.

### Hardware-vs-HCG discrepancy

Manufacturer: 25 µm/click (vertical burr travel). Back-fit: 12.5 µm/click (effective gap / particle-size-equivalent). Ratio is exactly 2:1 — more symmetric than the K-Ultra (2.6:1) or C2 (2.3:1) cases. At 120 clicks the manufacturer figure implies 3000 µm total; HCG caps at 1360 µm. Same pattern as all other 1Zpresso grinders: the published µm/click is vertical thread travel, not effective particle-size delta. Follow HCG.

## Recommended config

```js
zpresso_q_air: {
  id: 'zpresso_q_air',
  name: '1Zpresso Q Air',
  model: 'Q Air',
  variants: 'Q Air / Q2 (heptagonal)',
  subtitle: 'Q Air · Heptagonal 38mm',
  minClick: 0,
  maxClick: 120,
  // 12.5 µm/click back-fits HCG's Q Air brew ranges across espresso/moka/
  // AeroPress/V60 within 1–2 clicks. Manufacturer's 25 µm/click figure
  // (1Zpresso comparison guide, Q-series manual: 0.75 mm/rotation ÷ 30)
  // implies a 3000 µm range at 120 clicks — contradicts HCG's 0–1360 µm
  // total. Exactly 2× discrepancy; follow HCG.
  micronsPerClick: 12.5,
  zeroOffset: 0,
  majorTick: 10,
  accentColor: '<pick — see implementer note>',
  dialNotation: 'numbered',
}
```

## Caveats

- **Coarse-end behaviour.** At 12.5 µm/click and 120 clicks max, the Q Air tops out at 1500 µm — above `METHOD_MICRON_RANGES` cold/French max of 1200 µm, so no clamping occurs. The calibrator will show cold brew capped at ~96 clicks (= 1200 µm), leaving ~24 clicks of extra range HCG uses for extreme coarse settings.
- **Dial notation.** Reads R.N.C (rotation.number.click-within-number). Within the first 4 rotations users see 0.N.C through 3.N.C. The `dialNotation: 'numbered'` flag plus `majorTick: 10` maps to the 10-number dial (same shape as S3/ZP6 already in the app), though the sub-click grouping is 3 not 10.
- **Variants.** Q Air and Q2 (heptagonal) share the mechanism and HCG ranges. Inner cone geometry differs (Q Air more aggressive pre-breakers), so cup character will differ even at the same click count. For grind-size mapping purposes, one config covers both.
- **Internal adjustment.** Users must remove the grinds bin to change settings — less convenient than external-dial grinders. No calibrator UI implication, but relevant context.
- **Zero may not land on #0.** 1Zpresso's manual notes the zero position may sit at a non-zero number on the dial. Users count from wherever the dial naturally stops, not from #0. `zeroOffset: 0` is correct (no factory gap), but practical zero is defined by feel not by dial markings.

## Do not confuse with

- **1Zpresso Q2 (original, "Q2 Standard")** — earlier Q2 with different (less aggressive) inner cone. Different cup character; same click/µm config applies.
- **1Zpresso Q2S / QS** — Q2 variant with slightly different body; same mechanism.
- **1Zpresso Q (original)** — older Q-series body, same 38 mm heptagonal burrs and 30-click/rotation dial. Likely same µm/click, but HCG has a separate page — verify before sharing a config entry.
- **1Zpresso ZP6 Special** — already in the calibrator; hexagonal (6-sided) burrs, 22 µm/click manufacturer spec, pour-over only. Different burr family.
- **1Zpresso K-Ultra** — heptagonal 48 mm K burr, 7.6 µm/click back-fit, external adjustment. Different class.
- **1Zpresso J-Max / JX-Pro** — separate burr families, espresso-oriented. Different configs entirely.

## Sources

- HCG Q Air grind settings — <https://honestcoffeeguide.com/1zpresso-q-air-grind-settings/>
- HCG Q2 (heptagonal) grind settings (identical ranges) — <https://honestcoffeeguide.com/1zpresso-q2-heptagonal-burrs-grind-settings/>
- HCG 1Zpresso comparison guide — <https://honestcoffeeguide.com/1zpresso-comparison-guide/>
- 1Zpresso Q-series user manual — <https://1zpresso.coffee/manual-q-en/>
- 1Zpresso calibration guide — <https://1zpresso.coffee/calibration/>
- 1Zpresso Q Air product page — <https://1zpresso.coffee/product/qair/>
- The Coffee Folk 1Zpresso comparison — <https://thecoffeefolk.com/1zpresso-grinder-comparison/>
- Coffee Chronicler Q Air review — <https://coffeechronicler.com/1zpresso-q-air-review/>
