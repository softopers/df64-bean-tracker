# 1Zpresso K-Ultra — Grinder Research

## TL;DR

- **Hardware spec:** 48 mm heptagonal stainless conical "K Burr" (redesigned, no base ring), external adjustment, 100 clicks/rotation, 10 ticks per number, 1Zpresso/Brew Coffee Home cite **20 µm/click**.
- **HCG back-fit says otherwise.** Honest Coffee Guide's K-Ultra page caps the total grind range at 760 µm over 100 clicks and places V60 at 0.5.3–0.9.2 (clicks 53–92), espresso at 0.2.4–0.5 (24–50), moka at 0.4.8–0.8.6 (48–86). Solving against `METHOD_MICRON_RANGES` gives **~7.6 µm/click with a ~40 µm factory zero offset**, not 20 µm/click.
- **Recommendation:** `micronsPerClick: 7.6, zeroOffset: 40, minClick: 0, maxClick: 100, dialNotation: 'numbered'`. No excluded methods. No variants share this config — K-Plus/K-Max/K-Pro all use the older 22 µm step + non-Ultra burr and need separate entries.
- Coarse-end methods (cold brew, top of French press) clamp at click 100 — that's HCG's own behaviour, not a calibrator artefact.

## Key findings

### Hardware

- **Burrs:** 48 mm heptagonal conical, SUS440 stainless. Redesigned vs the K-Max/K-Plus "K burr" — no base ring at the bottom, more aggressive cut geometry. Tuned for pour-over but marketed as all-round.
- **Adjustment:** External numbered ring, 100 clicks per rotation, 10 ticks per number (so reads as `R.N.T`, e.g. `0.5.3` = 0 rotations + number 5 + 3 ticks = click 53). Manufacturer says the dial can pass 1 full rotation; brewcoffeehome reports ~150 clicks of useful travel. HCG only uses the first 100.
- **Manufacturer µm/click:** 20 µm/click (1Zpresso product page, brewcoffeehome, coffeeness). This implies a ~2 mm total range at 100 clicks — inconsistent with HCG's 0–760 µm claim and with the brew-method placements below.
- **Zero-point:** Turn the adjustment ring counter-clockwise until the handle starts to spin against resistance — that's #0. Not "burrs touching" — there's a small factory clearance.

### HCG K-Ultra ranges

Source: <https://honestcoffeeguide.com/1zpresso-k-ultra-grind-settings/>. Notation `R.N.T` decoded to total clicks (`R*100 + N*10 + T`).

| Method | HCG setting | Total clicks |
|---|---|---|
| Turkish | 0.0.6 – 0.2.8 | 6 – 28 |
| Espresso | 0.2.4 – 0.5 | 24 – 50 |
| Moka | 0.4.8 – 0.8.6 | 48 – 86 |
| AeroPress | 0.4.3 – 1 | 43 – 100 |
| V60 | 0.5.3 – 0.9.2 | 53 – 92 |
| Pour-over | 0.5.4 – 1 | 54 – 100 |
| French press | 0.9.1 – 1 | 91 – 100 |
| Cold brew / cold drip | 1 | 100 |

HCG's own chart visualisation labels the dial 0–760 µm. Chemex is not listed.

### Back-fit µm/click

Truth ranges from `METHOD_MICRON_RANGES`. Solving `µm = zeroOffset + micronsPerClick * click` independently per method:

| Method | µm range | Click range | implied slope | implied intercept |
|---|---|---|---|---|
| Espresso | 210–390 | 24–50 | 6.92 | 44 |
| Moka | 420–720 | 48–86 | 7.89 | 41 |
| V60 | 450–750 | 53–92 | 7.69 | 42 |
| AeroPress | 360–1050 | 43–100 | 12.1 | — (range too wide, unreliable) |
| French | 780–1200 | 91–100 | 46.7 | — (clamped, unreliable) |

The reliable trio (espresso/moka/V60) clusters tightly at **slope ≈ 7.5 µm/click, intercept ≈ 42 µm**. AeroPress and French press are noisy because HCG hits the dial limit at click 100 — that's a clamping artefact, not a slope signal.

Picking `micronsPerClick = 7.6, zeroOffset = 40` (round numbers within the noise floor) and re-checking:

| Method | Predicted clicks | HCG clicks | Δ |
|---|---|---|---|
| Espresso | 22 – 46 | 24 – 50 | within 4 |
| Moka | 50 – 89 | 48 – 86 | within 3 |
| V60 | 54 – 93 | 53 – 92 | within 1 |
| AeroPress | 42 – 133→100 | 43 – 100 | clamped, lo within 1 |
| Chemex | 93 – 129→100 | (not published) | matches HCG's "near full rotation" expectation |
| French | 97 – 152→100 | 91 – 100 | hi clamps; lo 6 clicks coarse of HCG |
| Cold | 113→100 – 152→100 | 100 | clamped |

