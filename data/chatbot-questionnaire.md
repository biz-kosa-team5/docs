# Chatbot 질문지

이 문서는 `docs`와 `server/tests`에 흩어진 챗봇 테스트 질문을 기능별 질문지로 모은 것이다.

수집 기준:

- 문서의 `질문`, `질문 예시`, `예시 질문`, `테스트 질문` 블록
- `server/tests`에 있는 실제 사용자 질문 문자열
- 설명문, URL, 응답 문장, 내부 message 문구는 제외
- 중복 문장은 하나로 합치되, 표현이 조금 다른 질문은 별도 케이스로 유지

사용 방법:

- `expected_handler`는 기대 라우팅 기준이다.
- `check`는 수동 QA 때 성공/실패를 표시하는 칸이다.
- 복합 질문은 fragment 분할과 다중 handler 호출 여부를 함께 본다.
- 자동/회귀 QA 기본 질문은 `server/tests/fixtures/import/complexes.csv` 또는 `server/db/import/complexes.csv`에서 확인되는 강남 3구 단지명을 우선 사용한다.
- 문서 원본에만 있고 `server/tests/fixtures`에 없는 단지명은 기본 실행 세트에서 제외하거나 검증 가능한 단지명으로 치환한다.
- `missingApartmentNames` 검증이 목적인 문서 케이스는 성공 비교 케이스와 구분해서 표시한다.
- 띄어쓰기, 붙여쓰기, 어순, 유사 표현 검증은 `8. 표현 변형 / Robustness` 섹션에서 별도로 본다.

## 0. 질문 품질과 검증 기준

질문 품질은 단순히 문장이 자연스러운지보다 아래 항목을 기준으로 본다.

- 데이터 검증성: 단지, 지역, 역, 학교 조건이 현재 fixture 또는 import 데이터에서 확인 가능해야 한다.
- 의도 명확성: 한 질문이 한 handler로 명확히 떨어지는지, 복합 질문이면 기대 fragment가 명확해야 한다.
- 실패 의도 분리: `missingApartmentNames`, `no_matching_tool`, 범위 외 지역처럼 실패가 목적이면 성공 케이스와 구분해서 표시한다.
- 표현 다양성: 띄어쓰기, 붙여쓰기, 단위 표현, 어순, 유사어가 바뀌어도 같은 대상과 같은 handler로 가는지 별도 검증한다.
- 답변 검증성: 최종 `answer`가 비어 있지 않고, 관측 JSON에 있는 값만 근거로 쓰며, 내부 필드나 nested `answer`를 노출하지 않아야 한다.

테스트는 최종적으로 아래 세 가지를 모두 확인해야 한다.

1. 어떤 실행 경로(`execution_path`)로 처리됐는가.
2. 실행 경로가 `direct_feature`이면 어떤 feature service가 직접 실행됐는가.
3. 실행 경로가 agent/tool 기반이면 어떤 specialist agent 또는 tool을 골랐는가.
4. 결과의 `handler`, `success/status`, 핵심 observation이 기대와 맞는가.
5. 사용자에게 반환된 최상위 `answer`가 기대 정보와 제약을 만족하는가.

public API 응답이 selected tool을 직접 노출하지 않는 경우에는 테스트에서 supervisor/tool/service를 spy 또는 monkeypatch해서 실행 경로를 기록한다. API contract 검증에서는 최상위 `answer`와 public response shape를 확인하고, 라우팅 검증에서는 `execution_path`, service, agent/tool 선택을 별도 단위 테스트로 확인한다.

`expected_tool`은 `expected_execution_path`가 `specialist_tool`, `supervisor_aggregate`, `fragmented`일 때만 필수 검증값이다. `direct_feature` 경로에서는 `expected_service`를 검증한다.

