# Physics Simulation Platform

전자공학 전공 지식을 인터랙티브 시뮬레이터로 구현한 웹 기반 학습 플랫폼.

**Live:**  (준비 중)

---

## 구조

```
physics-sim-platform/
├── index.html                      ← 메인 랜딩 페이지
├── signals/                        ← 신호처리 & 통신이론
│   ├── 01_fourier/                 ← 푸리에 변환 (FFT, 스펙트럼, 깁스)
│   ├── 02_am_fm_dsb/               ← AM / FM / DSB-SC 변조
│   ├── 03_pm_dm/                   ← PM / DM / ADM
│   ├── 04_sampling/                ← 샘플링 & 나이퀴스트 정리
│   └── 05_digital_modulation/      ← BPSK / QPSK / FSK / QAM
├── em_optics/                      ← 전자기학 & 광학 (준비 중)
│   ├── 01_waveguide/
│   ├── 02_fdtd_1d/
│   ├── 03_fdtd_2d/
│   └── ...
└── robotics/                       ← 로봇 제어 (준비 중)
```

## 각 시뮬레이터

| # | 주제 | 핵심 개념 |
|---|------|---------|
| S01 | 푸리에 변환 | FFT, 중첩, 구형파, 깁스 현상 |
| S02 | AM / FM / DSB-SC | 변조지수, 베셀 함수, 카슨 대역폭 |
| S03 | PM / DM / ADM | 각도 변조, 과부하 왜곡, 적응형 스텝 |
| S04 | 샘플링 & 나이퀴스트 | 에일리어싱, sinc 재구성, 스펙트럼 복제 |
| S05 | 디지털 변조 | 성상도, BER, 스펙트럼 효율 |

## 기술 스택

- **프론트엔드:** 순수 HTML/CSS/JavaScript (외부 라이브러리 없음)
- **수치 계산:** Cooley-Tukey FFT, 베셀 함수 급수, sinc 보간 직접 구현
- **배포:** GitHub Pages + 커스텀 도메인

## GitHub Pages 배포

```bash
git clone https://github.com/-------/physics-sim-platform
cd physics-sim-platform
# GitHub repo Settings → Pages → Branch: main → / (root)
# Custom domain: -------
```

---

*전자공학 학부 수준 (신호 및 시스템, 통신이론, 전자기학)*
