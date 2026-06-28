# Chatbot QA Results - 2026-06-29

## Summary

- mode: live_llm
- total: 144
- passed: 143
- failed: 1

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
| MX-LLM-002 | chatbot.qa.aggregate.supervisor | FAIL | supervisor_aggregate | supervisor_execution_failed | recommendation, price_trend | - | Y | Y | plan expected one of ('supervisor_llm',), got [] |
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
- answer: 래미안대치팰리스 위치는 대치동 1027입니다. 좌표는 위도 37.4941149, 경도 127.0579205입니다.

### SL-002

- question: 래미안대치팰리스 위치 알려줘
- answer: 래미안대치팰리스 위치는 대치동 1027입니다. 좌표는 위도 37.4941149, 경도 127.0579205입니다.

### SL-003

- question: 래미안대치팰리스 주소 알려줘
- answer: 래미안대치팰리스 위치는 대치동 1027입니다. 좌표는 위도 37.4941149, 경도 127.0579205입니다.

### SL-004

- question: 래미안대치팰리스 최근 거래 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-005

- question: 래미안대치팰리스 최근 5건 보여줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-006

- question: 래미안대치팰리스 전용 84㎡ 최근 실거래 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-08 43.0억원 전용 84.99㎡ 15층, 2026-05-07 42.5억원 전용 84.97㎡ 13층입니다.

### SL-007

- question: 래미안대치팰리스 최근 1년 거래 내역 보여줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-008

- question: 래미안대치팰리스 전용 84㎡ 최근 1년 실거래 5건 보여줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-08 43.0억원 전용 84.99㎡ 15층, 2026-05-07 42.5억원 전용 84.97㎡ 13층입니다.

### SL-009

- question: 래미안대치팰리스 얼마야?
- answer: 1. 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입…

### SL-010

- question: 래미안대치팰리스 가격 알려줘
- answer: 1. 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입…

### SL-011

- question: 래미안대치팰리스 최고가 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-02-21 65.5억원 전용 151.31㎡ 24층입니다.

### SL-012

- question: 래미안대치팰리스 가장 비싼 거래 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-02-21 65.5억원 전용 151.31㎡ 24층입니다.

### SL-013

- question: 래미안대치팰리스 전용 84㎡ 최고가는 얼마야?
- answer: 래미안대치팰리스 실거래 내역은 2025-11-06 47.5억원 전용 84.97㎡ 21층입니다.

### SL-014

- question: 래미안대치팰리스 최근 1년 최고가 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-02-21 65.5억원 전용 151.31㎡ 24층입니다.

### SL-015

- question: 잠실엘스 위치 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

### SL-016

- question: 잠실엘스 어디 있어?
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

### SL-017

- question: 잠실 엘스 시세 알려줘
- answer: 1. 잠실엘스 실거래 내역은 2026-05-26 33.0억원 전용 84.8㎡ 23층, 2026-05-25 32.8억원 전용 84.8㎡ 12층, 2026-05-22 28.3억원 전용 59.96㎡ 18층입니다. 2. …

### SL-018

- question: 래미안대치팰리스 최근 실거래가 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-019

- question: 래미안대치팰리스 가장 최근 실거래가 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-020

- question: 래미안대치팰리스 최근 거래 3건 보여줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### SL-021

- question: 래미안대치팰리스 전용 84㎡ 얼마야
- answer: 1. 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-08 43.0억원 전용 84.99㎡ 15층, 2026-05-07 42.5억원 전용 84.97㎡ 13층입니…

### RC-001

- question: 가락시장역 근처의 아파트를 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 동부썬빌: 최근 거래가 10.5억원, 가까운 역 가락시장역(128m), 가까운 교육시설 서울평화초등학교(335m), 800m 생활편의 롯데마트 …

### RC-002

- question: 가락시장역 300m 안에 있는 아파트 3개 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 동부썬빌: 최근 거래가 10.5억원, 가까운 역 가락시장역(128m), 가까운 교육시설 서울평화초등학교(335m), 800m 생활편의 롯데마트 …

