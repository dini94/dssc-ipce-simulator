# DSSC IPCE Simulator

A self-contained web tool that generates simulated **IPCE (Incident Photon-to-Current Conversion Efficiency)** spectra for organic DSSC dyes directly from TD-DFT excitation data.

**Live demo:** `https://your-username.github.io/dssc-ipce-simulator/`

![Preview](preview.png)

---

## What it does

Implements the standard theoretical IPCE pipeline used in DFT/TD-DFT DSSC photovoltaic studies:

```
IPCE(λ) = LHE(λ) × Φ_inj × η_coll
```

where:

| Symbol | Meaning | Source |
|--------|---------|--------|
| `LHE(λ) = 1 − 10^−f(λ)` | Light-harvesting efficiency | Gaussian-broadened oscillator strengths from TD-DFT |
| `Φ_inj` | Electron injection efficiency | Constant, from ΔG_inj, or Marcus τ_inj/τ_relax |
| `η_coll` | Electron collection efficiency | User-set (typically 0.80–0.95) |

### References
- Consiglio et al., *New J. Chem.* **2024**, 48, 10026–10037
- Khadiri et al., *RSC Adv.* **2025**, 15, 26807–26829

---

## Features

- **Multi-dye overlay** — add up to 8 dyes with individual colors and dash patterns
- **Three Φ_inj modes** — constant / from ΔG_inj / from Marcus τ_inj/τ_relax ratio
- **Four output tabs** — IPCE spectra, LHE curves, broadened absorption, numerical data table
- **Export** — CSV (for Excel), PNG (publication figure), JSON (further analysis)
- **Dark mode** — automatic based on OS preference
- **Zero dependencies to install** — single HTML file, loads Chart.js from CDN

---

## How to deploy on GitHub Pages

### Option A — Simplest (no build step)
1. Create a new GitHub repository (e.g. `dssc-ipce-simulator`)
2. Upload `index.html` to the root of the repository
3. Go to **Settings → Pages → Source**: select `main` branch, folder `/root`
4. Click Save — your site will be live at:
   ```
   https://your-username.github.io/dssc-ipce-simulator/
   ```

### Option B — Upload via GitHub web interface
1. Go to your repository on GitHub
2. Click **Add file → Upload files**
3. Drag `index.html` and `README.md` into the upload area
4. Click **Commit changes**
5. Enable GitHub Pages as in Option A Step 3

---

## How to use

### Step 1 — Extract excitation data from Gaussian
Open your `.log` file and search for `Excited State`. You will find lines like:
```
 Excited State   1:      Singlet-A      2.8765 eV  430.96 nm  f=0.8821  <S**2>=0.000
 Excited State   2:      Singlet-A      3.2800 eV  377.99 nm  f=0.3140  <S**2>=0.000
 Excited State   3:      Singlet-A      3.8500 eV  321.99 nm  f=0.1520  <S**2>=0.000
```

Copy the **nm** value and **f** value for each excited state, one per line:
```
430.96  0.8821
377.99  0.3140
321.99  0.1520
```
Paste into the text area for that dye.

### Step 2 — Configure parameters

| Parameter | Recommended range | Notes |
|-----------|-------------------|-------|
| Broadening σ | 20–35 nm | Higher = broader band; match experimental UV-vis shape |
| η_coll | 0.80–0.95 | Standard TiO₂/I⁻/I₃⁻ electrolyte |
| Φ_inj (constant) | 0.90–0.99 | Use when assuming fast injection |
| Dye loading Γ | 10–30 nmol/cm² | Match your experimental device |

### Step 3 — Generate and export
Click **Generate IPCE Spectra**. Switch between the output tabs. Use the export buttons to save data.

---

## Repository structure

```
dssc-ipce-simulator/
├── index.html      ← entire application (HTML + CSS + JS, no build needed)
└── README.md       ← this file
```

---

## Formula reference

| Parameter | Formula |
|-----------|---------|
| Gaussian broadening | `f(λ) = Σᵢ fᵢ × exp(−(λ−λᵢ)²/(2σ²))` |
| LHE | `LHE(λ) = 1 − 10^−f(λ)` |
| Φ_inj from ΔG | `Φ_inj ∝ f(−ΔG_inj)` |
| Φ_inj from τ | `Φ_inj = 1/(1 + τ_inj/τ_relax)` |
| IPCE | `IPCE(λ) = LHE(λ) × Φ_inj × η_coll` |
| APCE | `APCE(λ) = IPCE(λ)/LHE(λ) = Φ_inj × η_coll` |

---

## License

MIT — free to use, modify, and share.
