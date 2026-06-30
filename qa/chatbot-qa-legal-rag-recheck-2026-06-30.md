# Chatbot Legal RAG 재검증 - 2026-06-30

이 문서는 `LC-001`부터 `LC-026`까지 법령 질문만 다시 확인한 결과다.

## 결론

- 기존 full QA에서 `LC-001`부터 `LC-026`까지 모두 `질문과 관련된 법령 근거를 찾지 못했습니다.`로 나온 결과는 현재 DB/embedding 상태에서는 재현되지 않았다.
- 현재 legal RAG 단독 경로는 26건 중 25건에서 법령 source를 반환한다.
- 실제 챗봇 경로도 대표 케이스 `LC-001`은 `specialist_tool -> legal_contract`로 정상 source 7건을 반환했다.
- 남은 실제 실패는 `LC-009` 1건이다.

## 실행 환경

| 항목 | 값 |
| --- | --- |
| server branch | `feat/supervisor-first-chatbot-routing` |
| DB | `postgresql+psycopg://home_search:home_search@127.0.0.1:55432/home_search` |
| `OPENAI_API_KEY` | `present` |
| chat model | `default` |
| embedding model | `default` |
| law documents | `3131` |
| embedded law documents | `3131` |
| embedding status | `EMBEDDED 3131건` |

## 실행 방식

먼저 `scripts/run_chatbot_qa.py`로 `LC-001`부터 `LC-026`까지 live LLM 재실행을 시도했다.

```bash
set -a
source .env
set +a

bash -lc 'args=(); for i in $(seq -f "%03g" 1 26); do args+=(--case "LC-$i"); done; /usr/bin/time -p .venv/bin/python scripts/run_chatbot_qa.py --from-docs --live-llm "${args[@]}" --suite-name chatbot-qa-legal-live-recheck --allow-failures'
```

이 경로는 15분 이상 출력 없이 대기해서 중단했다. runner가 결과를 마지막에 한 번에 쓰는 구조라 중간 케이스별 진행 상태는 확인하지 못했다.

이후 같은 `LC-*` 질문을 `legal_contract` RAG service에 직접 넣어 source 반환 여부를 확인했다.

## Legal RAG 단독 결과

| id | success | reason | sources | summary |
| --- | --- | --- | ---: | --- |
| `LC-001` | `true` | - | 7 | 부동산 거래신고 등에 관한 법률, 부동산등기규칙, 소득세법 시행령, 집합건물법 관련 조문 반환 |
| `LC-002` | `true` | - | 7 | 지방세법, 소득세법, 종합부동산세법 관련 조문 반환 |
| `LC-003` | `true` | - | 7 | 공인중개사법, 민법, 부동산등기규칙 관련 조문 반환 |
| `LC-004` | `true` | - | 7 | 공인중개사법, 부동산 거래신고 등에 관한 법률, 부동산등기법 관련 조문 반환 |
| `LC-005` | `true` | - | 7 | 부동산 거래신고 등에 관한 법률의 신고 관련 조문 반환 |
| `LC-006` | `true` | - | 7 | 주택임대차보호법, 민법, 부동산등기법 관련 조문 반환 |
| `LC-007` | `true` | - | 7 | 부동산등기규칙, 부동산등기법, 민법 관련 조문 반환 |
| `LC-008` | `true` | - | 6 | 공인중개사법, 민법 계약금/해약금 관련 조문 반환 |
| `LC-009` | `false` | `no_legal_sources` | 0 | `부모님이 돈을 보태주면 문제가 있어?` 질문은 source를 찾지 못함 |
| `LC-010` | `true` | - | 7 | 부동산등기법, 부동산등기규칙, 주택임대차보호법 관련 조문 반환 |
| `LC-011` | `true` | - | 7 | 민법 매매 성립 관련 조문 반환 |
| `LC-012` | `true` | - | 7 | 공인중개사법, 민법 계약금/담보책임 관련 조문 반환 |
| `LC-013` | `true` | - | 7 | 부동산 거래신고 등에 관한 법률 관련 조문 반환 |
| `LC-014` | `true` | - | 7 | 공인중개사법 거래계약서/거짓 기재 관련 조문 반환 |
| `LC-015` | `true` | - | 7 | 부동산등기법과 부동산등기규칙의 등기 가능 권리 관련 조문 반환 |
| `LC-016` | `true` | - | 7 | 소유권 이전등기 관련 부동산등기법/규칙 조문 반환 |
| `LC-017` | `true` | - | 7 | 토지거래허가구역 관련 부동산 거래신고 등에 관한 법률 조문 반환 |
| `LC-018` | `true` | - | 7 | 부동산 거래신고필증과 등기 관련 조문 반환 |
| `LC-019` | `true` | - | 7 | 민법 매매/계약금 관련 조문 반환 |
| `LC-020` | `true` | - | 7 | 집합건물법과 부동산등기 관련 조문 반환 |
| `LC-021` | `true` | - | 4 | 공인중개사법, 민법, 주택임대차보호법 관련 조문 반환 |
| `LC-022` | `true` | - | 7 | 민법 제565조, 공인중개사법 제31조 등 계약금 해제 관련 조문 반환 |
| `LC-023` | `true` | - | 7 | 계약 전 확인 사항 관련 공인중개사법/거래신고법 조문 반환 |
| `LC-024` | `true` | - | 7 | 거래가격 거짓 기재 관련 공인중개사법/거래신고법 조문 반환 |
| `LC-025` | `true` | - | 7 | 전세 낀 주택 매수와 보증금 관련 주택임대차보호법 조문 반환 |
| `LC-026` | `true` | - | 7 | 민법 매매, 공인중개사법, 거래신고법 관련 조문 반환 |

