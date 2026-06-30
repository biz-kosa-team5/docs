# Chatbot QA 실패/품질 미달 묶음 - 2026-06-30

이 문서는 `chatbot-qa-docs-live-results-2026-06-30` 결과를 사용자 관점에서 다시 분류한 것이다.

중요한 기준은 다음과 같다.

- runner의 `PASS`는 answer가 비어 있지 않고 형식 계약을 만족했다는 뜻일 수 있다.
- 사용자가 요청한 추천/비교/복수 질문 결과를 실제로 제공하지 못하면 제품 품질 기준으로는 실패다.
- `status=failed` 또는 `success=false` payload가 있어도 docs 기대값이 `failed`를 허용하면 runner가 PASS로 기록할 수 있다.
- 따라서 아래 목록은 runner FAIL뿐 아니라 "PASS지만 사용자 기대 결과를 못 준 케이스"도 포함한다.

## 요약

| 분류 | 케이스 | 판단 |
| --- | --- | --- |
| runner hard fail | 12건 | handler/path 기대값을 만족하지 못했다. |
| PASS지만 사용자 관점 실패 | `RC-013`, `CP-002`, `CP-003` | 후보 없음, 비교 데이터 부족, 비교 대상 파싱 실패가 answer에 노출됐다. |
| tool 미선택/미지원 | `UB-001`, `UB-002`, `UB-003`, `UB-005`, `UB-006`, `MX-FR-002` | `no_matching_tool` 또는 부분 미지원으로 처리됐다. 일부는 의도된 boundary지만 확장 후보로 남긴다. |
| 복수 질문 누락 | `MX-DP-001`, `MX-ST-001`, `MX-AM-001`, `SL-009`, `SL-010`, `SL-017`, `SL-021` | 질문이 요구한 복수 tool 또는 복수 호출이 완전히 반영되지 않았다. |

## PASS지만 사용자 관점 실패

### RC-013

| 항목 | 값 |
| --- | --- |
| question | `초등학교 근처 아파트 추천해줘` |
| runner status | `PASS` |
| actual path / handlers | `direct_feature` / `recommendation` |
| answer | `조건에 맞는 역/교육시설을 찾지 못했습니다. 가격, 지역, 역/학교 반경 같은 조건을 조금 완화해 보세요.` |
| 사용자 관점 판단 | 추천 후보를 요청했지만 후보가 0건이다. 성공 답변이 아니라 추천 실패 또는 추가 질문 필요 상태로 봐야 한다. |
| 필요한 처리 | 지역 조건이 필수라면 `어느 지역 기준으로 찾을까요?`로 되묻고, 전체 DB 검색을 허용한다면 초등학교 거리 기준 후보를 반환해야 한다. |

### CP-002

| 항목 | 값 |
| --- | --- |
| question | `래미안대치팰리스랑 압구정현대 가격 비교해줘` |
| runner status | `PASS` |
| actual path / handlers | `supervisor_aggregate` / `comparison, simple_lookup` |
| 2026-06-30 저장 answer | `전문 에이전트 결과를 처리하지 못했습니다.` |
| payload 핵심 | `comparison`은 `압구정현대`를 `missingApartmentNames`로 반환했고, `simple_lookup`도 `target_not_found`를 반환했다. |
| 사용자 확인 answer | `일부 아파트를 찾지 못했습니다: 압구정현대 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.` |
| 사용자 관점 판단 | 비교 질문인데 비교 결과가 없다. runner가 `failed` status를 허용해서 PASS가 됐지만 제품 품질상 실패다. |
| 필요한 처리 | 비교 대상 2개 중 1개만 찾으면 `partial_success`로 한쪽 데이터와 누락 대상을 명확히 보여주거나, 후보명 disambiguation을 제안해야 한다. |

### CP-003

| 항목 | 값 |
| --- | --- |
| question | `동부썬빌이랑 두산위브 가격이랑 학교 거리 비교해줘` |
| runner status | 결과 파일에 따라 변동 |
| 2026-06-30 저장 결과 | 현재 저장된 full JSONL에서는 비교 answer가 성공 형태로 기록됐다. |
| 사용자 확인 answer | `일부 아파트를 찾지 못했습니다: 두산위브 거리 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.` |
| payload에서 관찰된 원인 | 과거 결과에서는 `두산위브 거리`처럼 비교 표현 일부가 아파트명에 붙어 `missingApartmentNames`가 발생했다. |
| 사용자 관점 판단 | 같은 질문에서 성공/실패가 흔들린다. LLM 또는 slot parser가 비교 문장 꼬리를 단지명에 섞는 문제가 있다. |
| 필요한 처리 | comparison slot에서 `가격`, `학교 거리`, `더 가까워`, `비교` 같은 metric/intent 표현을 apartment name 후보에서 제거해야 한다. |

