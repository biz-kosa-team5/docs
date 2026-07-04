# Chatbot Live API QA 요약 - 2026-06-30

## 실행

- 실행 위치: `server`
- 실행 모드: `live_llm`
- 실행 기준:
  - server branch: `feat/supervisor-first-chatbot-routing`
  - server base: `origin/main` `35aaef0`
  - docs branch: `docs/chatbot-qa-preflight-checks`
  - docs base: `origin/main` `64b0c17`
- API 키 값은 기록하지 않았고, `server/.env`의 `OPENAI_API_KEY=present`만 확인했다.
- chat model: `default`
- embedding model: `default`

## 실행 명령

```bash
set -a; source .env; set +a

/usr/bin/time -p .venv/bin/python scripts/run_chatbot_qa.py \
  --from-docs \
  --live-llm \
  --case SL-002 \
  --case RC-021 \
  --case CP-008 \
  --case UB-001 \
  --suite-name chatbot-qa-smoke-live \
  --allow-failures

/usr/bin/time -p .venv/bin/python scripts/run_chatbot_qa.py \
  --from-docs \
  --live-llm \
  --suite-name chatbot-qa-docs-live-results \
  --allow-failures
```

## 저장 결과

- Smoke Markdown: `docs/qa/chatbot-qa-smoke-live-2026-06-30.md`
- Smoke JSONL: `docs/qa/chatbot-qa-smoke-live-2026-06-30.jsonl`
- Full Markdown: `docs/qa/chatbot-qa-docs-live-results-2026-06-30.md`
- Full JSONL: `docs/qa/chatbot-qa-docs-live-results-2026-06-30.jsonl`
- 사용자 관점 실패 묶음: `docs/qa/chatbot-qa-failure-triage-2026-06-30.md`

## 결과 요약

| 구분 | 값 |
| --- | --- |
| smoke total/pass/fail | `4 / 4 / 0` |
| smoke elapsed | `64.53s` |
| full total/pass/fail | `144 / 132 / 12` |
| full elapsed | `2054.32s` |
| answer ok | `144 / 144` |
| nested answer absent | `144 / 144` |
| token check | `not_captured 144건` |

주의:

- 위 PASS/FAIL은 runner의 기계 판정이다.
- 일부 케이스는 `answer_ok=true`로 PASS 처리됐지만 추천 후보 없음, 비교 데이터 부족, RAG 근거 없음처럼 사용자 관점에서는 실패다.
- 사용자 관점 실패는 `chatbot-qa-failure-triage-2026-06-30.md`에 별도 분류했다.

## 실행 경로 분포

| actual path | count |
| --- | ---: |
| `specialist_tool` | 103 |
| `supervisor_aggregate` | 20 |
| `direct_feature` | 13 |
| `direct_no_matching_tool` | 5 |
| `specialist_tool,specialist_tool` | 1 |
| `specialist_tool,direct_no_matching_tool` | 1 |
| `direct_independent_features` | 1 |

해석:

- `specialist_tool` 또는 `supervisor_aggregate`가 포함된 케이스는 125건이다.
- `direct_feature`, `direct_no_matching_tool`, `direct_independent_features`만 남은 케이스는 19건이다.
- direct 계열 케이스는 정상 경로가 아니라 supervisor 실패 또는 no tool 이후 fallback으로 기록됐다.

## Fallback 분포

| fallback path / reason | count |
| --- | ---: |
| `direct_feature` / `specialist_tool` | 9 |
| `direct_feature` / `supervisor_no_tool` | 4 |
| `direct_no_matching_tool` / `supervisor_no_tool` | 4 |
| `direct_no_matching_tool` / `specialist_tool` | 2 |
| `direct_independent_features` / `supervisor_execution_failed` | 1 |

## 실패 12건 분류

