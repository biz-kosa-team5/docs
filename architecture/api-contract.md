# API 계약

모든 public API는 JSON을 반환한다. list endpoint는 프론트엔드 normalizer와 호환되도록 배열을 직접 반환한다.

## Health

### `GET /health`

응답:

```json
{ "status": "ok" }
```

## 지도

### `POST /api/v1/map/regions`

요청은 `home-search` public web client의 flat body를 기준으로 한다.

```json
{
  "swLat": 37.45,
  "swLng": 126.95,
  "neLat": 37.55,
  "neLng": 127.15,
  "region": "si-gun-gu"
}
```

응답:

```json
[
  {
    "id": 11680,
    "name": "강남구",
    "lat": 37.5172,
    "lng": 127.0473,
    "unitCntSum": 120000
  }
]
```

### `POST /api/v1/map/complexes`

요청:

```json
{
  "swLat": 37.45,
  "swLng": 126.95,
  "neLat": 37.55,
  "neLng": 127.15,
  "pyeongMin": null,
  "pyeongMax": null,
  "priceEokMin": 10,
  "priceEokMax": 50,
  "ageMin": null,
  "ageMax": null,
  "unitMin": null,
  "unitMax": null
}
```

응답:

```json
[
  {
    "parcelId": 9001001,
    "complexId": 1001,
    "name": "래미안대치팰리스",
    "lat": 37.4979,
    "lng": 127.058,
    "latestDealAmount": 420000,
    "unitCntSum": 1608
  }
]
```

좌표가 없는 단지는 이 응답에서 제외한다.

## 검색

### `GET /api/v1/search/complexes/suggestions?q=`

응답:

```json
[
  {
    "complexId": 1001,
    "complexName": "래미안대치팰리스",
    "parcelId": 9001001,
    "address": "서울특별시 강남구 대치동"
  }
]
```

### `GET /api/v1/search/complexes?q=`

응답:

```json
[
  {
    "complexId": 1001,
    "complexName": "래미안대치팰리스",
    "parcelId": 9001001,
    "latitude": 37.4979,
    "longitude": 127.058,
    "address": "서울특별시 강남구 대치동"
  }
]
```

## 지역

### `GET /api/v1/region`

응답:

```json
[
  { "id": 11680, "name": "강남구" }
]
```

### `GET /api/v1/region/{regionId}`

응답:

```json
{
  "id": 11680,
  "name": "강남구",
  "latitude": 37.5172,
  "longitude": 127.0473,
  "children": []
}
```

### `GET /api/v1/region/{regionId}/complexes?limit=&offset=`

응답:

```json
[
  {
    "complexId": 1001,
    "complexName": "래미안대치팰리스",
    "parcelId": 9001001,
    "latitude": 37.4979,
    "longitude": 127.058,
    "address": "서울특별시 강남구 대치동",
    "dongCnt": 13,
    "unitCnt": 1608,
    "useDate": "2015-09-18"
  }
]
```

## 상세

### `GET /api/v1/detail/{parcelId}?complexId=`

응답:

```json
{
  "parcelId": 9001001,
  "complexId": 1001,
  "latitude": 37.4979,
  "longitude": 127.058,
  "address": "서울특별시 강남구 대치동",
  "tradeName": "래미안대치팰리스",
  "name": "래미안대치팰리스",
  "dongCnt": 13,
  "unitCnt": 1608,
  "platArea": null,
  "archArea": null,
  "totArea": null,
  "bcRat": null,
  "vlRat": null,
  "useDate": "2015-09-18"
}
```

### `GET /api/v1/detail/{parcelId}/complexes`

동일 `parcelId`를 공유하는 단지 목록을 지역 단지 summary와 같은 배열 shape로 반환한다.

## 거래

### `GET /api/v1/trade/{parcelId}?complexId=&page=&size=`

`page`는 프론트 호환을 위해 0부터 시작한다.

```json
{
  "parcelId": 9001001,
  "complexId": 1001,
  "content": [
    {
      "tradeId": 5001,
      "dealDate": "2026-01-15",
      "exclArea": 84.97,
      "dealAmount": 420000,
      "aptDong": "101",
      "floor": 12
    }
  ],
  "page": 0,
  "size": 20,
  "totalElements": 1,
  "totalPages": 1
}
```

### `GET /api/v1/trade/{parcelId}/trend?complexId=`

응답:

```json
[
  {
    "month": "2026-01",
    "avgAmount": 420000,
    "count": 2,
    "minAmount": 405000,
    "maxAmount": 435000
  }
]
```

## 단지

### `GET /api/v1/complex/{complexId}`

단지 상세 한 건을 반환한다.

### `GET /api/v1/complex/{complexId}/trades?page=&size=`

단지 기준 거래 목록을 거래 page shape로 반환한다.

### `GET /api/v1/complex/{complexId}/trade-trend`

단지 기준 월별 추세 배열을 반환한다.

