# GigaPower100V supply module — J1 / J2 pinout

Two 2x10, 1.00 mm pitch connectors (Hanxia HX PM1.0-2x10P ZC, LCSC C22465702; mating
HX PZ1.0-2x10P WZ, C22465711). **J1 carries the battery bus, J2 carries only low voltage.**

Connector ratings from the LCSC listing: 750 mA per contact, catalogue rated voltage 500 V,
−40…+105 °C, 1 x 1 mm contact grid, gold-plated phosphor bronze.

## J1 — HV input

| Col | Odd | Signal | Even | Signal |
|---:|---:|---|---:|---|
| 1 | 1 | `VBAT` | 2 | `VBAT` |
| 2 | 3 | *NC* | 4 | *NC* |
| 3 | 5 | *NC* | 6 | *NC* |
| 4 | 7 | *NC* | 8 | *NC* |
| 5 | 9 | *NC* | 10 | *NC* |
| 6 | 11 | *NC* | 12 | *NC* |
| 7 | 13 | *NC* | 14 | *NC* |
| 8 | 15 | *NC* | 16 | *NC* |
| 9 | 17 | `GND` | 18 | `GND` |
| 10 | 19 | `GND` | 20 | `GND` |

- `VBAT` is paired because 12 W at the 20 V design minimum draws about 0.7 A, right at one
  contact's 750 mA rating. Four `GND` contacts carry the 0.7 A return with margin.
- Columns 2-8 are a deliberate gap: 8 mm contact to contact, about 7.2 mm pad edge to pad edge,
  against roughly 0.71 mm creepage required at 100 V and 3.2 mm at 650 V (PD2, group IIIa).
- **The gap only delivers its full distance if those contacts are physically absent.** A
  populated connector leaves 14 floating contacts in the path; floating metal can assume any
  potential, so each 1 mm segment must meet the full creepage requirement on its own. That
  passes at 100 V and fails at 650 V.
- The module is **not isolated**: `GND` is battery negative. The whole low-voltage side sits at
  battery-negative potential, so touch safety is the enclosure's job, not this connector's.

## J2 — LV output

| Col | Odd | Signal | Even | Signal |
|---:|---:|---|---:|---|
| 1 | 1 | `GND` | 2 | `GND` |
| 2 | 3 | `+12V` | 4 | `+12V` |
| 3 | 5 | `GND` | 6 | `GND` |
| 4 | 7 | `+5V` | 8 | `+5V` |
| 5 | 9 | `GND` | 10 | `GND` |
| 6 | 11 | `+3.3V` | 12 | `+3.3V` |
| 7 | 13 | `GND` | 14 | `GND` |
| 8 | 15 | `GND` | 16 | `GND` |
| 9 | 17 | *NC — reserved* | 18 | *NC — reserved* |
| 10 | 19 | `GND` | 20 | `GND` |

- Every rail uses a pair of contacts (1.5 A) with a ground column beside it, so the supply and
  return currents stay adjacent.
- 12 ground contacts give about 9 A of return capacity, well past the sum of the three rails.
- Pins 17/18 are reserved for `PGOOD` and a divided `VBAT-SENSE` (≤ 3.3 V). Neither circuit
  exists yet: the LM5164's PGOOD is still unconnected and there is no sense divider.

## Voltage limits — read before reusing at 650 V

This pinout is qualified for the **100 V family only**.

| Working voltage | Creepage needed (PD2, group IIIa) | Available |
|---|---|---|
| 100 V | ≈ 0.71 mm | 1.0 mm contact-to-contact, 1.2 mm pad-to-pad across the gap |
| 650 V | ≈ 3.2 mm (≈ 1.8 mm if conformal coated, treated as PD1) | unchanged |

For a 650 V+ version:

1. 650 V exceeds the connector's 500 V catalogue rating, so pin spacing alone cannot make it
   compliant. The HV entry needs a different, properly rated connector or soldered leads.
2. The floating contacts between HV and GND subdivide the creepage path, so simply skipping
   more columns does not buy the full distance.
3. The LM5164 is a 100 V part; a 650 V bus needs a different converter topology anyway.

Keep J2's pinout unchanged across the family. Only the HV entry changes.

## Assembly hazard

J1 and J2 are the same connector, so a swapped cable puts the battery bus straight onto the
3.3 V rail. Before building harnesses, do one of: use a different connector family or pin count
for J1, key/block the housings, or place them far enough apart that no harness can reach the
wrong socket.
