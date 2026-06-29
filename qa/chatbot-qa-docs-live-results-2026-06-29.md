# Chatbot QA Results - 2026-06-29

## Summary

- mode: live_llm
- total: 144
- passed: 144
- failed: 0

## Results

| id | package | status | expected path | actual path | expected handlers | actual handlers | answer ok | nested answer absent | notes |
|---|---|---|---|---|---|---|---|---|---|
| SL-001 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-002 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-003 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-004 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-005 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-006 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-007 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-008 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-009 | chatbot.qa.specialist_tool.lookup | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| SL-010 | chatbot.qa.specialist_tool.lookup | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| SL-011 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-012 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-013 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-014 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-015 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-016 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-017 | chatbot.qa.specialist_tool.lookup | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| SL-018 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-019 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-020 | chatbot.qa.specialist_tool.lookup | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| SL-021 | chatbot.qa.specialist_tool.lookup | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| RC-001 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-002 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-003 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-004 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-005 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-006 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-007 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-008 | chatbot.qa.known_gap.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | docs expectation: recommendation 또는 no_matching_tool |
| RC-009 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-010 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-011 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-012 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-013 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-014 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-015 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-016 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-017 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-018 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-019 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-020 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-021 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RC-022 | chatbot.qa.direct_feature.recommendation | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| CP-001 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-002 | chatbot.qa.known_gap.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | docs expectation: comparison 부분 실패(missingApartmentNames) |
| CP-003 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-004 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-005 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-006 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-007 | chatbot.qa.known_gap.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | docs expectation: comparison 실패 |
| CP-008 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-009 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-010 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-011 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-012 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-013 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-014 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-015 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-016 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-017 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-018 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| CP-019 | chatbot.qa.direct_feature.comparison | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| PT-001 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-002 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-003 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-004 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-005 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-006 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-007 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-008 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-009 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-010 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-011 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-012 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| PT-013 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| PT-014 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| PT-015 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| PT-016 | chatbot.qa.specialist_tool.price_trend | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| LC-001 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-002 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-003 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-004 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-005 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-006 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-007 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-008 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-009 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-010 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-011 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-012 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-013 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-014 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-015 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-016 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-017 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-018 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-019 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-020 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-021 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-022 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-023 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-024 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-025 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| LC-026 | chatbot.qa.specialist_tool.legal_contract | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| MX-FR-001 | chatbot.qa.aggregate.fragmented | PASS | fragmented | direct_feature,direct_feature | simple_lookup, legal_contract | simple_lookup, legal_contract | Y | Y | - |
| MX-FR-002 | chatbot.qa.aggregate.fragmented | PASS | fragmented | direct_feature,direct_no_matching_tool | simple_lookup, no_matching_tool | simple_lookup, no_matching_tool | Y | Y | - |
| MX-IN-001 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | recommendation, price_trend | recommendation, price_trend | Y | Y | - |
| MX-IN-002 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | recommendation, legal_contract | recommendation, legal_contract | Y | Y | - |
| MX-IN-003 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | simple_lookup, legal_contract | simple_lookup, legal_contract | Y | Y | - |
| MX-IN-004 | chatbot.qa.aggregate.independent | PASS | direct_independent_features | direct_independent_features | comparison, legal_contract | comparison, legal_contract | Y | Y | - |
| MX-DP-001 | chatbot.qa.aggregate.dependent | PASS | direct_dependent_features | direct_dependent_features | recommendation, comparison | recommendation, comparison | Y | Y | - |
| MX-AM-001 | chatbot.qa.aggregate.ambiguous | PASS | direct_ambiguous_features | direct_ambiguous_features | simple_lookup, price_trend | simple_lookup, price_trend | Y | Y | - |
| MX-AM-002 | chatbot.qa.aggregate.ambiguous | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | docs expectation: single_feature / direct_feature 또는 specialist_tool |
| MX-AM-003 | chatbot.qa.aggregate.ambiguous | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | docs expectation: single_feature / direct_feature 또는 specialist_tool |
| MX-ST-001 | chatbot.qa.aggregate.same_tool | PASS | direct_same_tool_features | direct_same_tool_features | price_trend, price_trend | price_trend, price_trend | Y | Y | - |
| MX-LLM-002 | chatbot.qa.aggregate.supervisor | PASS | supervisor_aggregate | supervisor_aggregate | recommendation, price_trend | recommendation | Y | Y | docs expectation: supervisor_llm / supervisor_aggregate 또는 known_gap |
| MX-DD-001 | chatbot.qa.aggregate.dedupe | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | docs expectation: single_feature 또는 supervisor_llm / direct_feature 또는 specialist_tool |
| UB-001 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | - |
| UB-002 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | docs expectation: no_matching_tool 또는 recommendation |
| UB-003 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | docs expectation: no_matching_tool 또는 simple_lookup 확장 |
| UB-004 | chatbot.qa.known_gap.boundary | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | docs expectation: legal_contract 실패 |
| UB-005 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | docs expectation: no_matching_tool 또는 범위 외 지역 실패 |
| UB-006 | chatbot.qa.known_gap.boundary | PASS | direct_no_matching_tool | direct_no_matching_tool | no_matching_tool | no_matching_tool | Y | Y | - |
| RV-001 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-002 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-003 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-004 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-005 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-006 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-007 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | simple_lookup | simple_lookup | Y | Y | - |
| RV-008 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| RV-009 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| RV-010 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | price_trend | price_trend | Y | Y | - |
| RV-011 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RV-012 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RV-013 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | recommendation | recommendation | Y | Y | - |
| RV-014 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| RV-015 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| RV-016 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | comparison | comparison | Y | Y | - |
| RV-017 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| RV-018 | chatbot.qa.robustness | PASS | direct_feature | direct_feature | legal_contract | legal_contract | Y | Y | - |
| RV-019 | chatbot.qa.robustness | PASS | direct_independent_features | direct_independent_features | simple_lookup, legal_contract | simple_lookup, legal_contract | Y | Y | - |
| RV-020 | chatbot.qa.robustness | PASS | direct_independent_features | direct_independent_features | recommendation, price_trend | recommendation, price_trend | Y | Y | - |
| RV-021 | chatbot.qa.robustness | PASS | direct_independent_features | direct_independent_features | simple_lookup, legal_contract | simple_lookup, legal_contract | Y | Y | - |

