# DESIGN.md

Visual design language for the Hand Grinder Calibrator. **Don't drift from this without the user explicitly asking.** The look has been iterated on carefully and is part of the product.

## Mood

Hi-fi industrial dial — like a vintage stereo knob or a film camera. Dark, earthy, tactile. Not flat. Not playful. Not skeuomorphic in a kitschy way; more like a precision instrument photographed in low light.

## Palette

Background and chrome are warm near-blacks with subtle gradients. Accent colours are low-saturation, earthy, and grinder-specific.

| Use | Value |
|---|---|
| Page background (radial) | `#1a1715` → `#0a0807` → `#050403` |
| Body | `#050403` |
| Bezel / outer body (radial) | `#2a2724` → `#0a0908` |
| Knurled cap (radial) | `#4a4540` → `#2a2520` → `#0a0908` |
| Center LCD well | `#0a1410` → `#050807` |
| Header / panels | `#14110f` → `#0a0807` |
| Buttons (gradient) | `#2a2520` → `#14110f` |
| Borders | `#0a0908` / `#000` |
| Stone text | `text-stone-100/200/300/500/600/700` |

Grinder accent colours (each grinder picks one — keep them earthy and visually distinct):

- Comandante C40: `#c89d6a` (warm gold)
- Timemore S3: `#9bb086` (muted green)
- Timemore C2: `#b89a7c` (warm tan)
- 1Zpresso ZP6: `#7ab4d0` (steel blue)

When adding a grinder, pick a desaturated earthy tone in a hue that's not already taken. Avoid pure-saturation colours; everything in this app is muted.

## Typography

- **DM Mono** (Google Fonts, weights 400 / 500) — every label, button, dial number, footer, dropdown. Loaded via `<link>` in `<head>`.
- **Bodoni Moda** italic (Google Fonts) — only used for the word "Calibrator" in the header. Don't use it elsewhere.
- **Inter / system sans** — fallback body font; effectively never seen.

### Letter spacing

DM Mono renders tight by default. The Tailwind tracking values used through the app are deliberately small. **Don't increase these without checking — over-tracked text was the most-reported visual issue during iteration.**

| Use | Tracking |
|---|---|
| Header "HAND GRINDER" supertitle | `tracking-[0.12em]` |
| "Clicks" label, history headings | `tracking-[0.08em]` |
| Subtitle under model | `tracking-[0.07em]` |
| Method chips, save/lock buttons, source badge, disclaimer | `tracking-[0.06em]` |
| Name plate above dial, footer | `tracking-[0.05em]` |
| Dropdown | `tracking-wider` (0.05em) — Tailwind built-in |
| Dial number | `letterSpacing: -0.02em` (negative — tight) |

### Sizes

Many elements use bracketed Tailwind sizes for precision: `text-[8px]`, `[9px]`, `[10px]`, `[11px]`, `[44px]` (dial number), `[18px]` (Calibrator title). Don't switch to default Tailwind tiers — the design relies on these specific sizes.

## The dial (hero element)

A 280×280 stack of concentric circles built from SVG and CSS gradients:

1. **Outer body** — radial gradient with strong drop-shadow. The grounding element.
2. **Bezel** — SVG `radialGradient` (top-left highlight to bottom-right shadow). Carries the tick marks and major-tick numbers.
3. **Recommended-range arc** — drawn with the grinder's accent colour, glows (drop-shadow) when the click is inside the range, fades when outside.
4. **Tick marks** — 91 minor lines, every Nth being a major tick (longer + brighter). Major-tick labels at radius 96.
5. **Knurled rotating cap** — radial gradient + 60 alternating-light/dark knurl lines on the rim. Rotates with the click. Carries an accent-coloured indicator dot at its 12 o'clock.
6. **Center LCD well** — recessed dark green-black radial. Carries the central readout (label + number + ≈µm).
7. **Source/Mapped badge** — pill at the top-centre (over the bezel), accent-filled when source, dark when mapped.

Drag the cap to scrub. `+` / `−` buttons below for one-click steps. A snap-y cubic-bezier transition (`cubic-bezier(0.34, 1.56, 0.64, 1)`) gives the cap a slight overshoot, like a real detented dial.

The audio engine plays a noise burst + low thump per click; a chime overlay fires when the click enters the recommended range. Don't break this.

## Backgrounds and texture

- The page has a subtle `grain-overlay` at 5% opacity using inline SVG fractal noise. It's load-bearing for the "low-light" feel — keep it.
- All concave / convex elements use `inset` shadows + matching gradients to read as depth. Buttons compress on `:active` via `active:translate-y-0.5`.

## Spacing rules

- The app is mobile-first; on desktop (`md:`) the two grinder columns sit side-by-side with a thin connector line between them. The connector is a simple fading vertical line (mobile) or horizontal line (desktop) — no icon. Don't reintroduce the coffee icon that was removed.
- Generous vertical breathing room between sections. Footer sits 64px below the right grinder.

## Footer

Two lines, centred, dim:

1. The mapping disclaimer (with the HCG link inline)
2. The "made by @filtercoffeeconnoisseur with Claude" credit (with the Instagram link inline)

Both use DM Mono at 10px, tracking-[0.05em], with the second line one stone-shade dimmer than the first. Don't change the order; don't change the credit.

## Things to avoid

- Bright / saturated colours
- Sharp pure-black-on-pure-white contrasts
- Sans-serif body fonts other than DM Mono in UI labels
- Loose letter-spacing on uppercase mono text (over 0.12em)
- Skeuomorphic flourishes that don't reinforce the dial metaphor (gears, beans, cup icons)
- Animations longer than ~250ms; the dial transition is the only "playful" motion in the app
- Light/system theme toggling — this app is dark only by intent
