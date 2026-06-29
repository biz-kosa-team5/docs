# Chatbot Live API QA 요약 - 2026-06-29

## 실행

- 실행 위치: `server`
- 실행 모드: `live_llm`
- 실행 명령:
  ```bash
  set -a; source .env; set +a
  .venv/bin/python scripts/run_chatbot_qa.py --from-docs --live-llm --suite-name chatbot-qa-docs-live-results --allow-failures
  ```
- API 키 값은 기록하지 않았고, `server/.env`의 `OPENAI_API_KEY` 존재 여부만 사용했다.

## 저장 결과

- Markdown: `docs/qa/chatbot-qa-docs-live-results-2026-06-29.md`
- JSONL: `docs/qa/chatbot-qa-docs-live-results-2026-06-29.jsonl`

## 결과 요약

- total: 144
- passed: 143
- failed: 1
- actual status 분포:
  - success: 94
  - partial_success: 7
  - failed: 43

## 실패 케이스

- `MX-LLM-002`
  - question: `송파구 30억 이하 추천하고 추천 후보들 가격 흐름도 알려줘`
  - expected path: `supervisor_aggregate`
  - actual path: `supervisor_execution_failed`
  - expected handlers: `recommendation`, `price_trend`
  - actual handlers: 없음
  - answer: `질문 처리 중 오류가 발생했습니다. 잠시 후 다시 시도해 주세요.`

## API 실행 중 관찰된 외부 오류

- OpenAI API 호출 중 `insufficient_quota` 429 오류가 발생했다.
- answer composer는 quota 오류 이후 fallback 답변으로 계속 진행했다.
- supervisor fallback이 필요한 케이스에서도 같은 quota 오류로 `supervisor_execution_failed`가 발생했다.
- 법령 RAG는 질문 embedding 생성이 필요하지만, API quota 문제로 `embedding_unavailable` 결과가 반복 발생했다.

## payload reason 분포

- `embedding_unavailable`: 70
- `no_matching_tool`: 12
- `poi_not_found`: 2
- `missing_apartment_names`: 2
- `agent_execution_failed`: 2
- `target_not_found`: 2

## 해석

- 이번 실행은 실제 `OPENAI_API_KEY`를 로드한 live 모드 실행이다.
- 다만 계정 quota 부족으로 인해 LLM 답변 생성, supervisor 실행, 법령 embedding 생성이 정상적으로 끝까지 검증되지는 못했다.
- 따라서 이 결과는 "실제 API 호출 경로가 시도되었고 quota 오류 상황에서도 fallback/결과 저장은 동작한다"는 검증으로 해석해야 한다.
- live LLM 품질과 법령 RAG 품질을 정확히 보려면 사용 가능한 quota가 있는 키로 재실행해야 한다.