### RC-003

- question: 잠실역 근처 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 롯데캐슬골드: 최근 거래가 27.8억원, 가까운 역 잠실(송파구청)역(144m), 가까운 교육시설 잠실중학교(374m), 800m 생활편의 롯데…

### RC-004

- question: 강남구 30억 이하 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일 20…

### RC-005

- question: 서초구 신축 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. DK밸리뷰서초2차: 최근 거래가 5.0억원, 가까운 역 남부터미널(예술의전당)역(385m), 가까운 교육시설 서울교육대학교부설초등학교(347m)…

### RC-006

- question: 1000세대 이상 대단지 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. e편한세상송파파크센트럴: 최근 거래가 20.6억원, 가까운 역 마천역(262m), 가까운 교육시설 서울마천초등학교(247m), 1199세대, 사…

### RC-007

- question: 30평 이상 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (1617-21): 최근 거래가 12.7억원, 가까운 역 남부터미널(예술의전당)역(509m), 가까운 교육시설 서울서일초등학교(300m), 10…

### RC-008

- question: 4인 가족이 살 수 있는 집을 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (1617-21): 최근 거래가 12.7억원, 가까운 역 남부터미널(예술의전당)역(509m), 가까운 교육시설 서울서일초등학교(300m), 10…

### RC-009

- question: 30억 예산 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (1617-21): 최근 거래가 12.7억원, 가까운 역 남부터미널(예술의전당)역(509m), 가까운 교육시설 서울서일초등학교(300m), 10…

### RC-010

- question: 500세대 이상 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 2차한양아파트: 최근 거래가 25.0억원, 가까운 역 송파나루역(678m), 가까운 교육시설 서울솔방울유치원(108m), 744세대, 사용승인일…

### RC-011

- question: 서초역 근처 아파트 알려줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 동일하이빌: 최근 거래가 16.2억원, 가까운 역 서초역(51m), 가까운 교육시설 서초고등학교(364m), 54세대, 사용승인일 2006-10…

### RC-012

- question: 신축 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. DK밸리뷰서초2차: 최근 거래가 5.0억원, 가까운 역 남부터미널(예술의전당)역(385m), 가까운 교육시설 서울교육대학교부설초등학교(347m)…

### RC-013

- question: 초등학교 근처 아파트 추천해줘
- answer: 조건에 맞는 역/교육시설을 찾지 못했습니다. 가격, 지역, 역/학교 반경 같은 조건을 조금 완화해 보세요.

### RC-014

- question: 25평 이상 아파트 싼 곳 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 금성리치몬드: 최근 거래가 3.0억원, 가까운 역 오금역(655m), 가까운 교육시설 오금고등학교(415m), 12세대, 사용승인일 2003-1…

### RC-015

- question: 서초역 근처 30억 이하 신축 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 서초아트래디앙: 최근 거래가 14.8억원, 가까운 역 남부터미널(예술의전당)역(515m), 가까운 교육시설 서초중학교(207m), 24세대, 사…

### RC-016

- question: 잠실 근처 10억 이하 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 트윈레이크팰리스빌: 최근 거래가 3.6억원, 가까운 역 송파나루역(413m), 가까운 교육시설 서울송파초등학교(515m), 29세대, 사용승인일…

### RC-017

- question: 강남구에 있는 아파트 3개를 추천해주고 그 이유를 알려줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일 20…

### RC-018

- question: 초/중/고 가까운 강남구 아파트 3개 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 도곡우성아파트: 최근 거래가 27.5억원, 가까운 역 양재(서초구청)역(496m), 가까운 교육시설 은성중학교(103m), 390세대, 사용승인…

### RC-019

