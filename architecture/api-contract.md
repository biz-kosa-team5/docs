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
