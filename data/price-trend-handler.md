# 시세추이 핸들러 문서

## 1. 목적

시세추이 핸들러는 특정 아파트 단지 또는 지역을 대상으로 시세 흐름, 가격 변화율 순위, 실거래가 순위를 조회하는 모듈임.

앞단에서는 사용자 질문을 분석해 슬롯을 만들고, 시세추이 핸들러는 전달받은 슬롯을 기반으로 기간별 시세 흐름 또는 순위 데이터를 조회함.

지원하는 조회 유형은 다음과 같음.

```text
complex_trend          : 특정 단지의 시세 추이 조회
region_trend           : 특정 지역의 시세 추이 조회 (지역명 범위는 강남 3구[강남구, 서초구, 송파구]만 포함하며 상세 동은 제외함 )
price_change_ranking   : 지역 내 가격 상승률/하락률 순위 조회
price_ranking          : 지역 내 최고가/최저가 실거래 순위 조회
```

---

## 2. 전체 흐름

```text
사용자 질문
→ 앞단에서 슬롯 추출
→ run_price_trend(session, slots)
→ TrendSlots 유효성 검증
→ normalize_trend_policy()
→ DB 조회용 criteria 생성
→ 단지 또는 지역 대상 확정
→ query_type에 맞는 DAO 메서드 선택
→ DB 조회
→ TrendResult 반환
```

정리하면 다음과 같음.

```text
앞단 역할:
사용자 질문을 보고 query_type, complex_name, region_name, period, limit 등을 슬롯으로 추출함

시세추이 핸들러 역할:
전달받은 슬롯을 검증하고 시세 추이 또는 순위 조회 결과를 구조화해서 반환함
```

---

## 3. 질문 예시와 슬롯 예시

## 3.1 단지 시세 추이 조회

질문 예시:

```text
은마아파트 시세추이 알려줘
은마아파트 최근 1년 시세 흐름 보여줘
은마아파트 34평 시세추이 알려줘
```

슬롯 예시:

```json
{
  "original_question": "은마아파트 최근 1년 시세추이 알려줘",
  "query_type": "complex_trend",
  "complex_name": "은마아파트",
  "period": "1y"
}
```

---

## 3.2 지역 시세 추이 조회

질문 예시:

```text
강남구 시세추이 알려줘
서초구 최근 1년 시세 흐름 보여줘
강남 3구 시세추이 알려줘
```

슬롯 예시:

```json
{
  "original_question": "강남구 최근 1년 시세추이 알려줘",
  "query_type": "region_trend",
  "region_name": "강남구",
  "period": "1y"
}
```

강남 3구처럼 복수 지역이 들어오는 경우:

```json
{
  "original_question": "강남 3구 최근 1년 시세추이 알려줘",
  "query_type": "region_trend",
  "region_names": ["강남구", "서초구", "송파구"],
  "period": "1y"
}
```

---

## 3.3 가격 변화율 순위 조회

질문 예시:

```text
최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘
최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘
강남구 상승률 높은 아파트 알려줘
```

슬롯 예시:

```json
{
  "original_question": "최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘",
  "query_type": "price_change_ranking",
  "region_name": "강남구",
  "period": "1y",
  "change_direction": "up",
  "limit": 5
}
```

하락률 조회 예시:

```json
{
  "original_question": "최근 1년 서초구에서 많이 내린 아파트 5곳 보여줘",
  "query_type": "price_change_ranking",
  "region_name": "서초구",
  "period": "1y",
  "change_direction": "down",
  "limit": 5
}
```

---

## 3.4 실거래가 순위 조회

질문 예시:

```text
강남구 최고가 아파트 TOP 5 알려줘
서초구에서 가장 비싼 아파트 보여줘
송파구 최저가 아파트 5곳 알려줘
```

슬롯 예시:

```json
{
  "original_question": "강남구 최고가 아파트 TOP 5 알려줘",
  "query_type": "price_ranking",
  "region_name": "강남구",
  "rank_order": "highest",
  "limit": 5
}
```

