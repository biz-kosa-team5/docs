# 단순조회 및 시세추이 기능 문서

## 구현 기능

담당 기능은 다음 두 가지다.

1. 단순조회 핸들러
2. 시세추이 핸들러

단순조회 핸들러는 특정 아파트의 최근 실거래가, 최고가, 위치, 신고가 정보를 조회한다.

시세추이 핸들러는 특정 아파트 또는 지역의 가격 흐름과 변화율을 조회한다.

---

# 1. 공통 정책

## 기준일 처리

기간이 필요한 질문은 `base_date`를 기준으로 `start_date`, `end_date`를 계산한다.

```
base_date = 2026-06-17
최근 1년 → 2025-06-17 ~ 2026-06-17
최근 3년 → 2023-06-17 ~ 2026-06-17
```

기간이 필요한 조회에서 사용자가 기간을 말하지 않으면 기본값은 최근 1년으로 처리한다.

단, 최근 실거래가 조회는 기간 조건 없이 거래일 기준 최신 거래를 조회한다.

---

## 면적 처리

전용면적은 ㎡ 기준으로 처리한다.

```
전용 84㎡ → area_min = 83, area_max = 85
31평 → 31 * 3.3058 = 102.48㎡
     → area_min = 101.48, area_max = 103.48
```

면적 조건이 없으면 `area_min`, `area_max`는 `null`로 전달한다.

---

## 가격 단위

거래금액은 만원 단위로 처리한다.

```
280000만원 = 28억
320000만원 = 32억
```

---

# 2. 공통 조회

## 단지 후보 조회

아파트명은 `complexes.name`, `complexes.trade_name` 기준으로 검색한다.

```sql
SELECT
    c.id AS complex_id,
    c.name AS complex_name,
    c.trade_name,
    c.address,
    c.region_id
FROM complexes c
WHERE c.name ILIKE '%' || :complex_name || '%'
   OR c.trade_name ILIKE '%' || :complex_name || '%'
ORDER BY
    CASE
        WHEN c.name = :complex_name THEN 1
        WHEN c.trade_name = :complex_name THEN 2
        ELSE 3
    END,
    c.name
LIMIT 10;
```

---

## 지역 후보 조회

지역명은 `regions.name` 기준으로 검색한다.

```sql
SELECT
    r.id AS region_id,
    r.name AS region_name,
    r.type,
    r.parent_id
FROM regions r
WHERE r.name ILIKE '%' || :region_name || '%'
ORDER BY
    CASE
        WHEN r.name = :region_name THEN 1
        ELSE 2
    END,
    r.name
LIMIT 10;
```

---

# 3. 단순조회 핸들러

## 구현 함수

```python
extract_lookup_slots(question: str, base_date: date) -> LookupSlots
lookup_complex_info(slots: LookupSlots) -> dict
```

## LookupSlots

```python
class LookupSlots:
    original_question: str
    query_type: str
    # recent_trades | record_high | location | recent_record_highs

    complex_name: str | None
    region_name: str | None

    area: float | None
    pyeong: float | None
    area_min: float | None
    area_max: float | None

    period: str | None
    start_date: str | None
    end_date: str | None

    limit: int | None
```

---

# 4. 최근 실거래가 조회

## query_type

```python
recent_trades
```

## 처리 기준

| 질문 | 처리 |
| --- | --- |
| 은마아파트 얼마야 | 최근 거래 5건 |
| 은마아파트 최근 실거래가 알려줘 | 최근 거래 5건 |
| 은마아파트 가장 최근 실거래가 알려줘 | 최근 거래 1건 |
| 은마아파트 최근 거래 3건 보여줘 | 최근 거래 3건 |
| 은마아파트 전용 84㎡ 얼마야 | 면적 조건 포함 |

## 핵심 SQL