| expected_handler | expected_execution_path | expected_agent | expected_tool | expected_service | result 검증 | answer 검증 |
|---|---|---|---|---|---|---|
| simple_lookup | specialist_tool | lookup_agent | simple_lookup | run_simple_lookup | `handler=simple_lookup`, `query_type`, `criteria`, 조회 데이터 | 단지명, 주소/거래/가격 등 질문 의도에 맞는 핵심값 포함 |
| recommendation | direct_feature 또는 specialist_tool | recommendation_agent | recommend_apartments | run_recommendation | `handler=recommendation`, `criteria`, `results`, 실패 시 `reason` | 후보명, 가격, 역/학교/생활편의 등 observation 기반 정보 포함 |
| comparison | direct_feature 또는 specialist_tool | comparison_agent | compare_apartments | run_comparison | `handler=comparison`, `criteria.apartment_names`, `results`, `missingApartmentNames` | 비교 대상별 가격/세대수/연식/인프라를 기준별로 설명 |
| price_trend | specialist_tool | price_trend_agent | analyze_price_trend | run_price_trend | `handler=price_trend`, `query_type/analysis_type`, 기간/순위 데이터 | 추이/순위 수치와 기간을 포함하고 단순조회 답변처럼 쓰지 않음 |
| legal_contract | specialist_tool | legal_contract_agent | search_legal_contract | run_legal_contract | `handler=legal_contract`, `sources`, `summary`, 실패 시 `reason` | 법령명/조문 기반으로만 답하고 `documentId`, `score`, `sourceUrl` 미노출 |
| no_matching_tool | no_matching_tool | 없음 | 없음 | 없음 | `reason=no_matching_tool` 또는 지원 범위 실패 | 지원 가능한 질문 범위를 안내 |
| mixed | fragmented 또는 supervisor_aggregate | 복수 agent | 복수 tool | 복수 service | `fragments` 순서 또는 aggregate `results`, 각 handler/status | fragment 또는 aggregate 순서를 반영하고 성공/실패를 함께 설명 |

### 테스트 패키지 분리

질문지는 하나로 관리하되, 테스트 구현은 아래 package 단위로 나눈다.

| test_package | 포함 범위 | execution_path | 목적 |
|---|---|---|---|
| `chatbot.qa.routing` | 전체 질문의 representative subset | 전체 | 질문이 기대 handler와 실행 경로로 가는지 검증 |
| `chatbot.qa.direct_feature` | 명확한 단일 recommendation/comparison | direct_feature | LLM/tool hop 없이 feature service가 직접 실행되는지 검증 |
| `chatbot.qa.specialist_tool` | simple_lookup, price_trend, legal_contract | specialist_tool | specialist agent와 tool 호출 결과 검증 |
| `chatbot.qa.aggregate` | MX, 일부 RV 복합 질문 | fragmented 또는 supervisor_aggregate | fragment 분할 또는 supervisor aggregate 결과 검증 |
| `chatbot.qa.answer_contract` | handler별 성공/실패 대표 질문 | 전체 | 최상위 `answer`, nested `answer` 부재, 금지 필드 노출 여부 검증 |
| `chatbot.qa.robustness` | RV | varies | 띄어쓰기, 붙여쓰기, 어순, 유사어 변형 검토 |
| `chatbot.qa.known_gap` | 의도적으로 실패/미지원/개선 후보 | no_matching_tool 또는 known_gap | 현재 미지원 동작을 명시하고 회귀 실패와 구분 |

