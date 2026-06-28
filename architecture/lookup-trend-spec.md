# 단순조회 및 시세추이 기능 문서

## 1. 구현 기능

담당 기능은 크게 두 가지다.

1. H1 단순조회 핸들러
2. H4 시세추이 핸들러

현재 구현 기준에서 두 핸들러의 역할은 다음과 같이 분리한다.

| 구분 | 담당 |
| --- | --- |
| H1 단순조회 | 단지 위치, 단지 실거래 내역, 단지 최고가/최저가, 지역 최고가/최저가 거래 랭킹 |
| H4 시세추이 | 단지/지역 시세 추이, 지역 가격 변화율 랭킹 |

신고가/신저가 갱신 여부는 현재 구현 범위에서 제외한다. 해당 기능은 과거 거래와의 비교가 필요한 분석성 기능이므로 H4 또는 별도 분석 기능에서 구현하는 것이 적절하다.

---

# 2. 공통 정책

## 2.1 기준일 처리

기간이 필요한 질문은 `BASE_DATE`를 기준으로 `start_date`, `end_date`를 계산한다.

```python
BASE_DATE = 2026-06-20
```

| 표현 | 계산 결과 |
| --- | --- |
| 최근 3개월 | `2026-03-21` ~ `2026-06-20` |
| 최근 6개월 | `2025-12-21` ~ `2026-06-20` |
| 최근 1년 | `2025-06-21` ~ `2026-06-20` |
| 최근 5년 | `2021-06-21` ~ `2026-06-20` |

기간 표현은 다음 기준으로 해석한다.

| 사용자 표현 | 처리 |
| --- | --- |
| 최근 N개월 / 지난 N개월 | `period="{N}m"` |
| 최근 N년 / 지난 N년 | `period="{N}y"` |
| 2024년 | `start_date="2024-01-01"`, `end_date="2024-12-31"` |
| 2020년부터 / 2020년 이후 | `start_date="2020-01-01"` |
| 2023년까지 / 2023년 말까지 | `end_date="2023-12-31"` |
| 2010년부터 5년간 | `start_date="2010-01-01"`, `end_date="2014-12-31"` |

`최근 6개월`의 숫자 6은 기간 숫자이므로 `limit=6`으로 해석하지 않는다.

---

## 2.2 면적 처리

전용면적은 ㎡ 기준으로 처리한다.

| 입력 | 처리 |
| --- | --- |
| 전용 84㎡ | `area_min=83`, `area_max=85` |
| 25평 | `25 * 3.3058 * 0.75 = 61.98375㎡`, 이후 ±3㎡ |
| 면적 조건 없음 | 면적 필터 없음 |

평형은 전용률 0.75를 적용해 전용면적으로 변환한다.

```
pyeong_to_area = pyeong * 3.3058 * 0.75
area_min = pyeong_to_area - 3
area_max = pyeong_to_area + 3
```

---

## 2.3 가격 단위

거래금액은 만원 단위로 처리한다.

```
280000만원 = 28억
320000만원 = 32억
```

면적당 가격은 `만원/㎡` 단위로 처리한다.

---

# 3. 공통 대상 확정

## 3.1 단지 후보 조회

아파트명은 `complexes.name`, `complexes.trade_name` 기준으로 검색한다.

처리 순서는 다음과 같다.

```
1. name 정확 일치
2. trade_name 정확 일치
3. name 부분 일치
4. trade_name 부분 일치
```

조회 결과가 없으면 `target_not_found`를 반환한다. 후보가 여러 개면 `ambiguous_target`과 후보 목록을 반환한다.

---

## 3.2 지역 후보 조회

지역명은 `regions.name` 기준으로 검색한다. H1의 `region_price_ranking`과 H4의 지역 시세추이/랭킹은 지역명을 기준으로 대상 지역을 확정한다.

조회 결과가 없으면 `target_not_found`를 반환한다. 후보가 여러 개면 `ambiguous_target`과 후보 목록을 반환한다.

