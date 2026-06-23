# 챗봇 질문 처리 플로우 상세 문서

이 문서는 `POST /api/v1/chatbot/query`로 들어온 자연어 질문이 어떤 함수들을 거쳐 처리되는지 설명한다.

핵심 구조는 다음과 같다.

```text
사용자 질문
  -> FastAPI controller
  -> chatbot_service
  -> 질문 split
  -> ChatbotAgent
  -> LangChain tool 선택
  -> tool별 slots 구성
  -> feature service 실행
  -> 필요하면 RAG 답변 생성
  -> 최종 JSON 응답
```

중요한 점:

- 질문 유형은 직접 `if recommendation`, `if comparison`처럼 분기하지 않는다.
- LangChain agent가 등록된 tool 설명을 보고 어떤 tool을 호출할지 결정한다.
- 각 tool은 다시 자기 feature의 `slots.py`, `service.py`, 필요하면 `rag_answer.py`를 호출한다.

---

## 1. 전체 요청 시작점

파일: `app/chatbot/controller/chatbot_controller.py`

```text
query_by_natural_language()
```

역할:

1. `/chatbot/query` POST 요청을 받는다.
2. request body를 `ChatbotQueryRequest`로 받는다.
3. DB session을 `Depends(get_session)`로 주입받는다.
4. 실제 처리는 `handle_chatbot_query(session, payload.model_dump())`에 위임한다.

즉 controller는 강의에서 배운 것처럼 얇다. 요청을 받고 service로 넘기는 역할만 한다.

---

## 2. 질문 전체 처리 함수

파일: `app/chatbot/service/chatbot_service.py`

```text
handle_chatbot_query(session, payload)
```

처리 순서:

```text
payload에서 question 꺼냄
  -> ChatbotAgent(session) 생성 시도
  -> split_question(question)
  -> fragment마다 handle_fragment()
  -> fragment 결과를 모음
  -> 하나라도 success면 전체 success=true
  -> 최종 응답 dict 반환
```

코드상 중요한 경우의 수:

### 2.1 Agent 생성 성공

```text
agent = ChatbotAgent(session)
```

이후 각 질문 조각은 `agent.run(text)`로 처리된다.

### 2.2 Agent 생성 실패

```text
except Exception:
  agent = None
```

이 경우 `handle_fragment()`에서 실제 agent 실행 대신 `agent_execution_failed_result()`를 반환한다.

결과 형태:

```json
{
  "success": false,
  "reason": "agent_execution_failed"
}
```

### 2.3 질문 조각이 하나인 경우

최종 응답의 `result`는 단일 dict다.

```json
{
  "success": true,
  "question": "...",
  "fragments": [...],
  "result": { "...": "..." }
}
```

### 2.4 질문 조각이 여러 개인 경우

최종 응답의 `result`는 list다.

```json
{
  "success": true,
  "question": "...",
  "fragments": [...],
  "result": [
    { "...": "..." },
    { "...": "..." }
  ]
}
```

전체 `success`는 fragment 중 하나라도 성공하면 `true`다.

---

## 3. 질문 Split 방식

파일: `app/chatbot/service/splitter.py`

```text
split_question(question)
```

현재 splitter는 정규식 `SPLIT_PATTERN`으로 질문을 나눈다.

의도는 이런 복합 질문을 처리하기 위한 것이다.

```text
"래미안대치팰리스 조회하고 강남구 아파트 추천해줘"
"A 단지 찾아주고 B 단지랑 비교해줘"
```

처리 방식:

```text
question.strip()
  -> SPLIT_PATTERN.split(...)
  -> 각 조각 strip()
  -> 빈 문자열 제거
  -> list[str] 반환
```

예상 예시:

```text
입력:
"래미안대치팰리스 조회하고 강남구 40억 이하 추천해줘"

분리:
[
  "래미안대치팰리스",
  "강남구 40억 이하 추천해줘"
]
```

### 3.1 Split 기준 표현

현재 split 기준은 `app/chatbot/service/splitter.py`의 `SPLIT_PATTERN`에 들어 있다.

```text
그리고
또
추천하고
조회하고
알려주고
찾아주고
해주고
추천해주고
찾아보고
```

즉 splitter는 위 표현을 기준으로 질문을 자른다.

예시:

```text
입력:
"30억 이하 아파트 추천하고 매매 계약 법률 알려줘"

split 결과:
[
  "30억 이하 아파트",
  "매매 계약 법률 알려줘"
]
```

```text
입력:
"신논현역 근처 추천해주고 래미안대치팰리스랑 비교해줘"

split 결과:
[
  "신논현역 근처",
  "래미안대치팰리스랑 비교해줘"
]
```

```text
입력:
"전세 계약 특약 알려주고 강남구 아파트 추천해줘"

split 결과:
[
  "전세 계약 특약",
  "강남구 아파트 추천해줘"
]
```

### 3.2 Split 이후 데이터 모양

`handle_chatbot_query()`는 split 결과를 바로 응답의 `fragments`에 넣지 않는다.
먼저 각 조각을 `handle_fragment()`로 처리하고, 처리 결과를 붙여서 `fragments`를 만든다.

입력:

```text
"래미안대치팰리스 조회하고 강남구 40억 이하 추천해줘"
```

`split_question()` 결과:

```json
[
  "래미안대치팰리스",
  "강남구 40억 이하 추천해줘"
]
```

최종 응답의 `fragments` 모양:

```json
[
  {
    "index": 0,
    "text": "래미안대치팰리스",
    "status": "handled",
    "result": {
      "success": true,
      "handler": "simple_lookup",
      "queryType": "location"
    }
  },
  {
    "index": 1,
    "text": "강남구 40억 이하 추천해줘",
    "status": "handled",
    "result": {
      "success": true,
      "handler": "recommendation",
      "criteria": {
        "district": "강남구",
        "max_price": 400000
      }
    }
  }
]
```

위 JSON은 구조를 보여주기 위한 예시다.
실제 응답에는 `results`, `message`, `answer` 같은 필드가 더 붙을 수 있다.

주의:

- splitter는 의미 분석을 하지 않는다.
- 단순히 연결 표현을 기준으로 문자열을 자른다.
- 실제 질문 유형 판단은 split 이후 `ChatbotAgent`가 한다.

---

## 4. Fragment 처리

파일: `app/chatbot/service/chatbot_service.py`

```text
handle_fragment(agent, index, text)
```

처리 순서:

```text
agent가 None인가?
  -> yes: agent_execution_failed_result()
  -> no: await agent.run(text)

예외 발생?
  -> agent_execution_failed_result()

result.success가 true인가?
  -> status="handled"
  -> 아니면 status="not_handled"
```

반환 형태:

```json
{
  "index": 0,
  "text": "질문 조각",
  "status": "handled",
  "result": { "...": "..." }
}
```

---

## 5. Agent 생성과 Tool 등록

파일: `app/chatbot/service/agent.py`

```text
ChatbotAgent.__init__(session, model=None)
```

처리 순서:

```text
build_chatbot_tools(session)
  -> simple_lookup tool 생성
  -> recommendation tool 생성
  -> comparison tool 생성
  -> price_trend tool 생성
  -> legal_contract tool 생성

create_agent(
  model=...,
  tools=위 tool 목록,
  system_prompt=CHATBOT_AGENT_SYSTEM_PROMPT
)
```

등록되는 tool 목록:

```text
simple_lookup
recommend_apartments
compare_apartments
analyze_price_trend
search_legal_contract
```

### 5.1 Fragment별 Tool 분기 예시

split된 각 fragment는 `ChatbotAgent.run(fragment_text)`로 따로 들어간다.
그 다음 LangChain agent가 tool 설명과 질문 내용을 보고 하나의 tool을 고른다.

예시 1. 단순 조회 + 추천

```text
원본 질문:
"래미안대치팰리스 조회하고 강남구 40억 이하 추천해줘"

split 결과:
[
  "래미안대치팰리스",
  "강남구 40억 이하 추천해줘"
]

fragment[0]:
"래미안대치팰리스"
  -> ChatbotAgent.run()
  -> simple_lookup tool 선택
  -> query_type="location" 또는 단지 기본 조회 계열

fragment[1]:
"강남구 40억 이하 추천해줘"
  -> ChatbotAgent.run()
  -> recommend_apartments tool 선택
  -> extract_recommendation_slots()
  -> district="강남구"
  -> max_price=400000
```

예시 2. 추천 + 법률 RAG

```text
원본 질문:
"30억 이하 아파트 추천하고 매매 계약 법률 알려줘"

split 결과:
[
  "30억 이하 아파트",
  "매매 계약 법률 알려줘"
]

fragment[0]:
"30억 이하 아파트"
  -> recommend_apartments
  -> max_price=300000

fragment[1]:
"매매 계약 법률 알려줘"
  -> search_legal_contract
  -> legal_contract RAG 검색
```

예시 3. 비교 질문

```text
원본 질문:
"래미안대치팰리스랑 은마아파트 가격이랑 학교 거리 비교해줘"

split 결과:
[
  "래미안대치팰리스랑 은마아파트 가격이랑 학교 거리 비교해줘"
]

fragment[0]:
  -> compare_apartments
  -> extract_compare_slots()
  -> apartment_names=["래미안대치팰리스", "은마아파트"]
  -> metrics=["latest_price", "nearest_school", ...]
```

예시 4. 가격 추세 질문

```text
원본 질문:
"래미안대치팰리스 최근 가격 추세 알려줘"

split 결과:
[
  "래미안대치팰리스 최근 가격 추세 알려줘"
]

fragment[0]:
  -> analyze_price_trend
  -> complex_trend 계열 처리
```

중요한 점:

```text
splitter
  -> 문자열을 조각으로 나누기만 함

ChatbotAgent
  -> 각 조각이 어떤 tool로 갈지 결정함

tool 내부 slots.py
  -> tool이 받은 조각에서 필요한 조건값을 추출함
```

분기 방식:

```text
사용자 질문
  -> ChatbotAgent.run()
  -> LangChain agent가 tool 설명과 질문을 보고 tool 선택
  -> 선택된 tool 호출
```

즉 코드 안에 이런 분기는 없다.

```python
if "추천" in question:
  run_recommendation(...)
```

대신 agent가 `recommend_apartments`, `compare_apartments` 같은 tool 설명을 보고 선택한다.

---

## 6. Agent 실행과 결과 파싱

파일: `app/chatbot/service/agent.py`

### 6.1 실행

```text
ChatbotAgent.run(question)
```

처리:

```text
self.agent.ainvoke({
  "messages": [{"role": "user", "content": question}]
})
  -> LangChain agent 실행
  -> tool 호출 결과 포함된 messages 반환
  -> extract_agent_result(result)
```

### 6.2 결과 추출

