# 청년정책 챗봇 (Youth Policy Chatbot)

## 📖 프로젝트 개요

이 프로젝트는 청년들이 정부의 청년 정책을 쉽고 빠르게 찾고 이해할 수 있도록 돕는 **RAG (Retrieval-Augmented Generation) 기반 챗봇**입니다. 하이브리드 검색 시스템과 LLM 기반 재순위화(Reranking) 기술을 활용하여 정확하고 개인화된 정책 추천을 제공합니다.

## ✨ 주요 기능

* **하이브리드 검색 시스템 (Hybrid Retrieval System)**: **벡터 검색 (Vector Search)**의 의미론적 유사성 검색과 **BM25**의 키워드 매칭을 결합하여 높은 재현율(Recall)과 정밀도(Precision)를 보장합니다.
* **지능형 쿼리 라우팅 (Intelligent Query Routing)**: 사용자 질문을 분석하여 세 가지 카테고리로 자동 분류하고 최적의 검색 전략을 적용합니다:
  * `Explanation`: 특정 정책에 대한 상세 설명
  * `Recommend`: 사용자 상황에 맞는 맞춤형 추천
  * `General`: 청년 정책 전반에 대한 일반적인 정보
* **LLM 재순위화 (LLM Reranking)**: 검색된 문서들을 LLM을 사용하여 사용자 질문 및 프로필과의 관련성에 따라 재평가하고 순위를 재조정합니다.
* **메타데이터 필터링 (Metadata Filtering)**: 질문에서 추출한 사용자 프로필 정보(나이, 거주지, 결혼 여부 등)를 바탕으로 정책을 필터링합니다.
* **다중 쿼리 생성 (Multi-Query Generation)**: 일반적인 질문의 경우, 검색 범위를 넓히기 위해 다양한 형태의 쿼리를 생성하여 검색 품질을 향상시킵니다.

## 🏗️ 아키텍처

이 시스템은 표준적인 RAG 파이프라인을 따릅니다:

1. **쿼리 처리 (Query Processing)**: 사용자 질문을 분석하여 프로필 데이터(나이, 지역 등)를 추출하고 의도(Router)를 파악합니다.
2. **검색 (Retrieval)**:
   * **벡터 검색**: Pinecone과 Upstage Solar 임베딩을 사용합니다.
   * **키워드 검색**: BM25 알고리즘을 사용합니다.
   * **필터링**: MySQL을 사용하여 메타데이터 기반 필터링을 수행합니다.
3. **재순위화 (Reranking)**: LLM이 검색된 문서의 관련성을 점수화하여 순위를 조정합니다.
4. **생성 (Generation)**: 상위 랭크된 정책들과 선택된 전략을 바탕으로 LLM이 자연어 답변을 생성합니다. 

## 🚀 시작하기

### 필수 조건

* Python 3.8 이상
* MySQL 데이터베이스
* Pinecone API Key
* Upstage API Key

### 설치 방법

1. **레포지토리 클론**
   
   ```bash
   git clone [repository_url]
   cd summer-ssafy-ai
   ```

2. **의존성 설치**
   
   ```bash
   cd rag_pipeline/backend
   pip install -r requirements.txt
   ```

3. **환경 변수 설정**
   `rag_pipeline/backend` 디렉토리에 `.env` 파일을 생성하고 다음 변수들을 설정하세요:
   
   ```env
   UPSTAGE_API_KEY=your_upstage_api_key
   PINECONE_API_KEY=your_pinecone_api_key
   DB_HOST=your_db_host
   DB_USER=your_db_user
   DB_PASSWORD=your_db_password
   DB_NAME=youth_policy
   ```

4. **데이터베이스 설정**
   
   * MySQL 데이터베이스에 `output.sql` 스크립트를 실행하여 정책 데이터를 입력합니다.
   * (선택 사항) 필요시 `open_api_parser/parser.py`를 사용하여 SQL 파일을 재생성할 수 있습니다.

5. **서버 실행**
   
   ```bash
   uvicorn app:app --host 0.0.0.0 --port 8000 --reload
   ```

### API 사용법

* **채팅 엔드포인트**: `POST /chat`
  
  ```json
  {
    "message": "서울에 사는 25살 취준생인데 월세 지원 받을 수 있어?"
  }
  ```

* **헬스 체크**: `GET /health`

## 🛠️ 기술 스택

* **Framework**: FastAPI
* **LLM**: Upstage Solar
* **Vector DB**: Pinecone
* **Database**: MySQL
* **Orchestration**: LangChain
