# 단순조회 핸들러 문서

## 1. 목적

단순조회 핸들러는 H1 범위의 단순 DB 조회를 담당한다.
사용자 질문에서 추출된 슬롯을 기준으로 단지 또는 지역을 확정한 뒤, 위치, 실거래 내역, 단지 최고가/최저가, 지역 최고가/최저가 거래 랭킹을 조회한다.

현재 지원하는 조회 유형은 다음과 같다.

```
location              : 단지 위치/주소/좌표 조회
trade_history         : 단지 실거래 내역 조회
complex_price_record  : 단지 최고가/최저가 거래 조회
region_price_ranking  : 지역 최고가/최저가 거래 랭킹 조회
```

신고가/신저가 갱신 여부는 현재 H1 범위가 아니다. 신고가/신저가는 단순 최고가 조회가 아니라 과거 거래와 비교해야 하는 분석성 기능이므로 H4 또는 별도 분석 기능에서 처리한다.

---

## 2. 전체 흐름

```
사용자 질문
→ simple_lookup tool 호출
→ query_type / target_name / 기간 / 면적 / 정렬 조건 추출
→ run_simple_lookup(session, slots)
→ SimpleLookupSlots 유효성 검증
→ normalize_simple_lookup_policy()
→ SimpleLookupCriteria 생성
→ 단지 또는 지역 대상 확정
→ query_type에 맞는 DAO 메서드 선택
→ DB 조회
→ SimpleLookupResult 반환
```

앞단은 사용자 질문을 보고 `query_type`, `target_name`, `area`, `pyeong`, `period`, `limit`, `sort_order`, `price_order` 등을 슬롯으로 전달한다. 단순조회 핸들러는 전달받은 슬롯을 검증하고 정책에 맞게 조회 조건을 만든 뒤 DB 조회 결과를 구조화해서 반환한다.

---

## 3. 입력 슬롯 형식

```json
{
  "query": "반포자이 84㎡ 최근 1년 최고가 알려줘",
  "query_type": "complex_price_record",
  "target_name": "반포자이",
  "area": 84,
  "pyeong": null,
  "period": "1y",
  "start_date": null,
  "end_date": null,
  "limit": null,
  "sort_order": null,
  "price_order": "highest"
}
```

| 필드 | 설명 |
| --- | --- |
| `query` | 사용자 원문 질문 |
| `query_type` | `location`, `trade_history`, `complex_price_record`, `region_price_ranking` |
| `target_name` | 단지 조회는 단지명, 지역 랭킹 조회는 지역명 |
| `area` | 전용면적㎡ 단일값 |
| `pyeong` | 평형 단일값 |
| `period` | 상대 기간. 예: `1m`, `3m`, `6m`, `1y`, `5y` |
| `start_date` | 조회 시작일 |
| `end_date` | 조회 종료일 |
| `limit` | 조회 건수 |
| `sort_order` | 거래일 정렬 방향. `latest`, `oldest` |
| `price_order` | 가격 정렬 방향. `highest`, `lowest` |

---

## 4. 공통 정책

## 4.1 기준일

```python
BASE_DATE = 2026-06-20
```

| 표현 | 계산 결과 |
| --- | --- |
| 최근 3개월 | `2026-03-21` ~ `2026-06-20` |
| 최근 6개월 | `2025-12-21` ~ `2026-06-20` |
| 최근 1년 | `2025-06-21` ~ `2026-06-20` |

`trade_history`는 기간 조건이 없으면 전체 거래 중 최신 거래일부터 조회한다. `complex_price_record`, `region_price_ranking`은 기간 조건이 있으면 해당 기간 안에서 가격 정렬을 수행한다.

## 4.2 기간 표현 해석

| 사용자 표현 | 처리 |
| --- | --- |
| 최근 N개월 / 지난 N개월 | `period="{N}m"` |
| 최근 N년 / 지난 N년 | `period="{N}y"` |
| 2024년 | `start_date="2024-01-01"`, `end_date="2024-12-31"` |
| 2020년부터 / 2020년 이후 | `start_date="2020-01-01"` |
| 2023년까지 / 2023년 말까지 | `end_date="2023-12-31"` |
| 2010년부터 5년간 | `start_date="2010-01-01"`, `end_date="2014-12-31"` |

`최근 6개월`의 숫자 6은 기간 숫자이므로 `limit=6`으로 해석하지 않는다. `최근 6개월 최고가`, `최근 1년 최저가`처럼 기간과 가격 조건만 말한 경우 `limit`은 생략한다.

## 4.3 면적 처리

| 입력 | 처리 |
| --- | --- |
| `area=84` | `area_min=83`, `area_max=85` |
| `pyeong=25` | `25 * 3.3058 * 0.75 = 61.98375㎡`, 이후 ±3㎡ |
| 면적 조건 없음 | 면적 필터 없음 |

평형은 실제 전용면적 기준으로 비교하기 위해 전용률 0.75를 적용한다.

```
pyeong_to_area = pyeong * 3.3058 * 0.75
area_min = pyeong_to_area - 3
area_max = pyeong_to_area + 3
```

`area`와 `pyeong`이 동시에 들어오면 조건이 모호하므로 정책 검증에서 거부한다.

## 4.4 기본값

| query_type | 기본 limit | 정렬 정책 |
| --- | --- | --- |
| `location` | 사용 안 함 | 사용 안 함 |
| `trade_history` | 5 | `sort_order=latest` |
| `complex_price_record` | 1 | `price_order=highest`, 보조 정렬 `sort_order=latest` |
| `region_price_ranking` | 5 | `price_order=highest` |

