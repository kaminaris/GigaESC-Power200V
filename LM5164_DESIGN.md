# LM5164 design — 20–90 V to nominal 12 V / 1 A

Schematic design pass, 2026-09-16. Not a production-qualified supply. Typical reported system consumption is about 2 W; retain 12 W output design target including downstream converters. No PCB changes in this pass.

## Selected components and inventory

| References | Value / part | Inventory / decision |
|---|---|---|
| U1 | LM5164DDAR | Not found; order |
| L4 | 68 uH MSS1210-683MEB | WEBENCH baseline, not stocked; correct Coilcraft footprint replaces unrelated 5 mm placeholder |
| R11 | 100k 0603WAF1003T5E | Stock C25803, R20, qty 200 |
| R12 | 453k RC0603FR-07453KL | WEBENCH value, not stocked |
| R13 | 50k RT0603BRD0750KL | WEBENCH value, not stocked |
| R17 / R18 | 150k + 200k, 0603 | Stock C22807 / C25811, R20; total 350k instead of WEBENCH 357k |
| C2 / C6 | 2.2 nF 50 V X7R 0603B222K500NT | Stock C1604, C1, qty 100 |
| C7 | 47 pF NP0 CC0603FRNPO0BN470 | Stock C527044, C1, qty 50 |
| C1 / C4 | 10 uF 100 V X7R FS32X106K101EGG, 1210 | Stock C5156756, C2, qty 32. PROVISIONAL pending DC-bias data |
| C3 / C5 | 22 uF 25 V X5R CL31A226KAHNNNE, 1206 | Stock C12891, C2, qty 10. Verify effective capacitance and local temperature; X5R is rated only to 85 C |

Counts are database qty, not qty minus inUse. No inventory was reserved or modified. The stock CYA0650-68UH (C5189964, qty 4, inUse 1, bin L1) remains an alternative, not an approved replacement: its specific datasheet could not be retrieved and the accessible family sheet does not list 68 uH. The 1.8 A catalog rating alone does not establish saturation margin. The other stocked 68 uH YSPI1365 is large and also needs a verified saturation specification.

## Calculated operating points

First-order CCM calculations at nominal 300 kHz, 68 uH, 12 V. These are calculations, not simulations or measurements.

| VIN | Duty | On time | Inductor ripple p-p | Peak at 1 A | Injected ramp estimate |
|---|---:|---:|---:|---:|---:|
| 20 V | 0.600 | 2.00 us | 0.235 A | 1.118 A | 20.8 mV |
| 48 V | 0.250 | 0.833 us | 0.441 A | 1.221 A | 39.0 mV |
| 90 V | 0.133 | 0.444 us | 0.510 A | 1.255 A | 45.0 mV |

At minus 20% inductance, 90 V ripple becomes 0.637 A p-p and peak 1.319 A, before switching-frequency tolerance, temperature, or DC-bias loss of inductance. Qualify saturation against the regulator current limit, not merely 1.319 A normal-load peak.

The 350k * 2.2nF network has a 770 us time constant, 2% below WEBENCH. Splitting it limits nominal 90 V switching-step resistor stress to about 33.4 V / 44.6 V across 150k / 200k once the ramp node is near 12 V; at startup with the ramp node near zero the split is about 38.6 V / 51.4 V. Check overshoot and actual resistor pulse ratings during bring-up.

The divider's ideal zero-ripple setpoint is 12.072 V. Ripple injection, reference/divider tolerances and operating mode affect the actual DC output. Do not describe this as precision 12.000 V. The 47 pF coupling choice is retained from WEBENCH; transient response needs bench validation with the downstream converters attached.

At roughly 0.167 A load, the LM can enter discontinuous / pulse-skipping operation at higher VIN. The 1 A current target does not imply continuous-conduction operation at typical load. The prior 9 mW small-inductor loss estimate excludes ripple; it is not a complete light-load loss prediction.

## Qualification and integration remaining

- C1/C4 must retain at least 2.2 uF total at 90 V, with tolerance, temperature and aging allowed for. 20 uF nominal is NOT evidence of that. No usable part-specific bias curve was obtained. If not verified, use a documented higher-voltage capacitor rather than relying on this provisional stock selection. WEBENCH's 450 V parts were not copied into the small-board design.
- C3/C5 target at least 10 uF effective total at 12 V. At that assumed minimum, nominal worst-case capacitive ripple is about 21 mV p-p (excluding ESR/transients). Stock X5R parts constrain local temperature to <=85 C; use X7R replacements if that is insufficient.
- 90 V DC leaves only 10 V to the IC's 100 V absolute maximum. VBAT is currently an explicit local net, not connected to the unassigned J2 connector. Battery leads, main-board bulk capacitance, hot-plug damping and regeneration overshoot must be addressed at integration. Do not assume an ordinary 90 V TVS clamps below 100 V.
- EN remains tied to VIN as in WEBENCH. No battery undervoltage cutoff is implied by the 20 V design minimum. PGOOD intentionally remains unconnected.
- Ground EP and keep the input switching loop short. Route the divider/ripple network away from SW copper and return it to quiet IC ground. Validate output ripple, load steps, startup, fault behavior and temperatures at both input extremes.
- PCB placement/routing and connector pin assignment are not part of this schematic pass. Reconcile the schematic with the PCB after component qualification.

## Sources

- User-provided WBSchematicLM5164DDAR.svg and WBBOMDesignLM5164DDAR.csv.
- TI LM5164 datasheet: https://www.ti.com/lit/ds/symlink/lm5164.pdf
- Coilcraft baseline inductor: https://www.coilcraft.com/getmedia/f1a1bc5f-bdff-42f8-9ca8-1a0fb1d5094f/MSS1210.pdf
- Samsung stock output capacitor: https://product.samsungsem.com/mlcc/CL31A226KAHNNN.do
- Read-only local bommanager inventory snapshot.