최저가 조회 예시:

```json
{
  "original_question": "송파구 최저가 아파트 5곳 알려줘",
  "query_type": "price_ranking",
  "region_name": "송파구",
  "rank_order": "lowest",
  "limit": 5
}
```

---

## 4. 입력 슬롯 형식

시세추이 핸들러는 다음 형식의 슬롯을 입력으로 받음.

```json
{
  "original_question": "최근 1년 강남구에서 많이 오른 아파트 TOP 5 알려줘",
  "query_type": "price_change_ranking",
  "complex_name": null,
  "region_name": "강남구",
  "region_names": null,
  "area": null,
  "area_min": null,
  "area_max": null,
  "pyeong": null,
  "pyeong_min": null,
  "pyeong_max": null,
  "period": "1y",
  "start_date": null,
  "end_date": null,
  "interval": null,
  "change_direction": "up",
  "rank_order": null,
  "limit": 5
}
```

| 필드                  | 설명                                                                              |
| ------------------- | ------------------------------------------------------------------------------- |
| `original_question` | 원본 사용자 질문                                                                       |
| `query_type`        | 조회 유형. `complex_trend`, `region_trend`, `price_change_ranking`, `price_ranking` |
| `complex_name`      | 단지 시세 추이 조회 대상 아파트 단지명                                                          |
| `region_name`       | 단일 지역명. 예: `강남구`                                                                |
| `region_names`      | 복수 지역명. 예: `["강남구", "서초구", "송파구"]`                                              |
| `area`              | 전용면적. 예: `84`                                                                   |
| `area_min`          | 전용면적 하한                                                                         |
| `area_max`          | 전용면적 상한                                                                         |
| `pyeong`            | 평형. 예: `34`                                                                     |
| `pyeong_min`        | 평형 범위 하한                                                                        |
| `pyeong_max`        | 평형 범위 상한                                                                        |
| `period`            | 상대 기간. 예: `6m`, `1y`, `3y`                                                      |
| `start_date`        | 조회 시작일                                                                          |
| `end_date`          | 조회 종료일                                                                          |
| `interval`          | 시계열 집계 간격. `month`, `quarter`, `year`                                           |
| `change_direction`  | 변화율 순위 방향. `up`, `down`, `absolute`                                             |
| `rank_order`        | 실거래가 순위 방향. `highest`, `lowest`                                                 |
| `limit`             | 순위 조회 개수                                                                        |

---

## 5. 반환 형식

시세추이 핸들러는 성공/실패 모두 동일한 구조로 반환함.

```json
{
  "handler": "price_trend",
  "success": true,
  "query_type": "region_trend",
  "data": [],
  "criteria": {},
  "summary": null,
  "reason": null,
  "message": null,
  "candidates": [],
  "ignored_slots": {}
}
```

---

## 5.1 시세 추이 성공 응답 예시

`complex_trend`, `region_trend`는 기간별 시세 데이터를 리스트로 반환함.

```json
{
  "handler": "price_trend",
  "success": true,
  "query_type": "region_trend",
  "data": [
    {
      "period_start": "2025-06-01",
      "avg_deal_amount": 250000.0,
      "avg_price_per_sqm": 2941.18,
      "min_deal_amount": 180000,
      "max_deal_amount": 320000,
      "trade_count": 15,
      "avg_exclusive_area": 84.92,
      "deal_amount_unit": "만원",
      "price_per_sqm_unit": "만원/㎡"
    }
  ],
  "criteria": {
    "region_name": "강남구",
    "start_date": "2025-06-20",
    "end_date": "2026-06-20",
    "interval": "month"
  },
  "summary": {
    "primary_metric": "avg_price_per_sqm",
    "first_period": "2025-06-01",
    "last_period": "2026-06-01",
    "first_value": 2800.0,
    "last_value": 2941.18,
    "change_amount": 141.18,
    "change_rate": 5.04,
    "observed_period_count": 12,
    "total_trade_count": 180
  },
  "reason": null,
  "message": "지역 시세 추이를 조회했습니다.",
  "candidates": [],
  "ignored_slots": {}
}
```

