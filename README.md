# Radiometric Calibration & Sensor Characterization Toolkit

A pre-launch radiometric calibration workflow for multispectral satellite sensors, implemented as a self-contained Jupyter notebook. The pipeline covers everything from raw Digital Number (DN) simulation to Top-of-Atmosphere (TOA) Reflectance, following internationally recognised standards including **EMVA1288**, **NIST traceability**, and **CEOS Cal/Val protocols**.

Sensor parameters are modelled after a Planet SuperDove-class small-satellite multispectral imager.

---

## Contents

| Section | Description |
|---|---|
| 1. Setup & Sensor Parameters | Sensor configuration, calibration coefficients, orbital geometry |
| 2. Sensor Noise Model | Shot noise, dark current, read noise, and PRNU simulation |
| 3. PTC Analysis (EMVA1288) | System gain, read noise, and full well capacity extraction |
| 4. Radiometric Correction | Dark subtraction and flat-field PRNU correction |
| 5. DN → Radiance → TOA Reflectance | Physical unit conversion pipeline |
| 6. Spectral Response Function Modelling | Gaussian SRF and band-integrated solar irradiance |
| 7. SNR & NEΔρ Analysis | Sensitivity analysis across all spectral bands |
| 8. Radiometric Anomaly Detection | Column striping, dead pixel, and hot pixel detection |
| 9. Calibration Report | EMVA1288-compliant summary report |

---

## Sensor Configuration

| Parameter | Value |
|---|---|
| Full well capacity | 60,000 e⁻ |
| System gain K | 0.5 e⁻/DN |
| Read noise | 12.0 e⁻ RMS |
| Dark current | 50 e⁻/s |
| Quantum efficiency | 65% |
| PRNU sigma | 3% |
| Bit depth | 17-bit (131,072 DN levels) |
| Integration time | 10 ms |
| Image size | 512 × 512 pixels |

**Spectral bands:** Coastal Blue (443 nm), Blue (490 nm), Green (560 nm), Red (665 nm), Red-Edge (705 nm), NIR (842 nm)

---

## Key Results

- **PTC analysis**: K recovered to within 0.1% of true value; FWC estimated at the saturation knee (~97% of true value, consistent with EMVA1288)
- **Flat-field correction**: ~3× non-uniformity reduction (PRNU removed; residual is irreducible single-frame shot noise)
- **TOA reflectance**: Scene flux configured for ~10% TOA reflectance — representative of a mid-brightness Earth scene
- **Anomaly detection**: 100% recall on injected column stripes, dead pixels, and hot pixels using scene-agnostic z-score thresholds

---

## Physical Model

The sensor noise model includes all major noise sources:

$$\sigma_{total}^2 = \sigma_{shot}^2 + \sigma_{dark}^2 + \sigma_{read}^2 + \sigma_{PRNU}^2$$

PRNU is a fixed per-pixel gain variation (hardware property, identical across frames) that is removed by flat-field calibration. The PTC uses the image-pair difference method to cancel PRNU and isolate temporal noise, following the EMVA1288 standard.

TOA reflectance conversion:

$$\rho_{TOA} = \frac{\pi \cdot L_{sensor} \cdot d^2}{E_{sun} \cdot \cos(\theta_s)}$$

where $d$ is Earth-Sun distance, $E_{sun}$ is band-integrated solar irradiance computed from Gaussian SRFs convolved with a 5778 K Planck blackbody spectrum, and $\theta_s$ is solar zenith angle.

---

## Requirements

```
python >= 3.9
numpy
matplotlib
scipy
pandas
jupyter
```

Install dependencies:

```bash
pip install numpy matplotlib scipy pandas jupyter
```

---

## Usage

```bash
jupyter notebook radcal_toolkit.ipynb
```

Run all cells top to bottom. Each section is self-contained and builds on variables defined in prior sections, so sequential execution is required.

**Outputs:** Ten figures are saved to the working directory (`fig1_noise_model.png` through `fig10_summary.png`).

---

## Standards

| Standard | Application |
|---|---|
| EMVA1288 v4.0 | PTC protocol — system gain, read noise, full well capacity |
| NIST traceability | Radiance calibration gain reference |
| CEOS Cal/Val | Calibration model structure |
| ISO 17025 | Z-score anomaly detection thresholds (scene-independent) |

---

## License

MIT