| id 범위 | test_package | test_tier | expected_execution_path | 비고 |
|---|---|---|---|---|
| SL-001..SL-021 | `chatbot.qa.specialist_tool.lookup` | regression | specialist_tool | lookup_agent/simple_lookup 검증 |
| RC-001..RC-007, RC-009..RC-022 | `chatbot.qa.direct_feature.recommendation` | regression 또는 import_smoke | direct_feature | 명확한 단일 추천은 `run_recommendation` 직접 실행을 우선 기대 |
| RC-008 | `chatbot.qa.known_gap` | boundary | no_matching_tool 또는 recommendation | 주거 상황 추론 질문이라 현재 지원 범위 판단 필요 |
| CP-001, CP-003..CP-006, CP-008..CP-019 | `chatbot.qa.direct_feature.comparison` | regression 또는 import_smoke | direct_feature | 명확한 단일 비교는 `run_comparison` 직접 실행을 우선 기대 |
| CP-002, CP-007 | `chatbot.qa.known_gap.comparison` | boundary | direct_feature | `missingApartmentNames` 또는 비교 대상 부족 실패 검증 |
| PT-001..PT-016 | `chatbot.qa.specialist_tool.price_trend` | regression 또는 import_smoke | specialist_tool | price_trend_agent/analyze_price_trend 검증 |
| LC-001..LC-026 | `chatbot.qa.specialist_tool.legal_contract` | regression | specialist_tool | legal_contract_agent/search_legal_contract 검증 |
| MX-FR-001..MX-FR-002 | `chatbot.qa.aggregate.fragmented` | regression | fragmented | `그리고`, `또` 기준 fragment split 검증 |
| MX-IN-001..MX-IN-004 | `chatbot.qa.aggregate.independent` | regression | direct_independent_features | 서로 다른 근거 도메인의 독립 실행 검증 |
| MX-DP-001 | `chatbot.qa.aggregate.dependent` | regression | direct_dependent_features | 추천 결과 후보를 비교 입력으로 전달 |
| MX-AM-001..MX-AM-003 | `chatbot.qa.aggregate.ambiguous` | regression | direct_ambiguous_features 또는 direct_feature | complex 시세 질문의 lookup/trend 분기 검증 |
| MX-ST-001 | `chatbot.qa.aggregate.same_tool` | regression | direct_same_tool_features | 같은 tool의 서로 다른 대상 multi-call 유지 |
| MX-LLM-002 | `chatbot.qa.aggregate.supervisor` | regression | supervisor_aggregate 또는 known_gap | v2 의존 chain fallback |
| MX-DD-001 | `chatbot.qa.aggregate.dedupe` | regression | varies | duplicate lookup/tool result 제거 |
| UB-001..UB-006 | `chatbot.qa.known_gap.boundary` | boundary | no_matching_tool 또는 known_gap | 미지원/범위 외/기능 후보 검증 |
| RV-001..RV-021 | `chatbot.qa.robustness` | exploratory | varies | 실패 시 슬롯/정규화 개선 후보로 기록 |

품질 리뷰 결과:

- `SL`, `PT`, 일부 `CP` 케이스는 `래미안대치팰리스`, `잠실엘스`처럼 fixture와 import 양쪽에서 확인되는 단지를 사용해 자동 회귀 테스트에 적합하다.
- `RC`의 역/학교/생활편의 조건은 import POI 기반 검증에는 적합하지만, fixture에 POI가 부족하면 fixture 자동 테스트 전에 fixture 보강이 필요하다.
- `CP-002`처럼 일부 단지를 찾지 못하는 케이스는 실패 품질 검증용이다. 성공 비교 품질 검증과 섞지 않는다.
- `RV` 케이스는 현재 동작 보장 목록이 아니라 parser/slot robustness를 검토하기 위한 후보군이다. 실패하면 질문지를 잘못 만든 것이 아니라 정규화나 슬롯 추출 개선 후보로 기록한다.
- `신고가` 계열 질문은 현재 라우팅/슬롯 추출에서 별도 query type으로 안정화되어 있지 않으므로 regression에서 제외하고 known gap으로 관리한다.

