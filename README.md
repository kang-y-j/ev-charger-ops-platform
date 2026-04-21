# Frontend (Next.js)

> 전기차 충전기 AI 운영 관리 시스템 &mdash; 프론트엔드

[![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)](https://react.dev)
[![MUI](https://img.shields.io/badge/MUI-5.18-007FFF?logo=mui&logoColor=white)](https://mui.com)

---

## 개요

운영자가 충전기 현황을 **실시간으로 모니터링**하고 AI 기반 운영 지원을 받을 수 있는 웹 대시보드입니다.

---

## 주요 페이지

### 모니터링

- **컴포넌트 Set** &mdash; 전국 충전소 통계, 리스트, 필터링 검색 기능
- **챗봇** &mdash; RAG 기반 운영 규정 질의응답
- **알림 전송** &mdash; 상태 미확인 충전소 &rarr; 관리업체 이메일로 상태 전송 및 조치 권고
- **컴포넌트 편집** &mdash; 추가/삭제/이동으로 대시보드 커스터마이징

### 모니터링 (상세 정보)

- 충전소 충전기 리스트 및 상세 정보 (도로명 주소, 관리기관, 특이사항)
- CCTV 영상 출력
- **이미지 분석** 버튼 &rarr; Multimodal Ensemble로 화재/고장/청결 분석

### 보고서 생성

- **보고서 유형 선택** &mdash; 감사용 / 맞춤형 보고서
- 프롬프트 입력 및 기간 선택 &rarr; AI Agent가 보고서 자동 생성
- **PDF 다운로드** 지원

### 민원 조회

- 등록된 민원 리스트 및 필터링 검색
- 민원 상세 조회 + Agent 자동 생성 답변 열람
- Agent 답변 처리 (자동 등록) / 삭제 기능

### 디지털 트윈 Simulator

- **Autopilot** &mdash; 우선 처리 대상 자동 선별
- **유지보수 수행사 선정** &mdash; 시뮬레이션 기반 업체 비교 및 랭킹
- **지도 모니터링** &mdash; 충전소 + Autopilot Top 10 표시

### 회원가입 및 로그인

- 개인정보 및 약관 동의, 아이디 중복 체크
- 비밀번호 해시 적용, 세션 기반 인증 관리

---

## 기술 스택

| 분류       | 기술                       |
| ---------- | -------------------------- |
| 프레임워크 | Next.js 16 (React 19)      |
| UI         | MUI (Material UI) 5.18     |
| 지도       | Leaflet + React-Leaflet    |
| 차트       | Recharts                   |
| HTTP       | Axios                      |
| UI/UX 설계 | Figma                      |
| CI/CD      | AWS CodeBuild + CodeDeploy |

## 디렉토리 구조

```
front/
├── appspec.yml          # AWS CodeDeploy 설정
├── buildspec.yml        # AWS CodeBuild 설정
├── scripts/
│   ├── start.sh
│   └── stop.sh
└── nextjs/
    ├── package.json
    ├── next.config.mjs
    ├── jsconfig.json
    ├── public/          # 정적 파일
    └── src/             # 페이지 및 컴포넌트
```

## 실행 방법

```bash
cd nextjs
npm install
npm run dev              # 개발 서버
npm run build && npm start   # 프로덕션
```

---

[< 전체 프로젝트로 돌아가기](../../tree/main)