```sql
SELECT
    t.id AS trade_id,
    c.id AS complex_id,
    c.name AS complex_name,
    t.deal_date,
    t.deal_amount,
    t.excl_area,
    t.floor,
    t.apt_dong
FROM trades t
JOIN complexes c
    ON c.id = t.complex_id
WHERE t.complex_id = :complex_id
  AND (
        :area_min IS NULL
        OR t.excl_area BETWEEN :area_min AND :area_max
      )
ORDER BY
    t.deal_date DESC,
    t.id DESC
LIMIT :limit;
```

---

# 5. 최고가 조회

## query_type

```python
record_high
```

## 처리 기준

특정 단지의 전체 거래 중 가장 높은 거래금액을 조회한다.

면적 조건이 있으면 해당 면적 범위 안에서 최고가를 조회한다.

## 핵심 SQL

```sql
SELECT
    t.id AS trade_id,
    c.id AS complex_id,
    c.name AS complex_name,
    t.deal_date,
    t.deal_amount,
    t.excl_area,
    t.floor,
    t.apt_dong
FROM trades t
JOIN complexes c
    ON c.id = t.complex_id
WHERE t.complex_id = :complex_id
  AND (
        :area_min IS NULL
        OR t.excl_area BETWEEN :area_min AND :area_max
      )
ORDER BY
    t.deal_amount DESC,
    t.deal_date DESC,
    t.id DESC
LIMIT 1;
```

---

# 6. 위치 조회

## query_type

```python
location
```

## 처리 기준

특정 단지의 주소와 좌표를 조회한다.

```sql
SELECT
    c.id AS complex_id,
    c.name AS complex_name,
    c.trade_name,
    c.address,
    c.latitude,
    c.longitude
FROM complexes c
WHERE c.id = :complex_id;
```

---

# 7. 신고가 조회

## query_type

```python
recent_record_highs
```

## 신고가 기준

신고가는 기존 최고 거래금액보다 더 높은 금액으로 거래된 경우를 말한다.

신고가는 `단지 + 면적` 기준으로 판단한다.

같은 단지라도 면적이 다르면 별도로 신고가를 판단한다.

```
은마아파트 76㎡
은마아파트 84㎡
은마아파트 101㎡
→ 각각 따로 신고가 판단
```

## 조회 범위

| 조건 | 처리 |
| --- | --- |
| complex_id 있음 | 특정 단지 신고가 조회 |
| region_id 있음 | 특정 지역 신고가 조회 |
| complex_id와 region_id 모두 있음 | complex_id 우선 |
| 둘 다 없음 | 예외 처리 |

## 노출 정책

기간 내 같은 단지와 같은 면적에서 신고가가 여러 번 발생하면 가장 높은 신고가 1건만 반환한다.

```
기존 최고가 30억
5월 31억 → 신고가
6월 32억 → 신고가

반환: 6월 32억 1건
```

## 핵심 SQL 구조

```sql
WITH RECURSIVE target_regions AS (
    SELECT r.id
    FROM regions r
    WHERE :region_id IS NOT NULL
      AND r.id = :region_id

    UNION ALL

    SELECT child.id
    FROM regions child
    JOIN target_regions parent
        ON child.parent_id = parent.id
),

scope_complexes AS (
    SELECT c.id, c.name
    FROM complexes c
    WHERE
        (:complex_id IS NOT NULL AND c.id = :complex_id)
        OR
        (
            :complex_id IS NULL
            AND :region_id IS NOT NULL
            AND c.region_id IN (SELECT id FROM target_regions)
        )
),

period_trades AS (
    SELECT
        c.id AS complex_id,
        c.name AS complex_name,
        t.id AS trade_id,
        t.deal_date,
        t.deal_amount,
        t.excl_area,
        FLOOR(t.excl_area) AS area_group,
        t.floor,
        t.apt_dong
    FROM scope_complexes c
    JOIN trades t
        ON t.complex_id = c.id
    WHERE t.deal_date BETWEEN :start_date AND :end_date
      AND (
            :area_min IS NULL
            OR t.excl_area BETWEEN :area_min AND :area_max
          )
),

record_candidates AS (
    SELECT
        pt.*,
        (
            SELECT MAX(prev.deal_amount)
            FROM trades prev
            WHERE prev.complex_id = pt.complex_id
              AND FLOOR(prev.excl_area) = pt.area_group
              AND prev.deal_date < pt.deal_date
              AND (
                    :area_min IS NULL
                    OR prev.excl_area BETWEEN :area_min AND :area_max
                  )
        ) AS previous_high_price
    FROM period_trades pt
),

new_records AS (
    SELECT
        *,
        deal_amount - previous_high_price AS record_gap
    FROM record_candidates
    WHERE previous_high_price IS NOT NULL
      AND deal_amount > previous_high_price
),

dedup_records AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY complex_id, area_group
            ORDER BY deal_amount DESC, deal_date DESC, trade_id DESC
        ) AS rn
    FROM new_records
)

SELECT *
FROM dedup_records
WHERE rn = 1
ORDER BY deal_amount DESC, record_gap DESC
LIMIT :limit;
```

