# 법령 RAG 데이터 적재

이 문서는 국가법령 API를 통해 법령 원문과 일상어-법률어 매핑 데이터를 수집하고, RAG 검색에 사용할 DB 테이블로 정리하는 과정을 설명한다.

## 목적

데이터 적재 단계의 목표는 다음 세 가지다.

1. 국가법령 API 응답 원본을 `raw_api_responses`에 보존한다.
2. 법령 본문을 조문/항 단위의 `law_documents`로 파싱한다.
3. 일상어-법률어 매핑을 `daily_legal_term_mappings`에 저장한다.

원본과 파싱 결과를 분리한 이유는 재파싱과 오류 추적을 쉽게 하기 위해서다. API 응답 구조가 바뀌거나 파서 규칙을 수정해도 Raw 데이터를 다시 호출하지 않고 재처리할 수 있다.

## 관련 파일

| 파일 | 책임 |
|---|---|
| `rag/controller/ingestion_controller.py` | 적재/파싱 API 엔드포인트 |
| `rag/service/ingestion_service.py` | Raw 수집, 파싱, 저장 흐름 |
| `rag/client/law_api_client.py` | 국가법령 API 호출 |
| `rag/parser/law_parser.py` | 법령 본문 Raw JSON을 `ParsedDocument`로 변환 |
| `rag/parser/term_mapping_parser.py` | 일상어-법률어 Raw JSON을 `ParsedTermMapping`으로 변환 |
| `rag/dao/ingestion_dao.py` | Raw, 조문, 매핑 저장 및 조회 |
| `rag/model/entities.py` | `raw_api_responses`, `law_documents`, `daily_legal_term_mappings` 모델 |
| `rag/constants.py` | 기본 수집 대상 법령명과 용어 목록 |
| `db/init/01_schema.sql` | PostgreSQL 스키마 |
| `db/init/02_seed.sql` | CSV seed 적재 |
| `db/import/law_documents.csv` | 다른 로컬 환경으로 이관할 조문/임베딩 CSV |
| `db/import/daily_legal_term_mappings.csv` | 다른 로컬 환경으로 이관할 용어 매핑 CSV |

## 테이블 구조

### raw_api_responses

국가법령 API 응답 원본을 저장한다.

| 컬럼 | 설명 |
|---|---|
| `source_type` | `law_api` 또는 `term_mapping_api` |
| `target` | `eflaw`, `dlytrmRlt` 등 API target |
| `query` | 요청에 사용한 법령명 또는 일상어 |
| `request_url` | API 요청 URL. `OC` 값은 `***`로 마스킹된다. |
| `response_json` | API 응답 원본 JSON |
| `status` | `SUCCESS`, `FAILED`, `PARSED`, `PARSE_FAILED`, `SKIPPED` |
| `error_message` | 수집 또는 파싱 실패 메시지 |

### law_documents

파싱된 법령 조문을 저장한다.

| 컬럼 | 설명 |
|---|---|
| `law_id`, `mst` | 국가법령 API의 법령 식별자 |
| `law_name` | 법령명 |
| `article_no` | 조문 번호 |
| `article_title` | 조문 제목 |
| `paragraph_no` | 항 번호. 조문 전체는 빈 문자열이다. |
| `doc_type` | `article` 또는 `paragraph` |
| `parent_document_id` | 항 문서가 상위 조문을 참조할 때 사용 |
| `content` | 검색과 답변 근거에 사용하는 조문 본문 |
| `metadata` | 법령 ID, 법령명, 가지번호 등 보조 메타데이터 |
| `source_url` | 국가법령 API 원문 URL |
| `effective_date` | 시행일 |
| `embedding` | 임베딩 벡터. 임베딩 단계에서 채워진다. |
| `embedding_status` | `PENDING`, `EMBEDDED`, `FAILED` |

`law_id`, `effective_date`, `article_no`, `paragraph_no` 조합은 unique 제약을 가진다. 같은 조문을 다시 파싱하면 새 행을 늘리지 않고 기존 행을 갱신한다.

### daily_legal_term_mappings

사용자가 입력하는 일상어를 법률 검색어로 확장하기 위한 매핑 테이블이다.

| 컬럼 | 설명 |
|---|---|
| `daily_term` | 사용자가 쓰는 표현 |
| `legal_term` | 검색에 추가할 법률 표현 |
| `relation_type` | `SYNONYM`, `BROADER`, `NARROWER`, `RELATED` |
| `domain` | 현재는 `apartment_sale` |
| `priority` | 매핑 우선순위 |
| `raw_data` | API 원본 관계 정보 또는 수동 정제 출처 |

## API 흐름

### 1. 법령 Raw 수집

Endpoint:

```http
POST /api/laws/ingest/raw
```

Request DTO:

```json
{
  "keywords": ["민법", "부동산등기법"]
}
```

`keywords`를 생략하면 `rag/constants.py`의 `LAW_NAMES`를 사용한다.

처리 순서:

1. `LawCollectionService.ingest()`가 수집 대상 법령명을 중복 제거한다.
2. `LawApiClient.search_laws()`로 법령 목록을 조회한다.
3. `LawApiClient.select_current_candidate()`가 현재 시행 중인 정확한 법령 후보를 고른다.
4. 선택된 `MST`로 `LawApiClient.get_law_body()`를 호출한다.
5. 응답 원본을 `raw_api_responses`에 `source_type='law_api'`, `target='eflaw'`로 저장한다.
6. 실패하면 같은 테이블에 `status='FAILED'`와 오류 메시지를 저장한다.

Response:

```json
{
  "processed": 2,
  "succeeded": 2,
  "failed": 0
}
```

### 2. 법령 조문 파싱

