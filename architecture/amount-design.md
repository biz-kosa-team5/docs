# 내가 구현할 기능

내가 담당하는 기능은 다음 세 가지다.

```
추천 핸들러
비교 핸들러
역·학교 좌표 Tool
```

추천 핸들러는 사용자의 조건을 받아 아파트를 필터링하고, 비교 핸들러는 사용자가 지정한 아파트들을 항목별로 비교한다.

역·학교 좌표 Tool은 “역 근처”, “학교 근처”, “가까운 역”, “가까운 학교” 같은 조건을 처리하기 위해 사용한다.

---

# 공통 전처리

## 추천 개수 제거 함수

사용자가 “몇 개 추천”, “몇 곳 추천”처럼 개수를 말해도 추천 조건으로 사용하지 않는다.

### 입력값

```json
{
  "question":"500세대 이상 아파트 3개 추천해줘"
}
```

### 반환값

```json
{
  "normalized_question":"500세대 이상 아파트 추천해줘"
}
```

### 처리 규칙

```
3개 추천 → 추천
3곳 추천 → 추천
몇 개 추천 → 추천
몇 곳 추천 → 추천
```

단, 조건 숫자는 제거하지 않는다.

```
500세대
30억
25평
3년
자녀 3명
강남 3구
```

---

# 추천 핸들러

## 역할

사용자 질문에서 조건을 추출하고, 해당 조건에 맞는 아파트를 DB에서 조회한다.

예시 질문:

```
30억 예산 아파트 추천해줘
500세대 이상 아파트 추천해줘
서초역 근처 아파트 알려줘
신축 아파트 추천해줘
초등학교 근처 아파트 추천해줘
25평 이상 아파트 싼 곳 추천해줘
서초역 근처 30억 이하 신축 아파트 추천해줘
```

---

## 구현할 함수

```python
extract_recommendation_slots(question: str)->RecommendationSlots
recommend_apartments_by_filters(slots: RecommendationSlots)->dict
```

LangChain Tool로 감쌀 경우 Tool 이름은 다음처럼 사용한다.

```python
recommend_apartments_by_filters
```

---

## 입력값

추천 핸들러는 자연어 질문에서 아래 슬롯을 추출해서 사용한다.

```python
classRecommendationSlots:
original_question:str
normalized_question:str

district:str|None
station_name:str|None
school_name:str|None
school_type:str|None

max_price:int|None
min_price:int|None
min_households:int|None
min_pyeong:float|None

is_new_build:bool
min_built_year:int|None

radius_m:int|None
sort_by:str|None
```

### 입력 예시

질문:

```
서초역 근처 30억 이하 신축 아파트 추천해줘
```

추출 결과:

```json
{
  "original_question":"서초역 근처 30억 이하 신축 아파트 추천해줘",
  "normalized_question":"서초역 근처 30억 이하 신축 아파트 추천해줘",
  "district":null,
  "station_name":"서초역",
  "school_name":null,
  "school_type":null,
  "max_price":300000,
  "min_price":null,
  "min_households":null,
  "min_pyeong":null,
  "is_new_build":true,
  "min_built_year":2020,
  "radius_m":800,
  "sort_by":"distance_asc"
}
```

가격은 DB에서 `만원` 단위로 통일한다.

```
30억 = 300000만원
```

---

## 처리 방식

추천 핸들러는 다음 순서로 동작한다.

```
질문 입력
↓
추천 개수 표현 제거
↓
추천 조건 슬롯 추출
↓
역 또는 학교 조건이 있으면 좌표 Tool 호출
↓
DB 필터 생성
↓
조건에 맞는 아파트 조회
↓
정렬 기준 적용
↓
결과 반환
```

---

## 조건별 처리 기준

| 사용자 표현 | 추출 슬롯 | 처리 |
| --- | --- | --- |
| `30억 예산` | `max_price=300000` | 30억 이하 필터 |
| `500세대 이상` | `min_households=500` | 세대수 필터 |
| `25평 이상` | `min_pyeong=25` | 평수 필터 |
| `신축` | `is_new_build=true` | 준공연도 필터 |
| `서초역 근처` | `station_name=서초역` | 역 좌표 기준 거리 필터 |
| `초등학교 근처` | `school_type=초등학교` | 학교 좌표 기준 거리 필터 |
| `싼 곳` | `sort_by=price_asc` | 가격 낮은 순 정렬 |

