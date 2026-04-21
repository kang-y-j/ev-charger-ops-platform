# AI Server (FastAPI)

> 전기차 충전기 AI 운영 관리 시스템 &mdash; AI/ML API 서버

[![FastAPI](https://img.shields.io/badge/FastAPI-0.128-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.20-FF6F00?logo=tensorflow&logoColor=white)](https://tensorflow.org)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.10-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org)
[![LangChain](https://img.shields.io/badge/LangChain-1.2-1C3C3C?logo=langchain&logoColor=white)](https://langchain.com)

---

## 개요

CCTV 영상 분석, 센서 이상 탐지, 고장 예측, Multi-Agent 서비스 등 **AI/ML 관련 기능을 API로 제공**하는 서버입니다.

---

## 주요 기능

### 1. Predictive Maintenance (고장 조기 탐지)
- 한국환경공단 OpenAPI에서 **10분 주기**로 충전기 상태 수집
- 상태 전이 로그 &rarr; 6h/24h 윈도우 피처 변환 &rarr; **RandomForest** 위험도 산출
- Top-K 우선 점검 리스트 제공 (Recall@500 = 0.731)

| 평가 방법 | ROC-AUC | PR-AUC |
|-----------|---------|--------|
| Time Split | **0.9868** | **0.8420** |
| Rolling Backtest (5 splits) | 0.9852 | 0.8395 |
| Charger Hold-out (20%) | 0.9942 | 0.8965 |

### 2. Multimodal Ensemble (CCTV + 센서 통합)
- **YOLO Gate**: 충전기 유무 1차 판별 (Accuracy 0.97)
- **멀티태스크 분류**: 화재/연기/청결 판별 (ResNet50, Hard Negative 학습)
- **센서 이상 탐지**: LightGBM + Optuna (Accuracy 0.9787, Macro F1 0.9319)
- **Rule 기반 통합**: 우선순위 규칙으로 최종 상태 결정 (LLM은 설명 전용, 의사결정 변경 불가)

### 3. Multi-Agent (RAG 기반)

| Agent | 기능 |
|-------|------|
| 민원 처리 | 유형 분류 &rarr; Top-k 문서 검색 &rarr; 답변 생성/검증, 빈 답변 시 재생성 |
| 운영 규정 챗봇 | 근거 문서 기반 질의응답, Temperature 0.1, '근거 없음' 정책 |
| 감사용 보고서 | 분기별 자동 생성, LLM-as-a-Judge 품질 검증, 사회 이슈 반영 |
| 맞춤형 보고서 | 프롬프트 기반 목차 생성, Tool Loop로 그래프/표 자동 삽입 |

### 4. 디지털 트윈 Autopilot
- Top-K 선별 &rarr; ETA 기반 동선 산정 &rarr; 조치 플랜 생성
- 유지보수 수행사 시뮬레이션 및 랭킹

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| API 서버 | FastAPI + Uvicorn |
| 딥러닝 | TensorFlow 2.20, PyTorch 2.10 |
| 객체 탐지 | Ultralytics YOLOv8 |
| 머신러닝 | RandomForest, LightGBM, scikit-learn |
| 하이퍼파라미터 | Optuna |
| 영상 처리 | OpenCV |
| LLM Agent | LangChain, LangGraph, OpenAI API |
| Vector DB | RAG 문서 저장소 (법령/지침/매뉴얼) |
| 데이터 처리 | Pandas, NumPy, Polars |

## 디렉토리 구조

```
AI_back/
├── main.py              # FastAPI 엔트리포인트
├── makestatNm.py        # 통계 데이터 생성
├── requirements.txt     # Python 의존성
├── .gitignore
└── app/
    ├── __init__.py
    ├── main.py          # 앱 설정
    ├── api/             # API 라우터 (엔드포인트)
    └── services/        # AI/ML 비즈니스 로직
```

## 실행 방법

```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

---

[< 전체 프로젝트로 돌아가기](../../tree/main)