## 1. Simple Lookup

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| SL-001 | [ ] | 래미안대치팰리스 어디야? | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-002 | [ ] | 래미안대치팰리스 위치 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-003 | [ ] | 래미안대치팰리스 주소 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-004 | [ ] | 래미안대치팰리스 최근 거래 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-005 | [ ] | 래미안대치팰리스 최근 5건 보여줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-006 | [ ] | 래미안대치팰리스 전용 84㎡ 최근 실거래 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-007 | [ ] | 래미안대치팰리스 최근 1년 거래 내역 보여줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-008 | [ ] | 래미안대치팰리스 전용 84㎡ 최근 1년 실거래 5건 보여줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-009 | [ ] | 래미안대치팰리스 얼마야? | simple_lookup + price_trend | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-010 | [ ] | 래미안대치팰리스 가격 알려줘 | simple_lookup + price_trend | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-011 | [ ] | 래미안대치팰리스 최고가 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-012 | [ ] | 래미안대치팰리스 가장 비싼 거래 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-013 | [ ] | 래미안대치팰리스 전용 84㎡ 최고가는 얼마야? | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-014 | [ ] | 래미안대치팰리스 최근 1년 최고가 알려줘 | simple_lookup | docs/data/simple-lookup-handler.md, server/tests/fixtures |
| SL-015 | [ ] | 잠실엘스 위치 알려줘 | simple_lookup | server/tests |
| SL-016 | [ ] | 잠실엘스 어디 있어? | simple_lookup | server/tests |
| SL-017 | [ ] | 잠실 엘스 시세 알려줘 | simple_lookup + price_trend | server/tests |
| SL-018 | [ ] | 래미안대치팰리스 최근 실거래가 알려줘 | simple_lookup | docs/architecture/lookup-trend-spec.md, server/tests/fixtures |
| SL-019 | [ ] | 래미안대치팰리스 가장 최근 실거래가 알려줘 | simple_lookup | docs/architecture/lookup-trend-spec.md, server/tests/fixtures |
| SL-020 | [ ] | 래미안대치팰리스 최근 거래 3건 보여줘 | simple_lookup | docs/architecture/lookup-trend-spec.md, server/tests/fixtures |
| SL-021 | [ ] | 래미안대치팰리스 전용 84㎡ 얼마야 | simple_lookup + price_trend | docs/architecture/lookup-trend-spec.md, server/tests/fixtures |

## 2. Recommendation

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| RC-001 | [ ] | 가락시장역 근처의 아파트를 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-002 | [ ] | 가락시장역 300m 안에 있는 아파트 3개 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-003 | [ ] | 잠실역 근처 아파트 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-004 | [ ] | 강남구 30억 이하 아파트 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-005 | [ ] | 서초구 신축 아파트 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-006 | [ ] | 1000세대 이상 대단지 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-007 | [ ] | 30평 이상 아파트 추천해줘 | recommendation | docs/data/recommendation-question-cases.md |
| RC-008 | [ ] | 4인 가족이 살 수 있는 집을 추천해줘 | recommendation 또는 no_matching_tool | docs/data/recommendation-question-cases.md |
| RC-009 | [ ] | 30억 예산 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-010 | [ ] | 500세대 이상 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-011 | [ ] | 서초역 근처 아파트 알려줘 | recommendation | docs/architecture/amount-design.md |
| RC-012 | [ ] | 신축 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-013 | [ ] | 초등학교 근처 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-014 | [ ] | 25평 이상 아파트 싼 곳 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-015 | [ ] | 서초역 근처 30억 이하 신축 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |
| RC-016 | [ ] | 잠실 근처 10억 이하 아파트 추천해줘 | recommendation | docs/architecture/pipeline-ai.md |
| RC-017 | [ ] | 강남구에 있는 아파트 3개를 추천해주고 그 이유를 알려줘 | recommendation | server/tests |
| RC-018 | [ ] | 초/중/고 가까운 강남구 아파트 3개 추천해줘 | recommendation | server/tests |
| RC-019 | [ ] | 서초구 20억 이하 저렴한 아파트 4곳 추천해줘 | recommendation | server/tests |
| RC-020 | [ ] | 청담역 주변 비싼 아파트 3개 추천해줘 | recommendation | server/tests |
| RC-021 | [ ] | 송파구 30억 이하 아파트 추천해줘 | recommendation | server/tests |
| RC-022 | [ ] | 서초역 근처 아파트 추천해줘 | recommendation | docs/architecture/amount-design.md |