```text
extract_agent_result(result)
```

처리 순서:

```text
result["messages"] 순회
  -> parse_tool_message(message)
  -> tool message만 dict로 파싱
  -> tool_results 리스트 생성
```

경우의 수:

```text
tool_results 없음
  -> no_matching_tool_result()

tool_results 1개
  -> 그 결과 그대로 반환

tool_results 여러 개
  -> {"success": any(...), "results": tool_results}
```

### 6.3 tool message 파싱

```text
parse_tool_message(message)
```

처리:

```text
message.type != "tool"
  -> None

content가 dict
  -> 그대로 반환

content가 list
  -> parse_tool_content_list()

content가 str
  -> parse_tool_content_text()
```

```text
parse_tool_content_text(content)
```

문자열 tool 결과는 두 방식으로 파싱을 시도한다.

1. `json.loads`
2. `ast.literal_eval`

둘 중 하나가 dict를 만들면 tool 결과로 사용한다.

---

## 7. Tool 공통 패턴

모든 tool은 거의 같은 구조다.

```text
build_xxx_tool(session)
  -> @tool 함수 정의
  -> query를 받음
  -> feature의 extract_xxx_slots(query) 호출
  -> LLM이 tool argument로 채운 값 중 None이 아닌 값만 slots에 merge
  -> run_xxx(session, slots, query) 호출
  -> dict 반환
```

여기서 중요한 함수:

파일: `app/chatbot/service/tools/utils.py`

```text
compact_none(values)
```

역할:

- tool argument 중 `None`인 값은 버린다.
- LLM이 실제로 채운 값만 slots에 덮어쓴다.

예시:

```python
slots = extract_recommendation_slots(query)
slots.update(compact_none({
  "district": district,
  "station_name": station_name,
  "max_price": max_price,
}))
```

의미:

1. 먼저 rule 기반 slots를 만든다.
2. LLM이 tool 인자로 더 정확히 채운 값이 있으면 덮어쓴다.
3. None 값은 덮어쓰지 않는다.

---

## 8. 단순 조회 플로우

질문 예:

```text
"래미안대치팰리스 어디 있어?"
"래미안대치팰리스 최근 거래 보여줘"
"래미안대치팰리스 최고가 알려줘"
```

### 8.1 전체 흐름

```text
ChatbotAgent
  -> simple_lookup tool 선택
  -> extract_simple_lookup_slots(query)
  -> run_simple_lookup(session, slots, query)
  -> SimpleLookupSlots 검증
  -> SimpleLookupService.handle()
  -> query_type별 처리
```

### 8.2 query_type 분기

`simple_lookup/slots.py`의 `infer_query_type(text)`가 질문을 보고 유형을 정한다.

대표 유형:

```text
location
  -> 위치/주소/좌표 질문

trade_history
  -> 최근 거래 내역 질문

record_high
  -> 최고가 질문
```

### 8.3 Service 내부 분기

파일: `app/chatbot/features/simple_lookup/service.py`

```text
SimpleLookupService.handle(slots)
```

처리 순서:

```text
1. normalize_simple_lookup_policy(slots)
   - query_type별 허용/무시할 slots 정리
   - 기간/면적/평형 조건 정규화

2. resolve_complex_target(dao, complex_name)
   - 정확 일치 검색
   - 없으면 부분 일치 검색
   - 0개면 target_not_found
   - 여러 개면 ambiguous_target

3. query_type 분기
   - LOCATION -> _handle_location()
   - TRADE_HISTORY -> _handle_trade_history()
   - RECORD_HIGH -> _handle_record_high()
```

### 8.4 실패 경우

```text
단지를 못 찾음
  -> target_not_found

비슷한 단지가 여러 개
  -> ambiguous_target + candidates 반환

조건에 맞는 거래 없음
  -> no_result

slots 검증 실패
  -> invalid_request
```

---

## 9. 추천 플로우

질문 예:

```text
"강남구 40억 이하 아파트 추천해줘"
"신논현역 근처 800m 안에 있는 아파트 추천해줘"
"초등학교 가까운 대단지 추천해줘"
```

### 9.1 전체 흐름

```text
ChatbotAgent
  -> recommend_apartments tool 선택
  -> extract_recommendation_slots(query)
  -> tool arguments merge
  -> run_recommendation(session, slots, query)
  -> RecommendationService.run()
  -> RecommendationService.recommend_apartments_by_filters()
  -> generate_recommendation_answer()
```

### 9.2 Slot 추출 세부 흐름

파일: `app/chatbot/features/recommendation/slots.py`

```text
extract_recommendation_slots(question)
```

처리 순서:

```text
1. text = question.strip()

2. extract_district(text)
   -> 강남구/서초구/송파구 같은 지역 조건 추출

3. extract_station_name(text)
   -> 역 근처 조건이 있으면 station_name 설정
   -> station_name이 있으면 radius_m 기본 800, sort_by=distance_asc 설정

4. extract_school_types(text)
   -> 학교 유형 조건 추출
   -> 하나면 school_type
   -> 여러 개면 school_types
   -> 학교 조건이 있으면 radius_m 기본 800
   -> 여러 학교 유형이면 sort_by=school_distance_asc

5. extract_radius_m(text)
   -> "800m", "1km" 같은 표현을 m 단위로 변환
   -> 있으면 기존 기본 radius_m을 덮어씀

6. extract_price_slots(text)
   -> "40억 이하" 같은 가격 조건 추출
   -> max_price 또는 min_price 설정

7. extract_min_households(text)
   -> "500세대 이상" 같은 조건 추출

8. extract_min_pyeong(text)
   -> "30평 이상" 같은 조건 추출

9. has_new_build_condition(text)
   -> 신축/준신축 표현이 있으면 is_new_build=True
   -> min_built_year=2020

10. extract_infra_preferences(text)
   -> transport / education / commercial 추출
   -> 인프라 조건이 있으면 radius_m 기본 800
   -> transport면 distance_asc 정렬
   -> education + 가까운 학교 질문이면 school_distance_asc 정렬

11. extract_sort_by(text)
   -> 질문에 명시된 정렬 조건이 있으면 sort_by 덮어씀

12. extract_limit(text)
   -> "3개 추천" 같은 개수 조건 추출
```