## Answer Excerpts

### SL-001

- question: 래미안대치팰리스 어디야?
- answer: 래미안대치팰리스는 대치동 1027에 위치해 있습니다. 이 단지는 대치동의 중심부에 자리 잡고 있어 접근성이 좋습니다. 지도에 표시해 두었으니, 위치를 확인해 보실 수 있습니다. 추가적인 정보가 필요하시면 언제든지 …

### SL-002

- question: 래미안대치팰리스 위치 알려줘
- answer: 래미안대치팰리스의 위치는 대치동 1027입니다. 이 단지는 대치동에 위치하고 있으며, 지도에서 확인할 수 있도록 표시해 두었습니다. 추가적인 정보가 필요하시면 언제든지 말씀해 주세요. 제공된 데이터 기준입니다.

### SL-003

- question: 래미안대치팰리스 주소 알려줘
- answer: 래미안대치팰리스의 주소는 대치동 1027입니다. 이 정보는 정확하며, 해당 단지의 위치를 지도에서 확인할 수 있습니다. 추가로 궁금한 사항이 있으시면 언제든지 질문해 주세요. 제공된 데이터 기준입니다.

### SL-004

- question: 래미안대치팰리스 최근 거래 알려줘
- answer: 래미안대치팰리스의 최근 거래 내역은 다음과 같습니다. 가장 최근 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원으로, 면적은 84.97㎡, 15층에 위치하고 있습니다. 그 이전 거래는 2…

### SL-005

- question: 래미안대치팰리스 최근 5건 보여줘
- answer: 래미안대치팰리스의 최근 거래 내역은 다음과 같습니다. 첫 번째 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원으로, 전용 면적은 84.97㎡이고, 15층에 위치합니다. 두 번째 거래는 2…

### SL-006

- question: 래미안대치팰리스 전용 84㎡ 최근 실거래 알려줘
- answer: 래미안대치팰리스 전용 84㎡의 최근 실거래 내역은 다음과 같습니다. 가장 최근 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000,000원입니다. 이 거래는 15층에서 이루어졌고, 전용 면적은 …

### SL-007

- question: 래미안대치팰리스 최근 1년 거래 내역 보여줘
- answer: 래미안대치팰리스의 최근 1년 거래 내역은 다음과 같습니다. 첫 번째 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원으로, 전용 면적은 84.97㎡이고, 15층에 위치합니다. 두 번째 거래…

### SL-008

- question: 래미안대치팰리스 전용 84㎡ 최근 1년 실거래 5건 보여줘
- answer: 래미안대치팰리스 전용 84㎡의 최근 1년 실거래 내역은 다음과 같습니다. 첫 번째 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000,000원이었고, 전용 면적은 84.97㎡로 15층에서 거래되…

### SL-009

- question: 래미안대치팰리스 얼마야?
- answer: 래미안대치팰리스의 최근 거래 가격은 440,000만원입니다. 이 가격은 2026년 6월 3일에 거래된 것으로, 면적은 84.97㎡이며, 평당가는 약 5,178.3만원입니다. 최근 거래 내역을 살펴보면, 2026년 …

### SL-010

- question: 래미안대치팰리스 가격 알려줘
- answer: 래미안대치팰리스의 최근 거래 가격은 다양합니다. 가장 최근 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원입니다. 이 외에도 2026년 5월 27일에는 570,000만원, 5월 13일에는…

### SL-011

- question: 래미안대치팰리스 최고가 알려줘
- answer: 래미안대치팰리스의 최고가는 655,000만원입니다. 이 거래는 2026년 2월 21일에 이루어졌으며, 해당 아파트는 대치동 1027에 위치하고 있습니다. 거래된 면적은 151.31㎡이며, 24층에 해당합니다. 지도…

### SL-012

- question: 래미안대치팰리스 가장 비싼 거래 알려줘
- answer: 래미안대치팰리스의 가장 비싼 거래는 2026년 2월 21일에 이루어진 것으로, 거래 금액은 655,000,000원입니다. 이 거래는 24층에 위치한 151.31㎡ 면적의 아파트에서 발생했으며, 평당 가격은 약 4,…

### SL-013

- question: 래미안대치팰리스 전용 84㎡ 최고가는 얼마야?
- answer: 래미안대치팰리스 전용 84㎡의 최고가는 475,000,000원입니다. 이 거래는 2025년 11월 6일에 이루어졌으며, 해당 아파트는 대치동 1027에 위치하고 있습니다. 거래된 층수는 21층이며, 전용 면적은 8…

### SL-014

- question: 래미안대치팰리스 최근 1년 최고가 알려줘
- answer: 래미안대치팰리스의 최근 1년 최고가는 655,000만원입니다. 이 거래는 2026년 2월 21일에 이루어졌으며, 해당 아파트의 전용 면적은 151.31㎡이고, 24층에 위치하고 있습니다. 주소는 대치동 1027이며…

### SL-015

- question: 잠실엘스 위치 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이곳은 잠실엘스라는 단지의 주소로, 지도에 표시해 두었습니다. 추가적인 정보가 필요하시면 언제든지 말씀해 주세요. 제공된 데이터 기준입니다.

### SL-016

- question: 잠실엘스 어디 있어?
- answer: 잠실엘스는 잠실동 19에 위치해 있습니다. 이 단지는 잠실 지역에서 잘 알려진 아파트 단지입니다. 지도에 표시해 두었으니, 위치를 확인하시기 편리할 것입니다. 추가적인 정보가 필요하시면 언제든지 말씀해 주세요. 제…

