# Engineering analysis and verification

This note is a worked small-signal interpretation of the **simplified SPICE model** used in the project. It is not a physical silicon measurement or process-qualified design.

## 1. Given design values

| Parameter | Value |
|---|---:|
| `VDD` | 1.8 V |
| `RD` | 9 kΩ |
| `VGS` | 0.65 V |
| Model threshold `VTO` | 0.45 V |
| `KP` | 200 µA/V² |
| `W/L` | 25 µm / 1 µm = 25 |
| `LAMBDA` | 0.05 V⁻¹ |
| Load sweep `Cload` | 2, 5, 10, 20 pF |
| Signal for transient/Fourier | 5 mV peak, 10 kHz |

## 2. Verify the operating region at the selected bias

With source grounded, `VGS = 0.65 V` and `VOV = VGS - VTO = 0.20 V`. The logged quiescent output is `VDS = Vout,Q = 0.861244023 V`. Because `VDS > VOV` and `VGS > VTO`, the device is in **saturation in this simplified long-channel model** at the design bias.

Inferring drain current from the drop across the drain resistor:

`ID ≈ (VDD - Vout,Q)/RD = (1.8 - 0.861244023)/9000 = 104.306 µA`.

## 3. Small-signal gain estimate

Using the project’s Level-1 square-law model with channel-length modulation at the simulated bias:

`ID = (1/2) · KP · (W/L) · VOV² · (1 + λ·VDS)`

`gm = KP · (W/L) · VOV · (1 + λ·VDS) ≈ 1.0431 mS`

`ro = (1 + λ·VDS)/(λ·ID) ≈ 200.00 kΩ`

`Av ≈ -gm · (RD || ro) ≈ -8.9833 V/V`.

The `.dc` sweep measurement around `Vin=0.65 V`, between 0.645 and 0.655 V, reports **-8.982867 V/V**. Small differences are expected between the differential small-signal estimate and a finite-difference DC slope.

## 4. Load capacitance vs bandwidth

The AC run uses an **AC 1 V** input for convenient extraction of `Vout/Vin`. Under AC analysis, the phase and magnitude of the voltage transfer should be considered together. The high-frequency pole is driven primarily by the output resistance interacting with the output load capacitance in this simplified model.

| Load | Measured −3 dB bandwidth |
|---:|---:|
| 2 pF | 9.239827 MHz |
| 5 pF | 3.695929 MHz |
| 10 pF | 1.847965 MHz |
| 20 pF | 0.923983 MHz |

Because the Level-1 device model used here does not capture modern transistor parasitics and process variation, these figures are **simulation-specific**, not guaranteed silicon bandwidths.

## 5. Transient and harmonic results

With a sinusoidal 10 kHz input of **5 mV peak**, `.meas` reports `Vin,pp ≈ 9.999514 mV` and `Vout,pp ≈ 89.826524 mV`. The peak-to-peak gain **magnitude** is **8.983089**. The common-source voltage response is inverted.

LTspice `.four 10k 9 5 V(vout)` reports **0.517208% total harmonic distortion**. The second harmonic is the dominant nonfundamental component in the logged spectrum. The THD result pertains only to this input amplitude, bias point, device model and analysis settings.

## 6. Scope, caveats and next work

- This is a **single-transistor resistor-loaded amplifier**. It is not a complete CMOS operational amplifier or fabricated IC.
- The device is a **generic `LEVEL=1` NMOS with a custom inline model**, not a foundry PDK or transistor from a commercial process.
- LTspice logs also report **“Length shorter than recommended for a level 1 MOSFET.”** This limitation should not be suppressed.
- This archive does not contain bench measurements, wafer data, corner sweeps, Monte Carlo mismatch results, or extracted parasitic layout.
- Next experiments: bias-point sweep, distortion versus amplitude, frequency response with realistic device models, temperature and model-parameter sensitivity, and a CMOS differential pair.

## Provenance

Numeric values were extracted from the uploaded **LTspice 26.0.2 for macOS** log files dated 3 November 2026, and plotted directly from the supplied LTspice raw data. `.asc` files in `circuits/` have been reorganized by **actual analysis type**, not the original misleading source filename; read `notes/source-file-mapping.md` for details. The original ZIP remains the unmodified record.