### 9.3 Tool argument merge

파일: `app/chatbot/service/tools/recommendation_tool.py`

tool 함수는 rule 기반 slots와 LLM tool arguments를 합친다.

```text
slots = extract_recommendation_slots(query)
slots.update(compact_none({
  "district": district,
  "station_name": station_name,
  ...
}))
```

의미:

- rule parser가 놓친 값을 agent가 tool argument로 채울 수 있다.
- 반대로 tool argument가 None이면 기존 slots를 지우지 않는다.

### 9.4 추천 Service 내부 처리

파일: `app/chatbot/features/recommendation/service.py`

```text
RecommendationService.recommend_apartments_by_filters(session, slots)
```

처리 순서:

```text
1. normalize_slots(slots)
   -> 빈 문자열, "none", "null" 등을 None처럼 정리

2. all_complexes_ordered(session)
   -> 전체 단지 후보 조회

3. complex_matches_base_filters()
   -> 지역, 세대수, 준공연도 조건으로 1차 필터

4. latest_trade_for_complex()
   -> 단지별 최신 거래 조회

5. latest_trade_matches()
   -> 가격, 평형 조건으로 2차 필터

6. find_poi_groups()
   -> 역/학교/교육/교통 인프라 조건에 필요한 POI 목록 조회

7. poi_groups가 None이면
   -> poi_not_found 실패 응답

8. filter_items_by_poi_distance_query()
   -> 각 POI 그룹마다 반경 안에 들어오는 단지만 남김

9. enrich_infrastructure()
   -> nearestStation, nearestEducation, school type별 거리 정보 추가

10. sort_query_results()
   -> distance_asc, school_distance_asc, price_asc, price_desc 처리

11. limit 적용
   -> 최대 RECOMMENDATION_RESULT_LIMIT 안에서 결과 제한

12. success/result/message 구성
```

리팩토링 후 파일 분리:

```text
recommendation/service.py
  -> 추천 처리 순서만 담당
  -> normalize, 후보 조회, POI 필터, 결과 조립 순서를 눈으로 따라가기 위한 파일

recommendation/filters.py
  -> 조건 판정 담당
  -> complex_matches_base_filters()
  -> latest_trade_matches()
  -> requested_infra()
  -> requested_school_types()
  -> radius_m()

recommendation/infrastructure.py
  -> 역/학교 POI 조회와 거리 기반 필터 담당
  -> find_poi_groups()
  -> filter_items_by_poi_distance_query()
  -> enrich_infrastructure()

recommendation/formatting.py
  -> 결과 dict 생성, 금액 표시, 정렬 담당
  -> query_result_item()
  -> format_deal_amount()
  -> sort_query_results()

recommendation/rag_answer.py
  -> 조회된 추천 결과를 한국어 설명 답변으로 바꾸는 담당
```

즉 `service.py`는 더 이상 모든 계산을 직접 들고 있지 않고,
각 단계에 맞는 helper를 호출해서 전체 흐름만 보여준다.

### 9.4.1 추천에서 쿼리문이 실행되는 방식

추천에서 아파트, 최신 거래, 역/학교를 찾을 때 실제 DB 조회는 `app/real_estate/dao/*` 함수에서 실행된다.

#### 1. 아파트 후보 전체 조회

파일: `app/real_estate/dao/complex_dao.py`

```python
def all_complexes_ordered(session: Session) -> list[Complex]:
  return list(session.scalars(select(Complex).order_by(Complex.name)).all())
```

실행되는 SQL 의미:

```sql
SELECT *
FROM complex
ORDER BY name;
```

이 단계에서는 아직 `강남구`, `세대수`, `신축` 같은 조건이 SQL에 들어가지 않는다.
전체 단지를 이름순으로 가져온 뒤 `RecommendationService._find_base_candidates()`에서 Python 조건문으로 거른다.

```text
all_complexes_ordered()
  -> Complex 전체 조회
  -> complex_matches_base_filters()
     -> district 비교
     -> min_households 비교
     -> min_built_year 비교
```

즉 지역/세대수/신축 필터는 현재 DB where 조건이 아니라 service helper에서 처리된다.

#### 2. 각 아파트의 최신 거래 조회

파일: `app/real_estate/dao/trade_dao.py`

```python
def latest_trade_for_complex(session: Session, complex_id: int) -> Trade | None:
  return session.scalar(
    select(Trade)
    .where(Trade.complex_id == complex_id)
    .order_by(Trade.deal_date.desc(), Trade.id.desc())
    .limit(1)
  )
```

실행되는 SQL 의미:

```sql
SELECT *
FROM trade
WHERE complex_id = :complex_id
ORDER BY deal_date DESC, id DESC
LIMIT 1;
```

이 쿼리는 후보 아파트마다 한 번씩 실행된다.
가져온 최신 거래 row는 `latest_trade_matches()`에서 가격/평형 조건을 검사한다.

```text
latest_trade_for_complex()
  -> 해당 complex_id의 가장 최신 거래 1건
  -> latest_trade_matches()
     -> min_price
     -> max_price
     -> min_pyeong
```

#### 3. 역을 이름으로 찾는 쿼리

파일: `app/real_estate/dao/poi_dao.py`

```python
def station_pois(session: Session, name: str) -> list[Poi]:
  return list(session.scalars(
    select(Poi).where(Poi.category == "station", Poi.name == name)
  ).all())
```

실행되는 SQL 의미:

```sql
SELECT *
FROM poi
WHERE category = 'station'
  AND name = :station_name;
```

사용자가 “신논현역 근처”처럼 특정 역을 말하면 이 쿼리로 역 POI를 먼저 찾는다.
역 이름은 `normalize_station_name()`에서 `역` suffix와 alias를 정리한 뒤 조회한다.

#### 4. 학교를 이름/유형으로 찾는 쿼리

파일: `app/real_estate/dao/poi_dao.py`

```python
def education_pois(session: Session, name: str | None = None, subtype: str | None = None) -> list[Poi]:
  statement = select(Poi).where(Poi.category == "education")
  if name is not None:
    statement = statement.where(Poi.name == name)
  if subtype is not None:
    statement = statement.where(Poi.subtype == subtype)
  return list(session.scalars(statement).all())
```

실행되는 SQL 의미:

```sql
SELECT *
FROM poi
WHERE category = 'education'
  -- school_name이 있으면
  AND name = :school_name
  -- school_type이 있으면
  AND subtype = :school_type;
```

경우의 수:

```text
"초등학교 가까운"처럼 유형만 있음
  -> category='education'
  -> subtype='초등학교'

"A초등학교 근처"처럼 이름이 있음
  -> category='education'
  -> name='A초등학교'

이름과 유형이 둘 다 있음
  -> category='education'
  -> name='A초등학교'
  -> subtype='초등학교'
```

#### 5. 역/학교 반경 안의 아파트를 찾는 거리 쿼리

파일: `app/real_estate/dao/complex_dao.py`

```python
statement = (
  select(Complex, Poi, distance_expr.label("distance_m"))
  .join(Poi, Poi.id.in_(poi_ids))
  .where(Complex.latitude.is_not(None))
  .where(Complex.longitude.is_not(None))
  .where(distance_expr <= max_distance_m)
  .order_by(distance_expr)
)
if complex_ids is not None:
  statement = statement.where(Complex.id.in_(complex_ids))
```

실행되는 SQL 의미:

```sql
SELECT complex.*, poi.*, distance_expr AS distance_m
FROM complex
JOIN poi ON poi.id IN (:poi_ids)
WHERE complex.latitude IS NOT NULL
  AND complex.longitude IS NOT NULL
  AND distance_expr <= :max_distance_m
  -- 앞 단계에서 후보 아파트가 이미 있으면
  AND complex.id IN (:complex_ids)
ORDER BY distance_expr;
```

여기서 `distance_expr`는 위도/경도를 이용한 haversine 거리 계산식이다.

```python
EARTH_RADIUS_M * 2 * func.asin(
  func.sqrt(
    func.pow(func.sin(func.radians(lat2 - lat1) / 2), 2)
    + func.cos(func.radians(lat1))
    * func.cos(func.radians(lat2))
    * func.pow(func.sin(func.radians(lon2 - lon1) / 2), 2)
  )
)
```

즉 DB가 각 아파트와 POI 사이의 거리를 계산하고,
`max_distance_m` 안에 들어오는 아파트만 반환한다.

반환 후 Python에서는 같은 아파트가 여러 POI와 매칭될 수 있으므로,
가장 가까운 POI 1개만 `nearest_by_complex_id`에 남긴다.

```text
complexes_near_pois_by_query()
  -> POI 목록 id 추출
  -> Complex와 Poi를 거리식으로 매칭
  -> 반경 안에 있는 결과만 조회
  -> distance_m 오름차순 정렬
  -> 같은 complex_id는 가장 가까운 POI만 유지
```

정리하면 추천 쿼리 흐름은 다음과 같다.

```text
아파트 전체 조회
  -> Python에서 지역/세대수/신축 필터
  -> 후보별 최신 거래 1건 조회
  -> Python에서 가격/평형 필터
  -> 역/학교 POI 조회
  -> DB 거리 쿼리로 반경 안 아파트 필터
  -> Python에서 인프라 정보 추가/정렬/limit
```

### 9.5 POI 조건 경우의 수

```text
station_name 있음
  -> station_pois(session, station_name)
  -> 없으면 poi_not_found
  -> 있으면 해당 역과의 거리 필터

school_types 있음
  -> 각 school_type별 education_pois 조회
  -> 하나라도 없으면 poi_not_found
  -> 모두 있으면 각 유형별 반경 필터

school_name 또는 school_type 있음
  -> education_pois 조회
  -> 없으면 poi_not_found

infra_preferences에 transport 있음
  -> 전체 station POI를 대상으로 가까운 역 검색

infra_preferences에 education 있음
  -> education POI를 대상으로 가까운 교육시설 검색

infra_preferences에 commercial 있음
  -> 현재 DB에는 상권 POI가 없으므로 notes에 데이터 부족 안내
```

