# 🎙️ 음성 데이터 전처리 성능 분석 — 배경소음 환경에서의 AI 음원 분리 서비스 비교

> **신호 및 시스템** 기말과제 | 학번: 202045801

---

## 📌 목차

1. [배경](#1-배경)
2. [목표](#2-목표)
3. [내용 및 방법](#3-내용-및-방법)
4. [결론](#4-결론)
5. [활용방안](#5-활용방안)
6. [기대효과](#6-기대효과)

---

## 1. 배경

### 1.1 개발의 필요성

현대 사회에서 음성 인식 서비스의 활용도와 보급이 급격히 증가하고 있습니다.

| 분야 | 활용 사례 |
|------|-----------|
| 스마트폰 / 스마트 스피커 | 음성 명령을 통한 핸즈프리(Handsfree) 기능 |
| 스마트 홈 | 조명 제어, 온도 조절, 가전제품 제어 |
| 보안 시스템 | 음성 인식 기반 개인 인증 |
| 음악 산업 | 해당 곡의 아티스트 찾기 등 |

그러나 현재 음성 인식 서비스는 다양한 환경 변수로 인해 안정적인 성능을 항상 보장하지 못합니다.

**주요 문제 요인:**
- **다중 발화자**: 온라인 가상 회의 등 멀티채널 환경에서 여러 사람이 동시에 말하는 경우 음성 분리가 어려움
- **개인별 음성 특징 차**: STT 서비스 사용 시 발화자를 구분하기 어렵고, TTS 목소리가 등록된 개인 인증을 우회하는 사고 사례 존재
- **음성 품질 저하**: 거리, 마이크 성능, 네트워크 속도 등으로 인해 인식 결과 품질이 저하됨
- **배경소음**: 공사 소음, 주변 환경음 등이 음성 인식에 직접적인 영향을 미침

이러한 문제들을 해결하기 위해 **Input 음성 데이터 전처리 기술**의 발전이 AI 모델의 학습 효율과 Output 품질 향상에 핵심적인 역할을 한다고 판단했습니다.

---

### 1.2 국내외 현황

#### 🇰🇷 국내 현황

> 출처: 한국전자통신연구원

- **2023년** 국내 음성 인식 서비스 시장 규모: 약 **1,500억 원**
- **2028년** 전망: **4,000억 원** 이상
- 주요 사업자: 네이버(`CLOVA Speech`), 카카오(`Kakao i`), SKT(`T Voice`), LG전자(`Deep Voice`) 등

#### 🌐 국외 현황

> 출처: MarketsandMarkets

- **2023년** 글로벌 음성 인식 서비스 시장 규모: 약 **200억 달러**
- **2028년** 전망: **600억 달러** 이상
- 주요 사업자: Google, Amazon, Microsoft, Meta 등 — 클라우드 기반 음성 인식 서비스 제공

---

## 2. 목표

본 연구는 음성 인식 서비스를 위한 **Input 데이터 전처리** 기술을 분석하고, 상용 AI 기반 음원 분리 서비스들의 전처리 성능을 비교합니다.

- 현 시점의 음성 데이터 전처리 기술(음성 필터링)의 **성능, 한계, 개선점** 도출
- 배경소음 환경에서 3가지 상용 서비스의 실제 성능 측정 및 분석

---

## 3. 내용 및 방법

### 3.1 문제 상황 선택

| 문제 상황 | 채택 여부 | 사유 |
|-----------|-----------|------|
| 다중 발화자 | ❌ 제외 | 표본 선정 및 장비 한계 |
| 개인별 음성 특징 차 | ❌ 제외 | 장비 및 소프트웨어 한계 |
| 음성 품질 저하 | ❌ 제외 | 분석 환경 구성의 어려움 |
| **배경소음** | ✅ **채택** | 보유 장비와 소프트웨어로 구현 가능 |

#### 사용 장비 및 소프트웨어

- **마이크**: FIFINE AM8 USB/XLR 다이나믹 마이크
- **DAW**: FL Studio (Image Line Software)
- **VST**: Supertone — Voice Separator, Clear

---

### 3.2 데이터 생성 및 가공

#### 원본 음성 샘플

상업적·비상업적 이용 가능한 보컬 샘플 사용:
> 📎 https://www.looperman.com/acapellas/detail/22440/diamonds-main-vocal-74bpm-chill-out-acapella

- 약 **34초** 구간으로 편집, 무음 구간 보정
- MP3 원본 → Gain 출력 + Equalizer + **Bandpass Filter**(가청 주파수 영역만 통과) 적용
- → **`Original Vocal Sample`** 명명

#### 노이즈 삽입 가공

`Original Vocal Sample`에 다음 요소를 추가하여 테스트 데이터 생성:
- 저·중·고 음역대의 **가상악기(Virtual Instrument)** 삽입
- 영상 분야의 **BGS(Background Sounds)** 및 **SE(Sound Effect)** 삽입
- 거리감 및 공간감을 위한 **Reverb** 적용
- **Panning** 조정
- → **`Vocal with instruments`** 명명 (Input 데이터)

#### 전처리 방법 (3종 비교)

| 명칭 | 사용 서비스 |
|------|-------------|
| `Vocal filter with Supertone` | Supertone — Voice Separator, Clear (VST 플러그인) |
| `Vocal filter with Gaudiolab` | Gaudiolab AI 음원 분리 서비스 |
| `Vocal filter with FL` | FL Studio 내장 AI Stem Separation |

---

### 3.3 코드 작성

| 항목 | 내용 |
|------|------|
| 분석 환경 | Jupyter Notebook |
| IDE | Visual Studio Code |
| 주요 라이브러리 | `librosa` |

📚 **참고 문서**: [Librosa Documentation](https://librosa.org/doc/latest/index.html)

---

### 3.4 데이터 비교

#### ① Original Vocal Sample vs. Vocal with instruments

- **Time domain Waveform** 비교 시 음성 이외의 노이즈가 명확히 삽입되었음을 확인

---

#### ② Vocal filter with Supertone

| 분석 방법 | 결과 |
|-----------|------|
| Waveform | Amplitude가 Original보다 높게 Gain 처리됨 |
| FFT | 큰 차이 없음 — Gain 증폭으로 인해 노이즈도 함께 증폭 |
| STFT (Log Scale) | 제거되지 못한 노이즈가 시각적으로 뿌옇게 잔존 |
| MFCC | 특징값 차이 미미 — 필터링 효과 거의 없음 |

> 💡 VST 플러그인 특성상 편곡 시 다른 악기 소리에 묻히지 않도록 Gain 증폭 처리가 되어, 줄어든 노이즈 또한 함께 증폭되었을 것으로 추측됨

---

#### ③ Vocal filter with Gaudiolab

> ⚠️ 결과물이 **MP3 포맷**으로 산출되어 원본 WAV와 압축 손실 차이가 있어 신뢰성에 제한이 있음

| 분석 방법 | 결과 |
|-----------|------|
| Waveform | Original과의 차이가 확연히 감소 |
| FFT | 시간 정보 유실로 큰 차이 확인 어려움 |
| STFT (Log Scale) | 노이즈 제거 효과 시각적으로 확인됨, 단 y값 64 이하 영역에 노이즈 잔존 |
| MFCC | 특정 주파수 이상 대부분의 노이즈 제거 확인 |

> 💡 Bandpass 필터링이 적절히 적용되지 않아 저주파 영역 노이즈가 잔존하는 것으로 분석됨

---

#### ④ Vocal filter with FL Studio

FL Studio 공식 설명:
> *"Extract bass, synth, drums, and vocal layers from any audio file, directly within FL Studio, using AI-powered Stem Separation."*

| 분석 방법 | 결과 |
|-----------|------|
| Waveform | Gaudiolab과 유사한 수준의 노이즈 제거 결과 |
| STFT (Log Scale) | 미세 노이즈 잔존 확인, 저음역대 가상악기의 MIDI 유사 패턴 잔존 |
| MFCC | Gaudiolab과 유사하게 특징값이 매우 옅게 나타남 |

---

## 4. 결론

여러 상용 AI 음원 분리 서비스를 분석한 결과:

- ✅ **청각적 품질**: 인간의 귀로는 노이즈가 크게 의식되지 않을 수준으로 분리 가능
- ⚠️ **저주파 노이즈**: Bandpass 필터 처리 없이는 불필요한 주파수 대역 신호가 잔존
- ⚠️ **수치적 노이즈**: 데이터 레벨에서 미세한 노이즈가 제거되지 않음

> 음악 산업에서는 리버브·노이즈 등이 의도적으로 활용되므로 큰 문제가 없으나, **음성 인식 AI 학습** 관점에서는 이러한 잔류 노이즈가 학습 오류나 성능 저하로 이어질 수 있습니다. 이를 해결하기 위한 집중적인 전처리 기술 연구가 필요합니다.

---

## 5. 활용방안

- 🎛️ **다중 Bandpass 필터**를 통한 음성 데이터 전처리 성능 향상 방향성 제시
- 📱 **온-디바이스 AI** 탑재 기기에서 아날로그 필터 추가를 통한 Input 데이터 효율 개선 연구
- 🤖 **AI 기반 가변 Sampling Rate** 튜닝 연구

---

## 6. 기대효과

- 📈 음성 인식 서비스의 상황 대응력, 정확성, 인식률 향상
- 🧠 음성 인식 AI 모델의 학습 용이성 증대

---

## 🛠️ 기술 스택

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat-square&logo=jupyter&logoColor=white)
![VSCode](https://img.shields.io/badge/VSCode-007ACC?style=flat-square&logo=visualstudiocode&logoColor=white)
![librosa](https://img.shields.io/badge/librosa-audio%20analysis-green?style=flat-square)

---

<div align="center">

**202045801 | 신호 및 시스템 기말과제**

</div>