## 챗봇

### `POST /api/v1/chatbot/query`

요청:

```json
{
  "question": "잠실엘스 시세 알려줘"
}
```

응답은 기존 자연어/원본 tool payload 필드를 유지하고, 지도 이동과 챗봇 패널 시각 자료를 위한 additive top-level field를 함께 반환한다.

기존 필드:

- `answer`: 사용자에게 그대로 표시할 최종 자연어 답변
- `fragments`: 분할 질문별 처리 결과
- `result`: 단일 또는 복수 tool 결과
- `executionSummary`: 처리 개수 요약

신규 필드:

- `uiActions`: 프론트 앱 상태를 바꾸는 동작 목록
- `uiArtifacts`: 챗봇 말풍선 아래에 렌더링할 compact 시각 자료 목록
- `uiSummary`: answer composer가 UI 동작을 자연스럽게 언급할 수 있도록 만든 좌표 없는 요약

예시:

```json
{
  "success": true,
  "status": "success",
  "question": "잠실엘스 시세 알려줘",
  "answer": "잠실엘스는 최근 실거래와 1년 흐름을 함께 보는 게 좋습니다. 단지 위치는 지도에 표시했습니다.",
  "fragments": [],
  "result": {},
  "message": "질문을 처리했습니다.",
  "executionSummary": {
    "total": 1,
    "succeeded": 1,
    "failed": 0
  },
  "uiActions": [
    {
      "id": "focus_map:complex:1002",
      "type": "focus_map",
      "label": "잠실엘스 지도 보기",
      "autoRun": true,
      "priority": "primary",
      "source": "simple_lookup.trade_history",
      "target": {
        "kind": "complex",
        "name": "잠실엘스",
        "complexId": 1002,
        "parcelId": 9001002,
        "latitude": 37.5124,
        "longitude": 127.0821,
        "level": 4,
        "openDetail": true
      }
    }
  ],
  "uiArtifacts": [
    {
      "id": "trend_line_chart:complex:잠실엘스",
      "type": "trend_line_chart",
      "title": "잠실엘스 시세 흐름",
      "source": "price_trend.timeseries",
      "unit": "만원",
      "points": [
        { "period": "2025-06", "value": 300000, "count": 1 },
        { "period": "2026-05", "value": 315000, "count": 2 }
      ]
    }
  ],
  "uiSummary": {
    "hasMapFocus": true,
    "primaryTargetName": "잠실엘스",
    "primaryActionLabel": "잠실엘스 지도 보기",
    "artifactTypes": ["trend_line_chart"]
  }
}
```

#### `uiActions`

v1은 `focus_map`만 지원한다.

```ts
type ChatbotUiAction = {
  id: string;
  type: 'focus_map';
  label: string;
  autoRun: boolean;
  priority: 'primary' | 'secondary';
  source: string;
  target: {
    kind: 'complex' | 'region';
    name: string;
    complexId: number | null;
    parcelId: number | null;
    latitude: number;
    longitude: number;
    level: number;
    openDetail: boolean;
  };
};
```

실행 규칙:

- `latitude`, `longitude`, `level`이 유효한 숫자인 action만 실행한다.
- 응답당 `autoRun=true`는 최대 1개다.
- complex target 기본 level은 `4`, region target 기본 level은 `7`이다.
- `target.kind=complex`이고 `openDetail=true`이면 프론트는 상세 패널을 연다.
- `target.kind=region`이면 지도만 이동하고 기존 상세 패널은 임의로 닫지 않는다.

#### `uiArtifacts`

v1 type은 4종이다.

- `comparison_bar_chart`: 비교 가능한 numeric metric의 compact bar chart
- `trend_line_chart`: 월별 시세 흐름 mini line chart
- `ranking_list`: 가격 변화율 또는 최고가/최저가 순위
- `recommendation_list`: 추천 후보 compact list

artifact item의 `actionId`가 `uiActions[].id`와 일치하면 프론트는 해당 item click/button으로 같은 `focus_map` 동작을 실행한다.

## Pagination 기준

- `page`는 0부터 시작한다.
- `size` 기본값은 20, 최대값은 100이다.
- `limit` 기본값은 20, 최대값은 100이다.
- `offset` 기본값은 0이다.

## Filter 기준

- `priceEokMin`, `priceEokMax`는 억 단위이며 `deal_amount / 10000`과 비교한다.
- `pyeongMin`, `pyeongMax`는 `excl_area / 3.3058`과 비교한다.
- 지도 bounds 필터는 단지 좌표 기준이다.
- 좌표가 없는 단지는 지도 bounds 조회에서 제외한다.

## Trend 기준

- 월별 그룹 키는 `YYYY-MM`이다.
- 평균가는 해당 월 거래가의 산술 평균이다.
- `count`는 해당 월 거래 건수다.
- 정렬은 오래된 월에서 최신 월 순서다.
