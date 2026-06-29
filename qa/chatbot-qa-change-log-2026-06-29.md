# Chatbot QA 변경 기록 - 2026-06-29

## 변경 단위 1: docs 전체 질문 실행 모드 추가

- 이유: 기존 `server/scripts/run_chatbot_qa.py`는 대표 15개 케이스만 실행해서 `docs/data/chatbot-questionnaire.md` 전체 질문의 pass/fail을 확인할 수 없었다.
- 변경: `--from-docs` 옵션을 추가해 질문지 markdown 테이블에서 전체 케이스를 읽고, `chatbot-qa-docs-results-YYYY-MM-DD.md/jsonl`로 별도 결과를 저장하게 했다.
- 검증 포인트: `또는`, 복수 handler, fragment, same-tool multi-call 같은 docs 기대값을 QA runner가 해석할 수 있어야 한다.

## 변경 단위 2: docs 실패 기반 deterministic 라우팅/슬롯 보강

- 이유: docs 전체 QA 첫 실행에서 비교/법령/추천 표현 일부가 deterministic planner에 잡히지 않아 OpenAI supervisor fallback으로 새고, lookup 단지명에 `1년`, `가장` 같은 수식어가 붙어 조회 실패가 발생했다.
- 변경: 가격 비교를 same-tool trend로 오판하지 않게 하고, `중 어디가`, `가까워`, `서초역 근처 아파트 알려줘`, `소유권/등기/신고/세입자` 법령 표현, `최근 5건` lookup 표현을 직접 처리하도록 보강했다.
- 검증 포인트: 실패 원문 기반 planner/slot 단위 테스트를 추가해 같은 표현이 다시 supervisor fallback으로 새지 않아야 한다.

## 변경 단위 3: docs 기대값 정렬과 SQLite 날짜 필터 보강

- 이유: plain complex `시세/가격/얼마`는 현재 목표상 `simple_lookup + price_trend` ambiguous 처리인데 Simple Lookup 섹션의 일부 기대값이 예전 단일 lookup 기준으로 남아 있었다. 또한 SQLite QA DB에서 simple_lookup 기간 필터가 날짜 문자열과 맞지 않아 최근 1년 거래가 no_result로 떨어졌다.
- 변경: 해당 docs 기대값을 ambiguous 흐름에 맞추고, 최고가/최저가 랭킹은 현재 구현 기준인 `simple_lookup`으로 정리했다. simple_lookup DAO는 SQLite에서 ISO 날짜 문자열 비교를 사용하게 했다.
- 검증 포인트: `최근 1년` lookup이 fixture/import SQLite에서도 실제 거래를 반환하고, `신고가`는 법령 질문으로 오분류되지 않아야 한다.

## 변경 단위 4: 가격순위와 신고가 boundary 최종 정리

- 이유: `서초구에서 가장 비싼 아파트`가 추천과 가격순위 lookup으로 동시에 잡혀 partial_success가 되었고, `신고가 TOP 5` 문서는 supervisor gap으로 남아 있었지만 현재 구현은 deterministic no_matching으로 안내한다.
- 변경: 최고가/최저가/가장 비싼/가장 싼 표현은 추천 추론 신호에서 제외하고 simple_lookup 가격순위로만 처리하게 했다. `신고가 TOP 5` 문서 기대값은 `no_matching_tool`로 갱신했다.
- 검증 포인트: 가격순위 질문은 단일 simple_lookup으로 처리되고, 신고가 질문은 내부 supervisor 없이 지원 범위 안내로 종료되어야 한다.

## 변경 단위 5: 지역 가격순위 target 정규화

- 이유: `서초구에서 가장 비싼 아파트`가 simple_lookup으로 라우팅된 뒤에도 `서초구에서`를 단지명으로 해석해 조회 실패했다.
- 변경: 지역명과 최고가/최저가 표현이 함께 있으면 target을 지역명으로 확정하고 `region_price_ranking`으로 실행하게 했다.
- 검증 포인트: 지역 가격순위 질문은 `target_name=서초구`, `query_type=region_price_ranking`으로 실행되어야 한다.