`trade_history`의 `limit` 상한은 20으로 제한한다.

---

## 5. query_type별 처리

## 5.1 단지 위치 조회

### query_type

```python
location
```

특정 단지의 주소, 위도, 경도를 조회한다. `target_name`은 단지명이며 기간, 면적, limit, 정렬 조건은 사용하지 않는다.

반환 데이터 예시는 다음과 같다.

```json
{
  "complex_id": 11471,
  "complex_name": "잠실엘스",
  "trade_name": "잠실엘스",
  "address": "잠실동 19",
  "latitude": 37.5141328,
  "longitude": 127.0793253
}
```

---

## 5.2 단지 실거래 내역 조회

### query_type

```python
trade_history
```

특정 단지의 실거래 내역을 조회한다. “얼마야”, “최근 실거래가”, “거래내역” 계열 질문은 `trade_history`로 처리한다.

| 슬롯 | 설명 |
| --- | --- |
| `target_name` | 조회할 단지명 |
| `area`, `pyeong` | 선택 면적 조건 |
| `period`, `start_date`, `end_date` | 선택 기간 조건 |
| `limit` | 기본 5, 최대 20 |
| `sort_order` | `latest` 또는 `oldest` |

| sort_order | 처리 |
| --- | --- |
| `latest` | 거래일 최신순 |
| `oldest` | 거래일 오래된순 |

---

## 5.3 단지 최고가/최저가 조회

### query_type

```python
complex_price_record
```

특정 단지의 최고가 또는 최저가 거래를 조회한다. 기존 문서의 `record_high`는 현재 구현 기준에서 `complex_price_record`로 확장되었다. 최고가와 최저가는 별도 query_type으로 나누지 않고 `price_order`로 구분한다.

| price_order | 처리 |
| --- | --- |
| `highest` | 최고가 거래 조회 |
| `lowest` | 최저가 거래 조회 |

| 슬롯 | 설명 |
| --- | --- |
| `target_name` | 조회할 단지명 |
| `area`, `pyeong` | 선택 면적 조건 |
| `period`, `start_date`, `end_date` | 선택 기간 조건 |
| `limit` | 기본 1 |
| `price_order` | `highest` 또는 `lowest` |
| `sort_order` | 가격이 같은 경우 보조 정렬 기준 |

정렬 기준은 다음과 같다.

```
highest: deal_amount DESC, deal_date DESC, id DESC
lowest : deal_amount ASC, deal_date DESC, id DESC
```

---

## 5.4 지역 최고가/최저가 거래 랭킹 조회

### query_type

```python
region_price_ranking
```

특정 지역 안에서 최고가 또는 최저가 실거래 랭킹을 조회한다. 지역명과 “최고가”, “최저가”, “TOP N”, “거래 N건” 계열 표현이 함께 들어오면 `region_price_ranking`으로 처리한다.

| 슬롯 | 설명 |
| --- | --- |
| `target_name` | 조회할 지역명. 예: 강남구, 서초구, 송파구 |
| `area`, `pyeong` | 선택 면적 조건 |
| `period`, `start_date`, `end_date` | 선택 기간 조건 |
| `limit` | 기본 5 |
| `price_order` | `highest` 또는 `lowest` |

---

## 6. 반환 형식

단순조회 핸들러는 성공/실패 모두 동일한 상위 구조로 반환한다.

```json
{
  "handler": "simple_lookup",
  "success": true,
  "query_type": "trade_history",
  "criteria": {},
  "data": [],
  "reason": null,
  "message": null,
  "candidates": []
}
```

---

## 7. 실패 reason

| reason | 의미 |
| --- | --- |
| `invalid_request` | 슬롯 형식이 잘못되었거나 필수 조건이 부족한 경우 |
| `unsupported_query` | H1에서 지원하지 않는 조회 유형인 경우 |
| `invalid_condition` | 면적, 정렬, limit 등 조건이 잘못된 경우 |
| `invalid_period_condition` | 기간 조건이 잘못된 경우 |
| `target_not_found` | 단지 또는 지역을 찾지 못한 경우 |
| `ambiguous_target` | 단지명 또는 지역명이 모호한 경우 |
| `no_result` | 조회 대상은 찾았지만 조건에 맞는 결과가 없는 경우 |

---

## 8. H1에서 처리하지 않는 질문

| 질문 유형 | 처리 방향 |
| --- | --- |
| 신고가 조회 | H4 또는 별도 분석 기능에서 구현 |
| 신저가 조회 | H4 또는 별도 분석 기능에서 구현 |
| 시세 추이 | H4 price_trend 핸들러 |
| 상승률/하락률 랭킹 | H4 price_trend 핸들러 |
| 단지 비교 | 비교 핸들러 |
| 추천 | 추천 핸들러 |

---

## 9. 정리

현재 H1 단순조회 핸들러에서 구현된 큰 기능은 다음 네 가지다.

| 기능 | query_type |
| --- | --- |
| 단지 위치/주소/좌표 조회 | `location` |
| 단지 실거래 내역 조회 | `trade_history` |
| 단지 최고가/최저가 조회 | `complex_price_record` |
| 지역 최고가/최저가 거래 랭킹 조회 | `region_price_ranking` |

신고가/신저가 갱신 여부는 현재 H1 구현 범위가 아니다.