---

# 4. H1 단순조회 핸들러

## 4.1 목적

단순조회 핸들러는 단순 DB 조회성 질문을 처리한다.

지원하는 query_type은 다음 네 가지다.

```
location              : 단지 위치/주소/좌표 조회
trade_history         : 단지 실거래 내역 조회
complex_price_record  : 단지 최고가/최저가 거래 조회
region_price_ranking  : 지역 최고가/최저가 거래 랭킹 조회
```

---

## 4.2 입력 슬롯

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
| `target_name` | 단지명 또는 지역명 |
| `area` | 전용면적㎡ |
| `pyeong` | 평형 |
| `period` | 상대 기간 |
| `start_date` | 조회 시작일 |
| `end_date` | 조회 종료일 |
| `limit` | 조회 건수 |
| `sort_order` | `latest`, `oldest` |
| `price_order` | `highest`, `lowest` |

---

## 4.3 query_type별 처리

| query_type | 처리 |
| --- | --- |
| `location` | 특정 단지의 주소, 위도, 경도 조회 |
| `trade_history` | 특정 단지의 실거래 내역 조회 |
| `complex_price_record` | 특정 단지의 최고가 또는 최저가 거래 조회 |
| `region_price_ranking` | 특정 지역의 최고가 또는 최저가 거래 랭킹 조회 |

### location

- `target_name`은 단지명이다.
- 기간, 면적, limit, 정렬 조건은 사용하지 않는다.

### trade_history

- `target_name`은 단지명이다.
- 기본 `limit`은 5이고 최대 20까지 허용한다.
- 기본 정렬은 `latest`다.
- `oldest`가 들어오면 오래된 거래순으로 조회한다.

### complex_price_record

- `target_name`은 단지명이다.
- `price_order=highest`면 최고가 거래를 조회한다.
- `price_order=lowest`면 최저가 거래를 조회한다.
- 기본 `limit`은 1이다.
- 기간과 면적 조건을 적용할 수 있다.

### region_price_ranking

- `target_name`은 지역명이다.
- `price_order=highest`면 지역 내 최고가 거래 랭킹을 조회한다.
- `price_order=lowest`면 지역 내 최저가 거래 랭킹을 조회한다.
- 기본 `limit`은 5다.
- 기간과 면적 조건을 적용할 수 있다.

---

## 4.4 H1 반환 형식

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

# 5. H4 시세추이 핸들러

## 5.1 목적

시세추이 핸들러는 기간 기반 가격 분석을 처리한다.

지원하는 analysis_type은 다음 두 가지다.

```
timeseries : 단지 또는 지역의 기간별 시세 추이 조회
ranking    : 지역 가격 변화율 랭킹 조회
```

---

## 5.2 입력 슬롯

```json
{
  "query": "최근 1년 강남구 상승률 TOP 5 알려줘",
  "analysis_type": "ranking",
  "target_type": "region",
  "target_name": "강남구",
  "region_names": ["강남구"],
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
  "rank_by": "change_rate",
  "direction": "desc",
  "limit": 5
}
```

| 필드 | 설명 |
| --- | --- |
| `query` | 사용자 원문 질문 |
| `analysis_type` | `timeseries`, `ranking` |
| `target_type` | `complex`, `region` |
| `target_name` | 단지명 또는 지역명 |
| `region_names` | 복수 지역명 |
| `area`, `area_min`, `area_max` | 전용면적 조건 |
| `pyeong`, `pyeong_min`, `pyeong_max` | 평형 조건 |
| `period` | 상대 기간 |
| `start_date`, `end_date` | 조회 기간 |
| `interval` | `month`, `quarter`, `year` |
| `rank_by` | 랭킹 기준. 주요 값은 `change_rate` |
| `direction` | `desc`, `asc` |
| `limit` | 랭킹 조회 개수 |

---

## 5.3 analysis_type별 처리