| 분류 | 케이스 | 판단 |
| --- | --- | --- |
| 시세/가격 애매 질문에서 `price_trend` 미호출 | `SL-009`, `SL-010`, `SL-017`, `SL-021`, `MX-AM-001` | `simple_lookup`만 호출되어 최근 거래 답변은 가능했지만, 기대한 추이 tool이 빠졌다. |
| 비교 질문에서 `comparison` 미호출 | `CP-010`, `CP-012`, `CP-013` | 더 신축/초등학교/역 거리 비교 질문을 `simple_lookup` 중심으로 처리했다. |
| 비교 질문에서 추가 tool 포함 | `CP-017` | `comparison`은 호출됐지만 `simple_lookup`도 함께 잡혀 runner 기준 실패가 됐다. 답변 자체는 비교를 수행했다. |
| 하락률 ranking 질문 오해 | `PT-011` | `price_trend`는 호출됐지만 `simple_lookup`도 함께 호출됐고, 답변이 "많이 내린"보다 일반 시세 흐름으로 기울었다. |
| 추천 후 후보 비교 누락 | `MX-DP-001` | recommendation만 결과화되고 comparison 결과가 빠졌다. |
| 같은 tool 다중 지역 판단 | `MX-ST-001` | payload에는 강남구/송파구 `price_trend` 결과가 모두 있으나 runner의 handler 수집이 중복 handler를 하나로 축약해 실패 처리했다. |

## PASS지만 사용자 관점 실패

| 분류 | 케이스 | 판단 |
| --- | --- | --- |
| 추천 후보 없음 | `RC-013` | `초등학교 근처 아파트 추천해줘`에 후보 없이 조건 완화 안내만 반환했다. |
| 비교 데이터 부족 | `CP-002` | `압구정현대`를 찾지 못해 비교 결과가 없는데 runner는 PASS 처리했다. |
| 비교 대상 파싱 흔들림 | `CP-003` | 사용자 확인 결과에서 `두산위브 거리`처럼 metric 표현이 단지명에 붙어 비교 실패가 발생했다. |
| 법령 RAG 근거 없음 | `LC-*`, `RV-017`, `RV-018` 일부 | `legal_contract` tool은 호출됐지만 source를 못 찾아 답을 제공하지 못했다. |

상세 목록과 보정 기준은 `docs/qa/chatbot-qa-failure-triage-2026-06-30.md`를 기준으로 본다.

## 품질 관찰

- 긍정적:
  - 명확한 단일 도메인 질문은 대부분 `specialist_tool`로 들어갔다.
  - 추천, 비교, 시세, 법령, 단순 조회 모두 live LLM에서 실제 tool wrapper를 호출했다.
  - 최종 answer는 자동 검증 기준상 내부 JSON, 중첩 answer, 금지 내부 용어를 노출하지 않았다.
  - unsupported 질문은 `no_matching_tool`로 정리됐다.

- 남은 문제:
  - multi-tool이 필요한 애매한 질문에서 supervisor가 하나의 specialist만 고르는 경우가 있다.
  - 비교 의도가 강한 질문 일부가 lookup으로 우회된다.
  - answer 일부에서 `deal_amount` 단위가 `만원`인데 `원`처럼 표현되는 사례가 있었다. 예: `440,000만원`을 `440,000,000원`처럼 쓰는 패턴.
  - token usage는 payload에 노출되지 않아 runner가 `not_captured`로 기록했다.
  - full run 중 OpenAI `APIConnectionError`가 1회 발생했고, 해당 케이스는 direct fallback으로 partial success 처리됐다.

## 결론

이번 live QA 기준으로 "LLM supervisor가 tool을 호출하는가"는 대체로 충족한다. 다만 "질문이 요구하는 모든 tool을 빠짐없이 호출하는가"는 아직 1차 개선이 필요하다.

우선순위는 다음과 같다.

1. ambiguous price 질문에서 `simple_lookup + price_trend`를 함께 호출하도록 supervisor prompt 또는 routing hint를 강화한다.
2. 비교 의도 표현(`더 신축`, `초등학교에 가까워`, `역이 더 가까워`)은 `comparison_agent`를 우선 호출하게 한다.
3. 추천 후 후보 비교는 recommendation 결과만으로 끝내지 말고 comparison까지 실행되도록 dependent intent를 강화한다.
4. answer composer에서 가격 단위 `만원`을 원 단위로 오해하지 않도록 관찰값 요약/후처리 규칙을 추가한다.
5. LangChain/OpenAI token usage metadata를 QA runner가 수집할 수 있게 service 또는 runner side channel을 추가한다.