### SL-017

- question: 잠실 엘스 시세 알려줘
- answer: 잠실 엘스의 최근 시세는 약 283,000만원에서 330,000만원 사이입니다. 최근 거래 내역을 살펴보면, 2026년 5월 26일에 330,000만원에 거래된 매물이 있으며, 5월 25일에는 328,000만원에 …

### SL-018

- question: 래미안대치팰리스 최근 실거래가 알려줘
- answer: 래미안대치팰리스의 최근 실거래가는 다음과 같습니다. 2026년 6월 3일에 440,000만원에 거래된 84.97㎡ 규모의 아파트가 있으며, 15층에 위치하고 있습니다. 또한, 2026년 5월 27일에는 570,00…

### SL-019

- question: 래미안대치팰리스 가장 최근 실거래가 알려줘
- answer: 래미안대치팰리스의 가장 최근 실거래가는 2026년 6월 3일에 440,000만원입니다. 이 거래는 15층의 84.97㎡ 면적을 가진 아파트에서 이루어졌습니다. 최근 거래 내역을 살펴보면, 2026년 5월 27일에는…

### SL-020

- question: 래미안대치팰리스 최근 거래 3건 보여줘
- answer: 래미안대치팰리스의 최근 거래 내역은 다음과 같습니다. 첫 번째 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원으로, 전용 면적은 84.97㎡, 15층에서 거래되었습니다. 두 번째 거래는 …

### SL-021

- question: 래미안대치팰리스 전용 84㎡ 얼마야
- answer: 래미안대치팰리스 전용 84㎡의 최근 거래가는 440,000만원입니다. 이 거래는 2026년 6월 3일에 이루어졌습니다. 최근 몇 개월 동안의 거래 내역을 살펴보면, 2026년 5월 8일에는 430,000만원, 20…

### RC-001

- question: 가락시장역 근처의 아파트를 추천해줘
- answer: 가락시장역 근처의 아파트로 "동부썬빌"을 추천드립니다. 이 아파트는 가락동 99-6에 위치하며, 최근 거래가는 10.5억원입니다. 가락시장역까지의 거리는 약 128m로 매우 가까워 교통이 편리합니다. 또한, "두산…

### RC-002

- question: 가락시장역 300m 안에 있는 아파트 3개 추천해줘
- answer: 가락시장역 300m 안에 있는 아파트로는 "동부썬빌", "두산위브", "이연파레스"를 추천드립니다. 첫 번째로 "동부썬빌"은 가락동 99-6에 위치하며, 최근 거래가는 10.5억원입니다. 이 아파트는 가락시장역에서…

### RC-003

- question: 잠실역 근처 아파트 추천해줘
- answer: 잠실역 근처 아파트로는 "롯데캐슬골드", "잠실시그마타워", "현대타워"를 추천드립니다. 이 아파트들은 모두 잠실역에서 가까운 거리에 위치해 있습니다. 첫 번째로, "롯데캐슬골드"는 신천동 7-18에 위치하며, 최…

### RC-004

- question: 강남구 30억 이하 아파트 추천해줘
- answer: 강남구에서 30억 이하 아파트로 추천할 만한 단지는 여러 곳이 있습니다. 첫 번째로, 역삼동 828-21에 위치한 아파트는 9.1억원에 거래되었으며, 2005년에 사용 승인되었습니다. 이 아파트는 강남역에서 약 4…

### RC-005

- question: 서초구 신축 아파트 추천해줘
- answer: 서초구에서 신축 아파트로 추천할 만한 단지는 여러 곳이 있습니다. 첫 번째로 "DK밸리뷰서초2차"는 서초동 1597-6에 위치하며, 2022년 6월 29일에 사용 승인을 받았습니다. 이 아파트는 5.0억원에 거래된…

### RC-006

- question: 1000세대 이상 대단지 추천해줘
- answer: 1000세대 이상의 대단지로는 "e편한세상송파파크센트럴", "가락(1차)쌍용아파트", "강남자곡 힐스테이트", "강남한양수자인(4단지)", "개포래미안포레스트"를 추천드립니다. 첫 번째로 "e편한세상송파파크센트럴"…

### RC-007

- question: 30평 이상 아파트 추천해줘
- answer: 30평 이상의 아파트로는 여러 후보가 있습니다. 첫 번째로 추천드리는 아파트는 서초동 1617-21에 위치한 "(1617-21)"입니다. 이 아파트는 33.63평으로, 최근 거래가는 12.7억원입니다. 두 번째는 …

### RC-008

- question: 4인 가족이 살 수 있는 집을 추천해줘
- answer: 4인 가족이 살 수 있는 집으로 두 곳을 추천드립니다. 첫 번째는 서초동 1617-21에 위치한 아파트로, 면적은 111.16㎡(약 33.63평)이며, 최근 거래가는 12.7억원입니다. 이 아파트는 2007년에 사…

### RC-009

- question: 30억 예산 아파트 추천해줘
- answer: 30억 예산에 맞는 아파트를 추천드립니다. 추천된 아파트는 다음과 같습니다. 첫 번째는 서초동 1617-21에 위치한 아파트로, 최근 거래가는 12.7억원이며, 2007년에 사용 승인이 되었습니다. 이 아파트는 남…

### RC-010

- question: 500세대 이상 아파트 추천해줘
- answer: 500세대 이상의 아파트로는 "2차한양아파트", "e-편한세상", "e편한세상송파파크센트럴"을 추천드립니다. 먼저, "2차한양아파트"는 송파동 151에 위치하며, 744세대로 1984년에 준공되었습니다. 최근 거래…

### RC-011

- question: 서초역 근처 아파트 알려줘
- answer: 서초역 근처의 아파트로는 "동일하이빌", "블루힐65", "현대ESA2" 등이 있습니다. 이 아파트들은 서초역에서 가까운 거리에 위치해 있습니다. "동일하이빌"은 서초동 1542-1에 위치하며, 서초역까지의 거리는…

