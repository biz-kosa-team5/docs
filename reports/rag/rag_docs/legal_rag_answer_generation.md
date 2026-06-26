# 법령 RAG 질문 답변 생성

이 문서는 사용자의 법률 질문이 입력된 뒤, 검색 근거 조문을 찾고 LLM 답변으로 변환되어 반환되기까지의 과정을 설명한다.

## 목적

답변 생성 단계의 목표는 다음이다.

1. 사용자 질문을 정규화한다.
2. 일상어-법률어 매핑과 질문 intent 기반 확장어를 만든다.
3. 질문을 임베딩하고 조문 후보를 검색한다.
4. 벡터 점수, 키워드 점수, intent focus, penalty를 조합해 근거 조문을 재정렬한다.
5. 상위 조문만 LLM에 전달한다.
6. LLM이 반환한 citation을 서버에서 검증하고 최종 답변에 출처 문장을 붙인다.

## 관련 파일

| 파일 | 책임 |
|---|---|
| `app/chatbot/features/legal_contract/slots.py` | 최초 질문 슬롯 생성 |
| `app/chatbot/features/legal_contract/normalization.py` | 질문 정규화 |
| `app/chatbot/features/legal_contract/service.py` | 챗봇 도구에서 RAG 검색과 답변 생성 연결 |
| `app/chatbot/service/tools/legal_contract_tool.py` | LangChain tool 등록 |
| `rag/controller/query_controller.py` | `/api/laws/query` 검색 API |
| `rag/dto/query.py` | 검색 요청/응답 DTO |
| `rag/service/query_service.py` | 검색 전체 흐름 |
| `rag/service/query_intent.py` | 질문 intent 감지 |
| `rag/service/query_expansion.py` | intent 기반 확장어 생성 |
| `rag/service/query_text.py` | 질문 임베딩 텍스트 구성 |
| `rag/service/query_ranking.py` | 하이브리드 랭킹 |
| `rag/service/query_response.py` | 검색 응답 dict 구성 |
| `rag/dto/answer.py` | 답변/citation DTO |
| `rag/service/answer_service.py` | 검색 결과를 LLM 답변으로 변환 |
| `rag/service/answer_prompt.py` | 시스템 프롬프트와 LLM 입력 context 구성 |
| `rag/service/answer_generator.py` | OpenAI chat completion 호출 |
| `rag/service/answer_response.py` | citation 검증과 출처 문장 포맷팅 |

## 검색 API와 챗봇 답변의 차이

`POST /api/laws/query`는 법령 근거 조문 검색 결과만 반환한다. 이 API는 LLM 답변을 생성하지 않는다.

챗봇 답변은 다음 경로를 탄다.

```text
legal_contract_tool.search_legal_contract()
-> extract_legal_contract_slots()
-> run_legal_contract()
-> LegalRagQueryService.query()
-> LegalAnswerService.answer()
-> OpenAILegalAnswerGenerator.generate()
```

따라서 “검색 결과 JSON”과 “사용자에게 보여줄 자연어 답변 JSON”은 서로 다른 출력이다.

## 입력 슬롯

`extract_legal_contract_slots()`는 질문을 다음 슬롯으로 변환한다.

```json
{
  "original_query": "계약금을 냈는데 계약을 취소할 수 있어?",
  "normalized_query": "계약금을 냈는데 계약을 취소할 수 있어",
  "expanded_terms": []
}
```

`run_legal_contract()`는 실제 실행 시 슬롯을 다시 보정한다.

1. `original_query`가 없으면 현재 입력 text를 사용한다.
2. `normalize_query()`로 `normalized_query`를 만든다.
3. 검색 결과의 `expandedTerms`를 슬롯의 `expanded_terms`에 넣는다.
4. 원문 질문과 검색 결과를 `LegalAnswerService.answer()`에 전달한다.

## 질문 정규화

정규화는 `normalization.py`의 `normalize_query()`가 수행한다.

처리 내용:

1. Unicode NFC 정규화
2. 일부 문장부호를 공백으로 치환
3. 소문자화
4. 연속 공백을 하나로 압축
5. 앞뒤 공백 제거

띄어쓰기를 완전히 제거하지는 않는다. “계약금 취소”처럼 단어 경계가 검색어 추출과 매핑에 의미가 있기 때문이다.

## 질문 intent 감지

`query_intent.py`는 질문에 포함된 표현을 기준으로 intent를 감지한다.