## 3. Comparison

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| CP-001 | [ ] | 래미안대치팰리스와 반포자이 비교해줘 | comparison | docs/data/comparison-question-cases.md |
| CP-002 | [ ] | 래미안대치팰리스랑 압구정현대 가격 비교해줘 | comparison 부분 실패(missingApartmentNames) | docs/data/comparison-question-cases.md |
| CP-003 | [ ] | 동부썬빌이랑 두산위브 가격이랑 학교 거리 비교해줘 | comparison | docs/data/comparison-question-cases.md |
| CP-004 | [ ] | 성원상떼빌과 롯데캐슬 세대수랑 연식 비교해줘 | comparison | docs/data/comparison-question-cases.md |
| CP-005 | [ ] | 잠실엘스랑 반포자이 교통 비교해줘 | comparison | docs/data/comparison-question-cases.md |
| CP-006 | [ ] | 래미안대치팰리스와 반포자이 상권 비교해줘 | comparison | docs/data/comparison-question-cases.md |
| CP-007 | [ ] | 래미안대치팰리스 비교해줘 | comparison 실패 | docs/data/comparison-question-cases.md |
| CP-008 | [ ] | 래미안대치팰리스랑 잠실엘스 비교해줘 | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-009 | [ ] | 래미안대치팰리스와 잠실엘스 가격 비교해줘 | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-010 | [ ] | 래미안대치팰리스랑 잠실엘스 중 어디가 더 신축이야? | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-011 | [ ] | 래미안대치팰리스랑 잠실엘스 세대수랑 가격 비교해줘 | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-012 | [ ] | 래미안대치팰리스랑 잠실엘스 중 어디가 초등학교에 가까워? | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-013 | [ ] | 래미안대치팰리스랑 잠실엘스 중 어디가 역이 더 가까워? | comparison | docs/architecture/amount-design.md, server/tests/fixtures |
| CP-014 | [ ] | 래미안대치팰리스랑 잠실엘스 가격 비교해줘 | comparison | server/tests |
| CP-015 | [ ] | 반포자이랑 래미안퍼스티지 초등학교 접근성 비교해줘 | comparison | server/tests |
| CP-016 | [ ] | 아크로리버파크랑 래미안원펜타스 가격이랑 평당가 비교해줘 | comparison | server/tests |
| CP-017 | [ ] | 도곡렉슬이랑 대치현대 어디가 더 대단지야 비교해줘 | comparison | server/tests |
| CP-018 | [ ] | 잠실엘스랑 리센츠 상권 학군 미래 가격 전망 비교해줘 | comparison | server/tests |
| CP-019 | [ ] | 잠실엘스랑 리센츠 재개발 전망 비교해줘 | comparison | server/tests |

## 4. Price Trend

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| PT-001 | [ ] | 잠실엘스 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md, server/tests/fixtures |
| PT-002 | [ ] | 잠실엘스 최근 1년 가격 흐름 보여줘 | price_trend | docs/data/price-trend-handler.md, server/tests/fixtures |
| PT-003 | [ ] | 잠실엘스 34평 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md, server/tests/fixtures |
| PT-004 | [ ] | 잠실엘스 최근 1년 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md, server/tests/fixtures |
| PT-005 | [ ] | 강남구 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-006 | [ ] | 서초구 최근 1년 가격 흐름 보여줘 | price_trend | docs/data/price-trend-handler.md |
| PT-007 | [ ] | 강남 3구 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-008 | [ ] | 강남구 최근 1년 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-009 | [ ] | 강남 3구 최근 1년 시세 추이 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-010 | [ ] | 최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-011 | [ ] | 최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘 | price_trend | docs/data/price-trend-handler.md |
| PT-012 | [ ] | 강남구 상승률 높은 아파트 알려줘 | price_trend | docs/data/price-trend-handler.md |
| PT-013 | [ ] | 강남구 최고가 아파트 TOP 5 알려줘 | simple_lookup | docs/data/price-trend-handler.md |
| PT-014 | [ ] | 서초구에서 가장 비싼 아파트 보여줘 | simple_lookup | docs/data/price-trend-handler.md |
| PT-015 | [ ] | 송파구 최저가 아파트 5곳 알려줘 | simple_lookup | docs/data/price-trend-handler.md |
| PT-016 | [ ] | 최근 1년 잠실엘스 시세 추이 알려줘 | price_trend | server/tests |