### RC-012

- question: 신축 아파트 추천해줘
- answer: 신축 아파트로 "DK밸리뷰서초2차"를 추천드립니다. 이 아파트는 서초동에 위치하며, 2022년 6월 29일에 사용 승인을 받았습니다. 최근 거래가는 5.0억원이며, 전용 면적은 28.58㎡(약 8.65평)입니다. …

### RC-013

- question: 초등학교 근처 아파트 추천해줘
- answer: 조건에 맞는 역/교육시설을 찾지 못했습니다. 가격, 지역, 역/학교 반경 같은 조건을 조금 완화해 보세요.

### RC-014

- question: 25평 이상 아파트 싼 곳 추천해줘
- answer: 25평 이상의 아파트 중에서 저렴한 곳으로 "금성리치몬드"를 추천드립니다. 이 아파트는 오금동 85-4에 위치하며, 최근 거래가는 3.0억원입니다. 면적은 25.33평으로, 2003년에 사용 승인이 났습니다. 또한…

### RC-015

- question: 서초역 근처 30억 이하 신축 아파트 추천해줘
- answer: 서초역 근처 30억 이하의 신축 아파트로는 "서초아트래디앙", "서초센트럴IPARK", "DK밸리뷰서초2차", "루컴즈힐서리풀"이 추천됩니다. 첫 번째로 "서초아트래디앙"은 서초동 1582-16에 위치하며, 202…

### RC-016

- question: 잠실 근처 10억 이하 아파트 추천해줘
- answer: 잠실 근처 10억 이하 아파트로 "트윈레이크팰리스빌", "현대레이크빌1", "신동아타워"를 추천드립니다. 첫 번째로 "트윈레이크팰리스빌"은 송파동 58-15에 위치하며, 최근 거래가는 3.6억원입니다. 이 아파트는…

### RC-017

- question: 강남구에 있는 아파트 3개를 추천해주고 그 이유를 알려줘
- answer: 강남구에 있는 아파트로 세 곳을 추천드립니다. 첫 번째는 역삼동 828-21에 위치한 아파트로, 최근 거래가는 9.1억원이며, 2005년에 사용 승인되었습니다. 강남역과의 거리는 약 441.5m로, 서울역삼초등학교…

### RC-018

- question: 초/중/고 가까운 강남구 아파트 3개 추천해줘
- answer: 강남구에서 초, 중, 고등학교와 가까운 아파트 3개를 추천드립니다. 첫 번째는 "도곡우성아파트"로, 도곡동 934-10에 위치하며, 최근 거래가는 27.5억원입니다. 이 아파트는 서울언주초등학교와 155m, 은성중…

### RC-019

- question: 서초구 20억 이하 저렴한 아파트 4곳 추천해줘
- answer: 서초구에서 20억 이하의 저렴한 아파트 4곳을 추천드립니다. 첫 번째는 "서초중흥S-클래스(도시형생활주택)"로, 주소는 서초동 1451-89이며, 최근 거래가는 1.9억원입니다. 두 번째는 "동아아파트"로, 서초동…

### RC-020

- question: 청담역 주변 비싼 아파트 3개 추천해줘
- answer: 청담역 주변에서 비싼 아파트 3개를 추천드립니다. 첫 번째는 "PH129"로, 청담동 129에 위치하며, 최근 거래가는 155.0억원입니다. 이 아파트는 청담역에서 약 746.55m 거리에 있습니다. 두 번째는 "…

### RC-021

- question: 송파구 30억 이하 아파트 추천해줘
- answer: 송파구에서 30억 이하 아파트로 추천할 만한 단지는 다음과 같습니다. 첫 번째로, 방이동 185-5에 위치한 "(185-5)" 아파트가 있습니다. 이 아파트는 14.7억원에 거래되었으며, 전용 면적은 133.78㎡…

### RC-022

- question: 서초역 근처 아파트 추천해줘
- answer: 서초역 근처의 아파트로 "동일하이빌", "블루힐65", "현대ESA2"를 추천드립니다. 이 아파트들은 서초역에서 가까운 거리에 위치해 있어 교통이 편리합니다. 먼저, 동일하이빌은 서초동 1542-1에 위치하며, 1…

### CP-001

- question: 래미안대치팰리스와 반포자이 비교해줘
- answer: 래미안대치팰리스와 반포자이를 비교해보면, 두 아파트의 가격과 특성이 다소 차이가 있습니다. 래미안대치팰리스의 최근 거래가는 44. 0억원으로, 평수는 25. 7평이며 평당가는 약 1. 7억원입니다. 이 아파트는 2…

### CP-002

- question: 래미안대치팰리스랑 압구정현대 가격 비교해줘
- answer: 일부 아파트를 찾지 못했습니다: 압구정현대 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-003

- question: 동부썬빌이랑 두산위브 가격이랑 학교 거리 비교해줘
- answer: 일부 아파트를 찾지 못했습니다: 두산위브 거리 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-004

- question: 성원상떼빌과 롯데캐슬 세대수랑 연식 비교해줘
- answer: 성원상떼빌과 롯데캐슬의 세대수와 연식을 비교해보면, 성원상떼빌은 324세대, 2006년에 준공되었습니다. 반면 롯데캐슬은 142세대이며, 2002년에 준공되었습니다. 따라서 성원상떼빌이 더 많은 세대를 보유하고 있…

### CP-005

- question: 잠실엘스랑 반포자이 교통 비교해줘
- answer: 잠실엘스와 반포자이의 교통을 비교해보면, 반포자이가 더 가까운 역에 위치해 있습니다. 잠실엘스는 종합운동장역까지 약 403.7m 거리에 있으며, 이 역은 9호선(연장)입니다. 반면, 반포자이는 반포역까지 약 212…

### CP-006

