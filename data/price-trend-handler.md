# 시세추이 핸들러 문서

## 1. 목적

시세추이 핸들러는 H4 범위의 기간 기반 가격 분석을 담당한다.
특정 아파트 단지 또는 지역을 대상으로 기간별 시세 흐름과 가격 변화율 랭킹을 조회한다.

H1 단순조회가 개별 거래를 그대로 조회하는 기능이라면, H4 시세추이는 거래 데이터를 기간 단위로 집계하거나 기간 간 변화를 비교하는 분석 기능이다.

현재 구현 기준의 핵심 조회 유형은 다음과 같다.

```
timeseries : 단지 또는 지역의 기간별 시세 추이 조회
ranking    : 지역 내 가격 변화율 랭킹 조회
```

---

## 2. 전체 흐름

```
사용자 질문
→ price_trend tool 호출
→ query / analysis_type / target_type / 대상명 / 기간 / 집계 간격 / 랭킹 조건 추출
→ run_price_trend(session, slots)
→ TrendSlots 유효성 검증
→ normalize_trend_policy()
→ TrendCriteria 생성
→ 단지 또는 지역 대상 확정
→ analysis_type에 맞는 DAO 메서드 선택
→ DB 조회
→ TrendObservation 반환
```

앞단은 사용자 질문을 보고 `analysis_type`, `target_type`, `target_name`, `region_names`, `period`, `interval`, `rank_by`, `direction`, `limit` 등을 슬롯으로 전달한다. 시세추이 핸들러는 전달받은 슬롯을 검증하고 기간별 집계 또는 변화율 랭킹 결과를 구조화해서 반환한다.

---

## 3. H1과 H4 역할 구분

| 질문 유형 | 담당 |
| --- | --- |
| 단지 최근 실거래가 | H1 simple_lookup |
| 단지 최고가/최저가 | H1 simple_lookup |
| 지역 최고가/최저가 거래 랭킹 | H1 simple_lookup |
| 단지/지역 시세 추이 | H4 price_trend |
| 지역 상승률/하락률 랭킹 | H4 price_trend |
| 신고가/신저가 갱신 여부 | 현재 미구현, H4 또는 별도 분석 기능 후보 |

---

## 4. 입력 슬롯 형식

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
| `analysis_type` | 분석 유형. `timeseries`, `ranking` |
| `target_type` | 대상 유형. `complex`, `region` |
| `target_name` | 단지명 또는 지역명 |
| `region_names` | 복수 지역명. 예: `["강남구", "서초구", "송파구"]` |
| `area`, `area_min`, `area_max` | 전용면적 조건 |
| `pyeong`, `pyeong_min`, `pyeong_max` | 평형 조건 |
| `period` | 상대 기간. 예: `6m`, `1y`, `5y` |
| `start_date`, `end_date` | 조회 기간 |
| `interval` | 시계열 집계 간격. `month`, `quarter`, `year` |
| `rank_by` | 랭킹 기준. 현재 주요 사용값은 `change_rate` |
| `direction` | 랭킹 방향. `desc`, `asc` |
| `limit` | 랭킹 조회 개수 |

---

## 5. 공통 정책

## 5.1 기준일

```python
BASE_DATE = 2026-06-20
```

| 표현 | 계산 결과 |
| --- | --- |
| 최근 6개월 | `2025-12-21` ~ `2026-06-20` |
| 최근 1년 | `2025-06-21` ~ `2026-06-20` |
| 최근 5년 | `2021-06-21` ~ `2026-06-20` |

기간이 필요한 분석에서 사용자가 기간을 말하지 않으면 기본적으로 최근 1년을 사용한다.

## 5.2 집계 간격

`interval`이 명시되지 않으면 기간에 따라 기본 집계 간격을 정한다.

| 기간 | 기본 interval |
| --- | --- |
| 최근 6개월 | `month` |
| 최근 1년 | `month` |
| 최근 3년 | `quarter` |
| 최근 5년 이상 | `year` |

## 5.3 면적 처리

H4도 H1과 동일하게 전용면적㎡ 또는 평형 조건을 받을 수 있다.