## 5. Legal Contract

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| LC-001 | [ ] | 30억 아파트 매매 시 알아야 할 법률이 있을까? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-002 | [ ] | 아파트 매매 시 세금 책정 관련 법을 알려줘. | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-003 | [ ] | 매매 계약서에서 중요하게 볼 부분은 어디야? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-004 | [ ] | 집을 살 때 알아야 할 법이 있을까? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-005 | [ ] | 아파트 매매계약 후 신고해야 하는 게 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-006 | [ ] | 세입자 있는 집을 사도 괜찮아? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-007 | [ ] | 명의 이전은 어떤 법과 관련 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-008 | [ ] | 계약금을 냈는데 계약을 취소할 수 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-009 | [ ] | 부모님이 돈을 보태주면 문제가 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-010 | [ ] | 등기부에서 빚 잡힌 집인지 보려면 뭘 봐야 해? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-011 | [ ] | 아파트 매매계약은 법적으로 언제 성립해? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-012 | [ ] | 매도인이 계약금을 받았는데 계약을 해제하려면 어떻게 해야 해? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-013 | [ ] | 부동산 거래 신고는 누가 해야 해? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-014 | [ ] | 공인중개사가 거래계약서를 거짓으로 작성하면 안 된다는 법이 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-015 | [ ] | 부동산 등기부에는 어떤 권리를 등기할 수 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-016 | [ ] | 소유권 이전등기는 어떤 법과 관련 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-017 | [ ] | 토지거래허가구역에서 집을 사려면 허가가 필요해? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-018 | [ ] | 부동산 거래 신고필증은 등기와 어떤 관련이 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-019 | [ ] | 매매대금을 지급하기로 한 계약도 매매로 볼 수 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-020 | [ ] | 아파트 구분소유자는 집합건물법과 관련이 있어? | legal_contract | docs/data/legal_rag_answerflow.md |
| LC-021 | [ ] | 계약금을 돌려받을 수 있나요? | legal_contract | server/tests |
| LC-022 | [ ] | 매매 계약금 해제 규정 알려줘 | legal_contract | server/tests |
| LC-023 | [ ] | 아파트 살 때 계약 전에 꼭 확인해야 할 법적 사항은 뭐야 | legal_contract | server/tests |
| LC-024 | [ ] | 집값을 실제보다 낮게 계약서에 쓰면 문제가 있어 | legal_contract | server/tests |
| LC-025 | [ ] | 전세 낀 아파트를 사면 보증금은 누가 돌려줘야 해 | legal_contract | server/tests/test_legal_rag_query_intent.py |
| LC-026 | [ ] | 매매 계약 법률 알려줘 | legal_contract | server/tests/test_chatbot_service.py |

## 6. Mixed / Multi-Tool Orchestration

Mixed 질문은 fragment split, independent multi-feature, dependent multi-feature, ambiguous multi-feature, same-tool multi-feature, supervisor LLM fallback, duplicate/dedupe로 나눠 검증한다.