- question: 래미안대치팰리스와 반포자이 상권 비교해줘
- answer: 래미안대치팰리스와 반포자이의 상권을 비교해보면, 두 단지의 위치와 인근 시설에서 차이를 보입니다. 래미안대치팰리스는 대치동 1027에 위치하며, 가장 가까운 역은 도곡역으로 약 401.91m 거리에 있습니다. 인근…

### CP-007

- question: 래미안대치팰리스 비교해줘
- answer: 비교할 아파트명을 2개 이상 입력해야 합니다.

### CP-008

- question: 래미안대치팰리스랑 잠실엘스 비교해줘
- answer: 래미안대치팰리스와 잠실엘스를 비교해보면, 두 단지의 가격과 특성이 다릅니다. 래미안대치팰리스의 최근 거래가는 44. 0억원으로, 평수는 25. 7평이며 평당가는 약 1. 7억원입니다. 이 단지는 2015년에 준공되…

### CP-009

- question: 래미안대치팰리스와 잠실엘스 가격 비교해줘
- answer: 래미안대치팰리스와 잠실엘스의 가격을 비교해보면, 래미안대치팰리스의 최근 거래가는 44.0억원이며, 평당가는 약 1.7억원입니다. 반면, 잠실엘스는 최근 거래가가 33.0억원이고, 평당가는 약 1.3억원입니다. 두 …

### CP-010

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 더 신축이야?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 더 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-011

- question: 래미안대치팰리스랑 잠실엘스 세대수랑 가격 비교해줘
- answer: 래미안대치팰리스와 잠실엘스의 세대수와 가격을 비교해보면 다음과 같습니다. 래미안대치팰리스는 1,608세대로, 최근 거래가는 44.0억원이며 평당가는 약 1.7억원입니다. 반면, 잠실엘스는 5,678세대로, 최근 거…

### CP-012

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 초등학교에 가까워?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 에 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-013

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 역이 더 가까워?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 역이 더 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-014

- question: 래미안대치팰리스랑 잠실엘스 가격 비교해줘
- answer: 래미안대치팰리스와 잠실엘스의 가격을 비교해보면, 래미안대치팰리스의 최근 거래가는 44.0억원이며, 평당가는 약 1.7억원입니다. 반면, 잠실엘스는 최근 거래가가 33.0억원이고, 평당가는 약 1.3억원입니다. 두 …

### CP-015

- question: 반포자이랑 래미안퍼스티지 초등학교 접근성 비교해줘
- answer: 반포자이와 래미안퍼스티지의 초등학교 접근성을 비교해보면, 반포자이는 서울원촌초등학교와의 거리가 약 149m로 더 가까운 반면, 래미안퍼스티지는 서울잠원초등학교까지 약 252m 떨어져 있습니다. 반포자이는 반포동 2…

### CP-016

- question: 아크로리버파크랑 래미안원펜타스 가격이랑 평당가 비교해줘
- answer: 아크로리버파크와 래미안원펜타스의 가격과 평당가를 비교해보면, 아크로리버파크의 최근 거래가는 71.0억원이며, 평당가는 1.8억원입니다. 반면, 래미안원펜타스는 최근 거래가가 100.0억원이고, 평당가는 1.7억원입…

### CP-017

- question: 도곡렉슬이랑 대치현대 어디가 더 대단지야 비교해줘
- answer: 도곡렉슬이 대치현대보다 더 대단지입니다. 도곡렉슬은 3,002세대로, 대치현대의 630세대에 비해 훨씬 많은 세대를 보유하고 있습니다. 도곡렉슬은 도곡동 527에 위치하며, 가까운 생활 편의시설로는 강남세브란스병원…

### CP-018

- question: 잠실엘스랑 리센츠 상권 학군 미래 가격 전망 비교해줘
- answer: 잠실엘스와 리센츠의 상권과 학군을 비교해보면, 두 아파트의 최근 거래 가격과 평당가에서 차이가 있습니다. 잠실엘스의 최근 거래가는 33.0억원이며, 평당가는 약 1.3억원입니다. 반면 리센츠는 35.9억원으로, 평…

### CP-019

- question: 잠실엘스랑 리센츠 재개발 전망 비교해줘
- answer: 잠실엘스와 리센츠의 비교 결과는 다음과 같습니다. 잠실엘스의 최근 거래가는 33.0억원이며, 평형은 25.65평, 평당가는 1.3억원입니다. 이 아파트는 2008년에 준공되었고, 종합운동장역에서 약 403.7m 거…

### PT-001

- question: 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스의 시세 추이에 대한 정보는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 약 314,500만원에서 시작하여, 2026년 1월에는 349,500만원으로 증가했습니다. 이 기간 …

### PT-002

- question: 잠실엘스 최근 1년 가격 흐름 보여줘
- answer: 잠실엘스의 최근 1년 가격 흐름은 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 314,500만원에서 시작하여 355,000만원까지 상승했습니다. 특히 2026년 1월에는 349,50…

### PT-003

- question: 잠실엘스 34평 시세 추이 알려줘
- answer: 잠실엘스 34평의 시세 추이에 대한 정보입니다. 2025년 6월부터 2026년 6월까지의 평균 거래 금액은 약 314,500만원에서 시작하여, 2026년 5월에는 약 315,765만원으로 소폭 변동했습니다. 이 기…

### PT-004

- question: 잠실엘스 최근 1년 시세 추이 알려줘
- answer: 잠실엘스의 최근 1년 시세 추이는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 314,500만원에서 시작하여 379,500만원까지 상승한 후, 다시 313,600만원으로 하락하는 흐…

### PT-005

- question: 강남구 시세 추이 알려줘
- answer: 강남구의 시세 추이에 대한 정보는 다음과 같습니다. 2025년 6월부터 2026년 6월까지의 평균 거래 금액은 316,810.86만원에서 시작하여, 2026년 5월에는 293,692.91만원으로 변화했습니다. 평균…

### PT-006

