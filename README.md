# TR-1um PCM TEG — `tr_1um_wladek60`

[![check](https://github.com/jun1okamura/Wladek_TR-1um-OpenTC/actions/workflows/check.yml/badge.svg?branch=main)](https://github.com/jun1okamura/Wladek_TR-1um-OpenTC/actions/workflows/check.yml)

This is a Process Control Monitor (PCM) test chip for the OpenSUSI TR-1um MPW.
It contains ten 16-pad test structures (WG1–WG10) for characterising MOSFETs, junction diodes, a MOS capacitor and a ring oscillator.

| Item | Value |
|---|---|
| Top cell | `tr_1um_wladek60` |
| Layout | `src/tr_1um_wladek60.gds` |
| Tile size | 2.5 mm × 2.5 mm |
| Test blocks | 10 × `OSS_PAD16` (WG1–WG10) |
| Block size | 1040 µm × 440 µm |
| Pads | 16 per block, 100 µm × 100 µm, 150 µm pitch |

---

## Floorplan

Block centre coordinates (µm):

| Left column | Right column |
|---|---|
| WG1 (-550, 920) | WG2 (550, 920) |
| WG3 (-550, 460) | WG4 (550, 460) |
| WG5 (-550, 0) | WG6 (550, 0) |
| WG7 (-550, -460) | WG8 (550, -460) |
| WG9 (-550, -920) | WG10 (550, -920) |

## Pad numbering

All blocks use the same pad numbering, counter-clockwise from the top-left:

```
 P1   P16  P15  P14  P13  P12  P11
 P2                            P10
 P3   P4   P5   P6   P7   P8   P9
```

---

## WG1–WG8: MOSFET W/L matrix

Each block contains four transistors of the same type. All eight blocks use the same pad assignment:

| Device | Gate | Source | Drain | Bulk |
|---|---|---|---|---|
| M1 | P1 | P2 | P16 | P3 |
| M2 | P15 | P4 | P14 | P5 |
| M3 | P13 | P6 | P12 | P7 |
| M4 | P10 | P9 / P11 | P11 / P9 | P8 |

- **NMOS blocks (WG1, WG3, WG5, WG7):** the bulk pads (P3, P5, P7, P8) all connect to the common P-substrate.
- **PMOS blocks (WG2, WG4, WG6, WG8):** each transistor sits in its own N-well, so each bulk pad is separate.

### Device sizes (W / L in µm)

| Block | Type | M1 | M2 | M3 | M4 | Purpose |
|---|---|---|---|---|---|---|
| **WG1** | NMOS | 3.4 / 1 | 3.4 / 2 | 3.4 / 4 | 50 / 30 | Short-channel effects at minimum width, plus a large long-channel reference device |
| **WG2** | PMOS | 3.4 / 1 | 3.4 / 2 | 3.4 / 4 | 50 / 30 | PMOS version of WG1 |
| **WG3** | NMOS | 30 / 1 | 30 / 2 | 30 / 3 | 30 / 5 | Short-L dependence with wide devices: Vth roll-off, DIBL, Idsat |
| **WG4** | PMOS | 30 / 1 | 30 / 2 | 30 / 3 | 30 / 5 | PMOS version of WG3 |
| **WG5** | NMOS | 30 / 7 | 30 / 10 | 30 / 20 | 30 / 30 | Long-L dependence: long-channel Vth, mobility, ΔL extraction |
| **WG6** | PMOS | 30 / 7 | 30 / 10 | 30 / 20 | 30 / 30 | PMOS version of WG5 |
| **WG7** | NMOS | 3.4 / 30 | 5 / 30 | 7 / 30 | 10 / 30 | W dependence at long L: narrow-width effect, ΔW extraction |
| **WG8** | PMOS | 3.4 / 30 | 5 / 30 | 7 / 30 | 10 / 30 | PMOS version of WG7 |

---

## WG9: Junction diodes and MOS capacitor

| Device | Structure | Size | Terminal A | Terminal B |
|---|---|---|---|---|
| D1 | N+ / P-sub diode, finger (14 × 3.6 µm stripes) | Area 5080 µm², perimeter 2923 µm | P16 (N+, cathode) | P4 (P-sub tap, anode) |
| D2 | N+ / P-sub diode, plate | Area 9798 µm², perimeter 396 µm | P15 (N+, cathode) | P5 (P-sub tap, anode) |
| D3 | P+ / N-well diode, finger (14 × 3.6 µm stripes) | Area 5080 µm², perimeter 2923 µm | P13 (P+, anode) | P7 (N-well tap, cathode) |
| D4 | P+ / N-well diode, plate | Area 9798 µm², perimeter 396 µm | P12 (P+, anode) | P8 (N-well tap, cathode) |
| C1 | MOS capacitor on CSIO (AC) region in N-well | Gate 59.8 × 59.8 µm (3576 µm²); AC 57 × 57 µm (3249 µm²) | P14 (gate) | P6 (N+ ring / N-well) |

- Each diode type comes as a finger/plate pair. Measuring both lets you separate the **area** and **perimeter (sidewall)** components of junction capacitance and leakage.
- D1 and D2 share the P-substrate, so P4 and P5 are connected through it. D3 and D4 share one N-well, so P7 and P8 are too.
- P1, P2, P3, P9, P10 and P11 are not used.

---

## WG10: Ring oscillator

| Item | Value |
|---|---|
| Structure | NAND2 (enable) + chain of 30 `INV_X1` cells (24 µm pitch) |
| `INV_X1` | PMOS W/L = 10.2 / 1 µm, NMOS W/L = 3.4 / 1 µm |
| P2 | IN (enable) |
| P4 | GND |
| P16 | VDD |
| P10 | OUT (buffered by the last two inverters) |
| Other pads | Not used |

- The ring has **29 inverting stages**: the NAND2 plus 28 inverters. One NAND2 input is IN (P2); the other is the feedback node `loop`, taken from the output of the 28th inverter.
- The 29th and 30th inverters form a two-stage output buffer from `loop` to OUT, which isolates the ring from the probe load.
- Operation: set IN = VDD to enable the ring (IN = GND stops it) and measure the frequency *f* at OUT. The delay per stage is t<sub>pd</sub> = 1 / (2 · 29 · *f*).

---

## Measurement notes

- The gates of all test devices connect directly to pads with **no ESD protection**. Probe with appropriate ESD precautions (grounded wrist strap, grounded chuck).
- For NMOS blocks, keep all bulk pads (P3, P5, P7, P8) at the same potential, because they share the P-substrate.

---

## Repository structure

| Path | Description |
|---|---|
| `src/tr_1um_wladek60.gds` | Layout (submission) |
| `info.yaml` | Project configuration (top cell, LVS, MDP output, PDK version) |
| `.github/workflows/check.yml` | CI: Pre-check → DRC → LVS (netlist extraction only) → MDP → IP62 DRC |
| `scripts/` | CI helper scripts |
| `docs/` | Template documentation |

The CI flow follows the [TR-1um MPW template](https://github.com/OpenSUSI/TR-1um_MPW_template). See [docs/info.md](docs/info.md) for details of the `info.yaml` fields and the check flow.

## License

Apache License 2.0. See [LICENSE](LICENSE).
