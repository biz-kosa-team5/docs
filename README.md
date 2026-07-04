# 강남 3구 실거래가 서비스 문서

이 저장소는 강남구, 서초구, 송파구 아파트 실거래가 조회 서비스의 v1 설계를 관리한다. v1의 기준은 화면 표시가 가능한 public read model이며, `home-search` public React 화면을 FastAPI 조회 API와 최소 DB로 구동하는 것을 목표로 한다.

## 문서 목록

- [문서 폴더 구조](docs-directory-structure.md)
- [프로젝트 개요](architecture/project-overview.md)
- [프론트엔드 이식 기준](architecture/frontend-porting.md)
- [API 계약](architecture/api-contract.md)
- [최소 ERD](database/erd.md)
- [레거시 스냅샷 적재](data/legacy-snapshot-import.md)
- [챗봇 QA 사전 점검](qa/chatbot-qa-preflight-checklist.md)
- [챗봇 QA 실패 묶음](qa/chatbot-qa-failure-triage-2026-06-30.md)
- [챗봇 Legal RAG 재검증](qa/chatbot-qa-legal-rag-recheck-2026-06-30.md)
- [로드맵](planning/roadmap.md)

## 작업 순서

1. docs PR에서 포함/제외 범위와 API/DB 계약을 확정한다.
2. web PR에서 `home-search/apps/web`의 public React/Vite 화면을 이식한다.
3. server PR에서 public 화면이 호출하는 FastAPI 조회 API와 최소 DB 모델을 구현한다.
4. 강남 3구 스냅샷/시드 데이터를 적재하고 smoke test를 수행한다.

각 저장소는 독립 Git 저장소이므로 브랜치, 커밋, PR을 반드시 저장소별로 분리한다.
