# 📈 Financial Pulse
![금융박동 메인화면](./view/photo/금융박동%20메인화면.png)
> 국내외 금융 뉴스를 자동 수집하고, 머신러닝으로 감성을 분석하여 오늘 시장의 분위기를 한눈에 보여주는 금융 뉴스 분석 플랫폼

<br>

## 📌 프로젝트 개요

기존 금융 서비스들은 복잡한 차트와 전문가 위주의 UI로 인해 일반 투자자가 시장 흐름을 파악하기 어렵다는 문제가 있습니다.  
**Financial Pulse**는 매일 국내외 금융 뉴스를 자동 수집하고, ML 감성 분석을 통해 오늘 시장의 분위기를 직관적으로 전달합니다.

- **개발 기간:** 2026.04 ~ 2026.05
- **팀 구성:** 4인 팀 프로젝트 (금융박동)
- **팀원:** 김서율, 곽충범, 방정환, 이재민

<br>

## 🛠 기술 스택

| 분류 | 기술 |
|---|---|
| **언어** | Python, HTML, CSS, JavaScript |
| **프레임워크** | FastAPI |
| **크롤링** | Selenium, BeautifulSoup4, feedparser, requests |
| **ML / NLP** | PyTorch, Hugging Face Transformers, SentenceTransformers, KR-FinBERT-SC, FinBERT, Zero-shot Classification, Customized KeyBERT, spaCy, Kiwi, FlashText |
| **LLM** | Google Gemini API (Gemini 2.5 Flash) |
| **보안** | Argon2 + Pepper, Session Cookie, API Key |
| **DB / 저장소** | Elasticsearch, MariaDB |
| **인프라 / 네트워킹** | Tailscale, APScheduler |
| **협업 / 툴** | Git, GitHub |

<br>

## ✨ 주요 기능

### 1. 메인 대시보드
- 오늘 시장 전체 분위기 (긍/부정 비율, 도넛 차트)
- 섹터별 긍/부정 비율 비교
- 오늘의 핫이슈 키워드
- 주요 경제 지표 및 경제 일정
- 국내 / 해외 단일·분할 보기

### 2. 성향 분석 검색
- 키워드 입력 시 관련 기사들의 전체 분위기 분석
- 연관 분야별 긍/부정 비교 (섹터 차트)
- 관련 뉴스 최대 8개 제공 (1순위 + 2순위)

### 3. 키워드 트렌드
- 오늘의 키워드 Top7 + 연관 강도 계기판
- 주요 키워드 7일 주간 트렌드 라인 차트
- 키워드 공출현 네트워크 시각화
- Top7 키워드별 최신 핫이슈 뉴스

### 4. 급등 기사 분석
- 평소 대비 오늘 긍정률 변화량 섹터별 제공
- 변화량 절댓값 기준 내림차순 정렬
- 섹터 클릭 시 관련 급등 기사 목록 제공

### 5. 관리자 시스템 (LogViewer)
- 실시간 로그 조회 (레벨 / 주체 / 시간 / 키워드 필터)
- CSV 내보내기
- 크롤링 스케줄 관리 및 오류 재시도
- ES 인덱스 현황 조회 및 누락 URL 재수집
- 비정형 성향치 수동 보정 (긍정 / 부정 / 중립 / 삭제)

<br>

## 🏗 시스템 아키텍처

```
[크롤링]                [ML 파이프라인]           [API 서버]
네이버 금융     →      1. 섹터 분류              FastAPI
한국경제        →  ES  2. 감성 분석      →  ES   ├── 대시보드
Google RSS     →      3. 키워드 추출            ├── 검색
                       4. NER 분석              ├── 키워드 트렌드
                                                ├── 급등 분석
[스케줄러]              [DB]                     └── 관리자
APScheduler    →      MariaDB (사용자 정보)
KO: 07:30/11:30        ES (뉴스 / 분석 / 로그)
    /18:30/23:59
EN: 06:10/21:00
재시도: 03:00
```

<br>

## 📂 ES 인덱스 구조

| 인덱스 | 설명 |
|---|---|
| `news_ko` | 한국어 뉴스 원문 (nori 형태소 분석기) |
| `news_en` | 영문 뉴스 원문 |
| `analyze` | ML 분석 결과 (sector, keywords, ner, tendency, tend_score) |
| `log_crawl` | 크롤링 로그 |
| `log_ml` | ML 파이프라인 로그 |
| `log_system` | 시스템 로그 |
| `log_user` | 사용자 로그 |

<br>

## 👤 나의 담당 역할 (My Role)

**팀 내 포지션: Backend & Infrastructure Engineer (백엔드 개발 및 로그/데이터 인프라 구축)**

### 백엔드 개발
- FastAPI 기반 RESTful API 서버 설계 및 구현
- ES `msearch`를 활용한 대시보드 데이터 API 개발
  - 오늘/7일치 `doc_id` 수집 → `sector × tendency` 집계 → 긍/부정 비율 계산