| id | check | question | expected_plan_type | expected_execution_path | expected_agents | expected_handlers | expected_dependency | expected_dedupe | answer_checks |
|---|---|---|---|---|---|---|---|---|---|
| MX-FR-001 | [ ] | 잠실엘스 위치 알려줘 그리고 매매 계약 법률 알려줘 | fragment split | fragmented | lookup_agent, legal_contract_agent | simple_lookup, legal_contract | 없음 | 없음 | fragment 순서대로 위치와 법령 근거를 구분 |
| MX-FR-002 | [ ] | 잠실엘스 위치 알려줘 그리고 오늘 날씨 알려줘 | fragment split | fragmented + partial_success | lookup_agent | simple_lookup, no_matching_tool | 없음 | 없음 | 위치는 답하고 날씨는 지원 범위 밖으로 안내 |
| MX-IN-001 | [ ] | 강남구 아파트 추천하고 최근 시세 추이도 알려줘 | independent_multi_feature | direct_independent_features | recommendation_agent, price_trend_agent | recommendation, price_trend | 없음 | 없음 | 추천 후보와 지역 시세 추이를 별도 기준으로 설명 |
| MX-IN-002 | [ ] | 강남구 아파트 추천하고 매매 계약 법령도 알려줘 | independent_multi_feature | direct_independent_features | recommendation_agent, legal_contract_agent | recommendation, legal_contract | 없음 | 없음 | 추천 근거와 계약 법령 근거를 섞지 않고 구분 |
| MX-IN-003 | [ ] | 잠실엘스 위치랑 매매 계약 법률 알려줘 | independent_multi_feature | direct_independent_features | lookup_agent, legal_contract_agent | simple_lookup, legal_contract | 없음 | 없음 | 위치와 계약 법령을 각각 답변 |
| MX-IN-004 | [ ] | 래미안대치팰리스랑 잠실엘스 비교하고 계약 시 주의할 법도 알려줘 | independent_multi_feature | direct_independent_features | comparison_agent, legal_contract_agent | comparison, legal_contract | 없음 | 없음 | 단지 비교와 계약 주의 법령을 구분 |
| MX-DP-001 | [ ] | 강남구 아파트 추천하고 후보 비교도 해줘 | dependent_multi_feature | direct_dependent_features | recommendation_agent, comparison_agent | recommendation, comparison | comparison dependsOn recommendation_agent | 없음 | 추천 후보를 먼저 요약하고 후보 간 차이를 비교 |
| MX-AM-001 | [ ] | 잠실엘스 시세 알려줘 | ambiguous_multi_feature | direct_ambiguous_features | lookup_agent, price_trend_agent | simple_lookup, price_trend | 없음 | 없음 | 최근 거래 기준과 최근 1년 추이 기준을 분리 |
| MX-AM-002 | [ ] | 잠실엘스 최근 실거래가 알려줘 | single_feature | direct_feature 또는 specialist_tool | lookup_agent | simple_lookup | 없음 | 없음 | 최근 실거래만 답하고 추이처럼 쓰지 않음 |
| MX-AM-003 | [ ] | 잠실엘스 시세 추이 알려줘 | single_feature | direct_feature 또는 specialist_tool | price_trend_agent | price_trend | 없음 | 없음 | 추이/기간 수치 중심으로 답변 |
| MX-ST-001 | [ ] | 강남구 시세추이랑 송파구 시세추이 알려줘 | same_tool_multi_feature | direct_same_tool_features | price_trend_agent, price_trend_agent | price_trend, price_trend | 없음 | 없음 | 강남구와 송파구 결과를 각각 구분 |
| MX-LLM-002 | [ ] | 송파구 30억 이하 추천하고 추천 후보들 가격 흐름도 알려줘 | supervisor_llm | supervisor_aggregate 또는 known_gap | recommendation_agent, price_trend_agent | recommendation, price_trend | v2 dependent chain 후보 | 없음 | 현재 v2 gap이면 추천 후보 가격 흐름 미지원 사유 명시 |
| MX-DD-001 | [ ] | 잠실엘스 위치랑 잠실엘스 위치 알려줘 | single_feature 또는 supervisor_llm | direct_feature 또는 specialist_tool | lookup_agent | simple_lookup | 없음 | 동일 lookup signature 제거 | 같은 위치 답변을 반복하지 않음 |

## 7. Unsupported / Boundary

| id | check | question | expected_handler | source |
|---|---|---|---|---|
| UB-001 | [ ] | 오늘 날씨 알려줘 | no_matching_tool | server/tests |
| UB-002 | [ ] | 부동산 후보 알려줘 | no_matching_tool 또는 recommendation | server/tests |
| UB-003 | [ ] | 래미안대치팰리스 근처 학교 알려줘 | no_matching_tool 또는 simple_lookup 확장 | docs/architecture/amount-design.md, server/tests/fixtures |
| UB-004 | [ ] | 부동산 매매와 관련 없는 질문 | legal_contract 실패 | docs/data/legal_rag_answerflow.md |
| UB-005 | [ ] | 서울 아파트 알려줘 | no_matching_tool 또는 범위 외 지역 실패 | web/src/features/chatbot/api/queryChatbot.test.ts |
| UB-006 | [ ] | 신고가 TOP 5 알려줘 | no_matching_tool | docs/architecture/lookup-trend-spec.md |

## 8. 표현 변형 / Robustness

이 섹션은 같은 의도를 띄어쓰기, 붙여쓰기, 단위 표현, 어순, 유사어로 바꿨을 때 같은 handler로 라우팅되고 같은 대상 단지/지역을 잡는지 확인한다.

