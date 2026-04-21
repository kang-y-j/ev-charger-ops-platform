# Backend Server (Spring Boot)

> 전기차 충전기 AI 운영 관리 시스템 &mdash; 백엔드 API 서버

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.9-6DB33F?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-17-ED8B00?logo=openjdk&logoColor=white)](https://openjdk.org)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?logo=mysql&logoColor=white)](https://mysql.com)
[![AWS](https://img.shields.io/badge/AWS-S3%20%7C%20CodeDeploy-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com)

---

## 개요

충전기 데이터 관리, 사용자 인증, 이미지 저장, AI 서버 연동 등 **핵심 비즈니스 로직을 처리**하는 REST API 서버입니다.

---

## 주요 기능

- **충전기 관리 API** &mdash; 충전소/충전기 CRUD 및 상태 조회
- **사용자 인증** &mdash; Spring Security 기반 인증/인가
- **이미지 업로드** &mdash; AWS S3 Presigned URL 방식
- **AI 서버 연동** &mdash; WebClient를 통한 FastAPI 서버 비동기 호출
- **API 문서** &mdash; Swagger UI 자동 생성

---

## 보안

| 항목 | 적용 내용 |
|------|----------|
| 비밀번호 암호화 | BCrypt (자동 Salt, Match()로 타이밍 공격 방지) |
| 세션 고정 공격 방지 | 로그인 시 기존 세션 무효화 + 새 세션 생성 |
| 사용자 열거 공격 방지 | 아이디/비밀번호 오류 시 동일 메시지 반환 |
| CSRF 방어 | SameSite=Lax + HttpOnly 쿠키 |
| HTTPS | SSL/TLS 인증서, HTTP &rarr; HTTPS 강제 전환 |

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| 프레임워크 | Spring Boot 3.5.9 (Java 17) |
| ORM | Spring Data JPA |
| 인증/보안 | Spring Security |
| 비동기 통신 | Spring WebFlux (WebClient) |
| 클라우드 스토리지 | AWS S3 (Presigned URL) |
| DB | MySQL (운영) / H2 (개발) |
| API 문서 | Springdoc OpenAPI (Swagger UI) |
| CI/CD | AWS CodeBuild + CodeDeploy |

## 디렉토리 구조

```
back/
├── appspec.yml          # AWS CodeDeploy 설정
├── buildspec.yml        # AWS CodeBuild 설정
├── scripts/
│   ├── start.sh         # 배포 시작 스크립트
│   └── stop.sh          # 배포 중지 스크립트
└── backend/
    ├── build.gradle     # Gradle 빌드 설정
    ├── settings.gradle
    ├── gradlew / gradlew.bat
    ├── gradle/
    └── src/             # Java 소스코드
```

## ERD

충전소/충전기, 멀티모달, 사용자/보고서/민원처리 도메인으로 구성

## 실행 방법

```bash
cd backend
./gradlew bootRun           # 개발 환경 (H2 DB)
./gradlew build             # 프로덕션 빌드

# Swagger UI: http://localhost:8080/swagger-ui/index.html
```

---

[< 전체 프로젝트로 돌아가기](../../tree/main)
