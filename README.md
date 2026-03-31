# Physics Simulation Platform

전자공학 전공 지식을 인터랙티브 웹 시뮬레이터로 구현한 학습 플랫폼.

**Live:** [leejh231.github.io/physics-sim-platform](https://leejh231.github.io/physics-sim-platform)

---

## 구조

```
physics-sim-platform/
├── index.html                          ← 메인 랜딩 페이지
│
├── signals/                            ← 신호처리 & 통신이론 (9개)
│   ├── 01_fourier/                     ← 푸리에 변환 (FFT, 구형파, 깁스)
│   ├── 02_am_fm_dsb/                   ← AM / FM / DSB-SC 변조
│   ├── 03_pm_dm/                       ← PM / DM / ADM
│   ├── 04_sampling/                    ← 샘플링 & 나이퀴스트 정리
│   ├── 05_digital_modulation/          ← BPSK/QPSK/FSK/QAM 개요
│   ├── 06_probability/                 ← 확률 & 통계 (가우시안, CLT)
│   ├── 07_bpsk_qpsk_pipeline/          ← BPSK/QPSK 전체 복조 파이프라인 ★
│   ├── 08_qam_pipeline/                ← QAM 16/64 전체 복조 파이프라인 ★
│   └── 09_am_fm_pipeline/              ← AM/FM 복조 파이프라인 ★
│
└── em_optics/                          ← 전자기학 & 광학 (2개 + 2 예정)
    ├── 01_ar_coating/                  ← 1D FDTD AR 코팅 시뮬레이터 ★
    └── 02_double_slit/                 ← 2D FDTD 이중 슬릿 간섭 ★
```

## 시뮬레이터 목록

### Signals & Systems

| # | 주제 | 핵심 개념 |
|---|------|---------|
| S01 | 푸리에 변환 | FFT, 중첩, 구형파, 깁스 현상 |
| S02 | AM/FM/DSB-SC | 변조지수, 베셀 함수, 카슨 대역폭 |
| S03 | PM/DM/ADM | 각도 변조, 과부하 왜곡, 적응형 스텝 |
| S04 | 샘플링 & 나이퀴스트 | 에일리어싱, sinc 재구성, 스펙트럼 복제 |
| S05 | 디지털 변조 개요 | 성상도, BER 곡선, 스펙트럼 효율 |
| S06 | 확률 & 통계 | 가우시안, 지수분포, CLT, 결합 분포 |
| S07 | BPSK/QPSK 파이프라인 ★ | 정합 필터, 아이 다이어그램, 반송파 복원, BER |
| S08 | QAM 16/64 파이프라인 ★ | Gray 코딩, I/Q 복조, AGC, 성상도 |
| S09 | AM/FM 복조 파이프라인 ★ | 포락선 검파, 리미터-판별기, FM 임계 효과 |

### EM & Optics

| # | 주제 | 핵심 개념 |
|---|------|---------|
| E01 | AR 코팅 (1D FDTD) ★ | Yee 알고리즘, PML, Fresnel, Poynting vector |
| E02 | 이중 슬릿 (2D FDTD) ★ | TM 모드, PEC 장벽, 간섭 패턴, 이론 비교 |

★ = 전체 파이프라인 / 심화 시뮬레이터

## 기술 스택

- **프론트엔드:** 순수 HTML/CSS/JavaScript (외부 의존성 없음)
- **수치 계산:** Cooley-Tukey FFT, 베셀 함수, sinc 보간, Yee FDTD 알고리즘 직접 구현
- **배포:** GitHub Pages

## 기반 Python 코드

`em_optics` 시뮬레이터는 직접 작성한 Python FDTD 코드를 JavaScript로 포팅:
- `AR_Coating.py` → `em_optics/01_ar_coating/`
- `Double_Slit_FDTD.py` → `em_optics/02_double_slit/`

## 배포

```bash
# GitHub Pages: Settings → Pages → Branch: main / (root)
# URL: leejh231.github.io/physics-sim-platform
```

---

*전자공학 학부 수준 | 신호 및 시스템 · 통신이론 · 전자기학*