| id | check | question | expected_handler | 검토 포인트 |
|---|---|---|---|---|
| RV-001 | [ ] | 잠실엘스 위치 알려줘 | simple_lookup | 기준 문장 |
| RV-002 | [ ] | 잠실 엘스 위치 알려줘 | simple_lookup | 단지명 띄어쓰기 |
| RV-003 | [ ] | 래미안 대치 팰리스 위치 알려줘 | simple_lookup | 긴 단지명 띄어쓰기 |
| RV-004 | [ ] | 래미안대치팰리스 전용84㎡ 최근 실거래 알려줘 | simple_lookup | 숫자/단위 붙여쓰기 |
| RV-005 | [ ] | 래미안대치팰리스 전용 84 제곱미터 최근 실거래 알려줘 | simple_lookup | 면적 단위 동의어 |
| RV-006 | [ ] | 래미안대치팰리스 실거래 최근 3건 보여줘 | simple_lookup | 어순 변경 |
| RV-007 | [ ] | 최근 실거래가 래미안대치팰리스 알려줘 | simple_lookup | 목적어 선행 |
| RV-008 | [ ] | 잠실엘스 34 평 시세 추이 알려줘 | price_trend | 평형/시세추이 띄어쓰기 |
| RV-009 | [ ] | 최근 1년 시세 추이 잠실엘스 알려줘 | price_trend | 기간 선행 |
| RV-010 | [ ] | 잠실엘스 가격 흐름 1년치 보여줘 | price_trend | 유사어: 시세추이 -> 가격 흐름 |
| RV-011 | [ ] | 송파구에서 30억 밑으로 아파트 추천해줘 | recommendation | 가격 상한 유사어 |
| RV-012 | [ ] | 서초구 20억 이하인 저렴한 단지 4곳 추천해줘 | recommendation | 어미/명사 변형 |
| RV-013 | [ ] | 서초역에서 가까운 서초구 아파트 추천해줘 | recommendation | known_gap: `서초구`의 `초`를 학교 조건으로 오탐할 수 있음 |
| RV-014 | [ ] | 래미안대치팰리스와 잠실엘스 시세 비교해줘 | comparison | 비교 표현 변형 |
| RV-015 | [ ] | 잠실엘스랑 리센츠 역 접근성 비교해줘 | comparison | metric 유사어 |
| RV-016 | [ ] | 래미안대치팰리스랑잠실엘스가격비교해줘 | comparison | 극단적 붙여쓰기 |
| RV-017 | [ ] | 계약 해제하려면 계약금은 어떻게 돼? | legal_contract | 법령 표현 축약 |
| RV-018 | [ ] | 전세 세입자 있는 아파트 사면 보증금은 누가 돌려줘? | legal_contract | 임대차 표현 변형 |
| RV-019 | [ ] | 잠실엘스 위치랑 매매계약 법률 같이 알려줘 | simple_lookup + legal_contract | 복합 질문 접속 표현 |
| RV-020 | [ ] | 강남구 아파트 추천 후 최근 1년 가격 흐름도 알려줘 | recommendation + price_trend | 복합 질문 어순 |
| RV-021 | [ ] | 래미안대치팰리스 실거래랑 계약금 해제 법 알려줘 | simple_lookup + legal_contract | 복합 질문 대상 혼합 |

## 9. QA 기록 양식

자동 QA는 우선 `server/scripts/run_chatbot_qa.py`의 deterministic regression set을 기준으로 실행한다. 기본 실행은 `OPENAI_API_KEY`를 제거해 최종 answer를 fallback formatter로 생성하므로, 외부 LLM quota/응답 변동과 분리해서 routing, execution trace, handler, status, nested answer contract를 검증한다. live LLM answer 품질 검증은 같은 질문지의 대표 subset을 별도 실행으로 관리한다.

결과 파일은 `docs/qa/chatbot-qa-results-YYYY-MM-DD.md`와 `docs/qa/chatbot-qa-results-YYYY-MM-DD.jsonl`에 저장한다.

| run_date | test_package | id | question | expected_handler | expected_execution_path | expected_agent | expected_tool | expected_service | actual_execution_path | actual_agent | actual_tool | actual_service | result_handler | actual_status | answer_ok | answer_excerpt | nested_answer_absent | notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
