# DF64 (Gen 2) research notes

Status: integrated from Honest Coffee Guide (HCG) source page.

## Primary source

- https://honestcoffeeguide.com/turin-df64-gen-2-grind-settings/

HCG explicitly states the grinder can grind between 180-1050 microns and provides a DF64 Gen 2 method table.

## HCG DF64 method ranges

- Turkish: 0-4
- Espresso: 0-20
- V60: 23-53
- AeroPress: 15-80
- Moka Pot: 19-49
- Pour Over: 24-77
- Siphon: 21-64
- Filter Coffee Machine: 13-74
- French Press: 53-90
- Cupping: 29-69
- Cold Brew: 65-90
- Cold Drip: 67-90
- Steep-and-release: 28-66

## Mapping to app methods

The app currently exposes these method chips:

- Espresso -> 0-20
- Moka -> 19-49
- AeroPress -> 15-80
- V60 -> 23-53
- Chemex -> 24-77 (mapped from "Pour Over")
- French Press -> 53-90
- Cold Brew -> 65-90

## Config decisions implemented

- id: turin_df64_gen2
- minClick: 0
- maxClick: 90
- methodOverrides: uses the mapped ranges above directly
- micronsPerClick baseline: 9.67
- zeroOffset: 180
- variants: MiiCoffee/Solo/G-IOTA/Coffee Tech/Lucca/DF64Coffee.com

The linear baseline is derived from HCG's 180-1050 micron span across a 0-90 dial range:

- (1050 - 180) / 90 = 9.67 microns per click

## Follow-up (recommended)

- If the app adds extra method chips (Turkish, Siphon, Filter, Cupping, Cold Drip, Steep-and-release), wire DF64 methodOverrides directly from this same HCG page.