### 9.6 추천 RAG 답변

파일: `app/chatbot/features/recommendation/rag_answer.py`

```text
generate_recommendation_answer()
  -> RecommendationRagAnswerAgent.run()
  -> generate_llm_answer()
```

처리:

```text
OPENAI_API_KEY 있음
  -> OpenAI Chat Completions 호출
  -> RECOMMENDATION_SYSTEM_PROMPT + compacted results 전달
  -> LLM 답변 반환

OPENAI_API_KEY 없음 또는 오류 발생
  -> fallback_recommendation_answer()
```

`compact_recommendation_results()`는 LLM에 넘길 데이터를 줄인다.

남기는 필드:

```text
complexName
address
unitCnt
useDate
latestDealAmount
latestDealAmountText
latestDealDate
pyeong
nearestStation
nearestEducation
nearestEducationByType
educationDistanceTotalM
notes
```

---

## 10. 비교 플로우

질문 예:

```text
"래미안대치팰리스랑 아크로리버파크 비교해줘"
"A 단지와 B 단지 가격이랑 학교 거리 비교해줘"
```

### 10.1 전체 흐름

```text
ChatbotAgent
  -> compare_apartments tool 선택
  -> extract_compare_slots(query)
  -> tool arguments merge
  -> run_comparison(session, slots, query)
  -> ComparisonService.run()
  -> ComparisonService.compare_apartments_by_metrics()
  -> generate_comparison_answer()
```

### 10.2 Slot 추출 세부 흐름

파일: `app/chatbot/features/comparison/slots.py`

```text
extract_compare_slots(question)
```

처리 순서:

```text
1. extract_apartment_names(text)
   -> "A랑 B", "A와 B", "A하고 B" 형태에서 두 단지명 추출

2. clean_apartment_name(value)
   -> 단지명 주변의 "비교해줘", "가격", "학교" 같은 불필요한 말 제거

3. extract_metrics(text)
   -> 가격/시세 표현 있으면 latest_price, pyeong, price_per_pyeong
   -> 세대수/규모 표현 있으면 households
   -> 신축/연식 표현 있으면 built_year
   -> 교통 표현 있으면 nearest_station
   -> 교육 표현 있으면 nearest_school
   -> 상권 표현 있으면 nearest_station, nearest_school
   -> 아무 metric도 못 찾으면 DEFAULT_METRICS 사용

4. extract_school_type(text)
   -> 학교 유형 조건 추출

5. extract_infra_preferences(text)
   -> transport / education / commercial 추출
```

### 10.3 Tool argument merge

파일: `app/chatbot/service/tools/comparison_tool.py`

```text
slots = extract_compare_slots(query)
slots.update(compact_none({
  "apartment_names": apartment_names,
  "metrics": metrics,
  "school_type": school_type,
  "infra_preferences": infra_preferences,
}))
```

LLM이 `apartment_names`를 더 정확히 채웠다면 rule parser 결과를 덮어쓴다.

### 10.4 비교 Service 내부 처리

파일: `app/chatbot/features/comparison/service.py`

```text
ComparisonService.compare_apartments_by_metrics(session, slots)
```

처리 순서:

```text
1. normalize_slots(slots)

2. apartment_names 확인
   -> list가 아니거나 2개 미만이면 missing_apartment_names 실패

3. metrics 확인
   -> 없으면 DEFAULT_METRICS 사용

4. requested_infra(slots)
   -> transport / education / commercial 정리

5. normalize_metrics(metrics, infra_preferences)
   -> transport면 nearest_station 추가
   -> education이면 nearest_school 추가
   -> commercial이면 station/school을 proxy로 추가
   -> dedupe()

6. apartment_names 순회
   -> find_complex_by_name()
   -> 못 찾으면 missing에 추가
   -> 찾으면 latest_trade_for_complex()
   -> comparison_item()으로 기본 비교 row 생성

7. nearest_station metric 있으면
   -> pois_by_category(session, "station")
   -> nearest_poi_for_complex()
   -> nearestStation 추가

8. nearest_school metric 있으면
   -> pois_by_category(session, "education", subtype=..., name=...)
   -> nearest_poi_for_complex()
   -> nearestSchool 추가

9. infrastructureNotes 추가

10. success/result/missingApartmentNames/message 구성
```

리팩토링 후 파일 분리:

```text
comparison/service.py
  -> 비교 처리 순서만 담당
  -> 아파트명 검증, metric 결정, row 생성, 응답 조립 순서를 보여주는 파일

comparison/metrics.py
  -> 비교 항목 정리 담당
  -> DEFAULT_METRICS
  -> normalize_metrics()
  -> requested_infra()
  -> infrastructure_notes()

comparison/formatting.py
  -> DB row를 비교 응답 row로 바꾸는 담당
  -> find_complex_by_name()
  -> comparison_item()
  -> format_deal_amount()

comparison/rag_answer.py
  -> 비교 결과를 한국어 설명 답변으로 바꾸는 담당
```

즉 비교 service도 추천 service와 같은 방식으로,
분기와 전체 흐름은 service에 남기고 세부 계산은 helper 파일로 이동했다.

### 10.4.1 비교에서 쿼리문이 실행되는 방식

비교 기능은 추천처럼 전체 후보를 넓게 가져오지 않고,
사용자가 말한 아파트 이름을 하나씩 DB에서 찾는다.

