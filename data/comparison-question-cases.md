# Comparison 질문별 처리 예시

이 문서는 내가 작성한 `comparison` 코드만 기준으로 한다.

목적은 긴 로직 설명이 아니라, 질문이 들어왔을 때 `SLOT`이 어떤 모양이 되고 결과 `DTO`가 어떤 형태로 나오는지 케이스별로 빠르게 보는 것이다.

## 공통 처리 흐름

```text
질문
-> comparison_tool.compare_apartments()
-> extract_compare_slots()
-> LLM tool argument와 슬롯 병합
-> run_comparison()
-> ComparisonService.run()
-> 아파트명 조회
-> metric에 맞는 데이터 생성
-> 역/학교 metric이면 POI 거리 계산
-> generate_comparison_answer()
-> comparison DTO 반환
```

## 출력 DTO 기본형

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {},
  "results": [],
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다.",
  "answer": "비교 답변"
}
```

## 1. 전체 기본 비교

### 질문

```text
래미안대치팰리스와 반포자이 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["래미안대치팰리스", "반포자이"],
  "metrics": [
    "latest_price",
    "pyeong",
    "price_per_pyeong",
    "households",
    "built_year",
    "nearest_station",
    "nearest_school"
  ]
}
```

### 처리

두 단지를 찾고 기본 metric 전체를 비교한다.

### 결과 DTO

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": ["래미안대치팰리스", "반포자이"],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year",
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "results": [
    {
      "complexName": "래미안대치팰리스",
      "latestDealAmountText": "44.0억원",
      "pyeong": 25.7,
      "pricePerPyeongText": "1.7억원",
      "unitCnt": 1608,
      "builtYear": 2015,
      "nearestStation": "도곡역",
      "nearestSchool": "단국대학교부속소프트웨어고등학교"
    },
    {
      "complexName": "반포자이",
      "latestDealAmountText": "45.0억원",
      "pyeong": 25.71,
      "pricePerPyeongText": "1.8억원",
      "unitCnt": 3410,
      "builtYear": 2009,
      "nearestStation": "반포역",
      "nearestSchool": "서울원촌초등학교"
    }
  ],
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
```

## 2. 가격 비교

### 질문

```text
래미안대치팰리스랑 압구정현대 가격 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["래미안대치팰리스", "압구정현대"],
  "metrics": ["latest_price", "pyeong", "price_per_pyeong"]
}
```

### 처리

가격 metric 중심으로 비교한다. DB에서 일부 단지를 못 찾으면 `missingApartmentNames`에 넣는다.

### 결과 DTO

```json
{
  "handler": "comparison",
  "success": false,
  "criteria": {
    "apartment_names": ["래미안대치팰리스", "압구정현대"],
    "metrics": ["latest_price"],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "results": [
    {
      "complexName": "래미안대치팰리스",
      "latestDealAmountText": "44.0억원"
    }
  ],
  "missingApartmentNames": ["압구정현대"],
  "message": "일부 아파트를 찾지 못했습니다."
}
```

## 3. 가격 + 학교 거리 비교

### 질문

```text
동부썬빌이랑 두산위브 가격이랑 학교 거리 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["동부썬빌", "두산위브   거리"],
  "metrics": ["latest_price", "pyeong", "price_per_pyeong", "nearest_school"],
  "infra_preferences": ["education"]
}
```

### 처리

가격과 `nearest_school`을 비교한다. LLM tool argument가 두 번째 아파트명을 `두산위브`로 보정했다.

### 결과 DTO

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": ["동부썬빌", "두산위브"],
    "metrics": ["latest_price", "nearest_school"],
    "school_type": null,
    "school_name": null,
    "infra_preferences": ["education"]
  },
  "results": [
    {
      "complexName": "동부썬빌",
      "latestDealAmountText": "10.5억원",
      "nearestSchool": "서울평화초등학교",
      "nearestSchoolDistanceM": 334.71
    },
    {
      "complexName": "두산위브",
      "latestDealAmountText": "16.5억원",
      "nearestSchool": "계성초등학교",
      "nearestSchoolDistanceM": 417.42
    }
  ],
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
```

## 4. 세대수 + 연식 비교

### 질문

```text
성원상떼빌과 롯데캐슬 세대수랑 연식 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["성원상떼빌", "롯데캐슬  연식"],
  "metrics": ["households", "built_year"]
}
```

### 처리

세대수와 준공연도만 비교한다. 가격/역/학교 필드는 결과에 포함되지 않는다.

### 결과 DTO

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": ["성원상떼빌", "롯데캐슬"],
    "metrics": ["households", "built_year"],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "results": [
    {
      "complexName": "성원상떼빌",
      "unitCnt": 324,
      "builtYear": 2006
    },
    {
      "complexName": "롯데캐슬",
      "unitCnt": 142,
      "builtYear": 2002
    }
  ],
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
```