Endpoint:

```http
POST /api/laws/parse
```

Request DTO:

```json
{
  "rawIds": [1, 2]
}
```

`rawIds`를 생략하면 `SUCCESS` 또는 `PARSE_FAILED` 상태의 법령 Raw를 대상으로 한다.

처리 순서:

1. `LawParsingService.parse()`가 대상 Raw를 조회한다.
2. `parse_law()`가 법령 메타데이터와 조문 목록을 찾는다.
3. 삭제 조문, 부칙 등 RAG 근거로 부적합한 항목은 제외한다.
4. 조문 본문이 긴 경우 항 단위 문서를 추가 생성한다.
5. `LegalDataDao.upsert_document()`가 `law_documents`에 upsert한다.
6. 항 단위 문서는 상위 조문 문서의 `id`를 `parent_document_id`로 가진다.
7. 성공한 Raw는 `PARSED`, 실패한 Raw는 `PARSE_FAILED`로 갱신한다.

Response:

```json
{
  "processed": 2,
  "succeeded": 2,
  "failed": 0,
  "documents_saved": 120
}
```

### 3. 용어 Raw 수집

Endpoint:

```http
POST /api/terms/ingest/raw
```

Request DTO:

```json
{
  "keywords": ["집", "계약금", "명의 이전"]
}
```

`keywords`를 생략하면 `rag/constants.py`의 `DAILY_TERMS`를 사용한다.

처리 순서:

1. `TermMappingCollectionService.ingest_raw()`가 수집 대상 일상어를 중복 제거한다.
2. `LawApiClient.get_term_mappings()`가 `target=dlytrmRlt` API를 호출한다.
3. 응답 원본을 `raw_api_responses`에 `source_type='term_mapping_api'`, `target='dlytrmRlt'`로 저장한다.

### 4. 용어 매핑 파싱

Endpoint:

```http
POST /api/terms/parse
```

Request DTO:

```json
{
  "rawIds": [10, 11]
}
```

처리 순서:

1. `TermMappingParsingService.parse()`가 대상 Raw를 조회한다.
2. `parse_term_mappings()`가 일상어와 연결 법률어를 추출한다.
3. 관계명은 `SYNONYM`, `BROADER`, `NARROWER`, `RELATED`와 priority로 변환한다.
4. `LegalDataDao.upsert_mapping()`이 `daily_legal_term_mappings`에 저장한다.
5. 저장된 매핑은 `domain='apartment_sale'`로 관리한다.

## 조회 API

운영 중 상태 확인을 위해 다음 조회 API가 제공된다.

| Method | Path | 설명 |
|---|---|---|
| GET | `/api/laws/raw` | 법령 Raw 목록 조회 |
| GET | `/api/terms/raw` | 용어 Raw 목록 조회 |
| GET | `/api/laws/documents` | 파싱된 조문 목록 조회 |
| GET | `/api/terms/mappings` | 일상어-법률어 매핑 목록 조회 |

## CSV 이관 구조

현재 로컬에서 적재한 데이터를 다른 로컬 DB에 바로 반영할 수 있도록 `db/import`에 CSV가 있다.

```text
db/import/law_documents.csv
db/import/daily_legal_term_mappings.csv
db/import/raw_api_responses.csv
```

Docker PostgreSQL 초기화 시 `db/init/02_seed.sql`이 다음 순서로 RAG 데이터를 적재한다.

1. `law_documents`, `daily_legal_term_mappings`, `raw_api_responses`를 truncate한다.
2. `daily_legal_term_mappings.csv`를 COPY 한다.
3. `raw_api_responses.csv`를 COPY 한다.
4. `law_documents.csv`를 COPY 한다.
5. 각 테이블의 sequence를 CSV의 최대 id 기준으로 재설정한다.
6. `ANALYZE`를 실행한다.

`law_documents.csv`에는 임베딩 벡터까지 포함되어 있으므로, 같은 CSV를 사용하면 다른 로컬에서도 즉시 pgvector 검색을 사용할 수 있다.

## 실행 예시

서버 실행:

```powershell
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

법령 Raw 수집:

```powershell
Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:8000/api/laws/ingest/raw `
  -ContentType "application/json" `
  -Body '{"keywords":["민법","부동산등기법"]}'
```

법령 파싱:

```powershell
Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:8000/api/laws/parse `
  -ContentType "application/json" `
  -Body '{}'
```

용어 Raw 수집:

```powershell
Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:8000/api/terms/ingest/raw `
  -ContentType "application/json" `
  -Body '{"keywords":["집","계약금","세입자"]}'
```

용어 파싱:

```powershell
Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:8000/api/terms/parse `
  -ContentType "application/json" `
  -Body '{}'
```

## 실패 처리

| 실패 지점 | 처리 방식 |
|---|---|
| `LAW_API_OC` 없음 | `LawApiClient`가 `ValueError` 발생 |
| API 호출 실패 | Raw 행을 `FAILED`로 저장 |
| 필수 법령 메타데이터 없음 | 파싱 실패 후 Raw 행을 `PARSE_FAILED`로 갱신 |
| 상위 조문 없이 항 문서 저장 | `Parent article not found` 오류 |
| 용어 매핑 없음 | Raw 행을 `SKIPPED`로 처리 가능 |

## 검증

파서 테스트:

```powershell
python -m pytest tests/test_legal_rag_parser.py
```

DB 적재 상태 확인:

```sql
SELECT status, count(*) FROM raw_api_responses GROUP BY status ORDER BY status;
SELECT parse_status, count(*) FROM law_documents GROUP BY parse_status ORDER BY parse_status;
SELECT count(*) FROM daily_legal_term_mappings;
```