현재 intent:

| Intent | 의미 |
|---|---|
| `BROAD` | “집 살 때 알아야 할 법”처럼 넓은 질문 |
| `TAX` | 세금, 취득세, 양도소득세, 증여세 관련 질문 |
| `CONTRACT` | 계약금, 해제, 계약 성립 등 계약 질문 |
| `CHECKLIST` | 계약서에서 중요하게 볼 부분, 체크리스트 질문 |
| `PRE_CONTRACT_CHECK` | 계약 전 확인사항 질문 |
| `REGISTRATION` | 명의 이전, 소유권 이전등기, 등기 질문 |
| `LEASE` | 세입자, 전세, 임대차, 보증금 질문 |
| `BROKER` | 공인중개사, 중개대상물 확인·설명 질문 |
| `RISK` | 근저당, 압류, 가압류, 위험 권리 질문 |
| `FALSE_PRICE` | 다운계약서, 허위 거래금액 질문 |
| `GENERAL` | 위 intent에 걸리지 않은 일반 질문 |

`BROAD`는 항상 붙는 것이 아니다. 예를 들어 등기부 위험 확인처럼 이미 좁은 `RISK` intent가 잡힌 질문에는 broad 확장을 붙이지 않는다. 너무 넓은 확장어가 오히려 검색을 흐릴 수 있기 때문이다.

## 확장어 생성

확장어는 두 경로에서 만들어진다.

1. DB 매핑: `daily_legal_term_mappings`
2. 코드 기반 intent 확장: `query_expansion.py`

예시:

| 질문 표현 | 확장 방향 |
|---|---|
| 계약금 | 해약금, 계약 해제 |
| 명의 이전 | 소유권 이전등기, 권리 이전 |
| 세입자 있는 집 | 임차인, 임대차, 대항력, 임대인 지위 승계 |
| 빚 잡힌 집 | 저당권, 근저당권, 압류, 가압류 |
| 실제보다 낮게 계약서 작성 | 실제 거래가격, 거짓 신고, 거래계약신고필증 |

DB 매핑은 운영 데이터로 보완 가능하고, intent 확장은 질문 유형별 검색 안정성을 보강하는 코드 레벨 규칙이다.

## 질문 임베딩 텍스트

`query_text.py`는 정규화된 질문과 확장어를 하나의 임베딩 입력으로 만든다.

개념적으로는 다음 형태다.

```text
질문: {normalized_query}
확장어: {expanded_term_1}, {expanded_term_2}, ...
```

이 텍스트를 `OpenAIEmbeddingClient.prepare_text()`로 토큰 길이 제한에 맞춘 뒤 임베딩한다.

## 검색 후보 구성

`LegalRagQueryService.query()`는 다음 후보군을 만든다.

1. pgvector 기반 벡터 유사도 검색 후보
2. 법령명, 조문 제목, 본문에 검색어가 포함된 키워드 후보

기본값:

| 값 | 현재 설정 |
|---|---:|
| `DEFAULT_TOP_K` | `7` |
| `DEFAULT_MIN_SCORE` | `0.45` |
| `DEFAULT_CANDIDATE_K` | `70` |

후보 개수:

```text
일반 질문: max(70, topK * 10)
BROAD 질문: max(candidateK, topK * 20)
```

PostgreSQL이 아니거나 pgvector 검색을 사용할 수 없는 테스트 환경에서는 `python_rank_documents()`가 Python으로 cosine similarity를 계산한다.

## 하이브리드 랭킹

최종 재정렬은 `query_ranking.py`의 `hybrid_rank_documents()`가 수행한다.

계산 개념:

```text
score = min(1.0, vectorScore + keywordScore + intentFocusScore) - penalty
score = max(0.0, score)
```

응답의 `keywordScore` 필드에는 순수 키워드 점수와 intent focus 점수를 합친 값이 들어간다.

### 키워드 점수

| 위치 | 기본 가산점 |
|---|---:|
| 법령명 `law_name` | `0.12` |
| 조문 제목 `article_title` | `0.10` |
| 조문 내용 `content` | `0.04` |

상한:

| 상수 | 값 |
|---|---:|
| `MAX_KEYWORD_SCORE` | `0.25` |
| `MAX_PRIMARY_KEYWORD_SCORE` | `0.20` |
| `MAX_EXPANDED_KEYWORD_SCORE` | `0.04` |

