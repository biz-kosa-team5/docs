# 레거시 스냅샷 적재

## 추출 기준

`home-search` 레거시 데이터에서 강남 3구만 추출한다.

- 강남구: `11680`
- 서초구: `11650`
- 송파구: `11710`

## 적재 대상

- 지역
- 단지
- 단지 좌표
- 거래 내역

## 적재 컬럼

### regions

- `id`
- `code`
- `name`
- `type`
- `parent_id`
- `center_lat`
- `center_lng`
- `unit_cnt_sum`

### complexes

- `id`
- `region_id`
- `parcel_id`
- `pnu`
- `name`
- `trade_name`
- `address`
- `latitude`
- `longitude`
- `dong_cnt`
- `unit_cnt`
- `use_date`

### trades

- `id`
- `complex_id`
- `deal_date`
- `deal_amount`
- `excl_area`
- `floor`
- `apt_dong`

## 적재 제외

- 원본 payload
- 수집 이력
- 실패 사유
- 백필 상태
- 관리자 보정 데이터

## 좌표 없는 데이터 처리

- 좌표 없는 단지는 `complexes.latitude`, `complexes.longitude`를 null로 저장한다.
- 지도 마커 조회에서는 제외한다.
- 검색, 상세, 거래 목록에서는 반환한다.
- 좌표 보정 관리자나 보정 이력 테이블은 v1에 포함하지 않는다.

## 스냅샷 검증

- 강남 3구 외 지역 데이터가 섞이지 않았는지 확인한다.
- 좌표가 있는 단지 수와 없는 단지 수를 분리 집계한다.
- 거래 목록이 단지 id로 연결되는지 확인한다.
- 월별 추세 집계가 거래 원장과 일치하는지 확인한다.
