# Source-file map and reproducibility note

The original archive contained 4 LTspice `.asc` files, logs and raw data. Some schematics had filenames that did not match their *saved simulation directives*. These derived, clearly named schematics present each analysis separately:

| Clean file | Source in uploaded archive | Actual saved directive |
|---|---|---|
| `01_DC_Transfer.asc` | `NMOS_CS_DC.asc` | `.dc V1 0 1.8 5m` |
| `02_AC_Load_Sweep.asc` | `NMOS_CS_Transient.asc` | `.ac dec 100 10 100Meg` and `.step param Cload ...` |
| `03_Transient.asc` | `NMOS_CS_Distortion.asc` | `.tran ...`; `.four` removed from the derived copy to provide a basic transient-only schematic |
| `04_Fourier_Distortion.asc` | `NMOS_CS_Distortion.asc` | `.tran ...` and `.four ...` |

`NMOS_CS_AC.asc` in the original archive also contained a `.dc` sweep; the clean repo excludes that duplicate to avoid confusion. The logged AC frequency response and bandwidth values are from `NMOS_CS_AC.log` and `NMOS_CS_AC.raw`, which **do correspond to the AC load sweep**, despite the corresponding saved schematic being named `NMOS_CS_Transient.asc`.

**Reproduction:** open a clean `.asc` file in LTspice 26 or later with the stock NMOS symbol library, run its command, and compare measurements to `results/measurements.csv`. Numerical outputs may vary with LTspice versions and solver settings. The inline `NDEV` model is retained in all circuits.

Original raw waveform files are intentionally not included in this GitHub-ready package because they can be regenerated from the schematics. The figures in `figures/` were derived from the supplied raw data, and the measurements table from the original simulation logs.