---

## 5.2 가격 변화율 순위 성공 응답 예시

`price_change_ranking`은 단지별 상승률 또는 하락률 순위를 반환함.

```json
{
  "handler": "price_trend",
  "success": true,
  "query_type": "price_change_ranking",
  "data": [
    {
      "rank": 1,
      "complex_id": 10,
      "complex_name": "OO아파트",
      "address": "서울특별시 강남구 ...",
      "start_avg_price_per_sqm": 2500.0,
      "end_avg_price_per_sqm": 3000.0,
      "change_amount": 500.0,
      "change_rate": 20.0,
      "start_trade_count": 3,
      "end_trade_count": 4,
      "avg_exclusive_area": 84.9,
      "price_per_sqm_unit": "만원/㎡"
    }
  ],
  "criteria": {
    "region_name": "강남구",
    "period": "1y",
    "change_direction": "up",
    "limit": 5
  },
  "summary": {
    "change_direction": "up",
    "result_count": 5,
    "window_months": 3,
    "min_trade_count": 2,
    "top_change_rate": 20.0
  },
  "reason": null,
  "message": "가격 변화율 순위를 조회했습니다.",
  "candidates": [],
  "ignored_slots": {}
}
```

---

## 5.3 실거래가 순위 성공 응답 예시

`price_ranking`은 지역 내 단지별 대표 최고가 또는 최저가 거래를 반환함.

```json
{
  "handler": "price_trend",
  "success": true,
  "query_type": "price_ranking",
  "data": [
    {
      "rank": 1,
      "complex_id": 10,
      "complex_name": "OO아파트",
      "address": "서울특별시 강남구 ...",
      "trade_id": 100,
      "deal_date": "2026-06-10",
      "deal_amount": 350000,
      "deal_amount_unit": "만원",
      "exclusive_area": 84.93,
      "floor": 10,
      "apt_dong": "101"
    }
  ],
  "criteria": {
    "region_name": "강남구",
    "rank_order": "highest",
    "limit": 5
  },
  "summary": {
    "rank_order": "highest",
    "result_count": 5,
    "top_deal_amount": 350000,
    "deal_amount_unit": "만원"
  },
  "reason": null,
  "message": "실거래가 순위를 조회했습니다.",
  "candidates": [],
  "ignored_slots": {}
}
```

---

## 5.4 실패 응답 예시

```json
{
  "handler": "price_trend",
  "success": false,
  "query_type": "region_trend",
  "data": [],
  "criteria": {},
  "summary": null,
  "reason": "target_not_found",
  "message": "입력한 이름과 일치하는 지역을 찾지 못했습니다: 강남",
  "candidates": [],
  "ignored_slots": {}
}
```

---

## 5.5 주요 실패 reason

| reason                | 의미                         |
| --------------------- | -------------------------- |
| `invalid_request`     | 슬롯 형식이 잘못되었거나 지원하지 않는 요청   |
| `target_not_found`    | 단지명 또는 지역명을 DB에서 찾지 못함     |
| `ambiguous_target`    | 단지 또는 지역 후보가 여러 개 검색됨      |
| `no_result`           | 조건에 맞는 시세 추이 또는 순위 데이터가 없음 |
| `insufficient_data`   | 변화율 비교에 필요한 최소 거래 건수가 부족함  |
| `unsupported_request` | 정책상 지원하지 않는 요청             |

---

## 6. 정리

시세추이 핸들러의 핵심 흐름은 다음과 같음.

```text
슬롯 받기
→ 슬롯 유효성 검증
→ 조회 조건 생성
→ 단지 또는 지역 확정
→ query_type별 DB 조회
→ 구조화된 결과 반환
```

앞단에서 가장 중요하게 만들어야 하는 값은 다음과 같음.

```text
query_type
complex_name 또는 region_name/region_names
area 또는 pyeong
period 또는 start_date/end_date
interval
change_direction
rank_order
limit
original_question
```
