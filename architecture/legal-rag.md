# 법령 RAG 파트 공유용 요약본

## 1. 담당 기능

아파트 매매 관련 법령 질문에 답변하는 RAG 모듈을 구현한다.

사용자는 일상적인 문장으로 질문하고, 시스템은 국가법령 API에서 수집한 법령 조문을 기반으로 관련 내용을 검색해 답변한다.

```txt
사용자 질문
→ 질문 확장
→ 조문 벡터 검색
→ 근거 조문 기반 답변 생성
```

---

## 2. 대표 질문

초기 기준 질문은 아래 3개로 둔다.

```txt
1. 30억 아파트 매매 시 알아야 할 법률이 있을까?
2. 아파트 매매 시 세금 책정 관련 법을 알려줘.
3. 매매 계약서에서 중요하게 볼 부분은 어디야?
```

---

## 3. 수집 데이터

수집 데이터는 2종류로 구성한다.

```txt
1. 법령 조문 데이터
2. 일상용어 → 법령용어 매핑 데이터
```

법령 조문 데이터는 RAG 검색 대상이다.  
일상용어 → 법령용어 매핑 데이터는 사용자 질문을 법령 검색에 적합한 문장으로 확장하는 데 사용한다.

---

## 4. 수집 대상 법령

초기 수집 대상 법령은 아래와 같다.

```txt
민법
공인중개사법
부동산 거래신고 등에 관한 법률
부동산 거래신고 등에 관한 법률 시행령
부동산등기법
부동산등기규칙
지방세법
지방세법 시행령
소득세법
소득세법 시행령
인지세법
주택임대차보호법
집합건물의 소유 및 관리에 관한 법률
종합부동산세법
상속세 및 증여세법
```

| 법령 | 사용 목적 |
|---|---|
| 민법 | 매매계약, 계약금, 해제, 손해배상 |
| 공인중개사법 | 중개대상물 확인·설명, 중개 책임 |
| 부동산 거래신고 등에 관한 법률 | 실거래신고, 자금조달계획서 |
| 부동산등기법 | 소유권이전등기, 등기부, 권리관계 |
| 지방세법 | 취득세, 재산세 |
| 소득세법 | 양도소득세 |
| 인지세법 | 매매계약서 인지세 |
| 주택임대차보호법 | 임차인, 대항력, 우선변제권 |
| 집합건물법 | 전유부분, 공용부분, 대지권 |
| 상속세 및 증여세법 | 증여, 자금출처 |

---

## 5. 법령 데이터 처리 방식

법령 데이터는 아래 순서로 처리한다.

```txt
국가법령 API 목록조회
→ 수집 대상 법령 확인
→ 법령 본문조회
→ Raw JSON 저장
→ 조문 단위 파싱
→ 기본 메타데이터 저장
→ 임베딩 텍스트 생성
→ VectorDB 저장
```

조문 임베딩 텍스트는 아래 형식으로 만든다.

```txt
법령명: {law_name}
법령구분: {law_type}
조문: {article_no}
조문제목: {article_title}
내용:
{article_text}
```

---

## 6. 기본 메타데이터

MVP에서는 조문별 `topic`, `intent`, `transaction_stage` 태깅을 사용하지 않는다.

API에서 제공되는 기본 메타데이터만 저장한다.

```json
{
  "law_id": "법령ID",
  "mst": "법령일련번호",
  "law_name": "민법",
  "law_type": "법률",
  "ministry": "법무부",
  "article_no": "제565조",
  "article_title": "해약금",
  "paragraph_no": null,
  "doc_type": "article",
  "effective_date": "2026-06-17",
  "source_url": "국가법령 원문 링크"
}
```

---

## 7. 일상용어 → 법령용어 매핑

사용자 질문의 일상어를 법령 검색에 적합한 용어로 확장한다.

```txt
집 → 주택, 부동산
집 살 때 → 취득, 매수, 매매
세입자 → 임차인
명의 이전 → 소유권이전등기
빚 잡힌 집 → 근저당권, 저당권, 가압류
부모님 돈 → 증여, 자금조달
```

수집 방향은 `일상용어 → 법령용어`만 사용한다.  
`법령용어 → 일상용어` 매핑은 사용하지 않는다.

매핑 테이블은 아래 필드를 가진다.

```txt
daily_term
legal_term
relation_type
domain
priority
use_for_rewrite
use_for_expansion
raw_data
```

---

## 8. 사용자 질문 처리 방식

사용자 질문은 아래 순서로 처리한다.

```txt
사용자 질문 입력
→ 질문 정규화
→ 일상용어 후보 추출
→ 매핑 테이블 조회
→ 법령용어 변환문 생성
→ 검색 확장어 생성
→ 최종 임베딩 텍스트 생성
→ 조문 벡터 검색
→ 답변 생성
```

예시:

```txt
사용자 질문:
집을 살 때 알아야 할 법이 있을까?

법령용어 변환 질문:
주택을 취득할 때 알아야 할 법이 있을까?

검색 확장어:
주택, 부동산, 취득, 매수, 매매, 매매계약, 실거래신고, 자금조달계획서, 취득세, 소유권이전등기
```

최종 임베딩 텍스트:

```txt
사용자 질문: 집을 살 때 알아야 할 법이 있을까?
법령용어 변환 질문: 주택을 취득할 때 알아야 할 법이 있을까?
검색 확장어: 주택, 부동산, 취득, 매수, 매매, 매매계약, 실거래신고, 자금조달계획서, 취득세, 소유권이전등기
```

---

## 9. DB 구성

### Raw API 응답 테이블

```sql
CREATE TABLE raw_api_responses (
    id BIGSERIAL PRIMARY KEY,
    source_type VARCHAR(50) NOT NULL,
    target VARCHAR(50),
    query VARCHAR(255),
    request_url TEXT,
    response_json JSONB,
    status VARCHAR(50),
    error_message TEXT,
    collected_at TIMESTAMP DEFAULT NOW()
);
```

### 법령 조문 테이블

```sql
CREATE TABLE law_documents (
    id BIGSERIAL PRIMARY KEY,
    law_id VARCHAR(50),
    mst VARCHAR(50),
    law_name VARCHAR(255),
    law_type VARCHAR(50),
    ministry VARCHAR(100),
    article_no VARCHAR(50),
    article_title VARCHAR(255),
    paragraph_no VARCHAR(50),
    doc_type VARCHAR(50),
    content TEXT NOT NULL,
    metadata JSONB,
    source_url TEXT,
    effective_date DATE,
    collected_at TIMESTAMP DEFAULT NOW(),
    embedding vector(1536)
);
```

### 일상용어-법령용어 매핑 테이블

```sql
CREATE TABLE daily_legal_term_mappings (
    id BIGSERIAL PRIMARY KEY,
    daily_term VARCHAR(255) NOT NULL,
    legal_term VARCHAR(255) NOT NULL,
    relation_type VARCHAR(50),
    domain VARCHAR(100),
    priority INT DEFAULT 0,
    use_for_rewrite BOOLEAN DEFAULT TRUE,
    use_for_expansion BOOLEAN DEFAULT TRUE,
    raw_data JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);
```