---

# 8. 시세추이 핸들러

## 구현 함수

```python
extract_trend_slots(question: str, base_date: date) -> TrendSlots
analyze_price_trend(slots: TrendSlots) -> dict
```

## TrendSlots

```python
class TrendSlots:
    original_question: str
    query_type: str
    # complex_trend | region_trend | price_change_ranking

    complex_name: str | None
    region_name: str | None

    area: float | None
    pyeong: float | None
    area_min: float | None
    area_max: float | None

    period: str | None
    start_date: str | None
    end_date: str | None

    group_by: str | None
    metric: str | None

    change_direction: str | None
    # up | down | absolute

    limit: int | None
```

## query_type 기준

| query_type | 설명 |
| --- | --- |
| complex_trend | 특정 단지 시세추이 |
| region_trend | 특정 지역 시세추이 |
| price_change_ranking | 가격 변화율 랭킹 조회 |

## 기간별 집계 기준

| 기간 | group_by |
| --- | --- |
| 최근 6개월 | month |
| 최근 1년 | month |
| 최근 3년 | quarter |
| 최근 5년 이상 | year |
| 전체 기간 | year |

기본 `metric`은 `median_price`로 처리한다.

---

# 9. 시세추이 조회

## query_type

```python
complex_trend
region_trend
```

## 처리 기준

특정 단지 또는 특정 지역의 기간별 가격 흐름을 조회한다.

단지 기준과 지역 기준은 하나의 SQL로 처리한다.

| 조건 | 처리 |
| --- | --- |
| complex_id 있음 | 특정 단지 기준 시세추이 조회 |
| complex_id 없음 + region_id 있음 | 특정 지역 기준 시세추이 조회 |
| complex_id와 region_id 모두 있음 | complex_id 우선 |
| 둘 다 없음 | 예외 처리 |

면적 조건이 있으면 해당 면적 범위만 조회한다.

하위 지역이 있으면 재귀적으로 포함한다.

## 핵심 SQL

```sql
WITH RECURSIVE target_regions AS (
    SELECT r.id
    FROM regions r
    WHERE :region_id IS NOT NULL
      AND r.id = :region_id

    UNION ALL

    SELECT child.id
    FROM regions child
    JOIN target_regions parent
        ON child.parent_id = parent.id
),

scope_complexes AS (
    SELECT c.id, c.name
    FROM complexes c
    WHERE
        (:complex_id IS NOT NULL AND c.id = :complex_id)
        OR
        (
            :complex_id IS NULL
            AND :region_id IS NOT NULL
            AND c.region_id IN (SELECT id FROM target_regions)
        )
)

SELECT
    DATE_TRUNC(:group_by, t.deal_date)::date AS period,
    PERCENTILE_CONT(0.5) WITHIN GROUP (
        ORDER BY t.deal_amount
    ) AS median_price,
    ROUND(AVG(t.deal_amount)) AS avg_price,
    MIN(t.deal_amount) AS min_price,
    MAX(t.deal_amount) AS max_price,
    COUNT(*) AS trade_count
FROM scope_complexes c
JOIN trades t
    ON t.complex_id = c.id
WHERE t.deal_date BETWEEN :start_date AND :end_date
  AND (
        :area_min IS NULL
        OR t.excl_area BETWEEN :area_min AND :area_max
      )
GROUP BY DATE_TRUNC(:group_by, t.deal_date)
ORDER BY period;
```

