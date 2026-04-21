# EV Charger Ops Platform

> **전기차 충전기 AI 운영 관리 시스템** &mdash; AI 기반 고장 예측, 멀티모달 이상 탐지, 지능형 운영 자동화 플랫폼

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.128-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5-6DB33F?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.20-FF6F00?logo=tensorflow&logoColor=white)](https://tensorflow.org)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.10-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org)
[![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20S3%20%7C%20RDS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com)

---

## 프로젝트 소개

### 문제 인식

- **미운영 충전기 2,796기** 방치 (전기요금 미납 등)
- **21,283기** 상태 정보 미확인
- 사후 대응 중심의 관리 체계 &rarr; 보조금 부적정 집행 **218.7억 원** 규모 위반 사례 적발

### 기존 서비스의 한계

| 문제점 | 기존 | 우리의 해결책 |
|--------|------|--------------|
| 통합 모니터링 부재 | 제조사/사업자별 관제 시스템 분산 | 전국 충전소 통합 모니터링 |
| 원인 분석 한계 | OCPP 1.6 통신 로그 중심 단일 관제 | 이미지 + 센서 + 로그 멀티모달 원인 분석 |
| 사전 감지 불가 | 사후 대응 중심 유지보수 | AI 기반 고장/통신 이상 사전 예측 |

### As-Is &rarr; To-Be

```
[As-Is]                              [To-Be]
정량 기준 없는 사업 선정               통합 모니터링 기반 운영 가시성 확보
     ↓                                    ↓
운영·모니터링 체계 부재                 AI 기반 사전 예측·이상 감지
     ↓                                    ↓
사후 대응 중심 운영 관리                지능형 운영 지원 및 자동 대응
     ↓                                    ↓
운영 관리 부실 및 보조금 환수 발생       데이터 기반 의사결정 및 확장
```

---

## 핵심 서비스

### 1. 실시간 모니터링
- 전국 충전소 현황을 **한 페이지에서 통합 모니터링** (상태 미확인 리스트, 충전기 총현황 차트 등)
- 충전소 검색 및 상세 보기 &mdash; 충전기 상태, 관리업체 정보 제공
- 상태 미확인 충전소 선택 &rarr; 관리업체 이메일로 **현재 상태 전송 및 조치 권고**
- 컴포넌트 편집 기능 (추가/삭제/이동)

### 2. AI 기반 운영 지원 및 자동 대응
- **보고서 생성** &mdash; 감사 대비용 / 맞춤형 보고서를 AI Agent가 자동 생성, PDF 출력 가능
- **민원 처리** &mdash; 접수된 민원을 RAG 기반 Agent가 관련 근거 문서를 탐색하여 답변 초안 자동 생성
- **운영 규정 챗봇** &mdash; 운영 규정/내부 매뉴얼/법령 등 근거 문서 기반 질의응답

### 3. 디지털 트윈
- **Autopilot** &mdash; 지속시간/다운위험도/교통혼잡도를 반영한 우선 처리 대상 자동 선별
- **지도 모니터링** &mdash; 충전소 위치 + Autopilot 위험 케이스 Top 10 표시
- **유지보수 수행사 선정** &mdash; SLA 충족률/ETA p90/원격복구 비교로 최적 업체 추천

---

## 시스템 아키텍처

```
                         ┌─────────────────────────────┐
                         │         Frontend             │
                         │   Next.js 16 · React 19      │
                         │   MUI · Leaflet · Recharts   │
                         └──────────────┬──────────────┘
                                        │ HTTPS
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
          ┌─────────────────┐  ┌────────────────┐  ┌───────────────┐
          │    Backend       │  │   AI Server    │  │    AWS S3     │
          │  Spring Boot 3.5 │  │   FastAPI      │  │  이미지/보고서  │
          │  JPA · Security  │  │                │  └───────────────┘
          └────────┬────────┘  │  LangChain      │
                   │           │  LangGraph       │
                   ▼           │  TensorFlow      │
          ┌─────────────────┐  │  PyTorch/YOLO   │  ┌───────────────┐
          │   AWS RDS       │  │  LightGBM       │  │  Vector DB    │
          │   MySQL         │  │  RandomForest   │  │  RAG 문서저장   │
          └─────────────────┘  └────────────────┘  └───────────────┘

          ──── AWS VPC (EC2 · S3 · RDS · CodePipeline) ────
```

---

## AI 모델 성능

### 1. 고장 조기 탐지 (Predictive Maintenance)

상태 전이 로그를 특징(6h/24h 윈도우)으로 변환 &rarr; **RandomForest**로 위험도 산출 &rarr; Top-K 우선 점검 리스트 제공

| 평가 방법 | ROC-AUC | PR-AUC | 비고 |
|-----------|---------|--------|------|
| Time Split (Chronological + 6h gap) | **0.9868** | **0.8420** | 시간 순서 기반 평가 |
| Rolling Backtest (5 splits, mean) | 0.9852 | 0.8395 | 기간 변화에도 안정적 |
| Charger Hold-out (20%) | 0.9942 | 0.8965 | 신규 충전기 일반화 확인 |

> **Recall@500 = 0.731** &mdash; 상위 500개만 우선 점검해도 6시간 내 DOWN 발생의 73%를 선제 포착

### 2. Multimodal Ensemble (CCTV + 센서 통합)

YOLO Gate(대상 검증) &rarr; 화재/연기 우선순위 &rarr; 센서+오염 통합 판단 &rarr; Rule 기반 최종 상태 결정

| 모델 | 핵심 지표 | 성능 |
|------|----------|------|
| **CCTV 충전기 유무 탐지** (YOLOv8 Fine-tuning) | Accuracy / Recall / Precision | 0.97 / 1.00 / 0.93 |
| **CCTV 멀티태스크 분류** (ResNet50, 화재/연기/청결) | Hard Negative 학습으로 오탐 감소 | 석양 &rarr; fire 오탐 해결 |
| **센서 이상 탐지** (LightGBM + Optuna) | Accuracy / Macro F1 | 0.9787 / 0.9319 |

### 3. Multi-Agent (RAG 기반)

| Agent | 기능 | 특징 |
|-------|------|------|
| **민원 처리 Agent** | 민원 유형 분류 &rarr; Top-k 문서 검색 &rarr; 답변 생성/검증 | 최신 충전기 상태 자동 병합, 빈 답변 시 강화 프롬프트 재생성 |
| **운영 규정 챗봇** | 근거 문서 기반 질의응답 | Temperature 0.1, 근거 없으면 '근거 없음' 명시, 정확도 ~90% |
| **감사용 보고서 Agent** | 분기별 감사 대비 보고서 자동 생성 | LLM-as-a-Judge로 품질 검증, 사회 이슈 검색 반영 |
| **맞춤형 보고서 Agent** | 사용자 프롬프트 기반 보고서 생성 | Tool Loop로 그래프/표 자동 삽입, RAG 근거 참조 |

### 4. 디지털 트윈 (Autopilot)

| 단계 | 기능 |
|------|------|
| **Top-K 선별** | 다운 지속시간 + 위험도 + 교통 혼잡도 &rarr; 가중합 스코어 &rarr; 우선 점검 큐 |
| **동선/ETA 산정** | 교통 데이터 반영 ETA 기준 점검 순서 제시 (거리순 X, 교통 반영 O) |
| **조치 플랜** | 원격 진단 &rarr; 원격 리셋 우선, SLA 기준 출동/에스컬레이션 분기 |
| **수행사 선정** | SLA 충족률/ETA p90/원격복구 시뮬레이션 &rarr; 업체 랭킹 + 추천 근거 |

---

## 기술 스택

### AI / ML
| 분야 | 기술 |
|------|------|
| 프레임워크 | TensorFlow 2.20, PyTorch 2.10 |
| 객체 탐지 | Ultralytics YOLOv8 |
| 머신러닝 | RandomForest, LightGBM, scikit-learn |
| 영상 처리 | OpenCV |
| LLM / Agent | LangChain, LangGraph, OpenAI API |
| 최적화 | Optuna (하이퍼파라미터 자동 탐색) |
| API 서버 | FastAPI, Uvicorn |

### Backend
| 분야 | 기술 |
|------|------|
| 프레임워크 | Spring Boot 3.5.9 (Java 17) |
| ORM | Spring Data JPA |
| 인증/보안 | Spring Security (BCrypt, CSRF 방어, 세션 고정 공격 방지) |
| 클라우드 | AWS S3 (Presigned URL) |
| DB | MySQL (운영) / H2 (개발) |
| API 문서 | Springdoc OpenAPI (Swagger UI) |
| CI/CD | AWS CodePipeline (CodeBuild + CodeDeploy) |

### Frontend
| 분야 | 기술 |
|------|------|
| 프레임워크 | Next.js 16 (React 19) |
| UI 라이브러리 | MUI (Material UI) |
| 지도 | Leaflet + React-Leaflet |
| 차트 | Recharts |
| HTTP | Axios |
| CI/CD | AWS CodePipeline (CodeBuild + CodeDeploy) |

### Infra
| 분야 | 기술 |
|------|------|
| 클라우드 | AWS VPC, EC2, S3, RDS |
| CI/CD | AWS CodePipeline |
| 통신 | HTTPS (SSL/TLS) |
| Vector DB | RAG 문서 저장소 (법령/지침/매뉴얼) |

---

## 보안

| 항목 | 적용 내용 |
|------|----------|
| 비밀번호 암호화 | BCrypt (자동 Salt, 타이밍 공격 방지) |
| 세션 고정 공격 방지 | 로그인 시 기존 세션 무효화 + 새 세션 생성 |
| 사용자 열거 공격 방지 | 아이디/비밀번호 오류 동일 메시지 반환 |
| CSRF 방어 | SameSite=Lax + HttpOnly 쿠키 |
| HTTPS | SSL/TLS 인증서, HTTP &rarr; HTTPS 강제 전환 |

---

## 데이터 소스

| 데이터 | 출처 |
|--------|------|
| 충전기 상태 로그 | 한국환경공단 전기자동차 충전소 정보 OpenAPI (10분 주기 수집) |
| 실시간 교통 | 서울시 실시간 도로 소통 정보 (OA-13291) |
| 도로 링크 | LINK_VERTEX 서비스 링크 보간점 |
| CCTV 이미지 (학습) | 웹 검색 + 생성형 AI (DALL-E, Gemini) + Roboflow |
| 센서 데이터 | 실제 충전소 센서 로그 (1,452,084건) |
| RAG 문서 | 국가법령정보센터, 환경부 지침, 한국전기안전공사 등 |

---

## 브랜치 구조

각 브랜치는 독립적인 서비스 단위로 관리됩니다.

| 브랜치 | 역할 | 바로가기 |
|--------|------|----------|
| [`main`](../../tree/main) | 프로젝트 전체 개요 | 현재 페이지 |
| [`AI_back`](../../tree/AI_back) | AI/ML API 서버 (FastAPI) | [README 보기](../../tree/AI_back) |
| [`back`](../../tree/back) | 백엔드 API 서버 (Spring Boot) | [README 보기](../../tree/back) |
| [`front`](../../tree/front) | 프론트엔드 (Next.js) | [README 보기](../../tree/front) |
| [`etc`](../../tree/etc) | 데이터 전처리 · 모델 실험 | [README 보기](../../tree/etc) |

---

## 프로젝트 구조

```
ev-charger-ops-platform/
│
├── [main]           ← 프로젝트 개요 (현재)
│
├── [AI_back]        ← AI/ML API 서버
│   ├── main.py          # FastAPI 엔트리포인트
│   ├── app/
│   │   ├── api/         # API 라우터
│   │   └── services/    # 비즈니스 로직 (모델 추론, Agent)
│   └── requirements.txt
│
├── [back]           ← Spring Boot 백엔드
│   ├── backend/
│   │   ├── src/         # Java 소스코드
│   │   └── build.gradle
│   ├── appspec.yml      # AWS CodeDeploy
│   └── buildspec.yml    # AWS CodeBuild
│
├── [front]          ← Next.js 프론트엔드
│   ├── nextjs/
│   │   ├── src/         # 페이지 및 컴포넌트
│   │   └── package.json
│   ├── appspec.yml
│   └── buildspec.yml
│
└── [etc]            ← 데이터/모델 실험
    ├── CCTV 이미지 멀티태스크 레이블 분류 모델/
    ├── CCTV 이미지 충전기 유무 판별 모델/
    ├── DigitalTwin/
    ├── Predictive maintenence/
    ├── 센서데이터 기반 이상 탐지 모델/
    └── 충전기 로그 데이터 수집 및 eda 후 학습 데이터셋 생성/
```

---

## 팀원

| 이름 | 역할 | 담당 |
|------|------|------|
| **김병재** | PM | 조기 탐지 모델 개발, 디지털 트윈 개발, 멀티모달 앙상블 Rule 설계 |
| **강영재** | Back-End | 이상 상태 분류 모델 개발, S3 · SpringBoot · FastAPI 연동, ERD/API 설계, 학습용 이미지 데이터 생성 |
| **박상희** | Back-End | 챗봇 에이전트 개발, SpringBoot · FastAPI 연동, DB 관리, S3 이미지 URL 변환, RAG Vector DB 관리 |
| **임예윤** | Back-End | 민원처리 에이전트 개발, SpringBoot · FastAPI 연동, ERD/API 설계, DB 관리, 민원 데이터 생성, RAG Vector DB |
| **조민석** | Infra / Full Stack | ERD/API 설계, 데이터 자동수집(OCI), 개발 인프라 관리(Git, Figma MCP, SSH), AWS 배포(EC2, RDS, CodePipeline), Full Stack Debugging, 맞춤형 보고서 에이전트 개발 |
| **구채윤** | Front-End | CCTV 이미지 분류 모델 개발, UI/UX 설계(Figma), 민원 처리 API 연동 및 페이지 구현 |
| **신관섭** | Front-End | UI/UX 설계(Figma), 보고서 생성 API 구현, 충전기 유무 분류 모델 개발, 학습용 이미지 데이터 생성 |
| **이수빈** | Front-End | UI/UX 설계(Figma), Chart · 챗봇 등 메인 페이지 Component 구현, 상세 페이지 API 연동, 감사용 보고서 에이전트 개발 |

> **KT AIVLE School** AI트랙 8기 13조 빅 프로젝트 (2026.02.20 발표)

---

> 본 README는 프로젝트 발표자료를 기반으로 작성되었습니다.