확장어는 원문 질문에 나온 핵심어보다 낮은 가중치를 가진다. 확장어가 너무 강하게 작동하면 질문이 특정 조문으로 과도하게 묶일 수 있기 때문이다.

### Intent focus 점수

intent별로 중요한 조문 제목이나 본문 표현에 약한 보정을 준다. 예를 들어 `LEASE` 질문에서는 대항력, 임대인 지위 승계, 보증금 반환 관련 조문이 조금 더 유리해진다.

상한:

```text
MAX_INTENT_FOCUS_SCORE = 0.08
```

이 값은 정답을 강제로 고정하는 장치가 아니라, 후보군 안에서 질문 의도와 더 가까운 조문을 약하게 올리는 장치다.

### Penalty

일반 질문에 특수 조문이 과도하게 올라오는 것을 줄이기 위해 감점한다.

예시:

| 상황 | 감점 |
|---|---:|
| 일반 질문에서 특수 문맥 조문 | `0.04` |
| 일반 세금 질문에서 부동산매매업자 특례 조문 | `0.07` |
| 일반 등기 질문에서 일부이전 등 특수 조문 | `0.08` |
| broad 질문에서 정의/자료관리 등 낮은 신호 조문 | `0.06` |

단, 사용자가 직접 “부동산매매업자”, “지분 일부 이전”처럼 특수 문맥을 말하면 해당 감점은 적용하지 않는다.

### Diversity

`diversify_ranked_documents()`는 상위 결과가 같은 법령에 과도하게 몰리는 것을 줄인다.

기본 기준:

```text
MAX_DOCUMENTS_PER_LAW_IN_TOP_RESULTS = 2
DIVERSITY_SCORE_TOLERANCE = 0.05
```

같은 법령에서 이미 2개가 선택되었고, 점수 차이가 크지 않은 다른 법령 후보가 있으면 다른 법령 후보를 먼저 보여준다.

## 검색 응답 DTO

`POST /api/laws/query` 요청:

```json
{
  "question": "계약금을 냈는데 계약을 취소할 수 있어?",
  "topK": 7
}
```

응답:

```json
{
  "handler": "legal_contract",
  "success": true,
  "question": "계약금을 냈는데 계약을 취소할 수 있어",
  "expandedTerms": ["해약금"],
  "sources": [
    {
      "documentId": 573,
      "lawId": "001706",
      "lawName": "민법",
      "articleNo": "제565조",
      "articleTitle": "해약금",
      "paragraphNo": "",
      "content": "조문 본문...",
      "score": 0.71,
      "vectorScore": 0.61,
      "keywordScore": 0.1,
      "sourceUrl": "https://...",
      "effectiveDate": "2026-06-22"
    }
  ],
  "summary": null,
  "reason": null,
  "message": "검색된 법령 근거를 찾았습니다."
}
```

## 답변 생성 프롬프트

`answer_prompt.py`의 주요 설정:

| 항목 | 값 |
|---|---:|
| `MAX_ANSWER_SOURCES` | `7` |
| `MAX_SOURCE_CONTENT_CHARS` | `12000` |
| 답변 길이 | 500자 이내 |
| 답변 톤 | 일반 사용자가 이해할 수 있는 쉬운 용어 |
| 출처 문장 | 모델이 쓰지 않고 서버가 붙임 |

LLM에는 상위 source 중 최대 7개만 전달한다. 각 source는 `documentId`, `lawName`, `articleNo`, `articleTitle`, `paragraphNo`, `content`로 축약된다.

프롬프트의 핵심 제약:

1. 제공된 조문만 근거로 답한다.
2. 근거 없는 판례, 관행, 절차, 결론을 만들지 않는다.
3. 검색된 조문이 질문 일부만 설명하면 확인되는 범위만 답한다.
4. 충분한 근거가 없으면 `insufficient_evidence`로 반환한다.
5. 답변 본문에는 별도 출처 목록, URL, 시행일, documentId, “근거는” 문장을 쓰지 않는다.

## LLM 구조화 출력

`answer_generator.py`는 OpenAI chat completion을 호출하고, `response_format`을 JSON schema로 고정한다.

기본 모델:

```text
gpt-5.5
```

환경변수 `OPENAI_CHAT_MODEL`로 변경할 수 있다.

LLM이 반환해야 하는 구조:

```json
{
  "answer": "답변 본문",
  "citedDocumentIds": [573],
  "status": "answered"
}
```

