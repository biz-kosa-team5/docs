# Chatbot QA Preflight Checklist

이 문서는 `docs/data/chatbot-questionnaire.md` 전체 질문을 실행하기 전에 먼저 채워야 하는 필수 점검 항목이다. 질문별 QA 결과를 만들기 전에 이 문서의 값이 비어 있으면 live LLM 결과를 품질 판단 근거로 쓰지 않는다.

## 적용 범위

- 대상 API: `POST /api/v1/chatbot/query`
- 대상 실행: `server/scripts/run_chatbot_qa.py --from-docs`
- 대상 모드:
  - deterministic QA: OpenAI 호출을 비활성화하고 handler/formatter 회귀를 확인한다.
  - live LLM QA: 실제 `OPENAI_API_KEY`로 supervisor routing, specialist tool 호출, answer composer를 확인한다.
- 현재 라우팅 기준:
  - 정상 데이터 기반 성공 답변은 `LLM supervisor -> specialist @tool -> handler/service -> JSON result -> answer composer` 흐름이어야 한다.
  - `direct_feature` 계열 실행은 supervisor가 tool을 고르지 못했거나 supervisor 초기화/실행 장애가 난 경우의 fallback으로만 허용한다.

## 실행 전 필수 체크

| 구분 | 필수 기록 | 확인 방법 | 통과 기준 |
| --- | --- | --- | --- |
| 시간 | 시작 시각, 종료 시각, timezone, 총 소요시간 | `date '+%Y-%m-%d %H:%M:%S %Z %z'`, `/usr/bin/time -p ...` | KST 기준 시각이 기록되어 있고 총 소요시간이 남아 있어야 한다. |
| 토큰/비용 | 실행 전 quota 상태, 실행 전후 token/cost 변화, token budget | OpenAI dashboard 또는 provider usage 화면 | full run 전에 사용 가능한 quota가 확인되어야 한다. quota를 확인하지 못하면 대표 smoke만 실행한다. |
| API 키 | `OPENAI_API_KEY` 존재 여부만 기록 | `test -n "$OPENAI_API_KEY"` | 키 값은 절대 문서에 쓰지 않고 `present`/`missing`만 기록한다. |
| 모델 | chat model, embedding model | `.env`, runtime env | 기본값 사용 여부 또는 명시 모델명이 기록되어야 한다. |
| Git 상태 | server/docs/web branch, commit, dirty 여부 | `git status --short --branch`, `git rev-parse --short HEAD` | 어떤 코드로 실행했는지 재현 가능해야 한다. dirty 상태면 변경 파일 목록을 기록한다. |
| DB 상태 | DB URL, migration/seed 상태, row count smoke | server env, DB smoke query | marker/complex/trade 조회가 가능한 상태여야 한다. |
| 서버 상태 | backend health, API base URL, frontend 필요 여부 | health endpoint 또는 API smoke | `/api/v1/chatbot/query`가 대표 질문 1개에 200을 반환해야 한다. |
| 라우팅 기준 | expected path policy | 이 문서의 "판정 기준" | supervisor-first 기준으로 결과를 해석해야 한다. |
| 결과 저장 | markdown/jsonl 출력 경로 | runner 출력 | `docs/qa/*-YYYY-MM-DD.md`, `docs/qa/*-YYYY-MM-DD.jsonl`이 생성되어야 한다. |

## 결과 테이블 필수 컬럼

`scripts/run_chatbot_qa.py`가 생성하는 markdown 결과의 `Results` 표는 아래 정보를 한 행에서 확인할 수 있어야 한다.

| 컬럼 | 의미 |
| --- | --- |
| `id` | 질문지 케이스 ID |
| `package` | QA 분류 |
| `status` | PASS/FAIL |
| `expected path` | 기대 실행 경로 |
| `actual path` | 실제 실행 경로 |
| `expected handlers` | 기대 handler |
| `actual handlers` | 실제 handler |
| `elapsed ms` | 케이스별 처리 시간 |
| `token check` | payload에서 수집 가능한 token usage. 없으면 `not_captured` |
| `answer ok` | answer 계약 검증 여부 |
| `nested answer absent` | 중첩 answer 제거 여부 |
| `answer` | 최종 answer 전문 |
| `notes` | 실패/제약/known gap |

## 권장 실행 순서

1. 환경을 로드한다.

```bash
cd server
set -a
source .env
set +a
```

2. API 키와 모델 정보를 값 없이 확인한다.

```bash
if [ -n "$OPENAI_API_KEY" ]; then echo "OPENAI_API_KEY=present"; else echo "OPENAI_API_KEY=missing"; fi
echo "OPENAI_CHAT_MODEL=${OPENAI_CHAT_MODEL:-default}"
echo "OPENAI_EMBEDDING_MODEL=${OPENAI_EMBEDDING_MODEL:-default}"
```

3. 전체 QA 전에 단위 테스트를 먼저 통과시킨다.

```bash
.venv/bin/pytest -q
```

4. live LLM full run 전에 대표 smoke만 먼저 실행한다.

```bash
.venv/bin/python scripts/run_chatbot_qa.py \
  --from-docs \
  --live-llm \
  --case SL-002 \
  --case RC-021 \
  --case CP-008 \
  --case UB-001 \
  --suite-name chatbot-qa-smoke-live \
  --allow-failures
```

5. smoke 결과에서 quota, routing, answer contract가 정상일 때만 전체 질문을 실행한다.

