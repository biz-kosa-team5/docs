# 프로젝트 개요

## 목표

강남 3구 아파트 실거래가를 지도와 검색 중심으로 조회할 수 있는 public 웹 서비스를 만든다. 사용자는 지역을 탐색하고, 단지를 검색하거나 지도 마커로 선택한 뒤, 단지 상세 정보와 거래 목록 및 거래 추세를 확인할 수 있어야 한다.

## 기준 저장소 역할

- `docs`: v1 범위, API 계약, DB read model, 데이터 적재 기준을 정의한다.
- `web`: `home-search/apps/web` public React/Vite 화면을 이식한다.
- `server`: FastAPI로 public 조회 API와 최소 DB 모델을 구현한다.

## 포함 범위

- Kakao 지도
- 지역 마커
- 단지 마커
- 단지 검색
- 자동완성
- 필터
- 지역 탐색
- 단지 상세
- 거래 목록
- 거래 추세
- 강남 3구 데이터 적재용 스냅샷/시드

## 제외 범위

- RTMS 백필 시스템
- 실시간 RTMS 수집 배치
- raw ingest 저장
- source key registry
- 좌표 보정 관리자
- 메타데이터 관리자
- `/admin/coordinates`
- `/admin/metadata`
- 추천, 알림, 즐겨찾기, 랭킹, 인증

## v1 원칙

- 화면 표시가 가능한 최소 read model을 우선한다.
- `parcel` 테이블은 만들지 않는다.
- 지도 좌표는 `complexes.latitude`, `complexes.longitude`에 직접 저장한다.
- 좌표 없는 단지는 지도 마커에서 제외하지만 검색과 상세 API에서는 반환할 수 있다.
