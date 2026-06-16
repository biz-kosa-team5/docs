# 프론트엔드 이식 기준

## 기준

`home-search/apps/web`의 public React/Vite 코드를 기준으로 이식한다. 화면 구성, 사용자 흐름, API 경로는 가능한 한 유지하고, admin 기능은 v1 public 빌드에서 제외한다.

## 유지 기능

- Kakao 지도 로딩 및 지도 surface
- 지역 마커 조회
- 단지 마커 조회
- 지도 bounds 기반 탐색
- 단지 검색과 자동완성
- 필터 상태
- 지역 목록 및 지역별 단지 목록
- 단지 상세 sidebar
- 거래 목록 pagination
- 거래 추세 표시

## 제외 기능

- admin route
- admin API client
- 좌표 보정 관리자 화면
- 메타데이터 관리자 화면
- 관리자 전용 테스트와 navigation

## API 호환 기준

- public API base path는 `/api/v1/...` 형태를 유지한다.
- 프론트 API client는 FastAPI 응답 shape와 호환되어야 한다.
- `parcelId`는 프론트 호환 필드로 유지하되 서버에서는 `complexes.parcel_id` 값을 제공한다.
- 지도 마커는 좌표가 있는 단지만 표시한다.
- 검색과 상세 화면은 좌표가 없는 단지도 표시할 수 있다.

## 운영 세팅 참고

`biz-team3`에서는 PR 템플릿, README 구성, 일반 운영 파일 구조만 참고한다. 구현 코드와 도메인 로직은 가져오지 않는다.