---

# 10. 가격 변화율 랭킹 조회

## query_type

```python
price_change_ranking
```

## 역할

특정 지역 또는 전체 단지 중 가격 변화율이 큰 단지를 조회한다.

“많이 오른 단지”, “많이 떨어진 단지”, “변동이 큰 단지”를 하나의 쿼리로 처리한다.

## change_direction

| 사용자 표현 | change_direction | 처리 |
| --- | --- | --- |
| 많이 오른 단지 | up | 상승률 높은 순 |
| 많이 떨어진 단지 | down | 하락률 큰 순 |
| 변동이 큰 단지 | absolute | 변화율 절댓값 큰 순 |

방향 표현이 없으면 기본값은 `absolute`로 처리한다.

## 처리 기준

- 기간이 없으면 최근 1년으로 처리한다.
- 시작 구간 대표 가격과 종료 구간 대표 가격을 비교한다.
- 대표 가격은 중앙값 기준이다.
- 같은 단지라도 면적이 다르면 따로 계산한다.
- 면적은 `FLOOR(excl_area)` 기준으로 묶는다.
- 비교 가능한 구간이 2개 이상 있어야 한다.
- 지역 조건이 없으면 전체 단지 기준으로 조회한다.

## 핵심 SQL 구조

```sql
WITH RECURSIVE target_regions AS (
    SELECT r.id
    FROM regions r
    WHERE :region_id IS NOT NULL
      AND r.id = :region_id

    UNION ALL

    SELECT child.id
    FROM regions child
    JOIN target_regions parent
        ON child.parent_id = parent.id
),

scope_complexes AS (
    SELECT c.id, c.name
    FROM complexes c
    WHERE
        :region_id IS NULL
        OR c.region_id IN (SELECT id FROM target_regions)
),

period_prices AS (
    SELECT
        c.id AS complex_id,
        c.name AS complex_name,
        FLOOR(t.excl_area) AS area_group,
        DATE_TRUNC(:group_by, t.deal_date)::date AS period,
        PERCENTILE_CONT(0.5) WITHIN GROUP (
            ORDER BY t.deal_amount
        ) AS median_price
    FROM scope_complexes c
    JOIN trades t
        ON t.complex_id = c.id
    WHERE t.deal_date BETWEEN :start_date AND :end_date
      AND (
            :area_min IS NULL
            OR t.excl_area BETWEEN :area_min AND :area_max
          )
    GROUP BY
        c.id,
        c.name,
        FLOOR(t.excl_area),
        DATE_TRUNC(:group_by, t.deal_date)
),

start_prices AS (
    SELECT DISTINCT ON (complex_id, area_group)
        complex_id,
        complex_name,
        area_group,
        period AS start_period,
        median_price AS start_price
    FROM period_prices
    ORDER BY complex_id, area_group, period ASC
),

end_prices AS (
    SELECT DISTINCT ON (complex_id, area_group)
        complex_id,
        area_group,
        period AS end_period,
        median_price AS end_price
    FROM period_prices
    ORDER BY complex_id, area_group, period DESC
),

price_changes AS (
    SELECT
        sp.complex_id,
        sp.complex_name,
        sp.area_group,
        sp.start_period,
        ep.end_period,
        sp.start_price,
        ep.end_price,
        ep.end_price - sp.start_price AS change_amount,
        ROUND(
            ((ep.end_price - sp.start_price)::numeric / sp.start_price) * 100,
            2
        ) AS change_rate,
        CASE
            WHEN ep.end_price > sp.start_price THEN 'up'
            WHEN ep.end_price < sp.start_price THEN 'down'
            ELSE 'same'
        END AS direction
    FROM start_prices sp
    JOIN end_prices ep
        ON ep.complex_id = sp.complex_id
       AND ep.area_group = sp.area_group
    WHERE sp.start_price > 0
      AND sp.start_period <> ep.end_period
)

SELECT *
FROM price_changes
WHERE
    (:change_direction = 'up' AND change_rate > 0)
    OR (:change_direction = 'down' AND change_rate < 0)
    OR (:change_direction = 'absolute' AND change_rate <> 0)
ORDER BY
    CASE WHEN :change_direction = 'up' THEN change_rate END DESC,
    CASE WHEN :change_direction = 'down' THEN change_rate END ASC,
    CASE WHEN :change_direction = 'absolute' THEN ABS(change_rate) END DESC
LIMIT :limit;
```