- question: 서초구 최근 1년 가격 흐름 보여줘
- answer: 서초구의 최근 1년 가격 흐름은 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 299,832.76만원에서 시작하여, 2025년 7월에는 282,682.76만원으로 감소한 후, 2025…

### PT-007

- question: 강남 3구 시세 추이 알려줘
- answer: 강남 3구의 시세 추이에 대한 정보는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 261,457.79만원에서 시작하여, 259,403.77만원으로 마무리되었습니다. 이 기간 동안 평…

### PT-008

- question: 강남구 최근 1년 시세 추이 알려줘
- answer: 강남구의 최근 1년 시세 추이는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 316,810.86만원에서 시작하여, 2026년 5월에는 293,692.91만원으로 변화했습니다. 이 기…

### PT-009

- question: 강남 3구 최근 1년 시세 추이 알려줘
- answer: 강남 3구의 최근 1년 시세 추이는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 261,457.79만원에서 시작하여 259,403.77만원으로 변화했습니다. 이 기간 동안 평균 가격…

### PT-010

- question: 최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘
- answer: 최근 1년 동안 강남구에서 가장 많이 오른 아파트 TOP 5는 다음과 같습니다. 1위는 **상지리츠빌카일룸(65-4)**로, 삼성동에 위치하며, 시작 가격이 2,523. 77만원/㎡에서 4,466. 57만원/㎡로 …

### PT-011

- question: 최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘
- answer: 최근 1년 동안 서초구에서 많이 내린 아파트 5곳은 다음과 같습니다. 1. **라인** (반포동 70): 시작 가격 3,051. 53만원/㎡에서 1,649. 19만원/㎡로 하락, 변화율 -45. 96%. 2. **…

### PT-012

- question: 강남구 상승률 높은 아파트 알려줘
- answer: 강남구에서 상승률이 높은 아파트는 상지리츠빌카일룸(65-4)입니다. 이 아파트는 2025년 8월부터 9월 사이에 ㎡당 가격이 2,523.77만원에서 4,466.57만원으로 상승하여, 무려 76.98%의 상승률을 기…

### PT-013

- question: 강남구 최고가 아파트 TOP 5 알려줘
- answer: 강남구의 최고가 아파트 TOP 3는 다음과 같습니다. 첫 번째는 "에테르노청담"으로, 청담동 106-7에 위치하며, 최근 거래가 2억 1800만원, 전용 면적은 231.28㎡입니다. 두 번째는 "PH129"로, 청…

### PT-014

- question: 서초구에서 가장 비싼 아파트 보여줘
- answer: 서초구에서 가장 비싼 아파트는 "아크로리버파크"입니다. 이 아파트는 반포동 2-12에 위치하고 있으며, 최근 거래가 1억 8천만 원에 이루어졌습니다. 면적은 234.91㎡이고, 거래일자는 2024년 8월 5일입니다…

### PT-015

- question: 송파구 최저가 아파트 5곳 알려줘
- answer: 송파구에서 최저가 아파트 5곳은 다음과 같습니다. 1. **오금그랑밸리** - 오금동 71-8, 2023년 2월 21일 거래, 11,000만원, 전용면적 36.48㎡, 평당가 301.54만원, 7층. 2. **동부…

### PT-016

- question: 최근 1년 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스의 최근 1년 시세 추이는 다음과 같습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 약 314,500만원에서 379,500만원까지 변동하며, 평균 평당가는 3,994.51만원에서 4,42…

### LC-001

- question: 30억 아파트 매매 시 알아야 할 법률이 있을까?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-002

- question: 아파트 매매 시 세금 책정 관련 법을 알려줘.
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-003

- question: 매매 계약서에서 중요하게 볼 부분은 어디야?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-004

- question: 집을 살 때 알아야 할 법이 있을까?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-005

- question: 아파트 매매계약 후 신고해야 하는 게 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-006

- question: 세입자 있는 집을 사도 괜찮아?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-007

- question: 명의 이전은 어떤 법과 관련 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-008

- question: 계약금을 냈는데 계약을 취소할 수 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-009

- question: 부모님이 돈을 보태주면 문제가 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-010

- question: 등기부에서 빚 잡힌 집인지 보려면 뭘 봐야 해?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-011

- question: 아파트 매매계약은 법적으로 언제 성립해?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-012

- question: 매도인이 계약금을 받았는데 계약을 해제하려면 어떻게 해야 해?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-013

- question: 부동산 거래 신고는 누가 해야 해?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-014

- question: 공인중개사가 거래계약서를 거짓으로 작성하면 안 된다는 법이 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-015

- question: 부동산 등기부에는 어떤 권리를 등기할 수 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-016

- question: 소유권 이전등기는 어떤 법과 관련 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-017

- question: 토지거래허가구역에서 집을 사려면 허가가 필요해?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-018

- question: 부동산 거래 신고필증은 등기와 어떤 관련이 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-019

- question: 매매대금을 지급하기로 한 계약도 매매로 볼 수 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-020

- question: 아파트 구분소유자는 집합건물법과 관련이 있어?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-021

- question: 계약금을 돌려받을 수 있나요?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-022

- question: 매매 계약금 해제 규정 알려줘
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-023

- question: 아파트 살 때 계약 전에 꼭 확인해야 할 법적 사항은 뭐야
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-024

- question: 집값을 실제보다 낮게 계약서에 쓰면 문제가 있어
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-025

- question: 전세 낀 아파트를 사면 보증금은 누가 돌려줘야 해
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### LC-026

- question: 매매 계약 법률 알려줘
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### MX-FR-001

- question: 잠실엘스 위치 알려줘 그리고 매매 계약 법률 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이곳은 잠실엘스라는 단지 이름으로도 알려져 있습니다. 지도에 표시해 두었으니 참고하시기 바랍니다. 하지만 매매 계약에 관한 법률에 대한 정보는 제공된 데이터로는 확인할 수 없습…

### MX-FR-002