| 입력 | 처리 |
| --- | --- |
| `area=84` | 전용 84㎡ 인근 거래만 조회 |
| `pyeong=25` | 전용률 0.75를 적용해 전용면적으로 변환 후 조회 |
| `pyeong_min`, `pyeong_max` | 평형 범위 조건을 전용면적 범위로 변환 |
| 면적 조건 없음 | 전체 면적 대상 |

```
pyeong_to_area = pyeong * 3.3058 * 0.75
area_min = pyeong_to_area - 3
area_max = pyeong_to_area + 3
```

---

## 6. analysis_type별 처리

## 6.1 시세 추이 조회

### analysis_type

```python
timeseries
```

단지 또는 지역의 기간별 가격 흐름을 조회한다.

| target_type | 처리 |
| --- | --- |
| `complex` | 특정 단지의 기간별 시세 추이 조회 |
| `region` | 특정 지역 또는 복수 지역의 기간별 시세 추이 조회 |

사용 슬롯은 `target_type`, `target_name`, `region_names`, `period`, `start_date`, `end_date`, `interval`, `area`, `pyeong`이다.

반환 row 예시는 다음과 같다.

```json
{
  "period_start": "2025-06-01",
  "avg_deal_amount": 384500,
  "avg_price_per_sqm": 4766.18,
  "trade_count": 2
}
```

---

## 6.2 가격 변화율 랭킹 조회

### analysis_type

```python
ranking
```

특정 지역 안에서 가격 변화율이 큰 단지를 조회한다. 상승률 TOP N은 `direction="desc"`로 처리하고, 하락률 조회는 `direction="asc"`로 처리한다.

| 슬롯 | 설명 |
| --- | --- |
| `target_type` | 일반적으로 `region` |
| `target_name` | 지역명 |
| `region_names` | 복수 지역 조회 시 사용 |
| `period`, `start_date`, `end_date` | 비교 기간 |
| `rank_by` | `change_rate` |
| `direction` | `desc` 또는 `asc` |
| `limit` | 조회 개수 |

반환 row 예시는 다음과 같다.

```json
{
  "rank": 1,
  "complex_id": 3937,
  "complex_name": "상지리츠빌카일룸(65-4)",
  "address": "삼성동 65-4",
  "start_period": "2025-08-01",
  "end_period": "2025-09-01",
  "start_price_per_sqm": 2523.77,
  "end_price_per_sqm": 4466.57,
  "change_amount": 1942.81,
  "change_rate": 76.98,
  "trade_counts": {
    "start": 1,
    "end": 1
  }
}
```

---

## 7. 반환 형식

시세추이 핸들러는 성공 시 다음 구조로 반환한다.

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

## 8. 실패 reason

| reason | 의미 |
| --- | --- |
| `invalid_request` | 슬롯 형식이 잘못되었거나 필수 조건이 부족한 경우 |
| `unsupported_query` | H4에서 지원하지 않는 요청인 경우 |
| `invalid_condition` | 면적, 정렬, limit 등 조건이 잘못된 경우 |
| `invalid_period_condition` | 기간 조건이 잘못된 경우 |
| `target_not_found` | 단지 또는 지역을 찾지 못한 경우 |
| `ambiguous_target` | 단지명 또는 지역명이 모호한 경우 |
| `no_result` | 조회 대상은 찾았지만 조건에 맞는 결과가 없는 경우 |
| `insufficient_data` | 변화율 비교에 필요한 데이터가 부족한 경우 |

---

## 9. H4에서 처리하지 않는 질문

| 질문 유형 | 처리 방향 |
| --- | --- |
| 최근 실거래가 조회 | H1 simple_lookup |
| 단지 최고가/최저가 조회 | H1 simple_lookup |
| 지역 최고가/최저가 거래 랭킹 | H1 simple_lookup |
| 위치 조회 | H1 simple_lookup |
| 신고가/신저가 갱신 여부 | 현재 미구현, H4 또는 별도 분석 기능 후보 |
| 단지 비교 | 비교 핸들러 |
| 추천 | 추천 핸들러 |

---

## 10. 정리

현재 H4 시세추이 핸들러에서 구현된 큰 기능은 다음 두 가지다.

| 기능 | analysis_type |
| --- | --- |
| 단지/지역 시세 추이 조회 | `timeseries` |
| 지역 가격 변화율 랭킹 조회 | `ranking` |

H4는 기간 기반 분석을 담당하고, H1은 단순 조회성 거래 데이터를 담당한다.