근거가 부족할 때:

```json
{
  "answer": null,
  "citedDocumentIds": [],
  "status": "insufficient_evidence"
}
```

## Citation 검증

LLM이 `citedDocumentIds`를 반환해도 그대로 믿지 않는다. `answer_response.py`의 `validated_citations()`가 검색 결과 source 안에 실제 존재하는 document id만 남긴다.

검증 규칙:

1. 검색 source에 없는 document id는 제거한다.
2. 중복 document id는 한 번만 사용한다.
3. 답변에 실제 citation이 없으면 최종 응답은 실패로 처리한다.
4. 모델이 답변 본문에 직접 쓴 “근거는 ...” 문장은 제거한다.
5. 서버가 검증된 citation만 사용해 출처 문장을 다시 붙인다.

최종 출처 형식:

```text
근거는 민법의 제565조(해약금)를 참조했습니다.
```

## 최종 답변 DTO

챗봇 답변의 최종 응답은 `LegalAnswerResponse` 형태다.

```json
{
  "handler": "legal_contract",
  "success": true,
  "question": "계약금을 냈는데 계약을 취소할 수 있어?",
  "expandedTerms": ["해약금"],
  "answer": "매매계약에서 계약금을 준 경우, 다른 약정이 없고 아직 이행에 착수하기 전이라면 매수인은 계약금을 포기하고 계약을 해제할 수 있습니다.\n\n근거는 민법의 제565조(해약금)를 참조했습니다.",
  "answerStatus": "answered",
  "citations": [
    {
      "documentId": 573,
      "lawName": "민법",
      "articleNo": "제565조",
      "articleTitle": "해약금",
      "paragraphNo": "",
      "sourceUrl": "https://...",
      "effectiveDate": "2026-06-22"
    }
  ],
  "sources": [],
  "retrievalScore": 0.71,
  "reason": null,
  "message": "검색된 법령 근거를 바탕으로 답했습니다."
}
```

## 실패 응답

검색 근거 부족:

```json
{
  "handler": "legal_contract",
  "success": false,
  "question": "부동산 매매와 관련 없는 질문",
  "expandedTerms": [],
  "answer": null,
  "answerStatus": "insufficient_evidence",
  "citations": [],
  "sources": [],
  "retrievalScore": null,
  "reason": "no_legal_sources",
  "message": "답변을 생성할 충분한 법령 근거를 찾지 못했습니다."
}
```

LLM 생성 실패:

```json
{
  "handler": "legal_contract",
  "success": false,
  "question": "계약금을 냈는데 계약을 취소할 수 있어?",
  "expandedTerms": ["해약금"],
  "answer": null,
  "answerStatus": "generation_failed",
  "citations": [],
  "sources": [],
  "retrievalScore": 0.71,
  "reason": "generation_failed",
  "message": "법률 답변을 생성하지 못했습니다."
}
```

## API 단독 검색 예시

```powershell
Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:8000/api/laws/query `
  -ContentType "application/json" `
  -Body '{"question":"계약금을 냈는데 계약을 취소할 수 있어?","topK":7}'
```

이 호출은 검색 source만 반환한다. 자연어 답변 생성을 확인하려면 챗봇 API 또는 `outputs/legal_rag_qa_30_runner.py`를 사용한다.

## QA runner

실제 DB와 OpenAI API를 사용해 30개 질문 답변을 생성한다.

```powershell
python outputs/legal_rag_qa_30_runner.py
```

출력 파일:

```text
outputs/legal_rag_qa_30_questions_20260625.md
```

QA runner는 실제 품질 점검용이다. 자동 평가는 현재 주석 처리하거나 사용하지 않을 수 있으며, 최종 판단은 생성된 답변과 근거 조문의 적합성을 사람이 확인하는 방식이 더 정확하다.

## 검증

단위 테스트:

```powershell
python -m pytest tests/test_legal_rag_query_intent.py tests/test_legal_rag_query_ranking.py tests/test_legal_rag_answer.py
```

검증 관점:

1. intent 감지가 예상 질문에서 맞게 동작하는가
2. broad/risk/lease/tax 등 intent별 보정이 과도하지 않은가
3. 일반 질문에서 특수 조문이 지나치게 올라오지 않는가
4. LLM이 없는 citation을 반환해도 서버가 제거하는가
5. 검색 source가 없을 때 LLM을 호출하지 않는가
