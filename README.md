# Physics Simulation Platform

An interactive web-based simulation platform covering core electrical engineering topics — built from scratch in pure HTML/CSS/JavaScript, with numerical algorithms implemented directly (no external libraries).

**Live:** [leejh231.github.io/physics-sim-platform](https://leejh231.github.io/physics-sim-platform)

---

## Structure

```
physics-sim-platform/
├── index.html                          ← Main landing page
│
├── signals/                            ← Signals & Systems (9 simulators)
│   ├── 01_fourier/                     ← Fourier Transform (3D interactive, FFT, Gibbs)
│   ├── 02_am_fm_dsb/                   ← AM / FM / DSB-SC modulation
│   ├── 03_pm_dm/                       ← PM / DM / ADM
│   ├── 04_sampling/                    ← Sampling & Nyquist theorem
│   ├── 05_digital_modulation/          ← BPSK / QPSK / FSK / QAM overview
│   ├── 06_probability/                 ← Probability & Statistics (Gaussian, CLT)
│   ├── 07_bpsk_qpsk_pipeline/          ← BPSK/QPSK full demodulation pipeline ★
│   ├── 08_qam_pipeline/                ← 16/64-QAM full demodulation pipeline ★
│   └── 09_am_fm_pipeline/              ← AM/FM demodulation pipeline ★
│
└── em_optics/                          ← EM & Optics (2 + 2 planned)
    ├── 01_ar_coating/                  ← 1D FDTD AR Coating simulator ★
    └── 02_double_slit/                 ← 2D FDTD Young's Double Slit ★
```

---

## Simulator Overview

### Signals & Systems

| # | Topic | Key Concepts |
|---|-------|-------------|
| S01 | Fourier Transform | 3D visualization, FFT, superposition, Gibbs phenomenon |
| S02 | AM / FM / DSB-SC | Modulation index, Bessel functions, Carson bandwidth |
| S03 | PM / DM / ADM | Angle modulation, slope overload, adaptive step |
| S04 | Sampling & Nyquist | Aliasing, sinc reconstruction, spectrum replication |
| S05 | Digital Modulation Overview | Constellation diagram, BER curves, spectral efficiency |
| S06 | Probability & Statistics | Gaussian, exponential, CLT, joint distribution |
| S07 | BPSK/QPSK Pipeline ★ | Matched filter, eye diagram, carrier recovery, BER |
| S08 | QAM 16/64 Pipeline ★ | Gray coding, I/Q demodulation, AGC, constellation |
| S09 | AM/FM Demodulation ★ | Envelope detection, limiter-discriminator, FM threshold |

### EM & Optics

| # | Topic | Key Concepts |
|---|-------|-------------|
| E01 | AR Coating (1D FDTD) ★ | Yee algorithm, PML, Fresnel coefficients, Poynting vector |
| E02 | Young's Double Slit (2D FDTD) ★ | TM mode, PEC barrier, interference pattern, theory comparison |

★ = Full pipeline / advanced simulator

---

## Physics Foundation

### Maxwell's Equations (basis of all EM simulations)

**Differential form:**
```
∇·D = ρ_free          (Gauss's Law — electric)
∇·B = 0               (Gauss's Law — magnetic, no magnetic monopoles)
∇×E = -∂B/∂t          (Faraday's Law — changing B induces E)
∇×H = J + ∂D/∂t       (Ampere-Maxwell Law — currents and changing E induce H)
```

**Integral form:**
```
∮_S D·dA = Q_enc       (total free charge enclosed)
∮_S B·dA = 0           (no net magnetic flux through closed surface)
∮_C E·dl = -d/dt ∫_S B·dA    (EMF = -rate of change of magnetic flux)
∮_C H·dl = I_enc + d/dt ∫_S D·dA  (Ampere's Law with displacement current)
```

**Wave equation (derived from Maxwell's equations in free space):**
```
∇²E = μ₀ε₀ ∂²E/∂t²    →    c = 1/√(μ₀ε₀) ≈ 3×10⁸ m/s
```

### Fourier Transform

```
F(ω) = ∫₋∞^∞ f(t) · e^{-jωt} dt      (Forward)
f(t) = (1/2π) ∫₋∞^∞ F(ω) · e^{jωt} dω  (Inverse)
```

The 3D visualizer shows: viewing along **Z-axis** → see f(t), viewing along **X-axis** → see |F(ω)|. Each frequency basis function e^{-jωt} traces a helix in (Re, Im, t) space.

---

## Tech Stack

- **Frontend:** Pure HTML / CSS / JavaScript — no external libraries
- **Numerical methods:** Cooley-Tukey FFT, Bessel functions, sinc interpolation, Yee FDTD — all implemented from scratch
- **Source code:** Python FDTD files (`AR_Coating.py`, `Double_Slit_FDTD.py`) ported to JS for browser execution
- **Deployment:** GitHub Pages

## Background

Built as a portfolio project covering EE undergraduate coursework:
- Signals & Systems / Communication Theory
- Electromagnetics / Wave Optics
- Numerical Analysis / FDTD simulation

---

*EE undergraduate level | Signals & Systems · Communication Theory · Electromagnetics*
