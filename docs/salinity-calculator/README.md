# Salinity Calculator (GitHub Pages)

[carlhammond.github.io/Git-Pages/salinity-calculator](https://carlhammond.github.io/Git-Pages/salinity-calculator)

A static, browser-only replica of the n8n **"Salinity Calculator V2"** workflow
(`n8n/Salinity Calculator/Salinity Calculator V2.json`), rebuilt for hosting on
GitHub Pages and improved for hobby **reef aquarium** use.

No server, no n8n instance and no network calls — everything runs in the
visitor's browser from a single `index.html`.

## Enabling GitHub Pages

1. Push this branch / merge to `main`.
2. Repo **Settings → Pages**.
3. **Source:** *Deploy from a branch*.
4. **Branch:** `main` (or your chosen branch) and **folder:** `/docs`.
5. Save. The calculator will be served at
   `https://<user>.github.io/<repo>/` (e.g. `https://carlhammond.github.io/oracle-oci/`).

> The page is fully self-contained, so it also works by simply opening
> `docs/index.html` locally in a browser.

## What it does

Inputs (mirrors the original n8n form):

- **Observed reading** — specific gravity *or* ppt
- **Observed temperature** — °C or °F
- **Instrument** — hydrometer (temperature-corrected) or refractometer (ATC)
- **Calibration temperature** — 25 °C / 20 °C / 60 °F

Outputs:

- **Salinity** in ppt (practical salinity / PSU)
- **Specific gravity** on the 25 °C/25 °C basis (the reef-hobby convention)
- **Conductivity** at 25 °C (approximate, mS/cm)
- **Density** at 25 °C
- A **reef status indicator** (in-range / slightly off / out of range vs the
  35 ppt natural-seawater target)

## How the salinity conversion was improved for reef use

The original V2 workflow had a few issues for aquarium use:

| Issue in V2 | Fix here |
|---|---|
| Displayed `ppt` used a crude `(SG − 1) × 1000 × 1.40` rule of thumb → reads **~37 ppt** at SG 1.0264 when the truth is **35 ppt**. | Salinity is solved from the full **EOS-80 / UNESCO 1981** seawater equation of state. SG 1.0264 → **35.0 ppt**. |
| Temperature correction was a flat `0.0003 / °C` linear fudge. | The reading is converted to absolute density using the density of pure water at the **calibration temperature**, then salinity is solved at the **sample temperature** — proper physics, no magic constant. |
| The accurate `psu` value was computed but **never shown** on the form. | Practical salinity (PSU/ppt) is the headline result. |
| No reef context. | Targets, an in-range indicator, °F support, ppt entry, conductivity, and separate hydrometer vs refractometer handling. |

### Reef reference values used

- Natural seawater target: **35 ppt** = **SG 1.0264** (25 °C/25 °C) ≈ **53 mS/cm** at 25 °C
- Comfortable reef band: **33–36 ppt**
- A console self-test logs `SG(35 ppt, 25 °C)` and the salinity round-trip on load.

### Accuracy notes

- EOS-80 is valid 0–40 ppt, −2 to 40 °C — comfortably covering aquarium conditions.
- Conductivity is a local quadratic fit anchored to standard seawater
  (30/35/40 ppt → 46.1/53.1/60.0 mS/cm at 25 °C); treat it as approximate.
- Hydrometer correction ignores glass thermal expansion (negligible, ~2.5×10⁻⁵ /°C).
- Refractometers are assumed to have automatic temperature compensation (ATC) and
  to read true salinity; for cheap brine/sucrose refractometers, calibrate with a
  35 ppt reference solution first.
