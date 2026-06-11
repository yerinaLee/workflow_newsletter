# 📧 n8n Newsletter Automation

n8n을 Docker로 실행하여 뉴스레터를 자동 수집·발송하는 자동화 프로젝트입니다.  
스케줄 트리거로 뉴스 사이트를 크롤링하고, Gmail로 정기 뉴스레터를 발송합니다.

## 워크플로우

| 워크플로우 | 설명 |
|---|---|
| **universe newsletter workflow** | 교직원신문 등 주요 뉴스를 스크래핑하여 뉴스레터 발송 |
| **kpop newsletter workflow** | K-POP 관련 뉴스를 수집하여 뉴스레터 발송 |
| **insure newsletter workflow** | 보험/금융 관련 뉴스를 수집하여 뉴스레터 발송 |

## 기술 스택

- **[n8n](https://n8n.io/)** — 노코드/로우코드 워크플로우 자동화 플랫폼
- **Docker / Docker Compose** — 컨테이너 기반 실행 환경
- **SQLite** — 워크플로우 실행 이력 및 데이터 저장
- **iconv-lite** — 한국어 인코딩(EUC-KR) 처리
- **Gmail** — 뉴스레터 발송

## 워크플로우 구조

```
Schedule Trigger
    └─> HTTP Request (뉴스 사이트 크롤링)
        └─> AI API (AI로 뉴스 주제 정리)
            └─> Code Node (HTML 파싱 / iconv-lite 인코딩 변환)
                    └─> Gmail (뉴스레터 발송)
```

## 시작하기

### 사전 요구사항

- [Docker](https://www.docker.com/) 및 Docker Compose 설치
- Gmail 계정 (OAuth2 연동 또는 앱 비밀번호)

### 실행 방법

**1. 저장소 클론**

```bash
git clone <repository-url>
cd newsletter_n8n
```

**2. 환경 변수 설정**

`.env.example`을 복사하여 `.env` 파일을 생성하고 값을 설정합니다.

```bash
cp .env.example .env
```

```env
N8N_USER=your_username
N8N_PASSWORD=your_secure_password
```

> **주의:** `.env` 파일은 절대 Git에 커밋하지 마세요.

**3. Docker Compose 실행**

```bash
docker-compose up -d
```

**4. n8n 접속**

브라우저에서 [http://localhost:5678](http://localhost:5678) 으로 접속합니다.

**5. 워크플로우 설정**

1. Gmail 노드에서 OAuth2 또는 앱 비밀번호로 자격 증명을 설정합니다.
2. 각 워크플로우의 Schedule Trigger에서 원하는 발송 주기를 설정합니다.
3. 워크플로우를 활성화(Active)합니다.

## 프로젝트 구조

```
newsletter_n8n/
├── docker-compose.yml      # Docker Compose 설정
├── .env                    # 환경 변수 (Git 제외)
├── .env.example            # 환경 변수 예시
└── n8n_data/               # n8n 데이터 영속성 볼륨
    ├── config              # n8n 암호화 키 설정
    ├── database.sqlite     # 워크플로우 및 실행 이력 DB
    └── storage/            # 실행 중 생성된 바이너리 데이터
```

## 환경 변수

| 변수 | 설명 | 기본값 |
|---|---|---|
| `N8N_USER` | n8n 관리자 계정 아이디 | `admin` |
| `N8N_PASSWORD` | n8n 관리자 계정 비밀번호 | `admin` |

## Docker Compose 설정 상세

```yaml
environment:
  - TZ=Asia/Seoul                          # 한국 시간대 적용
  - GENERIC_TIMEZONE=Asia/Seoul
  - N8N_RUNNERS_ALLOW_BUILTIN=*            # 모든 내장 모듈 허용
  - N8N_RUNNERS_ALLOW_EXTERNAL=iconv-lite  # 한국어 인코딩 처리 모듈 허용
  - NODE_FUNCTION_ALLOW_EXTERNAL=iconv-lite
```

## 주의사항

- `n8n_data/` 디렉토리에는 워크플로우 설정, 자격 증명(암호화), 실행 이력이 저장됩니다. 정기적으로 백업하세요.
- `n8n_data/config` 파일의 암호화 키(`encryptionKey`)는 자격 증명 복호화에 사용됩니다. 분실 시 저장된 자격 증명을 복구할 수 없습니다.
- 프로덕션 환경에서는 반드시 기본 비밀번호를 변경하세요.

## 라이선스

MIT
