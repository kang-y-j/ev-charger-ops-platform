# Data & Model Experiments

> 전기차 충전기 AI 운영 관리 시스템 &mdash; 데이터 전처리 및 모델 개발

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)](https://python.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org)

---

## 개요

AI 모델 개발 과정에서 사용된 **데이터 수집, 전처리, EDA, 모델 학습 실험 코드**를 관리하는 브랜치입니다.

---

## 모델 상세

### 1. CCTV 이미지 멀티태스크 레이블 분류 모델
- **파일**: `fire_dirty.ipynb`
- **목적**: CCTV 이미지에서 화재/연기/청결 상태 동시 분류
- **데이터**: 화재/연기 3,346장 + 석양(Hard Negative) 90장 + 청결/오염 506장
- **데이터 출처**: Roboflow (fire smoke dataset), 웹 검색, 생성형 AI (Gemini, Stable Diffusion)
- **전처리**: RGB 디코딩 &rarr; 224x224 리사이즈 &rarr; ResNet50 preprocess_input
- **모델 고도화**: 석양 이미지의 fire 오탐을 Hard Negative 학습으로 해결

### 2. CCTV 이미지 충전기 유무 판별 모델
- **파일**: `charger_yn.py`
- **목적**: CCTV 영상에서 충전기 존재 여부 판별
- **데이터**: 충전소 557장, 도로/주차장/빌딩/지하주차장 464장
- **모델 고도화**: 키오스크/배전함 등 유사 객체 파인튜닝, 하드케이스 데이터 보강
- **성능**: Accuracy 0.97 / Recall 1.00 / Precision 0.93

### 3. 센서데이터 기반 이상 탐지 모델
- **파일**: `Sensor_LGBM_Classifier.py`
- **목적**: 충전기 센서 로그로 정상/저온고장/고온고장 3클래스 분류
- **데이터**: 실제 충전소 센서 로그 **1,452,084건** (전압, 전류, 전력, 온도 등 8개 센서값)
- **피처 엔지니어링**: 도메인 지식 기반 파생 피처 9종 (temp_per_current, current_per_temp 등)
- **클래스 불균형**: 정상 94.39% / 저온고장 5.55% / 고온고장 0.06% &rarr; class_weight 적용
- **최적화**: Optuna 하이퍼파라미터 탐색 + Early Stopping
- **성능**: Accuracy 0.9787 / Macro F1 0.9319

### 4. 예측 정비 (Predictive Maintenance)
- **파일**: `Pdm.ipynb`
- **데이터 수집**: 한국환경공단 OpenAPI, 상태 변경 이벤트만 저장 (중복/노이즈 제거)
- **전처리**: segment 기반 상태 지속시간 복원, 정상 시점만 학습 (이미 다운이 아닌 조기 경보 목표)
- **피처**: normal_ratio_prev6h, 최근 6h/24h 전이 횟수
- **라벨**: 다음 6h 내 DOWN 진입 + DOWN 점유 10분 이상
- **모델**: RandomForest (RF/XGB/LGBM 벤치마크 후 PR-AUC 및 Top-K에서 가장 안정적)
- **성능**: ROC-AUC 0.9868 / PR-AUC 0.8420 / Recall@500 0.731

### 5. 디지털 트윈
- **파일**: `collect_link_traffic.py`, `eda_link_map_distance.py`, `make_tsv.py`
- **데이터**: 서울시 실시간 도로 소통 정보 + LINK_VERTEX 보간점
- **파이프라인**: 충전소 좌표(WGS84) &rarr; TM 변환 &rarr; KDTree 최근접 탐색 &rarr; link_map.tsv &rarr; traffic 조인

### 6. 충전기 로그 데이터 수집 및 학습 데이터셋 생성
- **파일**: `collector.py`, `eda_thresholds_windowed_fixed.py`, `make_train_down6h.py`
- **파이프라인**: 원시 로그 수집 &rarr; 임계값 기반 윈도우 분석 &rarr; 6시간 다운타임 기준 레이블링
- **품질 관리**: lock 파일로 동시 실행 방지, 오래된 lock(>2h) 자동 해제

---

## 디렉토리 구조

```
etc/
├── README.md
├── CCTV 이미지 멀티태스크 레이블 분류 모델/
│   └── fire_dirty.ipynb
├── CCTV 이미지 충전기 유무 판별 모델/
│   └── charger_yn.py
├── DigitalTwin/
│   ├── collect_link_traffic.py
│   ├── eda_link_map_distance.py
│   └── make_tsv.py
├── Predictive maintenence/
│   └── Pdm.ipynb
├── 센서데이터 기반 이상 탐지 모델/
│   └── Sensor_LGBM_Classifier.py
└── 충전기 로그 데이터 수집 및 eda 후 학습 데이터셋 생성/
    ├── collector.py
    ├── eda_thresholds_windowed_fixed.py
    └── make_train_down6h.py
```

---

## 참고 문헌

1. *Predictive Maintenance for Equipment: A Data-driven Approach*, SIGKDD 2014
2. *Deep Sequence Modeling for Event Log-based Predictive Maintenance*, KDD MiLeTS Workshop
3. *Fault prediction and management of charging station combined with deep learning model*, IJLCT (Oxford Academic), 2025
4. Ke et al., *LightGBM: A Highly Efficient Gradient Boosting Decision Tree*, NeurIPS 2017
5. Akiba et al., *Optuna: A Next-generation Hyperparameter Optimization Framework*, KDD 2019

---

[< 전체 프로젝트로 돌아가기](../../tree/main)