---

## 반환값

추천 핸들러는 문장이 아니라 구조화된 결과를 반환한다.

```json
{
  "handler":"recommendation",
  "success":true,
  "criteria": {
    "station_name":"서초역",
    "radius_m":800,
    "max_price":300000,
    "min_built_year":2020
  },
  "sort": [
"distance_asc",
"price_desc"
  ],
  "results": [
    {
      "apartment_id":"apt_001",
      "apartment_name":"예시아파트",
      "latest_price":285000,
      "pyeong":34,
      "households":1200,
      "built_year":2022,
      "station_name":"서초역",
      "station_distance_m":420,
      "school_name":null,
      "school_distance_m":null
    }
  ],
  "message":"조건에 맞는 아파트를 조회했습니다."
}
```

결과가 없을 때:

```json
{
  "handler":"recommendation",
  "success":false,
  "criteria": {
    "station_name":"서초역",
    "max_price":100000
  },
  "results": [],
  "message":"조건에 맞는 아파트를 찾지 못했습니다."
}
```

---

# 비교 핸들러

## 역할

사용자가 말한 아파트들을 찾아 같은 항목 기준으로 비교한다.

예시 질문:

```
은마아파트랑 잠실엘스 비교해줘
은마아파트와 잠실엘스 가격 비교해줘
은마아파트랑 잠실엘스 중 어디가 더 신축이야?
은마아파트랑 잠실엘스 세대수랑 가격 비교해줘
```

---

## 구현할 함수

```python
extract_compare_slots(question: str)->CompareSlots
compare_apartments_by_metrics(slots: CompareSlots)->dict
```

LangChain Tool로 감쌀 경우 Tool 이름은 다음처럼 사용한다.

```
compare_apartments_by_metrics
```

---

## 입력값

```python
classCompareSlots:
original_question:str
apartment_names:list[str]
metrics:list[str]|None

pyeong:float|None
transaction_type:str|None
period:str|None
```

### 입력 예시

질문:

```
은마아파트랑 잠실엘스 가격 비교해줘
```

추출 결과:

```json
{
  "original_question": "은마아파트랑 잠실엘스 가격 비교해줘",
  "apartment_names": [
    "은마아파트",
    "잠실엘스"
  ],
  "metrics": [
    "latest_price",
    "pyeong",
    "price_per_pyeong"
  ],
  "pyeong": null,
  "transaction_type": "매매",
  "period": null
}
```

---

## 처리 방식

```
질문 입력
↓
비교 대상 아파트명 추출
↓
아파트명 정규화 및 후보 매칭
↓
아파트 기본 정보 조회
↓
최근 실거래가 조회
↓
가까운 역·학교 정보 조회
↓
비교표 생성
↓
요약 생성
↓
결과 반환
```

---

## 기본 비교 항목

사용자가 특정 항목을 말하지 않으면 아래 항목을 기본으로 비교한다.

```
위치
최근 실거래가
거래 평형
평당가
세대수
준공연도
가까운 역
가까운 학교
```

사용자가 특정 항목을 말하면 해당 항목 중심으로 비교한다.

```
가격 비교 → 최근 실거래가, 평형, 평당가
신축 비교 → 준공연도
세대수 비교 → 세대수
```

---

## 반환값

```json
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "은마아파트",
      "잠실엘스"
    ],
    "price_policy": "latest_transaction",
    "area_policy": "latest_transaction_area"
  },
  "subjects": [
    {
      "apartment_id": "apt_001",
      "apartment_name": "은마아파트"
    },
    {
      "apartment_id": "apt_002",
      "apartment_name": "잠실엘스"
    }
  ],
  "table": [
    {
      "metric": "최근 실거래가",
      "은마아파트": "280000만원",
      "잠실엘스": "240000만원"
    },
    {
      "metric": "거래 평형",
      "은마아파트": "31평",
      "잠실엘스": "33평"
    },
    {
      "metric": "평당가",
      "은마아파트": "9032만원",
      "잠실엘스": "7272만원"
    },
    {
      "metric": "세대수",
      "은마아파트": "4424세대",
      "잠실엘스": "5678세대"
    },
    {
      "metric": "준공연도",
      "은마아파트": "1979년",
      "잠실엘스": "2008년"
    },
    {
      "metric": "가까운 역",
      "은마아파트": "대치역 350m",
      "잠실엘스": "잠실새내역 420m"
    }
  ],
  "summary": {
    "cheaper": "잠실엘스",
    "newer": "잠실엘스",
    "larger_complex": "잠실엘스"
  },
  "message": "아파트 비교 결과를 조회했습니다."
}
```

