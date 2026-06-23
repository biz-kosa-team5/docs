# Recommendation 질문별 처리 예시

이 문서는 내가 작성한 `recommendation` 코드만 기준으로 한다.

목적은 긴 로직 설명이 아니라, 질문이 들어왔을 때 `SLOT`이 어떤 모양이 되고 결과 `DTO`가 어떤 형태로 나오는지 케이스별로 빠르게 보는 것이다.

## 공통 처리 흐름

```text
질문
-> recommendation_tool.recommend_apartments()
-> extract_recommendation_slots()
-> LLM tool argument와 슬롯 병합
-> run_recommendation()
-> RecommendationService.run()
-> 조건 필터 / POI 필터 / 정렬 / limit
-> generate_recommendation_answer()
-> recommendation DTO 반환
```

## 출력 DTO 기본형

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {},
  "results": [],
  "message": "조건에 맞는 아파트를 조회했습니다.",
  "answer": "추천 답변"
}
```

## 1. 역 근처 추천

### 질문

```text
가락시장역 근처의 아파트를 추천해줘
```

### SLOT

```json
{
  "station_name": "가락시장역",
  "radius_m": 800,
  "sort_by": "distance_asc",
  "infra_preferences": ["transport"]
}
```

### 처리

가락시장역 POI를 찾고, 기본 반경 800m 안에 있는 아파트를 거리순으로 정렬한다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "가락시장역",
    "radius_m": 800,
    "sort_by": "distance_asc",
    "infra_preferences": ["transport"]
  },
  "results": [
    {
      "complexName": "동부썬빌",
      "address": "가락동 99-6",
      "latestDealAmountText": "10.5억원",
      "pyeong": 43.07,
      "distanceM": 128.17,
      "infrastructure": {
        "nearestStation": {
          "name": "가락시장역",
          "distanceM": 128.17
        },
        "nearestEducation": {
          "name": "서울평화초등학교",
          "distanceM": 334.71
        }
      }
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 2. 역 반경 직접 지정

### 질문

```text
가락시장역 300m 안에 있는 아파트 3개 추천해줘
```

### SLOT

```json
{
  "station_name": "가락시장역",
  "radius_m": 300,
  "sort_by": "distance_asc",
  "limit": 3
}
```

### 처리

가락시장역 POI 기준 300m 안에 있는 아파트만 남기고, 결과 개수를 3개로 제한한다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "가락시장역",
    "radius_m": 300,
    "sort_by": "distance_asc",
    "limit": 3
  },
  "results": [
    {
      "complexName": "동부썬빌",
      "latestDealAmountText": "10.5억원",
      "pyeong": 43.07,
      "distanceM": 128.17
    },
    {
      "complexName": "두산위브",
      "latestDealAmountText": "1.4억원",
      "pyeong": 8.42,
      "distanceM": 201.65
    },
    {
      "complexName": "이연파레스",
      "latestDealAmountText": "12.0억원",
      "pyeong": 29.04,
      "distanceM": 223.37
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 3. 다른 역 근처 추천

### 질문

```text
잠실역 근처 아파트 추천해줘
```

### SLOT

```json
{
  "station_name": "잠실역",
  "radius_m": 800,
  "sort_by": "distance_asc",
  "infra_preferences": ["transport"]
}
```

### 처리

잠실역 이름을 DB의 역 이름과 맞춘 뒤, 잠실역 주변 아파트를 거리순으로 찾는다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "잠실역",
    "radius_m": 800,
    "sort_by": "distance_asc",
    "infra_preferences": ["transport"]
  },
  "results": [
    {
      "complexName": "롯데캐슬골드",
      "latestDealAmountText": "27.8억원",
      "pyeong": 50.43,
      "distanceM": 144.17,
      "nearestStation": "잠실(송파구청)역"
    },
    {
      "complexName": "잠실시그마타워",
      "latestDealAmountText": "23.0억원",
      "pyeong": 61.06,
      "distanceM": 161.13,
      "nearestStation": "잠실(송파구청)역"
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 4. 지역 + 가격 상한

### 질문

```text
강남구 30억 이하 아파트 추천해줘
```

### SLOT

```json
{
  "district": "강남구",
  "max_price": 300000
}
```

### 처리

강남구 아파트 중 최신 거래가가 30억 이하인 후보를 찾는다. 가격 단위는 DB에서 사용하는 만원 단위 기준이다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "강남구",
    "max_price": 300000,
    "min_price": 0,
    "limit": 5
  },
  "results": [
    {
      "complexName": "경남",
      "address": "도곡동 967",
      "latestDealAmountText": "21.0억원",
      "pyeong": 18.08
    },
    {
      "complexName": "경복",
      "address": "논현동 276",
      "latestDealAmountText": "6.5억원",
      "pyeong": 28.88
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 5. 신축 조건

### 질문

```text
서초구 신축 아파트 추천해줘
```

### SLOT

```json
{
  "district": "서초구",
  "school_type": "초등학교",
  "radius_m": 800,
  "is_new_build": true,
  "min_built_year": 2020
}
```

### 처리

서초구에서 `useDate`가 2020년 이후인 아파트를 찾는다. 현재 슬롯 추출 로직에서는 `서초구`의 `초` 때문에 `school_type`도 같이 잡힌다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "서초구",
    "school_type": "초등학교",
    "radius_m": 800,
    "is_new_build": true,
    "min_built_year": 2020
  },
  "results": [
    {
      "complexName": "더클래스",
      "latestDealAmountText": "5.0억원",
      "pyeong": 8.73,
      "nearestEducation": "서울교육대학교부설초등학교"
    },
    {
      "complexName": "반포르엘",
      "latestDealAmountText": "56.0억원",
      "pyeong": 36.79,
      "nearestEducation": "서울반원초등학교"
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 6. 세대수 조건

### 질문

```text
1000세대 이상 대단지 추천해줘
```

### SLOT

```json
{
  "min_households": 1000
}
```

### 처리

`unitCnt`가 1000 이상인 대단지 후보만 남긴다.

### 결과 DTO

```json
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "min_households": 1000
  },
  "results": [
    {
      "complexName": "은마",
      "latestDealAmountText": "34.0억원",
      "pyeong": 23.23,
      "unitCnt": 4424
    },
    {
      "complexName": "리센츠",
      "latestDealAmountText": "35.9억원",
      "pyeong": 25.71,
      "unitCnt": 5563
    }
  ],
  "message": "조건에 맞는 아파트를 조회했습니다."
}
```

## 7. 평형 조건만 있는 질문

### 질문

```text
30평 이상 아파트 추천해줘
```

### SLOT

```json
{
  "min_pyeong": 30.0
}
```

### 처리

`slots.py`는 `min_pyeong`을 만들지만, 실제 API에서는 agent가 추천 tool을 선택하지 않았다.

### 결과 DTO

```json
{
  "success": false,
  "reason": "no_matching_tool",
  "message": "현재 챗봇은 부동산 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 관련 법령 질문을 처리할 수 있습니다."
}
```

## 8. 현재 슬롯이 없는 질문

### 질문

```text
4인 가족이 살 수 있는 집을 추천해줘
```

### SLOT

```json
{}
```

### 처리

현재 recommendation 슬롯에는 `family_size`, `room_count` 같은 필드가 없다. 그래서 추천 조건으로 연결되지 않고 처리 가능한 tool을 찾지 못한다.

### 결과 DTO

```json
{
  "success": false,
  "reason": "no_matching_tool",
  "message": "현재 챗봇은 부동산 단지 조회, 아파트 추천, 단지 비교, 시세 추이, 계약 관련 법령 질문을 처리할 수 있습니다."
}
```

## 9. Recommendation에서 자주 달라지는 DTO 형태

### 역 기준 추천

```json
{
  "criteria_fields": ["station_name", "radius_m", "sort_by", "infra_preferences"],
  "result_fields": ["matchedPois", "distanceM", "infrastructure.nearestStation"]
}
```

### 지역 + 가격 추천

```json
{
  "criteria_fields": ["district", "min_price", "max_price", "limit"],
  "result_fields": ["latestDealAmount", "latestDealAmountText", "pyeong"]
}
```

### 학교 기준 추천

```json
{
  "criteria_fields": ["school_type", "school_types", "radius_m", "sort_by"],
  "result_fields": ["infrastructure.nearestEducation", "infrastructure.nearestEducationByType"]
}
```

### 세대수 추천

```json
{
  "criteria_fields": ["min_households"],
  "result_fields": ["unitCnt"]
}
```

### tool 선택 실패

```json
{
  "criteria_fields": [],
  "result_fields": ["reason", "message"]
}
```
