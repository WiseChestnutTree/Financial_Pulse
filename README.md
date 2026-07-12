# 📈 Financial Pulse
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

**팀 내 포지션: Backend & Infrastructure Engineer (백엔드 API 개발 및 로그/데이터 인프라 구축)**

### 백엔드 개발
- FastAPI 기반의 고성능 RESTful API 서버 아키텍처 설계 및 구현
- 메인 대시보드(긍/부정 도넛 차트, 섹터별 비교) 및 성향 분석 검색 API 개발
- 평소 대비 섹터별 긍정률 변화량을 계산하고 정렬하여 서빙하는 급등 기사 분석 로직 구현

### 로그 시스템
- 시스템 안정성 및 파이프라인 모니터링을 위한 Elasticsearch 기반 다중 로그 인덱스 설계
- 운영 목적에 따른 4개 영역(crawl, ml, system, user) 로그 분리 및 수집 체계 구축
- 실시간 로그 필터링(레벨/주체/시간/키워드) 및 CSV 내보내기 기능의 관리자용 LogViewer 백엔드 구현

### ES / DB 서버 구축
- 뉴스 원문 및 분석 데이터 저장·색인을 위한 Elasticsearch 분산 데이터 노드 구축
- 한국어 형태소 분석기(Nori) 및 영문 Standard Analyzer 매핑 설계를 통한 검색 성능 최적화
- 서비스 회원 관리 및 권한 제어를 위한 MariaDB 관계형 데이터베이스(RDB) 스키마 설계

### 보안 및 인증 (단방향 암호화)
- 사용자 비밀번호 보호를 위해 Argon2와 Pepper(HMAC-SHA256)를 결합한 안전한 이중 해싱 단방향 암호화 적용
- 브라우저용 Session Cookie 및 외부 툴 대응을 위한 관리자용 API Key 인증 체계 구축
- 악성 접근 차단을 위한 로그인 5회 실패 시 24시간 계정 잠금 보안 로직 구현

### 에러 해결 및 사전 테스팅
- Pytest 기반의 API 엔드포인트 기능 테스트 및 모듈별 통합 사전 테스팅 주도
- 크롤링 및 ML 파이프라인 에러 발생 시 자동 재시도 및 누락 URL 재수집을 처리하는 운영 예외 처리 로직 구현
- 분석 결과 예외 처리를 위한 '비정형 성향치 수동 보정(긍정/부정/중립/삭제) API' 개발로 데이터 무결성 확보

### 프로젝트 초기 문서 작업
- 프로젝트의 전체적인 기능 요구사항 정의서 및 시스템 아키텍처 초안 작성
- 팀원 간 효율적인 협업을 위한 데이터베이스 개체 관계 다이어그램(ERD) 및 API 명세서 설계

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
| 김서율 | 백엔드 개발, 로그 시스템, ES/DB 서버 구축, 테스팅 |
| 곽충범 | 크롤링, ML/NLP 파이프라인, ES 서버 구축 |
| 방정환 | 프론트엔드 개발, UI/UX 설계 |
| 이재민 | 크롤링, 백엔드 개발 |
