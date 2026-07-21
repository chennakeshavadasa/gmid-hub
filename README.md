# gm/ID Explorer Hub

**A unified, browser-based gm/ID characterisation portal for three open-source PDKs.**

**Live site:** https://chennakeshavadasa.github.io/gmid-hub/

---

## Overview

The gm/ID methodology uses transconductance efficiency (`gm/ID`, units V⁻¹) as the primary MOSFET design variable instead of overdrive voltage `V_OV`. It works across all inversion levels — weak, moderate, and strong — without approximation, and naturally handles short-channel effects that classical square-law models miss.

This hub consolidates three separate interactive gm/ID tools — one per open-source PDK — into a single webpage with a unified dark-mode UI. Switch between PDKs instantly without opening multiple browser tabs.

---

## Supported PDKs

| Tab | PDK | Node | Type | Devices | VDD |
|-----|-----|------|------|---------|-----|
| **IHP SG13G2** | IHP Microelectronics SG13G2 | 130 nm BiCMOS | Open source (Apache 2.0) | LV-NMOS, LV-PMOS, HV-NMOS, HV-PMOS | 1.2V / 3.3V |
| **SKY130A** | SkyWater / Google SKY130A | 130 nm CMOS | Open source (Apache 2.0) | NMOS/PMOS SVT/LVT/HVT + HV (5V/10.5V) | 1.8V / 5V |
| **GF180MCU-D** | GlobalFoundries GF180MCU-D | 180 nm CMOS | Open source (Apache 2.0) | NMOS/PMOS 3.3V/5V/6V + DSS variants | 3.3V / 5V / 6V |

---

## Features

### PDK Switcher
- Three tabs in the top bar — **IHP SG13G2 · SKY130A · GF180MCU-D**
- Keyboard shortcuts: **1**, **2**, **3** to jump between PDKs
- Each tool is lazy-loaded on first visit and kept alive in the background — switching back is instant
- Per-PDK colour coding: cyan (IHP) · green (SKY130) · amber (GF180)

### Per-tool Features (identical across all three PDKs)
- **gm/ID Explorer** — 7 interactive Plotly plots:
  - `gm/ID vs Vgs`
  - `gm/ID vs Vov` (= Vgs − Vth)
  - `gm/gds vs gm/ID` (intrinsic voltage gain)
  - `ID/W vs gm/ID` (normalised current density, µA/µm)
  - `fT vs gm/ID` (transit frequency, GHz)
  - `Cgd/Cgg vs gm/ID`
  - `Cgs/Cgg vs gm/ID`
- **Overview mode** — 6 plots simultaneously in a grid
- **Channel length filter** — toggle individual L values or quick-select All / Short / Long
- **Log X / Log Y** axis toggles
- **PNG export** of any plot
- **Design Helper** (IHP) — σ-normalised weighted L² optimiser to find the best operating point given target gm/ID and gm/gds
- **PDK Reference Panel** — device specs, passives, metal stack, corner guide, EDA tool links

### Unified UI
All three tools share the IHP design system applied via same-origin CSS injection:
- **IBM Plex Mono** for all labels, values, buttons, and navigation
- **IBM Plex Sans** for body text and descriptions
- Dark background (`#0b0e11`), dark surface (`#111518`), `#1e2530` borders
- Per-PDK accent colour applied to active states, selected buttons, and value readouts
- Normalised Plotly modebar, scrollbars, tooltips, tables, and form inputs

---

## Simulation Details

All characterisation data was generated with **ngspice** DC sweeps at:

| Parameter | Value |
|-----------|-------|
| Corner | TT (typical-typical) |
| Temperature | 27°C (300 K) |
| V_DS | V_DD / 2 |
| V_SB | 0 V |
| W | 2 µm |
| Points/curve | 300 (downsampled from 8k–60k raw points) |

---

## Individual Tool Repositories

