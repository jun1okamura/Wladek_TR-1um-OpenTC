## PCM TEG overview (`tr_1um_wladek60`)

The 2.5 mm × 2.5 mm tile contains ten 16-pad TEG blocks (`OSS_PAD16`), labelled **WG1–WG10** on the layout.
Each block is 1040 × 440 µm with sixteen 100 × 100 µm pads at a 150 µm pitch.

### Block placement (block centre, µm)

| Left column | Right column |
|---|---|
| WG1 (-550, 920) | WG2 (550, 920) |
| WG3 (-550, 460) | WG4 (550, 460) |
| WG5 (-550, 0) | WG6 (550, 0) |
| WG7 (-550, -460) | WG8 (550, -460) |
| WG9 (-550, -920) | WG10 (550, -920) |

### Pad numbering (common to all blocks)

```
 P1   P16  P15  P14  P13  P12  P11
 P2                            P10
 P3   P4   P5   P6   P7   P8   P9
```

---

## WG1–WG8: MOSFET W/L matrix

Each block contains four transistors of the same type, all using the same pad assignment:

| Device | Gate | Source | Drain | Bulk |
|---|---|---|---|---|
| M1 | P1 | P2 | P16 | P3 |
| M2 | P15 | P4 | P14 | P5 |
| M3 | P13 | P6 | P12 | P7 |
| M4 | P10 | P9 / P11 | P11 / P9 | P8 |

- **NMOS blocks (WG1, 3, 5, 7):** the bulk pads (P3, P5, P7, P8) all connect to the common P-substrate.
- **PMOS blocks (WG2, 4, 6, 8):** each transistor sits in its own N-well, so each bulk pad is separate.
- The gates connect directly to pads with **no ESD protection**. Handle and probe the wafers with ESD precautions.

### Device sizes (W / L in µm)

| Block | Type | M1 | M2 | M3 | M4 | Purpose |
|---|---|---|---|---|---|---|
| **WG1** | NMOS | 3.4 / 1 | 3.4 / 2 | 3.4 / 4 | 50 / 30 | Short-channel effects at minimum width, plus a large long-channel reference device |
| **WG2** | PMOS | 3.4 / 1 | 3.4 / 2 | 3.4 / 4 | 50 / 30 | PMOS version of WG1 |
| **WG3** | NMOS | 30 / 1 | 30 / 2 | 30 / 4 | 30 / 5 | Channel-length dependence (short L) with wide devices: Vth roll-off, DIBL, Idsat |
| **WG4** | PMOS | 30 / 1 | 30 / 2 | 30 / 3 | 30 / 5 | PMOS version of WG3 |
| **WG5** | NMOS | 30 / 7 | 30 / 10 | 30 / 20 | 30 / 30 | Channel-length dependence (long L): long-channel Vth, mobility, ΔL extraction |
| **WG6** | PMOS | 30 / 7 | 30 / 10 | 30 / 20 | 30 / 30 | PMOS version of WG5 |
| **WG7** | NMOS | 3.4 / 30 | 5 / 30 | 7 / 30 | 10 / 30 | Channel-width dependence at long L: narrow-width effect, ΔW extraction |
| **WG8** | PMOS | 3.4 / 30 | 5 / 30 | 7 / 30 | 10 / 30 | PMOS version of WG7 |

---

## WG9: Junction diodes and MOS capacitor

| Device | Structure | Size | Terminal A | Terminal B |
|---|---|---|---|---|
| D1 | N+ / P-sub diode, **finger** (14 × 3.6 µm stripes) | Area 5080 µm², perimeter 2923 µm | P16 (N+, cathode) | P4 (P-sub tap, anode) |
| D2 | N+ / P-sub diode, **plate** | Area 9798 µm², perimeter 396 µm | P15 (N+, cathode) | P5 (P-sub tap, anode) |
| D3 | P+ / N-well diode, **finger** (14 × 3.6 µm stripes) | Area 5080 µm², perimeter 2923 µm | P13 (P+, anode) | P7 (N-well tap, cathode) |
| D4 | P+ / N-well diode, **plate** | Area 9798 µm², perimeter 396 µm | P12 (P+, anode) | P8 (N-well tap, cathode) |
| C1 | MOS capacitor on CSIO (AC) region in N-well | Gate 59.8 × 59.8 µm (3576 µm²), AC 57 × 57 µm (3249 µm²) | P14 (Gate) | P6 (N+ ring / N-well) |

- The finger and plate diodes pair up so that the **area and perimeter (sidewall) components** of junction capacitance and leakage can be separated.
- D1 and D2 share the P-substrate, so P4 and P5 are resistively connected. D3 and D4 share one N-well, so P7 and P8 are too.
- P1, P2 and P3 are not connected. P9, P10 and P11 are shorted by metal but connect to no device.

---

## WG10: Ring oscillator

| Item | Value |
|---|---|
| Structure | NAND2 (enable) + chain of 30 `INV_X1` (24 µm pitch) |
| INV_X1 | PMOS W/L = 10.2 / 1 µm, NMOS W/L = 3.4 / 1 µm |
| P2 | IN (enable, NAND2 input A) |
| P4 | GND |
| P16 | VDD |
| P10 | OUT (buffered by the last inverter) |
| Other pads | Not connected |

- NAND2 input B is the feedback node (`loop`), taken from the output of the 29th inverter. The 30th inverter drives OUT as an output buffer.
- Measure the frequency at OUT with IN = VDD. The gate delay per stage is t_pd = 1 / (2 · N · f).