---

## 예외 반환

비교 대상이 부족할 때:

```json
{
  "handler":"comparison",
  "success":false,
  "reason":"not_enough_subjects",
  "message":"비교할 아파트를 두 개 이상 알려주세요."
}
```

아파트명이 모호할 때:

```json
{
  "success": false,
  "reason": "ambiguous_apartment_name",
  "input_name": "래미안",
  "candidates": [
    "래미안원베일리",
    "래미안대치팰리스",
    "래미안퍼스티지"
  ]
}
```

---

# 역·학교 좌표 Tool

## 역할

역이나 학교 관련 조건을 처리하기 위해 만든다.

사용되는 질문 예시:

```
서초역 근처 아파트 추천해줘
초등학교 근처 아파트 추천해줘
은마아파트랑 잠실엘스 중 어디가 역이 더 가까워?
은마아파트 근처 학교 알려줘
```

---

## 구현할 함수

```python
import_station_data()->dict
import_school_data()->dict

resolve_station(station_name: str)->dict
resolve_school(
school_name:str|None=None,
school_type:str|None=None,
district:str|None=None
)->dict

calculate_distance_m(
lat1:float,
lon1:float,
lat2:float,
lon2:float
)->float

find_apartments_near_station(
station_name:str,
radius_m:int=800
)->dict

find_apartments_near_school(
school_name:str|None=None,
school_type:str|None="초등학교",
district:str|None=None,
radius_m:int=800
)->dict

get_nearest_pois_for_apartment(
apartment_id:str
)->dict
```

---

## 역 좌표 조회 입력과 반환

### 입력값

```json
{
  "station_name":"서초역"
}
```

### 반환값

```json
{
  "success":true,
  "poi_type":"station",
  "station_id":"st_001",
  "station_name":"서초역",
  "line_name":"2호선",
  "latitude":37.4919,
  "longitude":127.0079
}
```

역을 찾지 못한 경우:

```json
{
  "success":false,
  "reason":"station_not_found",
  "station_name":"서초역",
  "message":"해당 역을 찾지 못했습니다."
}
```

---

## 학교 좌표 조회 입력과 반환

### 입력값

```json
{
  "school_type":"초등학교",
  "district":"서초구"
}
```

### 반환값

```json
{
  "success":true,
  "poi_type":"school",
  "results": [
    {
      "school_id":"sc_001",
      "school_name":"서울서초초등학교",
      "school_type":"초등학교",
      "district":"서초구",
      "latitude":37.49,
      "longitude":127.00
    }
  ]
}
```

학교를 찾지 못한 경우:

```json
{
  "success":false,
  "reason":"school_not_found",
  "message":"조건에 맞는 학교를 찾지 못했습니다."
}
```

---

## 역 근처 아파트 조회 입력과 반환

### 입력값

```json
{
  "station_name":"서초역",
  "radius_m":800
}
```

### 반환값

```json
{
  "success":true,
  "source":"cache",
  "criteria": {
    "station_name":"서초역",
    "radius_m":800
  },
  "results": [
    {
      "apartment_id":"apt_001",
      "apartment_name":"예시아파트",
      "distance_m":420
    }
  ]
}
```

캐시가 없으면 그때 계산하고 저장한다.

```json
{
  "success":true,
  "source":"calculated",
  "criteria": {
    "station_name":"서초역",
    "radius_m":800
  },
  "results": [
    {
      "apartment_id":"apt_001",
      "apartment_name":"예시아파트",
      "distance_m":420
    }
  ]
}
```

---

