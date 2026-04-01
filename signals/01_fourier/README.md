# Fourier Transform

> **Platform path:** `signals/01_fourier/`
> **Simulator:** `index.html` — 3D interactive visualization
> **Related:** AM/FM modulation, FFT, sampling theory, digital filters

---

## Table of Contents

1. [Core Idea](#1-core-idea)
2. [Fourier Series](#2-fourier-series)
3. [Continuous Fourier Transform (CTFT)](#3-continuous-fourier-transform-ctft)
4. [Discrete Fourier Transform (DFT)](#4-discrete-fourier-transform-dft)
5. [Fast Fourier Transform (FFT)](#5-fast-fourier-transform-fft)
6. [Square Wave & Gibbs Phenomenon](#6-square-wave--gibbs-phenomenon)
7. [3D Visualization Concept](#7-3d-visualization-concept)
8. [Key Properties](#8-key-properties)
9. [Implementation Notes](#9-implementation-notes)
10. [Connections to Other Topics](#10-connections-to-other-topics)

---

## 1. Core Idea

Any signal can be expressed as a **sum of sinusoids** — this is the fundamental insight of Fourier analysis.

In the time domain, a complex waveform looks arbitrary. In the frequency domain, it decomposes into distinct sinusoidal components, each with a specific frequency, amplitude, and phase.

```
Time domain  →  [Fourier Transform]  →  Frequency domain
x(t)         →                       →  X(f)

Complex waveform  →               →  Frequency spikes
```

**Why it matters:**
- Signal analysis: detect hidden frequency components buried in noise
- Communications: mathematical foundation of modulation/demodulation
- Filter design: pass or block specific frequencies
- Image/audio compression: JPEG, MP3 are Fourier-based

---

## 2. Fourier Series

Applies to **periodic signals**. For a signal with period T:

$$x(t) = \sum_{n=-\infty}^{\infty} c_n \cdot e^{j 2\pi n f_0 t}$$

- `f₀ = 1/T`: fundamental frequency
- `cₙ`: complex coefficient of the n-th harmonic

Coefficients computed by:

$$c_n = \frac{1}{T} \int_0^T x(t) \cdot e^{-j 2\pi n f_0 t} \, dt$$

### Trigonometric form

$$x(t) = A_0 + \sum_{n=1}^{\infty} \left[ A_n \cos(2\pi n f_0 t) + B_n \sin(2\pi n f_0 t) \right]$$

### Superposition principle

```
x(t) = x₁(t) + x₂(t)  ⟺  X(f) = X₁(f) + X₂(f)
```

---

## 3. Continuous Fourier Transform (CTFT)

Extends Fourier series to **aperiodic signals** by letting T → ∞:

$$X(f) = \int_{-\infty}^{\infty} x(t) \cdot e^{-j 2\pi f t} \, dt \quad \leftarrow \text{Forward}$$

$$x(t) = \int_{-\infty}^{\infty} X(f) \cdot e^{j 2\pi f t} \, df \quad \leftarrow \text{Inverse}$$

### Key transform pairs

| Time domain x(t) | Frequency domain X(f) |
|---|---|
| `δ(t)` (impulse) | `1` (flat spectrum) |
| `1` (constant) | `δ(f)` (DC only) |
| `sin(2πf₀t)` | `δ(f-f₀)/2j - δ(f+f₀)/2j` |
| `rect(t/T)` | `T·sinc(fT)` |
| `e^{-at}u(t)` | `1/(a + j2πf)` |

### 3D Interpretation

The Fourier transform at a specific frequency ω₀ can be visualized as:
1. Multiply f(t) by the rotating phasor e^{-jω₀t} = cos(ω₀t) - j·sin(ω₀t)
2. The product f(t)·e^{-jω₀t} winds around the time axis as a helix
3. The **average** of this helix = F(ω₀)

This is exactly what the 3D simulator shows: the helix collapses to a point on the frequency axis that gives |F(ω₀)|.

---

## 4. Discrete Fourier Transform (DFT)

Computers work with discrete samples, so we apply the DFT:

$$X[k] = \sum_{n=0}^{N-1} x[n] \cdot e^{-j 2\pi k n / N}$$

### Parameter relationships

| Parameter | Symbol | Simulator value |
|---|---|---|
| Number of samples | N | 512 |
| Sampling frequency | fs | 100 Hz |
| Frequency resolution | Δf = fs/N | 0.195 Hz |
| Max representable freq | fs/2 (Nyquist) | 50 Hz |
| Signal duration | T = N/fs | 5.12 s |

---

## 5. Fast Fourier Transform (FFT)

Direct DFT computation: **O(N²)** operations. For N=512: 262,144 operations.

The Cooley-Tukey FFT reduces this to **O(N log N)**: 4,608 operations.

**Algorithm:** Split even/odd indices recursively. Each stage performs butterfly operations:
```
A' = A + W·B
B' = A - W·B
```
where W = e^{-j2π/N} is the twiddle factor.

```js
// Implementation in simulator
function fftMag(signal) {
  // 1. Bit-reversal permutation
  // 2. Butterfly operations for each length len
  // 3. Return normalized magnitude: |X[k]| / N
}
```

---

## 6. Square Wave & Gibbs Phenomenon

A square wave contains **only odd harmonics** with amplitudes decaying as 1/k:

$$x_{sq}(t) = \frac{4}{\pi} \sum_{k=1,3,5,...} \frac{1}{k} \sin(2\pi k f_0 t)$$

**Gibbs phenomenon:** At discontinuities, the partial sum always overshoots by ~8.9%, regardless of how many harmonics are included:

$$\text{overshoot} \approx 8.95\%$$

This ringing is the reason digital filters use window functions (Hamming, Hann, Blackman).

---

## 7. 3D Visualization Concept

The 3D simulator (based on the image reference) uses three axes:

```
Y-axis:  amplitude
Z-axis:  time  →  view along Z to see f(t) waveform
X-axis:  frequency  →  view along X to see |F(ω)| spectrum
```

The helices represent the basis functions e^{-jωt}. As you rotate the view:
- **Looking from +Z**: see the time-domain signal f(t) projected on the YZ plane
- **Looking from +X**: see the frequency spectrum |F(ω)| projected on the XY plane
- **Oblique view**: see both simultaneously

---

## 8. Key Properties

| Property | Formula | Meaning |
|---|---|---|
| Linearity | aX(f) + bY(f) | Superposition |
| Time shift | X(f)·e^{-j2πft₀} | Phase shift only, magnitude unchanged |
| Frequency shift | x(t)·e^{j2πf₀t} ↔ X(f-f₀) | Basis of AM modulation |
| Convolution | x(t)*h(t) ↔ X(f)·H(f) | Basis of linear filtering |
| Parseval's theorem | ∫|x(t)|²dt = ∫|X(f)|²df | Energy conservation |
| Duality | X(t) ↔ x(-f) | Time-frequency symmetry |

> **Frequency shift property is the key to AM modulation.** Multiplying by cos(2πfct) shifts the spectrum by ±fc. → See: `signals/02_am_fm_dsb/`

---

## 9. Implementation Notes

### Parameter rationale

```
N  = 512   → power of 2 required by Cooley-Tukey FFT
fs = 100Hz → max representable frequency = 50Hz (Nyquist)
Δf = 100/512 ≈ 0.195Hz → sufficient frequency resolution
T  = 5.12s → multiple periods even for 1Hz signals
```

### Known limitations

- The simulator displays **amplitude spectrum** |X[k]| only; phase spectrum ∠X[k] is shown separately
- Normalization: `/N` applied so amplitude matches original signal
- No window function applied → spectral leakage possible at non-integer frequencies

### Potential improvements

- [ ] 2D spectrogram (time-frequency display)
- [ ] STFT (Short-Time Fourier Transform)
- [ ] Window function selector (Hamming, Hann, Blackman)

---

## 10. Connections to Other Topics

```
Fourier Transform
    │
    ├── Sampling Theory & Nyquist    → signals/04_sampling/
    │       "fs > 2·fmax to reconstruct"
    │
    ├── AM Modulation                → signals/02_am_fm_dsb/
    │       "multiply by cos(2πfct) = spectrum shift"
    │
    ├── FM Modulation                → signals/02_am_fm_dsb/
    │       "instantaneous frequency = fc + kf·m(t)"
    │
    ├── Digital Modulation (PSK, QAM) → signals/05_digital_modulation/
    │       "each symbol = specific phase/frequency/amplitude"
    │
    └── FDTD (Wave equation)         → em_optics/01_ar_coating/
            "plane wave = single frequency in spatial domain"
```

---

*Physics Simulation Platform — Signals & Systems Module*
*Level: EE undergraduate (Signals & Systems, Communication Theory)*