- question: 잠실엘스 위치 알려줘 그리고 오늘 날씨 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이 정보는 잠실엘스에 대한 단지 조회 결과로 확인되었습니다. 지도에 표시해 두었으니, 필요하시면 지도를 통해 위치를 확인하실 수 있습니다. 오늘 날씨에 대한 정보는 제공되지 않…

### MX-IN-001

- question: 강남구 아파트 추천하고 최근 시세 추이도 알려줘
- answer: 강남구에서 추천할 만한 아파트는 여러 곳이 있습니다. 첫 번째로, 역삼동 828-21에 위치한 아파트는 9.1억원에 거래되었으며, 강남역과 가까운 거리에 있습니다. 두 번째로, 역삼동 828-76에 위치한 아파트는…

### MX-IN-002

- question: 강남구 아파트 추천하고 매매 계약 법령도 알려줘
- answer: 강남구에서 추천할 아파트는 여러 곳이 있습니다. 첫 번째로, 역삼동 828-21에 위치한 아파트는 9.1억원에 거래되었으며, 2005년에 사용 승인되었습니다. 이 아파트는 강남역에서 약 441.5m 거리에 위치하고…

### MX-IN-003

- question: 잠실엘스 위치랑 매매 계약 법률 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이 단지는 지도에 표시되었습니다. 하지만 매매 계약에 관한 법률 정보는 제공된 데이터로는 확인할 수 없습니다. 관련 법령 근거를 찾지 못해 구체적인 내용을 안내해 드릴 수 없는…

### MX-IN-004

- question: 래미안대치팰리스랑 잠실엘스 비교하고 계약 시 주의할 법도 알려줘
- answer: 래미안대치팰리스와 잠실엘스를 비교한 결과는 다음과 같습니다. 래미안대치팰리스의 최근 거래가는 44.0억원이며, 평형은 25.7평, 평당가는 약 1.7억원입니다. 이 단지는 2015년에 준공되었고, 도곡역에서 약 4…

### MX-DP-001

- question: 강남구 아파트 추천하고 후보 비교도 해줘
- answer: 강남구에서 추천할 만한 아파트는 "(828-21)", "(828-76)", 그리고 "Bienestar"입니다. 먼저, "(828-21)"은 역삼동 828-21에 위치하며, 최근 거래가는 9. 1억원이고, 전용 면적…

### MX-AM-001

- question: 잠실엘스 시세 알려줘
- answer: 잠실엘스의 최근 시세는 2026년 5월 기준으로 330,000만원입니다. 이 외에도 2026년 5월 25일에는 328,000만원, 2026년 5월 22일에는 283,000만원에 거래된 기록이 있습니다. 최근 1년간…

### MX-AM-002

- question: 잠실엘스 최근 실거래가 알려줘
- answer: 잠실엘스의 최근 실거래가는 다음과 같습니다. 가장 최근 거래는 2026년 5월 26일에 이루어진 것으로, 거래 금액은 330,000만원이며, 전용 면적은 84.8㎡이고 23층에 위치해 있습니다. 그 이전 거래는 2…

### MX-AM-003

- question: 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스의 시세 추이는 최근 12개월 동안의 거래 데이터를 기반으로 분석되었습니다. 2025년 6월부터 2026년 5월까지의 평균 거래 금액은 314,500만원에서 시작하여 346,250만원, 379,500만원 등…

### MX-ST-001

- question: 강남구 시세추이랑 송파구 시세추이 알려줘
- answer: 강남구와 송파구의 시세 추이를 알려드리겠습니다. 강남구의 평균 거래 금액은 2025년 6월부터 2026년 5월까지의 기간 동안 316,810.86만원에서 시작하여 293,692.91만원으로 변화했습니다. 평균 평당…

### MX-LLM-002

- question: 송파구 30억 이하 추천하고 추천 후보들 가격 흐름도 알려줘
- answer: 송파구에서 30억 이하의 아파트 추천 후보는 다음과 같습니다. 첫 번째는 방이동 185-5에 위치한 "(185-5)" 아파트로, 최근 거래가는 14.7억원입니다. 두 번째는 송파동 151에 있는 "2차한양아파트"로…

### MX-DD-001

- question: 잠실엘스 위치랑 잠실엘스 위치 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이 단지는 서울 송파구에 위치하고 있습니다. 지도에 표시해 두었으니, 필요하시면 잠실엘스의 지도를 확인해 보세요. 제공된 데이터 기준입니다.

### UB-001

- question: 오늘 날씨 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### UB-002

- question: 부동산 후보 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### UB-003

- question: 래미안대치팰리스 근처 학교 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### UB-004

- question: 부동산 매매와 관련 없는 질문
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### UB-005

- question: 서울 아파트 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### UB-006

- question: 신고가 TOP 5 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### RV-001

- question: 잠실엘스 위치 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이곳은 잠실엘스라는 이름으로도 알려져 있으며, 지도에 표시해 두었습니다. 추가적인 정보가 필요하시면 언제든지 말씀해 주세요. 제공된 데이터 기준입니다.

### RV-002

- question: 잠실 엘스 위치 알려줘
- answer: 잠실 엘스의 위치는 잠실동 19입니다. 이 단지는 잠실 지역에 위치하고 있으며, 지도에 표시했습니다. 추가적인 정보가 필요하시면 말씀해 주세요. 제공된 데이터 기준입니다.

### RV-003

- question: 래미안 대치 팰리스 위치 알려줘
- answer: 래미안 대치 팰리스의 위치는 대치동 1027입니다. 이 단지는 대치동에 위치하고 있으며, 지도에 표시했습니다. 추가적인 정보가 필요하시면 언제든지 질문해 주세요. 제공된 데이터 기준입니다.

### RV-004

- question: 래미안대치팰리스 전용84㎡ 최근 실거래 알려줘
- answer: 래미안대치팰리스 전용 84㎡의 최근 실거래 내역은 다음과 같습니다. 가장 최근 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000,000원이었습니다. 이 거래는 15층에서 이루어졌고, 전용 면적…