Espresso/moka/V60 land within 1–4 clicks of HCG. Cold brew and the top of French press clip at click 100 because HCG itself caps the dial there — pretending the K-Ultra can grind 1200 µm (truth max for French/cold) would require letting clicks run past 100 into the second rotation, which HCG does not endorse.

### Hardware-vs-HCG discrepancy

The 20 µm/click manufacturer spec implies the K-Ultra's 100-click rotation covers 2000 µm — coarser than V60 by a wide margin at click 100. HCG instead places V60's *coarse* end at click 92 and only goes to ~760 µm at the dial limit. Either the manufacturer figure refers to mechanical thread pitch (not effective particle-size step) or HCG/the broader pour-over community uses far less of the dial than 1Zpresso designed for. Same shape as the C2 case (Timemore says 80 µm/click, HCG back-fits ~35) — we follow HCG.

## Recommended config

```js
k_ultra: {
  id: 'k_ultra',
  name: '1Zpresso',
  model: 'K-Ultra',
  subtitle: 'K-Ultra · Heptagonal 48mm',
  minClick: 0,
  maxClick: 100,
  // 7.6 µm/click + 40 µm factory zero back-fits HCG's K-Ultra brew ranges
  // across espresso/V60/moka within 1–4 clicks. Manufacturer's 20 µm/click
  // figure (1Zpresso, brewcoffeehome) implies a 2 mm total range that HCG's
  // own 0–760 µm chart and click placements both contradict.
  micronsPerClick: 7.6,
  zeroOffset: 40,
  majorTick: 10,
  accentColor: '<pick one — see implementer note>',
  dialNotation: 'numbered',
}
```

## Caveats

- **Coarse-end clamping.** With zeroOffset 40 and 100 clicks max, the K-Ultra tops out at ~800 µm. French press (truth 780–1200) and cold brew (900–1200) will display clamped at the coarse edge of the dial. This matches HCG's own behaviour — both methods sit at "click 100" on HCG's chart — so the calibrator will not be lying, but a user comparing K-Ultra ↔ C40 at 1100 µm will find the K-Ultra pinned at click 100. That's reality, not a bug.
- **Dial notation.** Reads `R.N.T` (rotation.number.tick). Within a single rotation users see `0.N.T`, which is functionally the same `N.C` shape that S3/ZP6 already use, so `dialNotation: 'numbered'` is correct. No special UI needed for the rotation digit since the recommended range never crosses one full rotation.
- **No excluded methods.** Heptagonal 48 mm K Burr is marketed as all-round and HCG places espresso confidently at 24–50.
- **No shared variants.** Despite the "K-series" branding, only the K-Ultra has the 20 µm step + new heptagonal-no-base burr. K-Plus, K-Max, and K-Pro all use 22 µm/click and the older K burr — separate configs if/when added.

## Do not confuse with

- **1Zpresso K-Plus** — 22 µm/click, older K burr (with base), magnetic catch cup with portafilter dosing bottom. Different config.
- **1Zpresso K-Max** — 22 µm/click, older K burr. Different config.
- **1Zpresso K-Pro** — 22 µm/click, older K burr, screw-on catch cup, smaller capacity. Different config.
- **1Zpresso K-Ultra Filter** — variant tuned for filter only; same body but burr/range tuned differently per 1Zpresso product line. Treat as a separate entry if added; do not assume this config covers it.
- **1Zpresso X-Ultra / J-Max / JX / JX-Pro** — different burr families entirely. HCG has dedicated pages for each.
- **1Zpresso ZP6 Special** — already in the calibrator, hex burr, 22 µm/click, espresso excluded. Visually similar branding but mechanically different grinder.

## Sources

- HCG K-Ultra grind settings — <https://honestcoffeeguide.com/1zpresso-k-ultra-grind-settings/>
- 1Zpresso K-Ultra product page — <https://1zpresso.coffee/product/kultra/>
- 1Zpresso K-Ultra user manual — <https://1zpresso.coffee/manual-kultra-en/>
- Brew Coffee Home review — <https://www.brewcoffeehome.com/1zpresso-k-ultra-review/>
- Coffeeness review — <https://www.coffeeness.de/en/1zpresso-k-ultra-review/>
- 1Zpresso K-Pro/K-Plus/K-Max comparison — <https://1zpresso.coffee/kpro-vs-kplus-vs-kmax/>
- HCG 1Zpresso comparison guide — <https://honestcoffeeguide.com/1zpresso-comparison-guide/>