```bash
/usr/bin/time -p .venv/bin/python scripts/run_chatbot_qa.py \
  --from-docs \
  --live-llm \
  --suite-name chatbot-qa-docs-live-results \
  --allow-failures
```

## 판정 기준

| 항목 | 통과 | 실패 또는 별도 분석 |
| --- | --- | --- |
| 정상 tool routing | `specialist_tool`, `supervisor_aggregate` | 명확한 지원 질문인데 `supervisor_no_tool` 후 direct fallback만 성공 |
| direct fallback | `fallbackFrom=supervisor`와 `fallbackReason`이 함께 기록됨 | `fallbackFrom` 없이 `direct_feature`만 기록됨 |
| 지원 범위 밖 질문 | `no_matching_tool`, `failed` | 부동산 데이터가 아닌데 임의 답변 생성 |
| 조회 실패 | 선택된 handler의 domain failure로 설명됨 | 다른 handler로 무의미하게 재시도해서 성공처럼 보임 |
| 답변 품질 | 좌표, JSON, 내부 용어 없이 자연어 answer | `handler`, `agent`, `tool`, `execution`, 좌표 노출 |
| UI payload | 지도/그래프가 필요한 질문에 `uiActions`/`uiArtifacts` 생성 | answer에 UI JSON이 섞이거나 action 누락 |
| 법령 RAG | embedding 가능 시 source 기반 답변 | quota/embedding 실패를 성공 답변처럼 표현 |

## 중단 조건

- `OPENAI_API_KEY=missing`이면 live LLM full run을 실행하지 않는다.
- OpenAI quota 또는 token budget을 확인하지 못하면 full run 대신 smoke만 실행한다.
- smoke에서 `insufficient_quota`, `rate_limit`, `authentication` 오류가 나오면 full run을 중단한다.
- `.venv/bin/pytest -q`가 실패하면 full run을 중단한다.
- marker/complex/trade DB smoke가 실패하면 full run을 중단한다.
- answer에 내부 용어 또는 좌표가 노출되면 full run 결과를 품질 통과로 보지 않는다.

## Preflight 기록 템플릿

| 항목 | 값 |
| --- | --- |
| run id | `YYYY-MM-DD-live-llm-001` |
| 점검자 |  |
| 시작 시각 |  |
| 종료 시각 |  |
| timezone |  |
| 실행 모드 | `deterministic` / `live_llm` |
| server branch / commit |  |
| server dirty files |  |
| docs branch / commit |  |
| web branch / commit |  |
| API base URL |  |
| `OPENAI_API_KEY` | `present` / `missing` |
| chat model |  |
| embedding model |  |
| token budget |  |
| 실행 전 token/cost |  |
| 실행 후 token/cost |  |
| 총 소요시간 |  |
| pytest 결과 |  |
| smoke QA 결과 |  |
| full QA 실행 여부 |  |
| 결과 markdown |  |
| 결과 jsonl |  |
| 주요 실패/제약 |  |

## 2026-06-30 준비 기록

| 항목 | 값 |
| --- | --- |
| run id | `2026-06-30-supervisor-first-preflight` |
| 점검 시각 | `2026-06-30 09:41:17 KST +0900` |
| 실행 모드 | live LLM QA 준비 |
| server branch / commit | `feat/supervisor-first-chatbot-routing` / `ce80e6a` |
| server dirty files | `app/chatbot/service/chatbot_service.py`, `tests/test_chatbot_service.py` |
| docs branch / commit | `docs/chatbot-qa-preflight-checks` / `64b0c17` |
| docs dirty files | `README.md`, `docs-directory-structure.md`, `qa/chatbot-qa-preflight-checklist.md` |
| web branch / commit | `main` / `b2e7a18` |
| `OPENAI_API_KEY` | `present` |
| chat model | `default` |
| embedding model | `default` |
| token/quota 확인 | API key present. provider usage 화면의 전후 token/cost는 미확인 |
| pytest 결과 | server branch에서 `261 passed`, 기존 deprecation warning 1건 |
| smoke QA 결과 | `4 passed / 0 failed`, `64.53s` |
| full QA 실행 여부 | 실행 완료. `144 total / 132 passed / 12 failed`, `2054.32s` |
| 결과 markdown | `docs/qa/chatbot-qa-docs-live-results-2026-06-30.md` |
| 결과 jsonl | `docs/qa/chatbot-qa-docs-live-results-2026-06-30.jsonl` |
| 주요 실패/제약 | multi-tool 누락, 비교 intent 일부 lookup 우회, 가격 단위 표현 오류, token usage `not_captured` |
| 비고 | supervisor-first 변경 이후 기존 2026-06-29 QA의 `direct_feature` 기대값은 최신 판정 기준으로 그대로 쓰면 안 된다. |

## 현재 runner 한계

- `scripts/run_chatbot_qa.py`는 case별 `elapsed ms`와 answer 전문을 markdown/jsonl에 저장한다.
- token usage는 payload 안에 `usage`, `usage_metadata`, `token_usage`, `tokenUsage`가 있을 때만 자동 표기한다.
- 현재 챗봇 응답 payload에는 provider token usage가 없을 수 있으므로, 이 경우 `token check=not_captured`가 정상이다.
- live full run 문서에는 `/usr/bin/time` 총 소요시간과 OpenAI usage 화면 기준 token/cost 전후 차이를 여전히 수동으로 기록한다.
- case별 정확한 token을 자동화하려면 LangChain/OpenAI 응답 metadata를 service payload 또는 QA runner side channel로 전달해야 한다.