| PDK | Tool repo | Data repo |
|-----|-----------|-----------|
| IHP SG13G2 | [gmid_IHP130_Tool](https://github.com/chennakeshavadasa/gmid_IHP130_Tool) | [gmid_IHP130](https://github.com/chennakeshavadasa/gmid_IHP130) |
| SKY130A | [gmid_SKY130A](https://github.com/chennakeshavadasa/gmid_SKY130A) | [gmid_SKY130](https://github.com/chennakeshavadasa/gmid_SKY130) |
| GF180MCU-D | [gmid_GF180MCUD](https://github.com/chennakeshavadasa/gmid_GF180MCUD) | [Low-Power-Circuits-IoT-Systems-Group/gmid_GF180MCUD](https://github.com/Low-Power-Circuits-IoT-Systems-Group/gmid_GF180MCUD) |

---

## How It Works

The hub is a **single static HTML file** (`index.html`) with no build step, no backend, and no dependencies beyond CDN-loaded fonts.

Each PDK tool runs in a full-height `<iframe>`. Since all three tools are hosted on the same origin (`chennakeshavadasa.github.io`), the hub can access each iframe's `contentDocument` after load and inject a normalising CSS theme — bringing SKY130A and GF180MCU-D visually in line with the hand-crafted IHP tool UI.

```
gmid-hub/
├── index.html     # entire hub — tab switcher + iframe manager + CSS injector
├── .nojekyll      # disables Jekyll so GitHub Pages serves index.html as-is
└── README.md
```

---

## Local Preview

No install needed:

```bash
# Python (built-in)
python3 -m http.server 8080

# Node
npx serve .
```

Then open `http://localhost:8080`.

---

## Deploy Your Own Fork

```bash
# Clone
git clone https://github.com/chennakeshavadasa/gmid-hub
cd gmid-hub

# (Optional) point tabs at your own PDK tool forks by editing PDK_CONFIG in index.html

# Push to your GitHub
gh repo create gmid-hub --public --source=. --remote=origin --push

# Enable Pages: repo Settings → Pages → Branch: master / (root) → Save
```

---

## gm/ID Design Methodology — Quick Reference

| Region | gm/ID (V⁻¹) | Inversion | Optimised for | Trade-off |
|--------|-------------|-----------|---------------|-----------|
| Sub-threshold (WI) | 20–28 | Weak | Ultra-low power, max current efficiency | Slow, exponential IDS sensitivity |
| Moderate (MI) | 10–20 | Moderate | Best power-bandwidth product | Complex modelling region |
| Strong inversion (SI) | 5–10 | Strong | High speed, good matching | Higher IDS for given gm |
| Deep saturation | 1–5 | Very strong | RF, max gm per area | Lowest current efficiency |

Key relations:
```
gm/ID = gm / IDS                    (transconductance efficiency)
gm·rds = gm / gds                   (intrinsic voltage gain)
fT = gm / (2π · Cgg)                (transit frequency)
W = gm / (gm/ID × ID/W)             (device sizing from LUT)
```

---

## Technology

| Component | Details |
|-----------|---------|
| Plotly.js 2.27 | Interactive charts (CDN) |
| IBM Plex Mono / Sans | Typography (Google Fonts CDN) |
| Pure HTML / CSS / JS | Zero build toolchain |
| Data format | JSON embedded in each tool's `index.html` |
| Hosting | GitHub Pages (static) |

---

## Author

**Nithin Purushothama**
Analog Design Engineer · IHP SG13G2, SKY130A, GF180MCU-D open-source PDK tools

- GitHub: [@chennakeshavadasa](https://github.com/chennakeshavadasa)
- IHP tool: https://chennakeshavadasa.github.io/gmid_IHP130_Tool/
- SKY130A tool: https://chennakeshavadasa.github.io/gmid_SKY130A/
- GF180MCU-D tool: https://chennakeshavadasa.github.io/gmid_GF180MCUD/

---

## License

Tool code: **MIT License** — free to use, modify, and distribute.

PDK data and models:
- IHP SG13G2: Apache 2.0 (© 2024 IHP PDK Authors)
- SKY130A: Apache 2.0 (© SkyWater Technology / Google)
- GF180MCU-D: Apache 2.0 (© GlobalFoundries PDK Authors)