### RV-005

- question: 래미안대치팰리스 전용 84 제곱미터 최근 실거래 알려줘
- answer: 래미안대치팰리스 전용 84 제곱미터의 최근 실거래 내역은 다음과 같습니다. 가장 최근 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원입니다. 이 거래는 15층에서 이루어졌고, 전용 면적은…

### RV-006

- question: 래미안대치팰리스 실거래 최근 3건 보여줘
- answer: 래미안대치팰리스의 최근 3건 실거래 내역은 다음과 같습니다. 첫 번째 거래는 2026년 6월 3일에 이루어졌으며, 거래 금액은 440,000만원으로, 전용 면적은 84.97㎡, 층수는 15층입니다. 두 번째 거래는…

### RV-007

- question: 최근 실거래가 래미안대치팰리스 알려줘
- answer: 래미안대치팰리스의 최근 실거래가는 다음과 같습니다. 2026년 6월 3일에 440,000만원에 거래된 84.97㎡ 규모의 아파트가 있으며, 15층에 위치하고 있습니다. 또한, 2026년 5월 27일에는 570,00…

### RV-008

- question: 잠실엘스 34 평 시세 추이 알려줘
- answer: 잠실엘스 34평의 시세 추이에 대한 정보는 다음과 같습니다. 2025년 6월부터 2026년 6월까지의 평균 거래 금액은 월별로 변동이 있었으며, 2025년 6월에는 평균 314,500만원에서 시작하여 2026년 5…

### RV-009

- question: 최근 1년 시세 추이 잠실엘스 알려줘
- answer: 처리하지 못한 이유는 target_not_found입니다.

### RV-010

- question: 잠실엘스 가격 흐름 1년치 보여줘
- answer: 잠실엘스의 가격 흐름을 1년치로 확인할 수 있습니다. 최근 1년 동안의 평균 거래 금액은 월별로 변동이 있었으며, 2025년 6월부터 2026년 5월까지의 데이터를 기준으로 합니다. 2025년 6월의 평균 거래 금…

### RV-011

- question: 송파구에서 30억 밑으로 아파트 추천해줘
- answer: 송파구에서 30억 이하의 아파트를 추천드립니다. 추천하는 아파트는 다음과 같습니다. 1. **(185-5)**: 방이동 185-5에 위치하며, 최근 거래가는 14. 7억원입니다. 이 아파트는 2002년에 사용 승인…

### RV-012

- question: 서초구 20억 이하인 저렴한 단지 4곳 추천해줘
- answer: 서초구에서 20억 이하의 저렴한 단지 4곳을 추천드립니다. 첫 번째는 "서초중흥S-클래스(도시형생활주택)"로, 주소는 서초동 1451-89이며, 최근 거래가는 1.9억원입니다. 두 번째는 "동아아파트"로, 서초동 …

### RV-013

- question: 서초역에서 가까운 서초구 아파트 추천해줘
- answer: 서초역에서 가까운 서초구 아파트로는 "APELBAUM", "DK밸리뷰서초2차", "N-Suite(1513-2)"를 추천드립니다. "APELBAUM"은 반포동에 위치하며, 최근 거래가는 24. 8억원입니다. 서초역까…

### RV-014

- question: 래미안대치팰리스와 잠실엘스 시세 비교해줘
- answer: 래미안대치팰리스와 잠실엘스의 시세를 비교해보면, 래미안대치팰리스의 최근 거래가는 44.0억원이며, 평당가는 약 1.7억원입니다. 반면, 잠실엘스는 최근 거래가가 33.0억원이고, 평당가는 약 1.3억원입니다. 두 …

### RV-015

- question: 잠실엘스랑 리센츠 역 접근성 비교해줘
- answer: 잠실엘스와 리센츠의 역 접근성을 비교해보면, 리센츠가 더 가까운 역에 위치해 있습니다. 잠실엘스는 종합운동장역까지 약 403.7m 거리에 있으며, 이 역은 9호선(연장)입니다. 반면, 리센츠는 잠실새내역까지 약 2…

### RV-016

- question: 래미안대치팰리스랑잠실엘스가격비교해줘
- answer: 래미안대치팰리스와 잠실엘스의 가격을 비교해보면, 래미안대치팰리스의 최근 거래가는 44.0억원이며, 평당가는 약 1.7억원입니다. 반면, 잠실엘스는 최근 거래가 33.0억원이고, 평당가는 약 1.3억원입니다. 두 단…

### RV-017

- question: 계약 해제하려면 계약금은 어떻게 돼?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### RV-018

- question: 전세 세입자 있는 아파트 사면 보증금은 누가 돌려줘?
- answer: 질문과 관련된 법령 근거를 찾지 못했습니다.

### RV-019

- question: 잠실엘스 위치랑 매매계약 법률 같이 알려줘
- answer: 잠실엘스의 위치는 잠실동 19입니다. 이 정보는 성공적으로 확인되었습니다. 하지만 매매계약에 관한 법률 정보는 제공된 데이터에서 확인할 수 없었습니다. 매매계약에 관련된 주요 용어로는 매매의 의의, 대금 지급, 계…

### RV-020

- question: 강남구 아파트 추천 후 최근 1년 가격 흐름도 알려줘
- answer: 강남구 아파트 추천과 최근 1년 가격 흐름을 알려드리겠습니다. 추천 아파트로는 역삼동에 위치한 "(828-21)"이 있으며, 9.1억원에 거래된 최신 정보가 있습니다. 이 아파트는 2005년에 사용 승인되었고, 면…

### RV-021

- question: 래미안대치팰리스 실거래랑 계약금 해제 법 알려줘
- answer: 래미안대치팰리스의 최근 실거래 정보는 확인할 수 있습니다. 최근 거래로는 2026년 6월 3일에 440,000만원에 거래된 84.97㎡ 규모의 매물이 있으며, 15층에 위치하고 있습니다. 또한, 2026년 5월 2…