## Runner Hard Fail 12건

### 시세/가격 애매 질문에서 `price_trend` 미호출

| id | question | expected | actual | 판단 |
| --- | --- | --- | --- | --- |
| `SL-009` | `래미안대치팰리스 얼마야?` | `simple_lookup + price_trend` | `simple_lookup` | 가격/시세 질문인데 최근 거래만 보거나 위치성 답변으로 기울었다. |
| `SL-010` | `래미안대치팰리스 가격 알려줘` | `simple_lookup + price_trend` | `simple_lookup` | 최근 거래는 답했지만 추이 tool이 빠졌다. |
| `SL-017` | `잠실 엘스 시세 알려줘` | `simple_lookup + price_trend` | `simple_lookup` | 시세 흐름 없이 최근 거래 중심으로 답했다. |
| `SL-021` | `래미안대치팰리스 전용 84㎡ 얼마야` | `simple_lookup + price_trend` | `simple_lookup` | 전용면적 가격대는 답했지만 추이 기준이 없다. |
| `MX-AM-001` | `잠실엘스 시세 알려줘` | `simple_lookup + price_trend` | `simple_lookup` | ambiguous price 질문의 multi-tool 처리가 빠졌다. |

필요한 처리:

- `얼마야`, `가격`, `시세`는 단순 거래 조회만으로 끝내지 않는다.
- 최근 거래를 `simple_lookup`, 월별 흐름을 `price_trend`로 함께 호출해야 한다.
- 답변은 최근 거래와 1년 흐름을 분리해 요약해야 한다.

### 비교 질문에서 `comparison` 미호출 또는 오해

| id | question | expected | actual | 판단 |
| --- | --- | --- | --- | --- |
| `CP-010` | `래미안대치팰리스랑 잠실엘스 중 어디가 더 신축이야?` | `comparison` | `simple_lookup` | 거래일을 준공연도로 오해해 잘못된 결론을 냈다. |
| `CP-012` | `래미안대치팰리스랑 잠실엘스 중 어디가 초등학교에 가까워?` | `comparison` | `simple_lookup` | 학교 거리 비교를 못 하고 위치 정보만 설명했다. |
| `CP-013` | `래미안대치팰리스랑 잠실엘스 중 어디가 역이 더 가까워?` | `comparison` | `simple_lookup` | 역 거리 비교를 못 하고 데이터 없음으로 답했다. |
| `CP-017` | `도곡렉슬이랑 대치현대 어디가 더 대단지야 비교해줘` | `comparison` | `simple_lookup + comparison` | 답변 자체는 비교됐지만 불필요한 lookup이 함께 잡혀 runner 기준 실패다. |

필요한 처리:

- `중 어디`, `더 신축`, `더 가까워`, `더 대단지`, `비교`는 `comparison_agent` 우선이다.
- 비교용 metric 표현을 단지명으로 오염시키면 안 된다.
- `builtYear`, `unitCnt`, `nearestStation.distanceM`, `nearestSchool.distanceM` 같은 비교 가능한 metric을 우선 사용해야 한다.

### Ranking/Trend 질문 오해

| id | question | expected | actual | 판단 |
| --- | --- | --- | --- | --- |
| `PT-011` | `최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘` | `price_trend` | `simple_lookup + price_trend` | `많이 내린 아파트` ranking 대신 지역 평균 흐름 중심으로 답했다. |

필요한 처리:

- `많이 오른`, `많이 내린`, `상승률`, `하락률`은 region timeseries가 아니라 complex ranking intent로 보내야 한다.
- 답변에는 상위/하위 단지 리스트가 있어야 한다.

## 복수 질문 / 복수 tool 누락

| id | question | expected | actual | 판단 |
| --- | --- | --- | --- | --- |
| `MX-DP-001` | `강남구 아파트 추천하고 후보 비교도 해줘` | `recommendation + comparison` | `recommendation` | 추천 후보만 반환하고 후보 간 비교를 실행하지 않았다. |
| `MX-ST-001` | `강남구 시세추이랑 송파구 시세추이 알려줘` | `price_trend + price_trend` | runner상 `price_trend` 1개 | payload에는 두 지역 결과가 있었지만 handler 수집이 중복 handler를 축약해 실패 처리했다. server runner 보정 대상이다. |
| `MX-AM-001` | `잠실엘스 시세 알려줘` | `simple_lookup + price_trend` | `simple_lookup` | ambiguous single question이지만 실제로는 복수 tool이 필요한 질문이다. |

필요한 처리:

- `추천하고 후보 비교`, `A랑 B 시세추이`, `시세 알려줘` 같은 질문은 한 번의 specialist 선택으로 끝내면 안 된다.
- supervisor가 여러 tool을 호출하거나, post-check에서 누락된 필수 handler를 보강해야 한다.
- QA runner는 `handlerCalls`로 중복 handler 호출을 보존해야 한다.