- 성향 분석 검색 API 개발
  - `title`, `keywords`, `ner.company`, `ner.person`, `ner.region` 5개 필드 OR 조건 검색
  - 1순위 기사 부족 시 연관도 Top3 섹터 기반 2순위 기사 자동 보완 로직 구현
- 급등 기사 분석 API 개발
  - 오늘 긍정률 - 7일 평균 긍정률 = 변화량 계산 후 절댓값 내림차순 정렬 서빙

### 로그 시스템 설계 및 구현
- Elasticsearch 기반 4개 영역 로그 인덱스 분리 설계
  - `log_crawl` / `log-ml` / `log-system` / `log-user` / `log-all`
- 커스텀 `ESHandler` 구현 (Python `logging.Handler` 상속)
  - 로그 발생 시 subject별 인덱스 + `fp-logs-all`에 동시 기록
- 관리자용 LogViewer 백엔드 구현
  - 레벨 / 주체 / 시간 범위 / 키워드 필터 기반 로그 조회
  - 조회 결과 CSV 내보내기 (utf-8-sig BOM 적용으로 Excel 한글 깨짐 방지)

### Elasticsearch / MariaDB 구축
- `news_ko` 인덱스: 한국어 Nori 형태소 분석기 (`decompound_mode: mixed`) 적용
- `news_en` 인덱스: Standard Analyzer 적용
- `analyze` 인덱스: ML 분석 결과 저장 (`sector`, `keywords`, `ner`, `tendency`, `tend_score`)
- MariaDB 스키마 설계
  - `user`, `userInterests`, `loginFail`, `retryQueue` 테이블 설계

### 보안 및 인증
- Argon2 + Pepper (HMAC-SHA256) 이중 해싱으로 비밀번호 단방향 암호화
- 브라우저용 세션 쿠키 + 외부 도구(Postman/curl)용 API Key 이중 인증 체계 구축
- 로그인 5회 실패 시 24시간 계정 잠금 로직 구현
- 임시 비밀번호 발급 기능 구현 (이메일 발송 실패 시 DB rollback 트랜잭션 처리)

### 운영 예외 처리
- 크롤링 실패 URL을 `retryQueue` 테이블에 적재하고 매일 03:00 스케줄에 자동 재시도하는 재크롤링 파이프라인 구현
  - 상태 관리: `PENDING → RUNNING → SUCCESS / FAIL`
  - 최대 3회 재시도 초과 시 영구 FAIL 처리
  - 성공 적재 후 ML 파이프라인 자동 실행 연동
- ES `delete_by_query`의 비동기 처리로 인한 문서 유실 버그 발견 및 `refresh=True` 옵션 적용으로 해결
- 비정형 성향치(tend_score 5점 이하 / 95점 이상) 수동 보정 API 개발
  - `analyze` + `search` 인덱스 동시 업데이트로 데이터 정합성 확보

### 프로젝트 초기 문서 작업
- 기능 요구사항 정의서 및 ERD 설계
- API 명세서 작성
- 발표 PPT 작성 및 질의 담당

<br>

## 🚀 실행 방법

### 1. 환경 설정
```bash
git clone https://github.com/{username}/Financial_Pulse.git
cd Financial_Pulse
pip install -r requirements.txt
```

### 2. `.env` 파일 생성
```
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=financial_pulse

ES_URL=http://your_es_host:9200

ADMIN_EMAIL=admin@finance.com
ADMIN_PASSWORD=your_admin_password
ADMIN_API_KEY=your_api_key

PEPPER_KEY=your_pepper_key
SESSION_SECRET_KEY=your_session_key

MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your_email@gmail.com
MAIL_PASSWORD=your_app_password
```

### 3. 서버 실행
```bash
uvicorn main:app --reload
```

### 4. 크롤링 단독 실행
```bash
# 한국어 뉴스
python crawling/collectKoNews.py

# 영문 뉴스
python crawling/collectEnNews.py
```

<br>

## 📊 데이터 수집 현황

| 구분 | 수집 소스 | 하루 평균 수집량 | 스케줄 |
|---|---|---|---|
| 국내 뉴스 | 네이버 금융, 한국경제 | 300 ~ 500건 | 07:30 / 11:30 / 18:30 / 23:59 |
| 해외 뉴스 | Google News RSS | 400 ~ 600건 | 06:10 / 21:00 |

<br>

## 🔒 보안

- **비밀번호:** Argon2 + Pepper (HMAC-SHA256) 이중 해싱
- **관리자 인증:** 세션 쿠키 (브라우저) + API Key (외부 도구)
- **로그인 보호:** 5회 실패 시 24시간 잠금

<br>

## 👥 팀원 역할 분담

| 이름 | 역할 |
|---|---|
| 김서율 | 백엔드 개발, 로그 시스템 설계, ES/DB 서버 구축, 보안/인증, 테스팅 |
| 곽충범 | 크롤링, ML/NLP 파이프라인, ES 서버 구축 |
| 방정환 | 프론트엔드 개발, UI/UX 설계 |
| 이재민 | 크롤링, 백엔드 개발 |
