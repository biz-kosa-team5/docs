# 로드맵

## 1. Docs

- 프로젝트 목표와 v1 범위를 확정한다.
- API 계약과 최소 ERD를 문서화한다.
- 좌표 정책과 데이터 적재 기준을 확정한다.

검증:

- 문서 링크 확인
- Mermaid ERD 확인
- 포함/제외 범위 확인

## 2. Web

- `home-search/apps/web` public React/Vite 코드를 이식한다.
- admin route와 admin API client를 public 빌드에서 제외한다.
- `/api/v1/...` API 경로를 유지한다.

검증:

- `npm run test`
- `npm run build`
- 검색 → 마커 → 상세 → 거래 목록 → 추세 흐름 확인

## 3. Server

- FastAPI 앱을 구성한다.
- public 조회 endpoint만 구현한다.
- 최소 DB schema와 seed 데이터를 제공한다.

검증:

- endpoint response shape 테스트
- 검색/자동완성 테스트
- 지도 bounds 테스트
- pagination 테스트
- trend aggregation 테스트
- 좌표 없는 단지의 marker 제외 테스트

## 4. Data

- 레거시 데이터에서 강남 3구 스냅샷을 추출한다.
- 표시용 컬럼만 새 DB에 적재한다.
- 좌표 없는 단지 처리 정책을 검증한다.

## 5. Smoke Test

- 서버를 실행한다.
- 웹을 실행한다.
- 검색, 지도 마커, 단지 상세, 거래 목록, 거래 추세 흐름을 확인한다.