## Tool 미선택 / 미지원 처리

| id | question | actual | 판단 |
| --- | --- | --- | --- |
| `UB-001` | `오늘 날씨 알려줘` | `no_matching_tool` | 의도된 미지원. |
| `UB-002` | `부동산 후보 알려줘` | `no_matching_tool` | 표현이 모호하지만 추천으로 확장할 수도 있다. 현재는 미지원 처리. |
| `UB-003` | `래미안대치팰리스 근처 학교 알려줘` | `no_matching_tool` | 사용자는 단지 주변 교육시설 조회를 기대할 수 있다. simple lookup 확장 후보. |
| `UB-005` | `서울 아파트 알려줘` | `no_matching_tool` | 범위가 넓어 미지원 처리됐지만 되묻기 후보다. |
| `UB-006` | `신고가 TOP 5 알려줘` | `no_matching_tool` | ranking 기능 확장 후보. |
| `MX-FR-002` | `잠실엘스 위치 알려줘 그리고 오늘 날씨 알려줘` | `simple_lookup + no_matching_tool` | 위치는 성공, 날씨는 미지원. partial success가 맞다. |

필요한 처리:

- 순수 미지원은 `no_matching_tool`이 맞다.
- 부동산 도메인에 가까운 모호 질문은 바로 실패보다 clarification을 우선 검토한다.
- fragment 질문에서는 지원 가능한 fragment와 미지원 fragment를 분리해 answer에 명확히 표시한다.

## 법령 RAG 근거 없음

이번 live 결과에서는 다수의 `legal_contract` 질문이 tool은 호출됐지만 answer가 `질문과 관련된 법령 근거를 찾지 못했습니다.`로 끝났다.

이 경우는 `tool을 못 찾은 것`이 아니라 `tool은 찾았지만 RAG source를 못 찾은 것`이다. 사용자 관점에서는 법령 질문에 대한 답을 받지 못했으므로 품질 실패로 별도 관리해야 한다.

대표 케이스:

- `LC-001` `30억 아파트 매매 시 알아야 할 법률이 있을까?`
- `LC-002` `아파트 매매 시 세금 책정 관련 법을 알려줘.`
- `LC-011` `아파트 매매계약은 법적으로 언제 성립해?`
- `LC-022` `매매 계약금 해제 규정 알려줘`
- `RV-017` `계약 해제하려면 계약금은 어떻게 돼?`
- `RV-018` `전세 세입자 있는 아파트 사면 보증금은 누가 돌려줘?`

필요한 처리:

- legal RAG 문서 적재 상태, embedding index, retrieval query를 별도로 점검한다.
- QA runner는 `legal_contract`의 `success=false` 또는 source 0건을 제품 실패로 분류해야 한다.

## QA Runner 보정 필요 항목

| 문제 | 현재 현상 | 보정 기준 |
| --- | --- | --- |
| `answer_ok`가 너무 약함 | 실패 메시지도 자연어면 PASS 가능 | positive recommendation/comparison은 결과 row가 있어야 PASS |
| `expected_status`가 넓음 | `failed`를 허용하면 제품 실패가 PASS 가능 | known gap이 아닌 positive case는 `status=success`와 domain success 필요 |
| comparison 실패 감지 부족 | `missingApartmentNames`가 있어도 PASS 가능 | 비교 대상 2개 이상 성공, `missingApartmentNames=[]`일 때만 positive PASS |
| recommendation 실패 감지 부족 | 후보 0건이어도 PASS 가능 | 추천 후보 1건 이상 또는 clarification answer만 PASS |
| 복수 tool 누락 감지 불안정 | 중복 handler 축약 또는 일부 handler 누락 | `handlerCalls` 우선 수집, expected handler sequence/subsequence 검사 |
| no_matching_tool 해석 혼재 | boundary와 확장 후보가 섞임 | pure unsupported, domain clarification, known gap을 분리 |

## 우선순위

1. positive case에서 `payload.status=failed`, `success=false`, 후보 0건, `missingApartmentNames`가 있으면 runner가 FAIL로 잡게 한다.
2. comparison parser에서 비교 metric 표현이 아파트명에 붙지 않게 한다.
3. `초등학교 근처`, `서울 아파트`, `부동산 후보`처럼 조건이 부족한 도메인 질문은 clarification으로 돌린다.
4. ambiguous price 질문은 `simple_lookup + price_trend`를 모두 호출하게 한다.
5. dependent multi-tool 질문은 추천 결과를 comparison 입력으로 넘기는 후속 실행을 강제한다.
6. legal RAG source 미조회 문제를 별도 결함으로 추적한다.
