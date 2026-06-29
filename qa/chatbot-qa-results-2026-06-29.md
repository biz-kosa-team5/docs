# Chatbot QA Results - 2026-06-29

## Summary

- mode: deterministic
- total: 15
- passed: 15
- failed: 0

## Results

| id | package | status | expected path | actual path | expected handlers | actual handlers | answer ok | nested answer absent | notes |
|---|---|---|---|---|---|---|---|---|---|
| SL-002 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-018 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RC-021 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | 데이터에 조건 충족 후보가 없으면 failed가 정상일 수 있다. |
| CP-008 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| PT-001 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-010 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | 랭킹 데이터가 없으면 failed가 정상일 수 있다. |
| LC-022 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | deterministic 모드에서는 OpenAI embedding 미설정으로 failed가 정상일 수 있다. |
| MX-FR-002 | chatbot.qa.aggregate.fragmented | PASS | fragmented | direct_feature,direct_no_matching_tool | simple_lookup | simple_lookup, no_matching_tool | Y | Y | - |
| MX-IN-001 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | recommendation, price_trend | recommendation, price_trend | Y | Y | - |
| MX-IN-004 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | comparison, legal_contract | comparison, legal_contract | Y | Y | deterministic 모드에서는 legal_contract가 embedding_unavailable로 partial_success일 수 있다. |
| MX-DP-001 | chatbot.qa.aggregate.dependent | PASS | direct_dependent_features | direct_dependent_features | recommendation, comparison | recommendation, comparison | Y | Y | 추천 후보가 2개 미만이면 비교 dependency가 실패할 수 있다. |
| MX-AM-001 | chatbot.qa.aggregate.ambiguous | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| MX-ST-001 | chatbot.qa.aggregate.same_tool | PASS | direct_same_tool_features | direct_same_tool_features | price_trend, price_trend | price_trend, price_trend | Y | Y | - |
| MX-DD-001 | chatbot.qa.aggregate.dedupe | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| UB-001 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | - |

## Answer Excerpts

### SL-002

- question: 래미안대치팰리스 위치 알려줘
- answer: 래미안대치팰리스 위치는 대치동 1027입니다. 좌표는 위도 37.4941149, 경도 127.0579205입니다.

### SL-018

- question: 래미안대치팰리스 최근 실거래가 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-02-21 65.5억원 전용 151.31㎡ 24층, 2026-03-07 65.0억원 전용 151.31㎡ 10층, 2026-03-13 41.5억원 전용 84.97㎡ 12층입니다.

### RC-021

- question: 송파구 30억 이하 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (185-5): 최근 거래가 14.7억원, 가까운 역 방이역(216m), 가까운 교육시설 올림픽유치원(430m), 18세대, 사용승인일 2002…

### CP-008

- question: 래미안대치팰리스랑 잠실엘스 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대, 2015년 준공, 가까운 역 도곡역(402m), 가까운 학교 단국대학…

### PT-001

- question: 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### PT-010

- question: 최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘
- answer: 강남구 가격 변화율 순위는 상지리츠빌카일룸(65-4) 76.98%, 논현한가람빌라트 66.04%, 까치마을 54.06%입니다.

### LC-022

- question: 매매 계약금 해제 규정 알려줘
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### MX-FR-002

- question: 잠실엘스 위치 알려줘 그리고 오늘 날씨 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다. 오늘 날씨 알려줘는 처리하지 못했습니다. 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이…

### MX-IN-001

- question: 강남구 아파트 추천하고 최근 시세 추이도 알려줘
- answer: 1. 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일…

### MX-IN-004

- question: 래미안대치팰리스랑 잠실엘스 비교하고 계약 시 주의할 법도 알려줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대, 2015년 준공, 가까운 역 도곡역(402m), 가까운 학교 단국대학…

### MX-DP-001

- question: 강남구 아파트 추천하고 후보 비교도 해줘
- answer: 1. 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일…

### MX-AM-001

- question: 잠실엘스 시세 알려줘
- answer: 1. 잠실엘스 실거래 내역은 2026-01-03 34.8억원 전용 84.8㎡ 9층, 2026-01-06 33.0억원 전용 84.97㎡ 5층, 2026-01-07 33.0억원 전용 84.97㎡ 6층입니다. 2. 잠실…

### MX-ST-001

- question: 강남구 시세추이랑 송파구 시세추이 알려줘
- answer: 1. 강남구 시세추이를 조회했습니다. 2025-06-01 평균 31.7억원에서 2026-06-01 평균 31.5억원으로 확인됩니다. 2. 송파구 시세추이를 조회했습니다. 2025-06-01 평균 19.1억원에서 2…

### MX-DD-001

- question: 잠실엘스 위치랑 잠실엘스 위치 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

### UB-001

- question: 오늘 날씨 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.
