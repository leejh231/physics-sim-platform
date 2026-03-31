# 푸리에 변환 (Fourier Transform)

> **플랫폼 위치:** `signals/fourier_transform/`  
> **시뮬레이터:** `fourier_transform.html`  
> **관련 주제:** AM 변복조, FFT, 샘플링 이론, 디지털 필터

---

## 목차

1. [핵심 아이디어](#1-핵심-아이디어)
2. [푸리에 급수 (Fourier Series)](#2-푸리에-급수-fourier-series)
3. [연속 푸리에 변환 (CTFT)](#3-연속-푸리에-변환-ctft)
4. [이산 푸리에 변환 (DFT)](#4-이산-푸리에-변환-dft)
5. [고속 푸리에 변환 (FFT)](#5-고속-푸리에-변환-fft)
6. [구형파와 깁스 현상](#6-구형파와-깁스-현상-gibbs-phenomenon)
7. [주요 성질 요약](#7-주요-성질-요약)
8. [시뮬레이터 구현 노트](#8-시뮬레이터-구현-노트)
9. [다음 주제 연결](#9-다음-주제-연결)

---

## 1. 핵심 아이디어

어떤 신호든 **사인파(정현파)의 합**으로 표현할 수 있다.

이것이 푸리에의 핵심 통찰이다. 시간 영역(time domain)에서 복잡하게 보이는 신호도, 주파수 영역(frequency domain)에서 보면 각 성분의 주파수와 진폭이 선명하게 드러난다.

```
시간 영역  →  [푸리에 변환]  →  주파수 영역
x(t)      →                 →  X(f)

복잡한 파형  →               →  주파수 스파이크들
```

**왜 중요한가?**
- 신호 분석: 노이즈 속에 숨겨진 주파수 성분 검출
- 통신: 반송파에 정보를 실어 보내는 변복조의 수학적 기반
- 필터 설계: 원하는 주파수만 통과/차단
- 이미지/오디오 압축: JPEG, MP3 모두 푸리에 변환 기반

---

## 2. 푸리에 급수 (Fourier Series)

**주기 신호**에 적용. 주기 `T`인 신호 `x(t)`를 다음과 같이 표현한다:

$$x(t) = \sum_{n=-\infty}^{\infty} c_n \cdot e^{j 2\pi n f_0 t}$$

- `f₀ = 1/T`: 기본 주파수 (fundamental frequency)
- `cₙ`: n번째 고조파의 복소 계수

계수는 다음으로 계산한다:

$$c_n = \frac{1}{T} \int_0^T x(t) \cdot e^{-j 2\pi n f_0 t} \, dt$$

### 실수 표현 (삼각 형식)

$$x(t) = A_0 + \sum_{n=1}^{\infty} \left[ A_n \cos(2\pi n f_0 t) + B_n \sin(2\pi n f_0 t) \right]$$

- `A₀`: 직류 성분 (DC offset)
- `Aₙ, Bₙ`: n번째 고조파의 코사인/사인 계수

### 중첩 원리 (Superposition)

두 신호의 합 = 각 신호의 스펙트럼의 합:

$$x(t) = x_1(t) + x_2(t) \quad \Leftrightarrow \quad X(f) = X_1(f) + X_2(f)$$

**시뮬레이터의 "파형 합성" 탭이 이 원리를 시각화한다.**

```js
// 구현: 각 성분을 직접 계산해 더함
const s1 = t.map(ti => A1 * Math.sin(2*Math.PI*f1*ti + phi1))
const s2 = t.map(ti => A2 * Math.sin(2*Math.PI*f2*ti + phi2))
const sum = s1.map((v, i) => v + s2[i])
```

---

## 3. 연속 푸리에 변환 (CTFT)

**비주기 신호**로 확장. 주기 `T → ∞`로 보내면 이산 스펙트럼이 연속 스펙트럼이 된다:

$$X(f) = \int_{-\infty}^{\infty} x(t) \cdot e^{-j 2\pi f t} \, dt \quad \leftarrow \text{정변환}$$

$$x(t) = \int_{-\infty}^{\infty} X(f) \cdot e^{j 2\pi f t} \, df \quad \leftarrow \text{역변환}$$

### 주요 변환 쌍 (Transform Pairs)

| 시간 영역 `x(t)` | 주파수 영역 `X(f)` |
|---|---|
| `δ(t)` (임펄스) | `1` (모든 주파수 균일) |
| `1` (상수) | `δ(f)` (0Hz에서만) |
| `sin(2πf₀t)` | `δ(f-f₀)/2j - δ(f+f₀)/2j` |
| `cos(2πf₀t)` | `δ(f-f₀)/2 + δ(f+f₀)/2` |
| `rect(t/T)` (구형 펄스) | `T·sinc(fT)` |
| `e^(-at)u(t)` (지수 감쇠) | `1/(a + j2πf)` |

> **sinc 함수:** `sinc(x) = sin(πx) / (πx)`

### 위상의 의미

푸리에 변환 결과는 복소수다:

$$X(f) = |X(f)| \cdot e^{j\angle X(f)}$$

- `|X(f)|`: 진폭 스펙트럼 (amplitude spectrum) — 각 주파수의 세기
- `∠X(f)`: 위상 스펙트럼 (phase spectrum) — 각 주파수 성분의 시간 지연

**진폭이 같아도 위상이 다르면 파형 모양이 달라진다.**  
시뮬레이터에서 위상 슬라이더를 움직여 확인할 수 있다.

---

## 4. 이산 푸리에 변환 (DFT)

컴퓨터는 연속 신호를 다룰 수 없으므로, **이산화(샘플링)**한 신호에 DFT를 적용한다.

$$X[k] = \sum_{n=0}^{N-1} x[n] \cdot e^{-j 2\pi k n / N}, \quad k = 0, 1, \ldots, N-1$$

$$x[n] = \frac{1}{N} \sum_{k=0}^{N-1} X[k] \cdot e^{j 2\pi k n / N}$$

### 파라미터 관계

| 파라미터 | 기호 | 시뮬레이터 값 |
|---|---|---|
| 샘플 수 | N | 512 |
| 샘플링 주파수 | fs | 100 Hz |
| 샘플링 간격 | Δt = 1/fs | 0.01 s |
| 주파수 해상도 | Δf = fs/N | 0.195 Hz |
| 최대 표현 주파수 | fs/2 (나이퀴스트) | 50 Hz |
| 신호 길이 | T = N/fs | 5.12 s |

### 스펙트럼 해석

DFT 결과 `X[k]`에서:
- `k = 0`: 직류(DC) 성분
- `k = 1, ..., N/2-1`: 양의 주파수 (실제 주파수 = k × Δf)
- `k = N/2, ..., N-1`: 음의 주파수 (대칭이므로 절반만 사용)

실수 신호의 경우 `X[k] = X*[N-k]` (켤레 대칭), 그러므로 `N/2`개의 결과만 의미 있다.

---

## 5. 고속 푸리에 변환 (FFT)

DFT를 그대로 계산하면 **O(N²)** 연산이 필요하다. N=512이면 262,144번.

FFT는 **Cooley-Tukey 분할정복** 알고리즘으로 **O(N log N)**으로 줄인다. N=512이면 4,608번.

### 알고리즘 원리

짝수/홀수 인덱스로 분할:

$$X[k] = \underbrace{\sum_{n=0}^{N/2-1} x[2n] \cdot W_N^{2nk}}_{E[k]} + W_N^k \cdot \underbrace{\sum_{n=0}^{N/2-1} x[2n+1] \cdot W_N^{2nk}}_{O[k]}$$

여기서 `W_N = e^{-j2π/N}` (회전 인자, twiddle factor)

이를 재귀적으로 적용하면 `log₂N`단계로 분해된다.

```
N=8 분해 예시:
x[0,1,2,3,4,5,6,7]
→ x[0,2,4,6] + x[1,3,5,7]
→ x[0,4] + x[2,6] + x[1,5] + x[3,7]
→ x[0] + x[4] + ...  (N=1이면 DFT = 자기 자신)
```

### 버터플라이 연산 (Butterfly Operation)

각 단계에서 수행하는 기본 연산:

```
A → A' = A + W·B
B → B' = A - W·B
```

### 구현 코드

```js
function fftMag(signal) {
  const n = signal.length
  const re = [...signal], im = new Array(n).fill(0)

  // 1단계: 비트 역순 정렬 (bit-reversal permutation)
  let j = 0
  for (let i = 1; i < n; i++) {
    let bit = n >> 1
    for (; j & bit; bit >>= 1) j ^= bit
    j ^= bit
    if (i < j) {
      [re[i], re[j]] = [re[j], re[i]]
      [im[i], im[j]] = [im[j], im[i]]
    }
  }

  // 2단계: 버터플라이 연산 (각 길이 len마다 반복)
  for (let len = 2; len <= n; len <<= 1) {
    const ang = -2 * Math.PI / len  // 회전 각도
    for (let i = 0; i < n; i += len) {
      for (let k = 0; k < len / 2; k++) {
        const wr = Math.cos(ang * k), wi = Math.sin(ang * k)  // 회전 인자
        const ur = re[i+k], ui = im[i+k]
        const vr = re[i+k+len/2]*wr - im[i+k+len/2]*wi
        const vi = re[i+k+len/2]*wi + im[i+k+len/2]*wr
        re[i+k] = ur + vr;  im[i+k] = ui + vi    // 버터플라이 +
        re[i+k+len/2] = ur - vr;  im[i+k+len/2] = ui - vi  // 버터플라이 -
      }
    }
  }

  // 3단계: 정규화된 진폭 스펙트럼 반환
  return re.map((r, i) => Math.sqrt(r*r + im[i]*im[i]) / n)
}
```

---

## 6. 구형파와 깁스 현상 (Gibbs Phenomenon)

구형파는 **홀수 고조파만** 포함하며, 각 성분의 진폭은 `1/k`로 감소한다:

$$x_{sq}(t) = \frac{4}{\pi} \sum_{k=1,3,5,...}^{\infty} \frac{1}{k} \sin(2\pi k f_0 t)$$

$$= \frac{4}{\pi} \left[ \sin(2\pi f_0 t) + \frac{1}{3}\sin(6\pi f_0 t) + \frac{1}{5}\sin(10\pi f_0 t) + \cdots \right]$$

### 깁스 현상

항 수 N을 아무리 늘려도 불연속점(엣지)에서 **약 8.9%의 오버슈트**가 발생하며 사라지지 않는다. 위치만 좁아질 뿐이다.

$$\text{오버슈트} \approx \frac{2}{\pi} \int_0^\pi \frac{\sin t}{t} dt - 1 \approx 0.0895 \quad (8.95\%)$$

**의미:** 불연속을 포함하는 신호를 유한한 대역폭(bandwidth)의 시스템으로 처리하면 반드시 링잉(ringing)이 발생한다. 디지털 필터의 윈도우 함수(Hamming, Hann, Blackman 등)는 이 현상을 완화하기 위해 고안된 것이다.

```js
// 구현: 홀수 고조파 합산
const sig = t.map(ti => {
  let v = 0
  for (let k = 1; k <= N; k += 2) {     // 홀수만: 1, 3, 5, ...
    v += (4 / (Math.PI * k)) * Math.sin(2 * Math.PI * k * ti)
  }
  return v
})
```

---

## 7. 주요 성질 요약

| 성질 | 수식 | 의미 |
|---|---|---|
| 선형성 | `aX(f) + bY(f)` | 중첩 원리 |
| 시간 이동 | `X(f)·e^(-j2πft₀)` | 위상만 변함, 진폭 불변 |
| 주파수 이동 | `x(t)·e^(j2πf₀t) ↔ X(f-f₀)` | AM 변조의 수학적 기반 |
| 시간 스케일링 | `x(at) ↔ X(f/a)/|a|` | 시간 압축 = 주파수 확장 |
| 컨볼루션 | `x(t)*h(t) ↔ X(f)·H(f)` | 필터링의 수학적 기반 |
| 파시발 정리 | `∫|x(t)|²dt = ∫|X(f)|²df` | 에너지 보존 |
| 쌍대성 | `X(t) ↔ x(-f)` | 시간·주파수 대칭 |

> **주파수 이동 성질은 AM 변조의 핵심이다.** 반송파 `cos(2πfct)`를 곱하면 스펙트럼이 `fc`만큼 이동한다. → 다음 문서: `AM_modulation/README.md`

---

## 8. 시뮬레이터 구현 노트

### 파라미터 설정 근거

```
N  = 512   → 2의 거듭제곱 (FFT 알고리즘 요구사항)
fs = 100Hz → 최대 표현 주파수 = 50Hz (나이퀴스트)
Δf = 100/512 ≈ 0.195Hz → 충분한 주파수 해상도
T  = 5.12s → 저주파(1Hz)도 여러 주기 포함 가능
```

### 알려진 한계

- 현재 시뮬레이터는 **진폭 스펙트럼**(`|X[k]|`)만 표시하고 **위상 스펙트럼**(`∠X[k]`)은 생략
- FFT 결과의 정규화: `/ N` 처리로 진폭이 원래 신호의 진폭과 일치하도록 조정
- 창(window) 함수 미적용 → 스펙트럼 누설(spectral leakage) 발생 가능

### 개선 가능한 방향

- [ ] 위상 스펙트럼 시각화 추가
- [ ] 해밍(Hamming)/한(Hann) 윈도우 옵션 추가
- [ ] 2D 스펙트로그램 (시간-주파수 동시 표시)
- [ ] STFT (Short-Time Fourier Transform) 구현

---

## 9. 다음 주제 연결

```
푸리에 변환
    │
    ├── 샘플링 이론 & 나이퀴스트 정리    → signals/sampling/
    │       "fs > 2·fmax 이어야 복원 가능"
    │
    ├── AM 변조 (Amplitude Modulation)   → signals/am_modulation/
    │       "cos(2πfct) 곱하기 = 스펙트럼 이동"
    │
    ├── FM 변조 (Frequency Modulation)   → signals/fm_modulation/
    │       "순간 주파수 = fc + kf·m(t)"
    │
    └── 디지털 변조 (PSK, FSK, QAM)     → signals/digital_modulation/
            "각 심볼 = 특정 위상/주파수/진폭"
```

---

*Physics Simulation Platform — Signals & Systems Module*  
*작성 기준: 전자공학 학부 수준 (통신이론, 신호 및 시스템)*