| analysis_type | 처리 |
| --- | --- |
| `timeseries` | 단지 또는 지역의 기간별 시세 흐름 조회 |
| `ranking` | 지역 내 가격 변화율 랭킹 조회 |

### timeseries

- `target_type=complex`이면 특정 단지의 시세 추이를 조회한다.
- `target_type=region`이면 특정 지역 또는 복수 지역의 시세 추이를 조회한다.
- `interval`이 없으면 기간에 따라 `month`, `quarter`, `year` 중 하나로 정규화한다.

### ranking

- 지역 내 단지별 가격 변화율을 계산한다.
- `rank_by=change_rate` 기준으로 정렬한다.
- 상승률 TOP N은 `direction=desc`로 처리한다.
- 하락률 조회는 `direction=asc`로 처리한다.

---

## 5.4 H4 반환 형식

```json
{
  "handler": "price_trend",
  "success": true,
  "observation_type": "timeseries",
  "criteria": {},
  "units": {
    "deal_amount": "만원",
    "price_per_sqm": "만원/㎡"
  },
  "summary_metrics": {
    "row_count": 10
  },
  "row_count": 10,
  "rows": []
}
```

랭킹 조회는 `observation_type="ranking"`으로 반환한다.

---

# 6. 예외 처리

공통 실패 reason은 다음과 같다.

| reason | 설명 |
| --- | --- |
| `invalid_request` | 슬롯 형식이 잘못되었거나 필수 조건이 부족한 경우 |
| `unsupported_query` | 해당 핸들러에서 지원하지 않는 요청인 경우 |
| `invalid_condition` | 면적, limit, 정렬 등 조건이 잘못된 경우 |
| `invalid_period_condition` | 기간 조건이 잘못된 경우 |
| `target_not_found` | 단지 또는 지역을 찾지 못한 경우 |
| `ambiguous_target` | 단지명 또는 지역명이 모호한 경우 |
| `no_result` | 조회 대상은 찾았지만 조건에 맞는 결과가 없는 경우 |
| `insufficient_data` | 변화율 비교에 필요한 데이터가 부족한 경우 |

---

# 7. Tool과 Query 매핑

| Tool | 처리 유형 | 설명 |
| --- | --- | --- |
| `simple_lookup` | `location` | 단지 위치/주소/좌표 조회 |
| `simple_lookup` | `trade_history` | 단지 실거래 내역 조회 |
| `simple_lookup` | `complex_price_record` | 단지 최고가/최저가 조회 |
| `simple_lookup` | `region_price_ranking` | 지역 최고가/최저가 거래 랭킹 조회 |
| `price_trend` | `timeseries` | 단지/지역 시세 추이 조회 |
| `price_trend` | `ranking` | 지역 가격 변화율 랭킹 조회 |

---

# 8. 전체 흐름

## 8.1 단순조회 흐름

```
사용자 질문
↓
simple_lookup tool 호출
↓
SimpleLookupSlots 검증
↓
normalize_simple_lookup_policy()
↓
SimpleLookupCriteria 생성
↓
단지 또는 지역 대상 확정
↓
query_type별 DAO 조회
↓
SimpleLookupResult 반환
```

## 8.2 시세추이 흐름

```
사용자 질문
↓
price_trend tool 호출
↓
TrendSlots 검증
↓
normalize_trend_policy()
↓
TrendCriteria 생성
↓
단지 또는 지역 대상 확정
↓
analysis_type별 DAO 조회
↓
TrendObservation 반환
```

---

# 9. 현재 미구현 또는 별도 처리 대상

| 기능 | 처리 방향 |
| --- | --- |
| 신고가 조회 | H4 또는 별도 분석 기능으로 구현 예정 |
| 신저가 조회 | H4 또는 별도 분석 기능으로 구현 예정 |

현재 H1/H4의 큰 구현 단위 기준으로 보면, H1 단순조회와 H4 시세추이/변화율 랭킹은 구현되어 있고 신고가/신저가 갱신 여부는 별도 구현 대상으로 남아 있다.