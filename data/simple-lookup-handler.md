# 단순조회 핸들러 문서

## 1. 목적

단순조회 핸들러는 특정 아파트 단지 1개를 대상으로 단순 DB 조회를 수행하는 모듈임.

앞단에서는 사용자 질문을 분석해 슬롯을 만들고, 단순조회 핸들러는 전달받은 슬롯을 기반으로 위치, 실거래 내역, 최고가 거래를 조회함.

지원하는 조회 유형은 다음과 같음.

```text
location       : 단지 위치/주소/좌표 조회
trade_history  : 단지 실거래 내역 조회
record_high    : 단지 최고가 거래 조회
```

---

## 2. 전체 흐름

```text
사용자 질문
→ 앞단에서 슬롯 추출
→ run_simple_lookup(session, slots)
→ SimpleLookupSlots 유효성 검증
→ normalize_simple_lookup_policy()
→ SimpleLookupCriteria 생성
→ query_type에 맞는 DAO 메서드 선택
→ DB 조회
→ SimpleLookupResult 반환
```

정리하면 다음과 같음.

```text
앞단 역할:
사용자 질문을 보고 query_type, complex_name, area, period, limit 등을 슬롯으로 추출함

단순조회 핸들러 역할:
전달받은 슬롯을 검증하고 DB 조회 결과를 구조화해서 반환함
```

---

## 3. 질문 예시와 슬롯 예시

## 3.1 위치 조회

질문 예시:

```text
은마아파트 어디야?
은마아파트 위치 알려줘
은마아파트 주소 알려줘
```

슬롯 예시:

```json
{
  "original_question": "은마아파트 어디야?",
  "query_type": "location",
  "complex_name": "은마아파트"
}
```

---

## 3.2 실거래 내역 조회

질문 예시:

```text
은마아파트 최근 거래 알려줘
은마아파트 최근 5건 보여줘
은마아파트 전용 84㎡ 최근 실거래 알려줘
은마아파트 최근 1년 거래 내역 보여줘
```

슬롯 예시:

```json
{
  "original_question": "은마아파트 전용 84㎡ 최근 1년 실거래 5건 보여줘",
  "query_type": "trade_history",
  "complex_name": "은마아파트",
  "area": 84,
  "period": "1y",
  "limit": 5
}
```

“은마아파트 얼마야?”, “은마아파트 가격 알려줘” 같은 질문도 현재 단순조회 기준에서는 최근 실거래 조회로 보고 `trade_history`로 전달함.

---

## 3.3 최고가 조회

질문 예시:

```text
은마아파트 최고가 알려줘
은마아파트 가장 비싼 거래 알려줘
은마아파트 전용 84㎡ 최고가는 얼마야?
은마아파트 최근 1년 최고가 알려줘
```

슬롯 예시:

```json
{
  "original_question": "은마아파트 전용 84㎡ 최고가는 얼마야?",
  "query_type": "record_high",
  "complex_name": "은마아파트",
  "area": 84
}
```

---

## 4. 입력 슬롯 형식

단순조회 핸들러는 다음 형식의 슬롯을 입력으로 받음.

```json
{
  "original_question": "은마아파트 전용 84㎡ 최근 1년 실거래 5건 보여줘",
  "query_type": "trade_history",
  "complex_name": "은마아파트",
  "area": 84,
  "pyeong": null,
  "period": "1y",
  "start_date": null,
  "end_date": null,
  "limit": 5
}
```

| 필드                  | 설명                                                |
| ------------------- | ------------------------------------------------- |
| `original_question` | 원본 사용자 질문                                         |
| `query_type`        | 조회 유형. `location`, `trade_history`, `record_high` |
| `complex_name`      | 조회할 아파트 단지명                                       |
| `area`              | 전용면적. 예: `84`                                     |
| `pyeong`            | 평형. 예: `34`                                       |
| `period`            | 상대 기간. 예: `6m`, `1y`                              |
| `start_date`        | 조회 시작일                                            |
| `end_date`          | 조회 종료일                                            |
| `limit`             | 실거래 내역 조회 개수                                      |

---

## 5. 반환 형식

단순조회 핸들러는 성공/실패 모두 동일한 구조로 반환함.

```json
{
  "handler": "simple_lookup",
  "success": true,
  "query_type": "trade_history",
  "criteria": {},
  "data": [],
  "reason": null,
  "message": "",
  "candidates": []
}
```

---

## 5.1 성공 응답 예시

```json
{
  "handler": "simple_lookup",
  "success": true,
  "query_type": "trade_history",
  "criteria": {
    "query_type": "trade_history",
    "complex_name": "은마아파트",
    "area_min": 83,
    "area_max": 85,
    "start_date": "2025-06-20",
    "end_date": "2026-06-20",
    "limit": 5
  },
  "data": [
    {
      "complex_id": 1,
      "complex_name": "은마아파트",
      "trade_name": "은마",
      "deal_date": "2026-06-10",
      "deal_amount": 300000,
      "exclusive_area": 84.93,
      "floor": 10,
      "apt_dong": "101"
    }
  ],
  "reason": null,
  "message": "실거래 내역을 조회했습니다.",
  "candidates": []
}
```

---

## 5.2 실패 응답 예시

```json
{
  "handler": "simple_lookup",
  "success": false,
  "query_type": "trade_history",
  "criteria": {},
  "data": [],
  "reason": "target_not_found",
  "message": "일치하는 아파트 단지를 찾지 못했습니다.",
  "candidates": []
}
```

---

## 5.3 주요 실패 reason

| reason             | 의미                       |
| ------------------ | ------------------------ |
| `invalid_request`  | 슬롯 형식이 잘못되었거나 지원하지 않는 요청 |
| `target_not_found` | 단지명을 DB에서 찾지 못함          |
| `ambiguous_target` | 단지 후보가 여러 개 검색됨          |
| `no_result`        | 단지는 찾았지만 조건에 맞는 결과가 없음   |

---

## 6. 정리

단순조회 핸들러의 핵심 흐름은 다음과 같음.

```text
슬롯 받기
→ 슬롯 유효성 검증
→ 조회 조건 생성
→ 단지명 확정
→ query_type별 DB 조회
→ 구조화된 결과 반환
```

앞단에서 가장 중요하게 만들어야 하는 값은 다음과 같음.

```text
query_type
complex_name
area 또는 pyeong
period 또는 start_date/end_date
limit
original_question
```