요약:

| total | ok | failed |
| ---: | ---: | ---: |
| 26 | 25 | 1 |

## 실제 챗봇 경로 대표 확인

### LC-001

| 항목 | 값 |
| --- | --- |
| question | `30억 아파트 매매 시 알아야 할 법률이 있을까?` |
| status | `success` |
| execution path | `specialist_tool` |
| handler | `legal_contract` |
| result success | `true` |
| sources | 7 |
| 판단 | 정상 |

### LC-009

| 항목 | 값 |
| --- | --- |
| question | `부모님이 돈을 보태주면 문제가 있어?` |
| status | `failed` |
| execution path | `direct_feature` |
| handler | `legal_contract` |
| result success | `false` |
| reason | `no_legal_sources` |
| sources | 0 |
| answer | `질문과 관련된 법령 근거를 찾지 못했습니다.` |
| 판단 | 실제 결함 |

## LC-009 원인

`LC-009`는 질문 의도가 `general`로만 잡힌다.

| 항목 | 값 |
| --- | --- |
| normalized question | `부모님이 돈을 보태주면 문제가 있어` |
| detected intents | `general` |
| matched mappings | 없음 |
| primary terms | `부모님`, `돈을`, `보태주면`, `문제`, `있어` |
| expanded terms | 없음 |
| top score | 약 `0.3539` |
| min score | `0.45` |

이 질문은 실제로 `증여`, `증여세`, `자금출처`, `특수관계인`, `부담부증여` 쪽으로 확장되어야 한다. 현재는 해당 intent/확장어가 없어 source threshold를 넘지 못한다.

## 판단

기존 full QA에서 법령 질문 전체가 실패한 것은 현재 상태 기준으로는 법령 RAG 자체의 전면 장애가 아니다. 당시 실행 환경의 DB/embedding 상태가 현재와 달랐거나, runner 실행 시점에 legal index가 준비되지 않았을 가능성이 높다.

다만 runner 판정도 여전히 보정이 필요하다. `legal_contract` positive case는 다음 조건을 모두 만족해야 PASS로 봐야 한다.

- `handler=legal_contract`
- `success=true`
- `sources.length >= 1`
- answer가 `질문과 관련된 법령 근거를 찾지 못했습니다.`가 아님

## 후속 수정 포인트

1. `부모님이 돈을 보태`, `가족이 돈을 지원`, `부모 자금 지원` 표현을 세금/증여 intent로 잡는다.
2. legal query expansion에 `증여`, `증여세`, `자금출처`, `특수관계인`, `부담부증여`를 추가한다.
3. QA runner에서 legal positive case의 `sources >= 1` 검사를 추가한다.
4. `scripts/run_chatbot_qa.py`는 장시간 실행 시 케이스별 진행 로그 또는 per-case flush를 남기도록 개선한다.