## 학교 근처 아파트 조회 입력과 반환

### 입력값

```json
{
  "school_type":"초등학교",
  "district":"서초구",
  "radius_m":800
}
```

### 반환값

```json
{
  "success":true,
  "source":"calculated",
  "criteria": {
    "school_type":"초등학교",
    "district":"서초구",
    "radius_m":800
  },
  "results": [
    {
      "apartment_id":"apt_003",
      "apartment_name":"예시아파트",
      "school_name":"서울서초초등학교",
      "distance_m":310
    }
  ]
}
```

---

# 거리 계산과 캐시 정책

역·학교 좌표는 미리 DB에 저장한다.

하지만 아파트와 모든 역·학교 사이의 거리는 미리 전부 저장하지 않는다.

처리 방식은 다음과 같다.

```
좌표는 미리 저장
거리는 질문이 들어왔을 때 계산
계산한 거리 중 일정 반경 안의 결과만 캐시 저장
같은 역이나 학교 질문이 다시 들어오면 캐시 사용
```

예시:

```
서초역 근처 아파트 추천
↓
서초역 좌표 조회
↓
거리 캐시 확인
↓
캐시 없으면 거리 계산
↓
서초역 주변 아파트 거리만 캐시 저장
↓
결과 반환
```

---

# 추가 DB 테이블

아파트 관련 테이블은 이미 있다고 보고, 내가 추가로 만들 테이블은 아래와 같다.

---

## stations

역 좌표를 저장하는 테이블이다.