#### 1. 아파트 이름으로 단지 찾기

파일: `app/real_estate/dao/complex_dao.py`

```python
def find_complex_by_name(session: Session, name: str) -> Complex | None:
  exact = session.scalar(
    select(Complex)
    .where(or_(Complex.name == name, Complex.trade_name == name))
    .order_by(Complex.id)
    .limit(1)
  )
  if exact is not None:
    return exact

  pattern = f"%{name}%"
  return session.scalar(
    select(Complex)
    .where(or_(Complex.name.like(pattern), Complex.trade_name.like(pattern)))
    .order_by(Complex.name)
    .limit(1)
  )
```

실행 순서:

```text
1차 정확히 일치하는 단지 검색
  -> Complex.name == 입력명
  -> Complex.trade_name == 입력명

없으면 2차 부분 일치 검색
  -> Complex.name LIKE '%입력명%'
  -> Complex.trade_name LIKE '%입력명%'
```

SQL 의미:

```sql
SELECT *
FROM complex
WHERE name = :name
   OR trade_name = :name
ORDER BY id
LIMIT 1;
```

정확히 일치하는 단지가 없으면:

```sql
SELECT *
FROM complex
WHERE name LIKE :pattern
   OR trade_name LIKE :pattern
ORDER BY name
LIMIT 1;
```

#### 2. 찾은 단지의 최신 거래 조회

비교에서도 추천과 같은 `latest_trade_for_complex()`를 사용한다.

```sql
SELECT *
FROM trade
WHERE complex_id = :complex_id
ORDER BY deal_date DESC, id DESC
LIMIT 1;
```

이 결과로 최신 가격, 평형, 평당가를 만든다.

#### 3. 가까운 역/학교 조회

비교에서는 먼저 전체 역 또는 조건에 맞는 학교 POI를 가져온 뒤,
Python에서 해당 아파트와 가장 가까운 POI를 계산한다.

역 조회:

```python
pois_by_category(session, "station")
```

SQL 의미:

```sql
SELECT *
FROM poi
WHERE category = 'station';
```

학교 조회:

```python
pois_by_category(
  session,
  "education",
  subtype=school_type,
  name=school_name,
)
```

SQL 의미:

```sql
SELECT *
FROM poi
WHERE category = 'education'
  -- school_type이 있으면
  AND subtype = :school_type
  -- school_name이 있으면
  AND name = :school_name;
```

그 다음 `nearest_poi_for_complex()`가 단지 좌표와 POI 목록을 비교해서 가장 가까운 POI를 고른다.
추천의 반경 필터처럼 DB에서 `distance_expr <= radius`를 거는 방식이 아니라,
비교에서는 가져온 POI 목록을 Python에서 순회하면서 haversine 거리를 계산한다.

정리하면 비교 쿼리 흐름은 다음과 같다.

```text
아파트명 1개씩 조회
  -> 정확 일치 검색
  -> 없으면 LIKE 검색
  -> 찾은 단지별 최신 거래 1건 조회
  -> metric에 역 비교가 있으면 station POI 전체 조회
  -> metric에 학교 비교가 있으면 education POI 조건 조회
  -> Python에서 가장 가까운 POI 계산
  -> 비교 row 생성
```

### 10.5 비교 실패 경우

```text
apartment_names가 2개 미만
  -> reason="missing_apartment_names"

일부 단지명을 못 찾음
  -> missingApartmentNames에 추가
  -> success는 rows가 있어도 missing이 있으면 false

상권 비교 요청
  -> 실제 상권 POI가 없으므로 infrastructureNotes에 데이터 부족 안내
```

### 10.6 비교 RAG 답변

파일: `app/chatbot/features/comparison/rag_answer.py`

```text
generate_comparison_answer()
  -> ComparisonRagAnswerAgent.run()
  -> generate_llm_answer()
```

처리:

```text
OPENAI_API_KEY 있음
  -> OpenAI Chat Completions 호출
  -> COMPARISON_SYSTEM_PROMPT + compacted results 전달
  -> LLM 답변 반환

OPENAI_API_KEY 없음 또는 오류 발생
  -> fallback_comparison_answer()
```

`compact_comparison_results()`가 LLM에 넘기는 필드:

```text
complexName
latestDealAmount
latestDealAmountText
pyeong
pricePerPyeong
pricePerPyeongText
unitCnt
builtYear
nearestStation
nearestSchool
infrastructureNotes
```

---

## 11. 가격 추세 플로우

질문 예:

```text
"래미안대치팰리스 최근 1년 가격 추이 보여줘"
"강남구에서 최근 많이 오른 아파트 알려줘"
"서초구와 송파구 가격 추세 비교해줘"
```

전체 흐름:

```text
ChatbotAgent
  -> analyze_price_trend tool 선택
  -> extract_price_trend_slots(query)
  -> tool arguments merge
  -> run_price_trend(session, slots, query)
  -> TrendSlots 검증
  -> TrendService.handle()
  -> query_type별 handler
  -> PriceTrendDao query
```

가격 추세 query_type:

```text
complex_trend
  -> 특정 단지 월별/기간별 추세

region_trend
  -> 특정 지역 월별/기간별 추세

price_ranking
  -> 가격 높은/낮은 순위

price_change_ranking
  -> 상승률/하락률 순위
```

내부 주요 단계:

```text
1. normalize_trend_policy()
   -> 기간, 면적, 평형, 정렬, limit 검증 및 정규화

2. target resolve
   -> 단지형이면 resolve_complex_target()
   -> 지역형이면 resolve_region_target() 또는 resolve_region_targets()

3. query_type별 DAO 호출
   -> PriceTrendDao가 실제 DB 집계 수행

4. TrendPoint / RankingItem DTO 변환

5. TrendResult 반환
```

---

## 12. 법률/계약 RAG 플로우

질문 예:

```text
"매매 계약 해제 규정 알려줘"
"전세 계약 관련 법령 근거 찾아줘"
```

전체 흐름:

```text
ChatbotAgent
  -> search_legal_contract tool 선택
  -> extract_legal_contract_slots(query)
  -> tool arguments merge
  -> run_legal_contract(session, slots, query)
  -> normalize_query()
  -> LegalRagQueryService.query()
  -> LegalAnswerService.answer()
```

내부 단계:

```text
1. original_query 결정
   -> slots["original_query"] 또는 text 사용

2. normalize_query(original_query)
   -> 법률 검색용 normalized_query 생성

3. LegalRagQueryService.query(normalized_query)
   -> 용어 확장
   -> embedding/keyword hybrid 검색
   -> source 목록과 expandedTerms 반환

4. slots["expanded_terms"] 저장

5. LegalAnswerService.answer(original_query, result)
   -> 검색 결과를 기반으로 답변 생성
   -> citation 포함
```

---

## 13. 실패/예외 케이스 전체 정리

### 13.1 Agent 생성 또는 실행 실패

```text
ChatbotAgent 생성 실패
또는 agent.run() 중 예외
  -> agent_execution_failed_result()
```

### 13.2 Tool 선택 실패

```text
agent가 tool을 호출하지 않음
  -> extract_agent_result()
  -> tool_results 없음
  -> no_matching_tool_result()
```

### 13.3 단지/지역 대상 확정 실패

```text
정확 일치 없음 + 부분 일치 없음
  -> target_not_found

후보가 여러 개
  -> ambiguous_target
  -> candidates 반환
```

### 13.4 추천 후보 없음

```text
기본 조건/거래 조건/POI 조건을 모두 적용한 뒤 결과 없음
  -> success=false
  -> results=[]
```

### 13.5 POI 조건 불만족

```text
요청한 역/학교 POI를 찾지 못함
  -> poi_not_found
```

### 13.6 LLM 답변 생성 실패

```text
OPENAI_API_KEY 없음
또는 OpenAI 호출 오류
  -> fallback_recommendation_answer()
  -> fallback_comparison_answer()
```

이 경우에도 추천/비교 데이터 자체는 유지되고, `answer`만 기본 문장으로 생성된다.

---

## 14. 질문별 최종 호출 요약

### 단순 조회

```text
query_by_natural_language
  -> handle_chatbot_query
  -> split_question
  -> ChatbotAgent.run
  -> simple_lookup
  -> extract_simple_lookup_slots
  -> run_simple_lookup
  -> SimpleLookupService.handle
  -> _handle_location / _handle_trade_history / _handle_record_high
```

### 추천

```text
query_by_natural_language
  -> handle_chatbot_query
  -> split_question
  -> ChatbotAgent.run
  -> recommend_apartments
  -> extract_recommendation_slots
  -> run_recommendation
  -> RecommendationService.run
  -> RecommendationService.recommend_apartments_by_filters
  -> generate_recommendation_answer
  -> RecommendationRagAnswerAgent.run
```

### 비교

```text
query_by_natural_language
  -> handle_chatbot_query
  -> split_question
  -> ChatbotAgent.run
  -> compare_apartments
  -> extract_compare_slots
  -> run_comparison
  -> ComparisonService.run
  -> ComparisonService.compare_apartments_by_metrics
  -> generate_comparison_answer
  -> ComparisonRagAnswerAgent.run
```

### 가격 추세

```text
query_by_natural_language
  -> handle_chatbot_query
  -> split_question
  -> ChatbotAgent.run
  -> analyze_price_trend
  -> extract_price_trend_slots
  -> run_price_trend
  -> TrendService.handle
  -> PriceTrendDao
```

### 법률/계약

```text
query_by_natural_language
  -> handle_chatbot_query
  -> split_question
  -> ChatbotAgent.run
  -> search_legal_contract
  -> extract_legal_contract_slots
  -> run_legal_contract
  -> LegalRagQueryService.query
  -> LegalAnswerService.answer
```

---

## 15. 코드를 읽는 추천 순서

처음 읽을 때는 이 순서가 가장 좋다.

```text
1. app/chatbot/controller/chatbot_controller.py
2. app/chatbot/service/chatbot_service.py
3. app/chatbot/service/splitter.py
4. app/chatbot/service/agent.py
5. app/chatbot/service/tools/__init__.py
6. 원하는 질문 유형의 tool 파일
7. 해당 feature의 slots.py
8. 해당 feature의 service.py
9. 필요하면 dao.py / policy.py / rag_answer.py
```

추천 질문만 볼 때:

```text
recommendation_tool.py
-> recommendation/slots.py
-> recommendation/service.py
   -> recommendation/filters.py
   -> recommendation/infrastructure.py
   -> recommendation/formatting.py
-> recommendation/rag_answer.py
```

비교 질문만 볼 때:

```text
comparison_tool.py
-> comparison/slots.py
-> comparison/service.py
   -> comparison/metrics.py
   -> comparison/formatting.py
-> comparison/rag_answer.py
```
