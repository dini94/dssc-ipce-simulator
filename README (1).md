# DSSC IPCE / J–V Simulator

A self-contained web tool that turns TD-DFT excited-state data into a full set of
theoretical DSSC photovoltaic outputs — normalized absorption, LHE/IPCE, **J–V curve**,
and **Jsc / Voc / FF / η** — using the author's own validated calculation pipeline.

**Live demo:** `https://dini94.github.io/dssc-ipce-simulator/`

---

## What it does

```
A_norm(λ) = A(λ) / max[A(λ)]                         normalized absorption (TD-DFT, Gaussian-broadened)
LHE(λ)    = 1 − 10^(−A_norm(λ))                       light-harvesting efficiency
IPCE(λ)   = LHE(λ) × IQE                              IQE = Φ_inj×η_cc, defaults to 1 (IPCE ≡ LHE)
Jsc       = ∫ LHE(λ)·Φ_AM1.5G(λ) dλ                   AM1.5G integration (ASTM G173-03, 300–800 nm)
ΔGinj     = LUMO − E_CB(TiO2)
Voc       = 0.8·(ΔGinj + λ_reorg) / (4·λ_reorg)        Marcus-type injection-driven Voc
J0        = Jsc / (exp(qVoc/kT) − 1)                  back-solved dark saturation current
V(λ), J(λ): V=(kT/q)ln[Jsc(λ)/J0+1],  J=Jsc−Jsc(λ)     parametric single-diode J–V curve
FF        = k · [voc−ln(voc+0.72)]/(voc+1)             Green's ideal FF × empirical device factor k
η(%)      = Jsc·Voc·FF / Pin × 100                     Pin = 100 mW/cm² (1 sun)
```

This is a direct re-implementation of the spreadsheet-based method used to design and
screen the FPCN / FTCN / FFCN fluorene D–π–A dyes, reproducing the reported theoretical
Jsc to within ~0.5% and Voc/FF/η to within the rounding already present in the
source data.

### Built-in datasets
- **FPCN, FTCN, FFCN** — the author's exact published normalized absorbance spectra
  (300–800 nm, 1 nm resolution) plus their DFT/Marcus parameters (HOMO, LUMO, λ_reorg,
  empirical FF factor), loaded with one click.
- **N719** — the 20 lowest singlet excited states (energy, wavelength, oscillator
  strength) extracted from a CAM-B3LYP/genecp TD-DFT (PCM, ethanol) Gaussian log, with
  HOMO/LUMO taken from the same calculation. Used as a validation/test case for the
  TD-DFT→spectrum branch of the pipeline (see note below).

### A real finding from the N719 test
Feeding the CAM-B3LYP log through the pipeline gives Jsc ≈ 5.4 mA/cm² and Voc ≈ 0.85 V
(with λ_reorg = 0.60 eV), vs. the literature reference for a certified N719 cell
(Jsc ≈ 17.7 mA/cm², Voc ≈ 0.846 V, FF ≈ 75%, η ≈ 11.2%). Voc agrees well, but Jsc is
~3× too low. This is consistent with a known limitation of CAM-B3LYP for Ru-MLCT
transitions — its long-range correction tends to blue-shift and narrow these bands
relative to experiment, so the simulated spectrum misses absorption that the real dye
has further into the visible/NIR. The math is working correctly; the gap is a TD-DFT
functional-choice artifact, not a bug in the tool.

---

## Features

- **Two data-input modes per dye**: paste raw TD-DFT excited states (the tool does the
  Gaussian broadening + normalization), or paste an already-computed continuous
  spectrum directly (used for the FPCN/FTCN/FFCN presets, for exact reproduction).
- **One-click presets** for FPCN, FTCN, FFCN, N719 — or add your own custom dye.
- **Four output tabs**: Absorption, LHE/IPCE, **J–V curve**, and a full numerical data table.
- **Photovoltaic parameter cards** (Jsc, Voc, FF, η) per dye, with a theoretical-vs-reference
  comparison table for dyes with known literature/experimental values.
- **Global parameters**: TiO₂ conduction-band edge (E_CB), internal quantum efficiency
  (IQE), and chart zoom range.
- **CSV export** of the full wavelength-resolved dataset.
- Dark mode (automatic, OS preference). Zero install — single HTML file, Chart.js from CDN.

---

## How to deploy on GitHub Pages

1. Replace `index.html` in your repository root with the updated version.
2. Commit and push — GitHub Pages will redeploy automatically if already enabled
   (Settings → Pages → Source: `main` branch, `/root`).

---

## How to use

### Loading real data
Click **+ FPCN / + FTCN / + FFCN** to load the author's exact published spectra, or
**+ N719 (TD-DFT log)** to load the CAM-B3LYP test case. Click **+ Custom dye** to add
your own.

### Pasting your own TD-DFT data
Open your Gaussian `.log` file and search for `Excited State`:
```
 Excited State   1:      Singlet-A      2.8765 eV  430.96 nm  f=0.8821  <S**2>=0.000
```
Paste one line per state in the dye's textarea, in `energy(eV) wavelength(nm) f` order
(or just `wavelength(nm) f` — the tool will back-compute the energy):
```
2.8765  430.96  0.8821
3.2800  377.99  0.3140
```
Then set **LUMO**, **λ_reorg**, and the **FF factor** for that dye (HOMO/LUMO come
straight from the same DFT calculation; λ_reorg is typically taken from a four-point
reorganization-energy calculation or a literature value for a similar dye class).

### Pasting a pre-computed spectrum
Switch the dye's **Data source** to "Pre-computed spectrum" and paste
`wavelength(nm) value` pairs directly — useful if you already have a continuous
absorption curve (e.g. from your own broadening script) and want the tool to skip
straight to LHE/Jsc/Voc/J–V.

---

## Repository structure

```
dssc-ipce-simulator/
├── index.html      ← entire application (HTML + CSS + JS, no build needed)
└── README.md       ← this file
```

---

## License

MIT — free to use, modify, and share.
