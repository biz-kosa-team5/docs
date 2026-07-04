# 문서 폴더 구조

## 구조

- `README.md`: 프로젝트 목표, 문서 목록, 작업 순서
- `architecture/`: 시스템 범위, 프론트엔드 이식 기준, API 계약
- `database/`: read model ERD, 좌표 정책, 인덱스
- `data/`: 레거시 데이터 추출 및 적재 기준
- `qa/`: 챗봇 질문지 실행 결과, live LLM QA 요약, 전체 실행 전 preflight 체크리스트
- `planning/`: 단계별 작업 로드맵과 검증 기준

## 작성 규칙

- v1 public 화면 표시와 직접 관련된 결정만 문서화한다.
- 제외 범위는 포함 범위만큼 명확히 적는다.
- API 문서는 프론트엔드가 소비하는 요청/응답 필드를 기준으로 작성한다.
- DB 문서는 서비스 표시용 read model을 기준으로 작성하고 raw ingest 구조를 포함하지 않는다.
- 구현 변경이 API shape 또는 DB shape를 바꾸면 docs PR을 먼저 갱신한다.