- question: 서초구 20억 이하 저렴한 아파트 4곳 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 서초중흥S-클래스(도시형생활주택): 최근 거래가 1.9억원, 가까운 역 남부터미널(예술의전당)역(454m), 가까운 교육시설 서울신중초등학교(3…

### RC-020

- question: 청담역 주변 비싼 아파트 3개 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. PH129: 최근 거래가 155.0억원, 가까운 역 청담역(747m), 가까운 교육시설 성요셉유치원(535m), 29세대, 사용승인일 2020-…

### RC-021

- question: 송파구 30억 이하 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (185-5): 최근 거래가 14.7억원, 가까운 역 방이역(216m), 가까운 교육시설 올림픽유치원(430m), 18세대, 사용승인일 2002…

### RC-022

- question: 서초역 근처 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 동일하이빌: 최근 거래가 16.2억원, 가까운 역 서초역(51m), 가까운 교육시설 서초고등학교(364m), 54세대, 사용승인일 2006-10…

### CP-001

- question: 래미안대치팰리스와 반포자이 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대, 2015년 준공, 가까운 역 도곡역(402m), 가까운 학교 단국대학…

### CP-002

- question: 래미안대치팰리스랑 압구정현대 가격 비교해줘
- answer: 일부 아파트를 찾지 못했습니다: 압구정현대 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-003

- question: 동부썬빌이랑 두산위브 가격이랑 학교 거리 비교해줘
- answer: 일부 아파트를 찾지 못했습니다: 두산위브 거리 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-004

- question: 성원상떼빌과 롯데캐슬 세대수랑 연식 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 성원상떼빌: 324세대, 2006년 준공, 800m 생활편의 롯데마트 송파점(428m, 대형마트) - 롯데캐슬: 142세대, 2002년 준공, 800m 생활편의…

### CP-005

- question: 잠실엘스랑 반포자이 교통 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 잠실엘스: 가까운 역 종합운동장역(404m) - 반포자이: 가까운 역 반포역(212m), 800m 생활편의 신세계백화점 강남점(592m, 백화점), 서울성모병원…

### CP-006

- question: 래미안대치팰리스와 반포자이 상권 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 가까운 역 도곡역(402m), 가까운 학교 단국대학교부속소프트웨어고등학교(168m) - 반포자이: 가까운 역 반포역(212m), 가까운 학교 …

### CP-007

- question: 래미안대치팰리스 비교해줘
- answer: 비교할 아파트명을 2개 이상 입력해야 합니다.

### CP-008

- question: 래미안대치팰리스랑 잠실엘스 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대, 2015년 준공, 가까운 역 도곡역(402m), 가까운 학교 단국대학…

### CP-009

- question: 래미안대치팰리스와 잠실엘스 가격 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원 - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원 학군은 평판…

### CP-010

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 더 신축이야?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 더 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-011

- question: 래미안대치팰리스랑 잠실엘스 세대수랑 가격 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대 - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억…

### CP-012

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 초등학교에 가까워?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 에 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-013

- question: 래미안대치팰리스랑 잠실엘스 중 어디가 역이 더 가까워?
- answer: 일부 아파트를 찾지 못했습니다: 잠실엘스 가 역이 더 비교할 아파트 데이터가 부족합니다. 아파트명을 2개 이상 입력해 주세요.

### CP-014

- question: 래미안대치팰리스랑 잠실엘스 가격 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원 - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원 학군은 평판…

### CP-015

- question: 반포자이랑 래미안퍼스티지 초등학교 접근성 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 반포자이: 가까운 학교 서울원촌초등학교(149m), 800m 생활편의 신세계백화점 강남점(592m, 백화점), 서울성모병원(753m, 병원) - 래미안퍼스티지:…

### CP-016

- question: 아크로리버파크랑 래미안원펜타스 가격이랑 평당가 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 아크로리버파크: 최근 거래가 71.0억원, 39.3평, 평당가 1.8억원 - 래미안원펜타스: 최근 거래가 100.0억원, 58.04평, 평당가 1.7억원 학군은…

### CP-017

- question: 도곡렉슬이랑 대치현대 어디가 더 대단지야 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 도곡렉슬: 3002세대, 800m 생활편의 강남세브란스병원(284m, 병원), 이마트 역삼점(645m, 대형마트) - 대치현대: 630세대 학군은 평판이 아니라…

### CP-018

- question: 잠실엘스랑 리센츠 상권 학군 미래 가격 전망 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원, 가까운 역 종합운동장역(404m), 가까운 학교 서울잠일초등학교(212m) - 리센츠: …

### CP-019

- question: 잠실엘스랑 리센츠 재개발 전망 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원, 5678세대, 2008년 준공, 가까운 역 종합운동장역(404m), 가까운 학교 서울잠일…

### PT-001

- question: 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### PT-002

- question: 잠실엘스 최근 1년 가격 흐름 보여줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### PT-003

- question: 잠실엘스 34평 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### PT-004

- question: 잠실엘스 최근 1년 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### PT-005

- question: 강남구 시세 추이 알려줘
- answer: 강남구 시세추이를 조회했습니다. 2025-06-01 평균 31.7억원에서 2026-06-01 평균 31.5억원으로 확인됩니다.

### PT-006

- question: 서초구 최근 1년 가격 흐름 보여줘
- answer: 서초구 시세추이를 조회했습니다. 2025-06-01 평균 30.0억원에서 2026-06-01 평균 25.2억원으로 확인됩니다.

### PT-007

- question: 강남 3구 시세 추이 알려줘
- answer: 강남3구 시세추이를 조회했습니다. 2025-06-01 평균 26.1억원에서 2026-06-01 평균 26.2억원으로 확인됩니다.

### PT-008

- question: 강남구 최근 1년 시세 추이 알려줘
- answer: 강남구 시세추이를 조회했습니다. 2025-06-01 평균 31.7억원에서 2026-06-01 평균 31.5억원으로 확인됩니다.

### PT-009

- question: 강남 3구 최근 1년 시세 추이 알려줘
- answer: 강남3구 시세추이를 조회했습니다. 2025-06-01 평균 26.1억원에서 2026-06-01 평균 26.2억원으로 확인됩니다.

### PT-010

- question: 최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘
- answer: 강남구 가격 변화율 순위는 상지리츠빌카일룸(65-4) 76.98%, 논현한가람빌라트 66.04%, 까치마을 54.06%입니다.

### PT-011

- question: 최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘
- answer: 서초구 가격 변화율 순위는 라인 -45.96%, 밀라텔쉐르빌 -37.87%, 반포훼미리(101동) -28.29%입니다.

### PT-012

- question: 강남구 상승률 높은 아파트 알려줘
- answer: 강남구 가격 변화율 순위는 상지리츠빌카일룸(65-4) 76.98%, 논현한가람빌라트 66.04%, 까치마을 54.06%입니다.

### PT-013

- question: 강남구 최고가 아파트 TOP 5 알려줘
- answer: 조회 결과를 처리했습니다.

### PT-014

- question: 서초구에서 가장 비싼 아파트 보여줘
- answer: 조회 결과를 처리했습니다.

### PT-015

- question: 송파구 최저가 아파트 5곳 알려줘
- answer: 조회 결과를 처리했습니다.

### PT-016

- question: 최근 1년 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### LC-001

- question: 30억 아파트 매매 시 알아야 할 법률이 있을까?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-002

- question: 아파트 매매 시 세금 책정 관련 법을 알려줘.
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-003

- question: 매매 계약서에서 중요하게 볼 부분은 어디야?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-004

- question: 집을 살 때 알아야 할 법이 있을까?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-005

- question: 아파트 매매계약 후 신고해야 하는 게 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-006

- question: 세입자 있는 집을 사도 괜찮아?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-007

- question: 명의 이전은 어떤 법과 관련 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-008

- question: 계약금을 냈는데 계약을 취소할 수 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-009

- question: 부모님이 돈을 보태주면 문제가 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-010

- question: 등기부에서 빚 잡힌 집인지 보려면 뭘 봐야 해?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-011

- question: 아파트 매매계약은 법적으로 언제 성립해?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-012

- question: 매도인이 계약금을 받았는데 계약을 해제하려면 어떻게 해야 해?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-013

- question: 부동산 거래 신고는 누가 해야 해?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-014

- question: 공인중개사가 거래계약서를 거짓으로 작성하면 안 된다는 법이 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-015

- question: 부동산 등기부에는 어떤 권리를 등기할 수 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-016

- question: 소유권 이전등기는 어떤 법과 관련 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-017

- question: 토지거래허가구역에서 집을 사려면 허가가 필요해?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-018

- question: 부동산 거래 신고필증은 등기와 어떤 관련이 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-019

- question: 매매대금을 지급하기로 한 계약도 매매로 볼 수 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-020

- question: 아파트 구분소유자는 집합건물법과 관련이 있어?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-021

- question: 계약금을 돌려받을 수 있나요?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-022

- question: 매매 계약금 해제 규정 알려줘
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-023

- question: 아파트 살 때 계약 전에 꼭 확인해야 할 법적 사항은 뭐야
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-024

- question: 집값을 실제보다 낮게 계약서에 쓰면 문제가 있어
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-025

- question: 전세 낀 아파트를 사면 보증금은 누가 돌려줘야 해
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### LC-026

- question: 매매 계약 법률 알려줘
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### MX-FR-001

- question: 잠실엘스 위치 알려줘 그리고 매매 계약 법률 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다. 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### MX-FR-002

- question: 잠실엘스 위치 알려줘 그리고 오늘 날씨 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다. 오늘 날씨 알려줘는 처리하지 못했습니다. 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이…

### MX-IN-001

- question: 강남구 아파트 추천하고 최근 시세 추이도 알려줘
- answer: 1. 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일…

### MX-IN-002

- question: 강남구 아파트 추천하고 매매 계약 법령도 알려줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일 20…

### MX-IN-003

- question: 잠실엘스 위치랑 매매 계약 법률 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다. 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### MX-IN-004

- question: 래미안대치팰리스랑 잠실엘스 비교하고 계약 시 주의할 법도 알려줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원, 1608세대, 2015년 준공, 가까운 역 도곡역(402m), 가까운 학교 단국대학…

### MX-DP-001

- question: 강남구 아파트 추천하고 후보 비교도 해줘
- answer: 1. 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일…

### MX-AM-001

- question: 잠실엘스 시세 알려줘
- answer: 1. 잠실엘스 실거래 내역은 2026-05-26 33.0억원 전용 84.8㎡ 23층, 2026-05-25 32.8억원 전용 84.8㎡ 12층, 2026-05-22 28.3억원 전용 59.96㎡ 18층입니다. 2. …

### MX-AM-002

- question: 잠실엘스 최근 실거래가 알려줘
- answer: 잠실엘스 실거래 내역은 2026-05-26 33.0억원 전용 84.8㎡ 23층, 2026-05-25 32.8억원 전용 84.8㎡ 12층, 2026-05-22 28.3억원 전용 59.96㎡ 18층입니다.

### MX-AM-003

- question: 잠실엘스 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### MX-ST-001

- question: 강남구 시세추이랑 송파구 시세추이 알려줘
- answer: 1. 강남구 시세추이를 조회했습니다. 2025-06-01 평균 31.7억원에서 2026-06-01 평균 31.5억원으로 확인됩니다. 2. 송파구 시세추이를 조회했습니다. 2025-06-01 평균 19.1억원에서 2…

### MX-LLM-002

- question: 송파구 30억 이하 추천하고 추천 후보들 가격 흐름도 알려줘
- answer: 질문 처리 중 오류가 발생했습니다. 잠시 후 다시 시도해 주세요.

### MX-DD-001

- question: 잠실엘스 위치랑 잠실엘스 위치 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

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
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### UB-005

- question: 서울 아파트 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### UB-006

- question: 신고가 TOP 5 알려줘
- answer: 지원 가능한 질문은 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 법령 질문입니다.

### RV-001

- question: 잠실엘스 위치 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

### RV-002

- question: 잠실 엘스 위치 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다.

### RV-003

- question: 래미안 대치 팰리스 위치 알려줘
- answer: 래미안대치팰리스 위치는 대치동 1027입니다. 좌표는 위도 37.4941149, 경도 127.0579205입니다.

### RV-004

- question: 래미안대치팰리스 전용84㎡ 최근 실거래 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-08 43.0억원 전용 84.99㎡ 15층, 2026-05-07 42.5억원 전용 84.97㎡ 13층입니다.

### RV-005

- question: 래미안대치팰리스 전용 84 제곱미터 최근 실거래 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-08 43.0억원 전용 84.99㎡ 15층, 2026-05-07 42.5억원 전용 84.97㎡ 13층입니다.

### RV-006

- question: 래미안대치팰리스 실거래 최근 3건 보여줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### RV-007

- question: 최근 실거래가 래미안대치팰리스 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.

### RV-008

- question: 잠실엘스 34 평 시세 추이 알려줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### RV-009

- question: 최근 1년 시세 추이 잠실엘스 알려줘
- answer: 처리하지 못한 이유는 target_not_found입니다.

### RV-010

- question: 잠실엘스 가격 흐름 1년치 보여줘
- answer: 잠실엘스 시세추이를 조회했습니다. 2025-06-01 평균 31.4억원에서 2026-05-01 평균 31.6억원으로 확인됩니다.

### RV-011

- question: 송파구에서 30억 밑으로 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (185-5): 최근 거래가 14.7억원, 가까운 역 방이역(216m), 가까운 교육시설 올림픽유치원(430m), 18세대, 사용승인일 2002…

### RV-012

- question: 서초구 20억 이하인 저렴한 단지 4곳 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. 서초중흥S-클래스(도시형생활주택): 최근 거래가 1.9억원, 가까운 역 남부터미널(예술의전당)역(454m), 가까운 교육시설 서울신중초등학교(3…

### RV-013

- question: 서초역에서 가까운 서초구 아파트 추천해줘
- answer: 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. APELBAUM: 최근 거래가 24.8억원, 가까운 역 서초역(647m), 가까운 교육시설 방배중학교(288m), 800m 생활편의 서울성모병원…

### RV-014

- question: 래미안대치팰리스와 잠실엘스 시세 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원 - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원 학군은 평판…

### RV-015

- question: 잠실엘스랑 리센츠 역 접근성 비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 잠실엘스: 가까운 역 종합운동장역(404m) - 리센츠: 가까운 역 잠실새내역(299m) 학군은 평판이 아니라 가까운 교육시설 거리 기준이며, 생활편의는 800…

### RV-016

- question: 래미안대치팰리스랑잠실엘스가격비교해줘
- answer: 조회된 데이터 기준으로 비교하면 다음과 같습니다. - 래미안대치팰리스: 최근 거래가 44.0억원, 25.7평, 평당가 1.7억원 - 잠실엘스: 최근 거래가 33.0억원, 25.65평, 평당가 1.3억원 학군은 평판…

### RV-017

- question: 계약 해제하려면 계약금은 어떻게 돼?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### RV-018

- question: 전세 세입자 있는 아파트 사면 보증금은 누가 돌려줘?
- answer: 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### RV-019

- question: 잠실엘스 위치랑 매매계약 법률 같이 알려줘
- answer: 잠실엘스 위치는 잠실동 19입니다. 좌표는 위도 37.5141328, 경도 127.0793253입니다. 질문 임베딩을 생성할 수 없어 법령 검색을 실행하지 못했습니다.

### RV-020

- question: 강남구 아파트 추천 후 최근 1년 가격 흐름도 알려줘
- answer: 1. 조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다. 1. (828-21): 최근 거래가 9.1억원, 가까운 역 강남역(442m), 가까운 교육시설 서울역삼초등학교(493m), 15세대, 사용승인일…

### RV-021

- question: 래미안대치팰리스 실거래랑 계약금 해제 법 알려줘
- answer: 래미안대치팰리스 실거래 내역은 2026-06-03 44.0억원 전용 84.97㎡ 15층, 2026-05-27 57.0억원 전용 151.31㎡ 5층, 2026-05-13 58.0억원 전용 114.14㎡ 10층입니다.…