```sql
CREATE TABLE stations (
    station_id VARCHAR(50) PRIMARY KEY,
    station_name VARCHAR(100) NOT NULL,
    normalized_name VARCHAR(100),
    line_name VARCHAR(50),
    is_transfer BOOLEAN DEFAULT FALSE,
    operator VARCHAR(100),
    road_address VARCHAR(255),
    district VARCHAR(50),
    latitude DOUBLE PRECISION NOT NULL,
    longitude DOUBLE PRECISION NOT NULL,
    data_source VARCHAR(100),
    data_date DATE,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 주요 사용처

```
서초역 근처 아파트 추천
아파트별 가까운 역 조회
비교표의 가까운 역 항목
```

---

## schools

학교 좌표를 저장하는 테이블이다.

```sql
CREATE TABLE schools (
    school_id VARCHAR(50) PRIMARY KEY,
    school_name VARCHAR(100) NOT NULL,
    normalized_name VARCHAR(100),
    school_type VARCHAR(50),
    operation_status VARCHAR(50),
    road_address VARCHAR(255),
    jibun_address VARCHAR(255),
    sido VARCHAR(50),
    district VARCHAR(50),
    latitude DOUBLE PRECISION NOT NULL,
    longitude DOUBLE PRECISION NOT NULL,
    data_source VARCHAR(100),
    data_date DATE,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 주요 사용처

```
초등학교 근처 아파트 추천
특정 학교 근처 아파트 추천
비교표의 가까운 학교 항목
```

---

## poi_aliases

역, 학교, 아파트의 별칭을 저장하는 테이블이다.

사용자가 정식 명칭이 아닌 줄임말로 질문할 수 있기 때문에 필요하다.

```sql
CREATE TABLE poi_aliases (
    alias_id BIGSERIAL PRIMARY KEY,
    target_type VARCHAR(50) NOT NULL,
    target_id VARCHAR(50) NOT NULL,
    official_name VARCHAR(100) NOT NULL,
    alias_name VARCHAR(100) NOT NULL,
    normalized_alias VARCHAR(100) NOT NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 예시 데이터

```
station | st_001 | 서초역 | 서초
school  | sc_001 | 서울서초초등학교 | 서초초
apartment | apt_001 | 은마아파트 | 은마
```

### 주요 사용처

```
서초 → 서초역
서초초 → 서울서초초등학교
은마 → 은마아파트
```

---

## poi_apartment_distance_cache

사용자가 실제로 요청한 역·학교 기준의 거리 계산 결과를 저장하는 테이블이다.

모든 아파트와 모든 역·학교 사이의 거리 조합을 저장하지 않고, 요청이 들어온 기준만 저장한다.

```sql
CREATE TABLE poi_apartment_distance_cache (
    cache_id BIGSERIAL PRIMARY KEY,
    poi_type VARCHAR(50) NOT NULL,
    poi_id VARCHAR(50) NOT NULL,
    poi_name VARCHAR(100) NOT NULL,
    apartment_id VARCHAR(50) NOT NULL,
    distance_m DOUBLE PRECISION NOT NULL,
    calculation_type VARCHAR(50) DEFAULT 'straight_line',
    computed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    source_version VARCHAR(100)
);
```

### 인덱스

```sql
CREATE INDEX idx_distance_cache_poi
ON poi_apartment_distance_cache (poi_type, poi_id, distance_m);

CREATE INDEX idx_distance_cache_apartment
ON poi_apartment_distance_cache (apartment_id);
```

### 주요 사용처

```
서초역 근처 아파트 추천
초등학교 근처 아파트 추천
같은 기준 질문 재사용
```

---

## apartment_nearest_pois

비교 기능에서 빠르게 보여줄 가까운 역·학교 정보를 저장하는 테이블이다.

모든 거리 조합이 아니라, 아파트별 가까운 시설만 저장한다.

```sql
CREATE TABLE apartment_nearest_pois (
    id BIGSERIAL PRIMARY KEY,
    apartment_id VARCHAR(50) NOT NULL,
    poi_type VARCHAR(50) NOT NULL,
    poi_subtype VARCHAR(50),
    poi_id VARCHAR(50) NOT NULL,
    poi_name VARCHAR(100) NOT NULL,
    distance_m DOUBLE PRECISION NOT NULL,
    rank_order INT NOT NULL,
    calculation_type VARCHAR(50) DEFAULT 'straight_line',
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 예시 데이터

```
apt_001 | station | 3호선 | st_001 | 대치역 | 350 | 1
apt_001 | school | 초등학교 | sc_001 | 서울대치초등학교 | 310 | 1
apt_002 | station | 2호선 | st_002 | 잠실새내역 | 420 | 1
```

### 주요 사용처

```
은마아파트랑 잠실엘스 비교해줘
→ 가까운 역, 가까운 학교 항목에 사용
```

---

# DB 테이블 관계

```
stations
    ↘
     poi_apartment_distance_cache
    ↗
existing apartments

schools
    ↗

existing apartments
    ↓
apartment_nearest_pois
    ↑
stations / schools

poi_aliases
    → stations / schools / apartments 이름 매칭 보조
```

---

# 필요한 데이터 출처

역과 학교 좌표는 DB에 저장한다.

API는 실시간 질문 처리용이 아니라 초기 적재와 보완용으로 사용한다.

| 목적 | 사용 데이터/API | 사용 방식 |
| --- | --- | --- |
| 역 좌표 | 전국도시철도역사정보표준데이터 | DB 저장 |
| 학교 좌표 | 전국초중등학교위치표준데이터 | DB 저장 |
| 서울 역 좌표 | 서울시 역사마스터 정보 | 서울 중심이면 사용 |
| 좌표 누락 보완 | Kakao Local API | 보완용 |
| 좌표 누락 보완 | Naver Geocoding API | 보완용 |

---

# 최종 구현 목록

내가 실제로 구현해야 할 것은 아래와 같다.

```
추천 질문 슬롯 추출
추천 조건 기반 DB 조회
비교 질문 아파트명 추출
비교 항목별 결과 생성
역 데이터 적재
학교 데이터 적재
역 이름으로 좌표 조회
학교 이름으로 좌표 조회
아파트와 역/학교 거리 계산
역 근처 아파트 조회
학교 근처 아파트 조회
거리 계산 결과 캐시 저장
아파트별 가까운 역/학교 조회
```

최종적으로 LangChain에 연결할 Tool은 다음과 같다.

```
recommend_apartments_by_filters
compare_apartments_by_metrics
resolve_station
resolve_school
find_apartments_near_station
find_apartments_near_school
get_nearest_pois_for_apartment
```

이렇게 만들면 추천은 조건 필터 조회로 처리하고, 비교는 항목별 비교로 처리하며, 역·학교 관련 조건은 좌표 Tool을 통해 공통 처리할 수 있다.