---

# 11. 예외 처리

예외 응답은 공통적으로 `success`, `reason`, `input_value`, `message`를 포함한다.

`input_value`는 사용자가 입력한 원본 값 또는 실패 원인이 된 값을 의미한다.

`reason`은 크게 4가지로 구분한다.

| reason | 설명 |
| --- | --- |
| target_not_found | 단지 또는 지역을 찾지 못한 경우 |
| ambiguous_target | 단지명 또는 지역명이 모호한 경우 |
| no_result | 조회 대상은 찾았지만 조건에 맞는 결과가 없는 경우 |
| invalid_request | 질문에서 필요한 조회 조건이 부족한 경우 |

## 조회 대상을 찾지 못한 경우

```json
{
  "success": false,
  "reason": "target_not_found",
  "target_type": "complex",
  "input_value": "존재하지않는아파트",
  "message": "조회 대상을 찾지 못했습니다."
}
```

## 조회 대상이 모호한 경우

```json
{
  "success": false,
  "reason": "ambiguous_target",
  "target_type": "complex",
  "input_value": "래미안",
  "candidates": [
    "래미안원베일리",
    "래미안대치팰리스"
  ],
  "message": "조회 대상이 모호합니다. 정확한 대상을 선택해주세요."
}
```

## 조회 결과가 없는 경우

```json
{
  "success": false,
  "reason": "no_result",
  "query_type": "recent_trades",
  "input_value": "은마아파트 전용 84㎡",
  "message": "조건에 맞는 조회 결과를 찾지 못했습니다."
}
```

## 요청 조건이 부족한 경우

```json
{
  "success": false,
  "reason": "invalid_request",
  "query_type": "recent_record_highs",
  "input_value": "신고가 TOP 5 알려줘",
  "message": "신고가 조회를 위해서는 아파트명 또는 지역명이 필요합니다."
}
```

---

# 12. 최종 Tool 목록

```python
extract_lookup_slots
lookup_complex_info

extract_trend_slots
analyze_price_trend
```

---

# 13. Tool과 Query 매핑

| Tool | query_type | 처리 |
| --- | --- | --- |
| lookup_complex_info | recent_trades | 최근 실거래가 조회 |
| lookup_complex_info | record_high | 최고가 조회 |
| lookup_complex_info | location | 위치 조회 |
| lookup_complex_info | recent_record_highs | 신고가 조회 |
| analyze_price_trend | complex_trend | 시세추이 조회 |
| analyze_price_trend | region_trend | 시세추이 조회 |
| analyze_price_trend | price_change_ranking | 가격 변화율 랭킹 조회 |

---

# 14. 전체 흐름

## 단순조회 흐름

```
사용자 질문
↓
LookupSlots 추출
↓
query_type 판단
↓
단지명 또는 지역명 후보 조회
↓
조회 대상 확정
↓
query_type에 맞는 SQL 실행
↓
결과 반환
```

## 시세추이 흐름

```
사용자 질문
↓
TrendSlots 추출
↓
query_type 판단
↓
단지명 또는 지역명 후보 조회
↓
조회 대상 확정
↓
기간/면적 조건 생성
↓
query_type에 맞는 SQL 실행
↓
가격 변화 요약 또는 가격 변화율 랭킹 계산
↓
결과 반환
```