## 5. 교통 비교

### 질문

```text
잠실엘스랑 반포자이 교통 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["잠실엘스", "반포자이"],
  "metrics": ["nearest_station"],
  "infra_preferences": ["transport"]
}
```

### 처리

`nearest_station` metric으로 가까운 역을 비교한다. 실제 API에서는 agent가 comparison tool을 2번 호출해서, 최종 응답의 `results` 안에 comparison DTO가 2개 들어갔다.

### 결과 DTO

```json
{
  "success": true,
  "results": [
    {
      "handler": "comparison",
      "criteria": {
        "apartment_names": ["잠실엘스", "반포자이"],
        "metrics": ["nearest_station"],
        "infra_preferences": ["transport"]
      },
      "results": [
        {
          "complexName": "잠실엘스",
          "nearestStation": "종합운동장역",
          "nearestStationDistanceM": 403.7
        },
        {
          "complexName": "반포자이",
          "nearestStation": "반포역",
          "nearestStationDistanceM": 212.08
        }
      ]
    },
    {
      "handler": "comparison",
      "criteria": {
        "apartment_names": ["잠실엘스", "반포자이"],
        "metrics": ["nearest_school", "nearest_station"],
        "infra_preferences": ["transport"]
      },
      "results": [
        {
          "complexName": "잠실엘스",
          "nearestStation": "종합운동장역",
          "nearestStationDistanceM": 403.7,
          "nearestSchool": "서울잠일초등학교",
          "nearestSchoolDistanceM": 212.03
        },
        {
          "complexName": "반포자이",
          "nearestStation": "반포역",
          "nearestStationDistanceM": 212.08,
          "nearestSchool": "서울원촌초등학교",
          "nearestSchoolDistanceM": 148.96
        }
      ]
    }
  ]
}
```

## 6. 상권/생활편의 비교 요청

### 질문

```text
래미안대치팰리스와 반포자이 상권 비교해줘
```

### SLOT

```json
{
  "apartment_names": ["래미안대치팰리스", "반포자이"],
  "metrics": ["nearest_station", "nearest_school"],
  "infra_preferences": ["commercial"]
}
```

### 처리

현재 DB에는 상권 POI가 없어서 `commercial`은 역/학교 proxy metric으로 바뀐다. 이 경우 결과에는 `infrastructureNotes`가 붙을 수 있다.

### 결과 DTO 형태

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "infra_preferences": ["commercial"],
    "metrics": ["nearest_station", "nearest_school"]
  },
  "results": [
    {
      "complexName": "래미안대치팰리스",
      "nearestStation": {},
      "nearestSchool": {},
      "infrastructureNotes": [
        "상권/생활편의 POI 데이터는 현재 DB에 없어 역과 교육시설 데이터만 근거로 비교합니다."
      ]
    }
  ]
}
```

## 7. 이름이 2개 미만인 비교 질문

### 질문

```text
래미안대치팰리스 비교해줘
```

### SLOT

```json
{
  "apartment_names": []
}
```

### 처리

comparison service는 `apartment_names`가 2개 미만이면 바로 실패 응답을 반환한다.

### 결과 DTO

```json
{
  "handler": "comparison",
  "success": false,
  "reason": "missing_apartment_names",
  "message": "비교할 아파트명을 2개 이상 입력해야 합니다."
}
```

## 8. Comparison에서 자주 달라지는 DTO 형태

### 가격 비교

```json
{
  "criteria_fields": ["apartment_names", "metrics"],
  "metric_values": ["latest_price", "pyeong", "price_per_pyeong"],
  "result_fields": ["latestDealAmount", "latestDealAmountText", "pyeong", "pricePerPyeong"]
}
```

### 교통 비교

```json
{
  "criteria_fields": ["apartment_names", "metrics", "infra_preferences"],
  "metric_values": ["nearest_station"],
  "result_fields": ["nearestStation"]
}
```

### 교육 비교

```json
{
  "criteria_fields": ["apartment_names", "metrics", "infra_preferences", "school_type", "school_name"],
  "metric_values": ["nearest_school"],
  "result_fields": ["nearestSchool"]
}
```

### 세대수 + 연식 비교

```json
{
  "criteria_fields": ["apartment_names", "metrics"],
  "metric_values": ["households", "built_year"],
  "result_fields": ["unitCnt", "builtYear"]
}
```

### 일부 단지 조회 실패

```json
{
  "criteria_fields": ["apartment_names", "metrics"],
  "result_fields": ["missingApartmentNames", "message"]
}
```
