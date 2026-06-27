# 추천/비교 챗봇 API QA 리포트 (최종)

- 실행 시각: 2026-06-28 00:18:42
- 서버 브랜치: `codex/chatbot-service-refactor` @ `2a885c3`
- 대상 API: `POST http://127.0.0.1:8080/api/v1/chatbot/query`
- 실행 환경: Docker PostgreSQL + 로컬 FastAPI `127.0.0.1:8080`
- DB 적재 확인: `regions=40`, `complexes=1993`, `trades=173340`, `pois=415`
- 범위: `recommendation` 20건, `comparison` 20건. 법령/가격추이/단순조회 제외

## 요약

- 전체: 40/40 PASS, 0 NEEDS_FIX
- 추천: 20/20 PASS
- 비교: 20/20 PASS

## 수정 확인

- `.env` 자동 로딩 추가
- 추천/비교 명확 질문 deterministic direct route 적용
- 구조화 결과 기반 답변으로 예산/조건 모순 제거
- 비교 단지명 분리/정제 보강
- 명시 반경 없는 기본 근처 검색은 결과 없음 시 1500m까지 확장

## 추천 QA 20건

### R01
질문
~~~~
송파구 40억 이하 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 각산: 최근 거래가 7.0억원, 가까운 역 송파역(499m), 가까운 교육시설 가락고등학교(71m), 12세대, 사용승인일 2000-11-30
2. 갑을: 최근 거래가 9.5억원, 가까운 역 잠실나루역(1784m), 가까운 교육시설 한가람유치원(276m), 85세대, 사용승인일 1996-07-22
3. 경덕: 최근 거래가 5.7억원, 가까운 역 석촌고분역(286m), 가까운 교육시설 서울삼전초등학교(117m), 19세대, 사용승인일 2003-12-29
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (3.5s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 각산, 갑을, 경덕; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "송파구",
    "max_price": 400000,
    "limit": 3
  },
  "resultNames": [
    "각산",
    "갑을",
    "경덕"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R02
질문
~~~~
강남구 30억 이하 아파트 5개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 경남: 최근 거래가 21.0억원, 가까운 역 매봉역(573m), 가까운 교육시설 서울언주초등학교(435m), 348세대, 사용승인일 2005-06-02
2. 경복: 최근 거래가 6.5억원
3. 구산: 최근 거래가 22.0억원, 가까운 역 청담역(815m), 가까운 교육시설 성요셉유치원(500m), 37세대, 사용승인일 1993-11-26
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (3.4s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 경남, 경복, 구산, 국제, 대림; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "강남구",
    "max_price": 300000,
    "limit": 5
  },
  "resultNames": [
    "경남",
    "경복",
    "구산",
    "국제",
    "대림"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R03
질문
~~~~
서초구 20억 이하 저렴한 아파트 4곳 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 서초중흥S-클래스(도시형생활주택): 최근 거래가 1.9억원, 가까운 역 남부터미널(예술의전당)역(454m), 가까운 교육시설 서울신중초등학교(393m), 91세대, 사용승인일 2013-08-29
2. 동아아파트: 최근 거래가 2.0억원, 가까운 역 양재(서초구청)역(631m), 가까운 교육시설 서울서이초등학교(481m), 62세대, 사용승인일 2015-04-28
3. 신사레전드: 최근 거래가 2.3억원, 가까운 역 신사역(220m), 가까운 교육시설 신동중학교(456m), 58세대, 사용승인일 2012-12-18
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (3.2s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 4건: 서초중흥S-클래스(도시형생활주택), 동아아파트, 신사레전드, urbanplace; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "서초구",
    "max_price": 200000,
    "sort_by": "price_asc",
    "limit": 4
  },
  "resultNames": [
    "서초중흥S-클래스(도시형생활주택)",
    "동아아파트",
    "신사레전드",
    "urbanplace"
  ],
  "resultCount": 4,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R04
질문
~~~~
잠실역 근처 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 롯데캐슬골드: 최근 거래가 27.8억원, 가까운 역 잠실(송파구청)역(144m), 가까운 교육시설 잠실중학교(374m), 400세대, 사용승인일 2005-11-22
2. 잠실시그마타워: 최근 거래가 23.0억원, 가까운 역 잠실(송파구청)역(161m), 가까운 교육시설 장미유치원(394m), 84세대, 사용승인일 1996-12-27
3. 현대타워: 최근 거래가 18.3억원, 가까운 역 잠실(송파구청)역(170m), 가까운 교육시설 장미유치원(342m), 55세대, 사용승인일 1996-12-30
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.9s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 롯데캐슬골드, 잠실시그마타워, 현대타워; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "잠실역",
    "radius_m": 800,
    "sort_by": "distance_asc",
    "infra_preferences": [
      "transport"
    ],
    "limit": 3
  },
  "resultNames": [
    "롯데캐슬골드",
    "잠실시그마타워",
    "현대타워"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R05
질문
~~~~
서초역 500m 이내 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 동일하이빌: 최근 거래가 16.2억원, 가까운 역 서초역(51m), 가까운 교육시설 서초고등학교(364m), 54세대, 사용승인일 2006-10-18
2. 블루힐65: 최근 거래가 2.7억원, 가까운 역 서초역(102m), 가까운 교육시설 서초고등학교(418m), 11세대, 사용승인일 2013-09-03
3. 현대ESA2: 최근 거래가 20.5억원, 가까운 역 서초역(110m), 가까운 교육시설 서초고등학교(339m), 174세대, 사용승인일 2003-01-29
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.9s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 동일하이빌, 블루힐65, 현대ESA2, 마제스타시티(힐스테이트서리풀101,102동), 월드메르디앙; 반경 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "서초역",
    "radius_m": 500,
    "sort_by": "distance_asc"
  },
  "resultNames": [
    "동일하이빌",
    "블루힐65",
    "현대ESA2",
    "마제스타시티(힐스테이트서리풀101,102동)",
    "월드메르디앙"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R06
질문
~~~~
대치역 근처 초등학교 가까운 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 선경1차(1동-7동): 최근 거래가 60.0억원, 가까운 역 대치역(289m), 가까운 교육시설 서울대치초등학교(108m), 644세대, 사용승인일 1983-12-12
2. 선경2차(8동-12동): 최근 거래가 48.0억원, 가까운 역 대치역(289m), 가까운 교육시설 서울대치초등학교(108m), 390세대, 사용승인일 1985-04-10
3. 동민맥스빌B동: 최근 거래가 20.0억원, 가까운 역 한티역(331m), 가까운 교육시설 서울도곡초등학교(205m), 18세대, 사용승인일 2004-12-24
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.8s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 선경1차(1동-7동), 선경2차(8동-12동), 동민맥스빌B동; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "대치역",
    "radius_m": 800,
    "sort_by": "school_distance_asc",
    "school_type": "초등학교",
    "infra_preferences": [
      "transport",
      "education"
    ],
    "limit": 3
  },
  "resultNames": [
    "선경1차(1동-7동)",
    "선경2차(8동-12동)",
    "동민맥스빌B동"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R07
질문
~~~~
송파구 초등학교 가까운 아파트 5개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 온누리: 최근 거래가 6.0억원, 가까운 역 송파나루역(318m), 가까운 교육시설 서울송파초등학교(49m), 17세대, 사용승인일 2004-10-27
2. 삼호아파트: 최근 거래가 10.9억원, 가까운 역 거여역(390m), 가까운 교육시설 서울영풍초등학교(60m), 142세대, 사용승인일 1999-06-12
3. 힐트리움송파: 최근 거래가 6.8억원, 가까운 역 거여역(417m), 가까운 교육시설 서울영풍초등학교(61m)
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (2.6s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 온누리, 삼호아파트, 힐트리움송파, 디벨라지오, 인텍스빌; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "송파구",
    "school_type": "초등학교",
    "radius_m": 800,
    "infra_preferences": [
      "education"
    ],
    "sort_by": "school_distance_asc",
    "limit": 5
  },
  "resultNames": [
    "온누리",
    "삼호아파트",
    "힐트리움송파",
    "디벨라지오",
    "인텍스빌"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R08
질문
~~~~
강남구 1000세대 이상 대단지 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 은마: 최근 거래가 34.0억원, 가까운 역 대치역(361m), 가까운 교육시설 서울대곡초등학교(319m), 4424세대, 사용승인일 1979-08-30
2. 신동아: 최근 거래가 15.8억원, 가까운 역 수서역(217m), 가까운 교육시설 반디유치원(124m), 1162세대, 사용승인일 1992-10-28
3. 까치마을: 최근 거래가 15.3억원, 가까운 역 일원역(316m), 가까운 교육시설 대왕중학교(146m), 1403세대, 사용승인일 1993-09-08
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.2s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 은마, 신동아, 까치마을, 도곡렉슬, 래미안포레; min_households 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "강남구",
    "min_households": 1000
  },
  "resultNames": [
    "은마",
    "신동아",
    "까치마을",
    "도곡렉슬",
    "래미안포레"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R09
질문
~~~~
서초구 신축 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 더클래스: 최근 거래가 5.0억원, 가까운 역 남부터미널(예술의전당)역(360m), 가까운 교육시설 서울교육대학교부설초등학교(343m), 20세대, 사용승인일 2020-07-10
2. 반포르엘: 최근 거래가 56.0억원, 가까운 역 고속터미널역(236m), 가까운 교육시설 한신유치원(271m), 596세대, 사용승인일 2022-08-30
3. 비버리캐슬: 최근 거래가 11.8억원, 가까운 역 남부터미널(예술의전당)역(762m), 가까운 교육시설 서울고등학교(229m), 42세대, 사용승인일 2020-07-22
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.3s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 더클래스, 반포르엘, 비버리캐슬, 신반포르엘, 지에스타워; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "서초구",
    "is_new_build": true,
    "min_built_year": 2020
  },
  "resultNames": [
    "더클래스",
    "반포르엘",
    "비버리캐슬",
    "신반포르엘",
    "지에스타워"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R10
질문
~~~~
강남구 25평 이상 40억 이하 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 가람: 최근 거래가 30.8억원, 가까운 역 일원역(147m), 가까운 교육시설 서울대모초등학교(152m), 496세대, 사용승인일 1993-03-09
2. 경복: 최근 거래가 6.5억원
3. 국제: 최근 거래가 10.2억원
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (2.5s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 가람, 경복, 국제, 대림, 대우; max_price 조건 만족; min_pyeong 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "강남구",
    "max_price": 400000,
    "min_pyeong": 25.0
  },
  "resultNames": [
    "가람",
    "경복",
    "국제",
    "대림",
    "대우"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R11
질문
~~~~
송파구 10억 이상 30억 이하 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 극동: 최근 거래가 17.4억원, 가까운 역 몽촌토성(평화의문)역(1092m), 가까운 교육시설 풍납중학교(108m), 415세대, 사용승인일 1987-12-15
2. 극동: 최근 거래가 25.8억원, 가까운 역 개롱역(327m), 가까운 교육시설 제일무궁화유치원(182m), 555세대, 사용승인일 1984-12-10
3. 대림: 최근 거래가 25.3억원, 가까운 역 방이역(323m), 가까운 교육시설 서울오금초등학교(216m), 749세대, 사용승인일 1988-12-10
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (1.4s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 극동, 극동, 대림, 대림, 대림; max_price 조건 만족; min_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "송파구",
    "min_price": 100000,
    "max_price": 300000
  },
  "resultNames": [
    "극동",
    "극동",
    "대림",
    "대림",
    "대림"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R12
질문
~~~~
반포역 근처 50억 이하 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 잠원현대훼밀리: 최근 거래가 27.9억원, 가까운 역 반포역(97m), 가까운 교육시설 반포자이유치원(133m), 113세대, 사용승인일 1997-08-27
2. 한신타운: 최근 거래가 27.0억원, 가까운 역 반포역(138m), 가까운 교육시설 신반포중앙유치원(141m), 110세대, 사용승인일 1989-06-30
3. 동아: 최근 거래가 34.0억원, 가까운 역 반포역(148m), 가까운 교육시설 반포자이유치원(156m), 991세대, 사용승인일 2002-07-04
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (1.0s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 잠원현대훼밀리, 한신타운, 동아; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "반포역",
    "radius_m": 800,
    "sort_by": "distance_asc",
    "max_price": 500000,
    "infra_preferences": [
      "transport"
    ],
    "limit": 3
  },
  "resultNames": [
    "잠원현대훼밀리",
    "한신타운",
    "동아"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R13
질문
~~~~
청담역 주변 비싼 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. PH129: 최근 거래가 155.0억원, 가까운 역 청담역(747m), 가까운 교육시설 성요셉유치원(535m), 29세대, 사용승인일 2020-08-12
2. 상지리츠빌카일룸(65-4): 최근 거래가 122.0억원, 가까운 역 청담역(318m), 가까운 교육시설 영동제일유치원(171m), 13세대, 사용승인일 2012-07-27
3. 상지리츠빌카일룸2차: 최근 거래가 100.0억원, 가까운 역 청담역(790m), 가까운 교육시설 성요셉유치원(528m), 15세대, 사용승인일 2006-08-25
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (1.1s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: PH129, 상지리츠빌카일룸(65-4), 상지리츠빌카일룸2차; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "청담역",
    "radius_m": 800,
    "sort_by": "price_desc",
    "infra_preferences": [
      "transport"
    ],
    "limit": 3
  },
  "resultNames": [
    "PH129",
    "상지리츠빌카일룸(65-4)",
    "상지리츠빌카일룸2차"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R14
질문
~~~~
수서역 근처 30억 이하 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 삼익: 최근 거래가 21.1억원, 가까운 역 수서역(197m), 가까운 교육시설 서울세종고등학교(188m), 645세대, 사용승인일 1992-10-28
2. 신동아: 최근 거래가 15.8억원, 가까운 역 수서역(217m), 가까운 교육시설 반디유치원(124m), 1162세대, 사용승인일 1992-10-28
3. 수서한아름: 최근 거래가 23.1억원, 가까운 역 수서역(310m), 가까운 교육시설 서울세종고등학교(167m), 498세대, 사용승인일 1993-11-01
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.8s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 4건: 삼익, 신동아, 수서한아름, 동익; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "수서역",
    "radius_m": 800,
    "sort_by": "distance_asc",
    "max_price": 300000,
    "infra_preferences": [
      "transport"
    ]
  },
  "resultNames": [
    "삼익",
    "신동아",
    "수서한아름",
    "동익"
  ],
  "resultCount": 4,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R15
질문
~~~~
역삼역 근처 500세대 이상 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 역삼I'PARK: 최근 거래가 10.1억원, 가까운 역 선릉역(594m), 가까운 교육시설 진선여자고등학교(303m), 541세대, 사용승인일 2006-09-28
2. 역삼푸르지오: 최근 거래가 26.6억원, 가까운 역 한티역(556m), 가까운 교육시설 역삼중학교(262m), 738세대, 사용승인일 2006-01-02
3. 럭키(963): 최근 거래가 29.4억원, 가까운 역 매봉역(721m), 가까운 교육시설 은광여자고등학교(459m), 1094세대, 사용승인일 1995-12-28
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.3s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 역삼I'PARK, 역삼푸르지오, 럭키(963), 래미안서초에스티지S, e-편한세상; min_households 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "역삼역",
    "radius_m": 1500,
    "sort_by": "distance_asc",
    "min_households": 500,
    "infra_preferences": [
      "transport"
    ]
  },
  "resultNames": [
    "역삼I'PARK",
    "역삼푸르지오",
    "럭키(963)",
    "래미안서초에스티지S",
    "e-편한세상"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R16
질문
~~~~
송파구 중학교 가까운 아파트 3곳 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 송파: 최근 거래가 11.9억원, 가까운 역 송파역(94m), 가까운 교육시설 잠실여자중학교(64m), 30세대, 사용승인일 2003-02-03
2. 한영해시안아파트: 최근 거래가 8.9억원, 가까운 역 송파역(716m), 가까운 교육시설 가락중학교(74m), 27세대, 사용승인일 2019-01-21
3. 코오롱아파트: 최근 거래가 20.0억원, 가까운 역 방이역(333m), 가까운 교육시설 방산중학교(79m), 758세대, 사용승인일 1991-10-18
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (1.7s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 송파, 한영해시안아파트, 코오롱아파트; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "송파구",
    "school_type": "중학교",
    "radius_m": 800,
    "infra_preferences": [
      "education"
    ],
    "sort_by": "school_distance_asc",
    "limit": 3
  },
  "resultNames": [
    "송파",
    "한영해시안아파트",
    "코오롱아파트"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R17
질문
~~~~
서초구 유치원 가까운 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 프라임: 최근 거래가 9.7억원, 가까운 역 양재시민의숲(매헌)역(979m), 가까운 교육시설 구룡유치원(37m), 10세대, 사용승인일 2002-10-14
2. 유원서초: 최근 거래가 27.3억원, 가까운 역 교대(법원.검찰청)역(403m), 가까운 교육시설 동화유치원(38m), 590세대, 사용승인일 1993-11-25
3. 현대서초3: 최근 거래가 19.7억원, 가까운 역 교대(법원.검찰청)역(605m), 가까운 교육시설 석문유치원(42m), 96세대, 사용승인일 1997-04-14
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (5.8s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 프라임, 유원서초, 현대서초3, 우솔마을서초리슈빌S아파트, 휴먼씨티; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "서초구",
    "school_type": "유치원",
    "radius_m": 800,
    "infra_preferences": [
      "education"
    ],
    "sort_by": "school_distance_asc"
  },
  "resultNames": [
    "프라임",
    "유원서초",
    "현대서초3",
    "우솔마을서초리슈빌S아파트",
    "휴먼씨티"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R18
질문
~~~~
강남구 교통 좋은 역세권 아파트 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 호산프라자: 최근 거래가 3.6억원, 가까운 역 선정릉역(36m), 가까운 교육시설 서울삼릉초등학교(355m), 41세대, 사용승인일 1997-12-23
2. 미켈란107: 최근 거래가 20.4억원, 가까운 역 봉은사역(78m), 가까운 교육시설 경기고등학교(450m), 64세대, 사용승인일 2003-12-31
3. 현대휴먼터치빌: 최근 거래가 2.3억원, 가까운 역 언주역(89m), 가까운 교육시설 성결유치원(570m), 160세대, 사용승인일 1999-06-19
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (3.8s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 호산프라자, 미켈란107, 현대휴먼터치빌; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "강남구",
    "infra_preferences": [
      "transport"
    ],
    "radius_m": 800,
    "sort_by": "distance_asc",
    "limit": 3
  },
  "resultNames": [
    "호산프라자",
    "미켈란107",
    "현대휴먼터치빌"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R19
질문
~~~~
잠실역 300m 안에 있는 아파트 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 롯데캐슬골드: 최근 거래가 27.8억원, 가까운 역 잠실(송파구청)역(144m), 가까운 교육시설 잠실중학교(374m), 400세대, 사용승인일 2005-11-22
2. 잠실시그마타워: 최근 거래가 23.0억원, 가까운 역 잠실(송파구청)역(161m), 가까운 교육시설 장미유치원(394m), 84세대, 사용승인일 1996-12-27
3. 현대타워: 최근 거래가 18.3억원, 가까운 역 잠실(송파구청)역(170m), 가까운 교육시설 장미유치원(342m), 55세대, 사용승인일 1996-12-30
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (1.1s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 5건: 롯데캐슬골드, 잠실시그마타워, 현대타워, 한신잠실코아, 한빛웰리스; 반경 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "station_name": "잠실역",
    "radius_m": 300,
    "sort_by": "distance_asc"
  },
  "resultNames": [
    "롯데캐슬골드",
    "잠실시그마타워",
    "현대타워",
    "한신잠실코아",
    "한빛웰리스"
  ],
  "resultCount": 5,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

### R20
질문
~~~~
서초구 40억 이하 신축 3개 추천해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로는 다음 후보를 우선 검토할 수 있습니다.
1. 더클래스: 최근 거래가 5.0억원, 가까운 역 남부터미널(예술의전당)역(360m), 가까운 교육시설 서울교육대학교부설초등학교(343m), 20세대, 사용승인일 2020-07-10
2. 비버리캐슬: 최근 거래가 11.8억원, 가까운 역 남부터미널(예술의전당)역(762m), 가까운 교육시설 서울고등학교(229m), 42세대, 사용승인일 2020-07-22
3. 지에스타워: 최근 거래가 4.4억원, 가까운 역 교대(법원.검찰청)역(271m), 가까운 교육시설 동화유치원(297m), 54세대, 사용승인일 2022-01-21
제공된 데이터만으로 작성한 요약이며, 상권이나 학군 평판처럼 데이터에 없는 내용은 판단하지 않았습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.2s)
좋았던 점: top-level success=true; handler=recommendation; feature success=true; 추천 결과 3건: 더클래스, 비버리캐슬, 지에스타워; max_price 조건 만족; 답변이 결과 단지명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "recommendation",
  "success": true,
  "criteria": {
    "district": "서초구",
    "max_price": 400000,
    "is_new_build": true,
    "min_built_year": 2020,
    "limit": 3
  },
  "resultNames": [
    "더클래스",
    "비버리캐슬",
    "지에스타워"
  ],
  "resultCount": 3,
  "missingApartmentNames": null,
  "message": "조건에 맞는 아파트를 조회했습니다."
}
~~

## 비교 QA 20건

### C01
질문
~~~~
은마랑 도곡렉슬 가격 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 은마: 최근 거래가 34.0억원
- 도곡렉슬: 최근 거래가 58.0억원
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 은마, 도곡렉슬; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "은마",
      "도곡렉슬"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "은마",
    "도곡렉슬"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C02
질문
~~~~
청담자이랑 래미안퍼스티지 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 청담자이: 최근 거래가 24.9억원, 708세대, 2011년 준공, 가까운 역 청담역(563m), 가까운 학교 서울봉은초등학교(469m)
- 래미안퍼스티지: 최근 거래가 78.5억원, 2444세대, 2009년 준공, 가까운 역 고속터미널역(397m), 가까운 학교 서울잠원초등학교(252m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 청담자이, 래미안퍼스티지; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "청담자이",
      "래미안퍼스티지"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year",
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "청담자이",
    "래미안퍼스티지"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C03
질문
~~~~
타워팰리스1이랑 타워팰리스2 세대수랑 연식 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 타워팰리스1: 최근 거래가 정보 없음, 1290세대, 2002년 준공
- 타워팰리스2: 최근 거래가 정보 없음, 813세대, 2003년 준공
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 타워팰리스1, 타워팰리스2; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "타워팰리스1",
      "타워팰리스2"
    ],
    "metrics": [
      "households",
      "built_year"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "타워팰리스1",
    "타워팰리스2"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C04
질문
~~~~
아크로리버파크랑 래미안원펜타스 가격이랑 평당가 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 아크로리버파크: 최근 거래가 71.0억원
- 래미안원펜타스: 최근 거래가 100.0억원
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 아크로리버파크, 래미안원펜타스; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "아크로리버파크",
      "래미안원펜타스"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "아크로리버파크",
    "래미안원펜타스"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C05
질문
~~~~
잠실엘스랑 리센츠 교통 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 잠실엘스: 최근 거래가 정보 없음, 가까운 역 종합운동장역(404m)
- 리센츠: 최근 거래가 정보 없음, 가까운 역 잠실새내역(299m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 잠실엘스, 리센츠; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "잠실엘스",
      "리센츠"
    ],
    "metrics": [
      "nearest_station"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": [
      "transport"
    ]
  },
  "resultNames": [
    "잠실엘스",
    "리센츠"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C06
질문
~~~~
반포자이랑 래미안퍼스티지 초등학교 접근성 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 반포자이: 최근 거래가 정보 없음, 가까운 학교 서울원촌초등학교(149m)
- 래미안퍼스티지: 최근 거래가 정보 없음, 가까운 학교 서울잠원초등학교(252m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 반포자이, 래미안퍼스티지; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "반포자이",
      "래미안퍼스티지"
    ],
    "metrics": [
      "nearest_school"
    ],
    "school_type": "초등학교",
    "school_name": null,
    "infra_preferences": [
      "education"
    ]
  },
  "resultNames": [
    "반포자이",
    "래미안퍼스티지"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C07
질문
~~~~
개포자이프레지던스랑 디에이치아너힐즈 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 개포자이프레지던스: 최근 거래가 50.8억원, 가까운 역 개포동역(636m), 가까운 학교 서울개포초등학교(167m)
- 디에이치아너힐즈: 최근 거래가 35.0억원, 1320세대, 2019년 준공, 가까운 역 개포동역(581m), 가까운 학교 서울개포유치원(307m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 개포자이프레지던스, 디에이치아너힐즈; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "개포자이프레지던스",
      "디에이치아너힐즈"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year",
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "개포자이프레지던스",
    "디에이치아너힐즈"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C08
질문
~~~~
헬리오시티랑 파크리오 세대수 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 헬리오시티: 최근 거래가 정보 없음, 9510세대
- 파크리오: 최근 거래가 정보 없음, 6864세대
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 헬리오시티, 파크리오; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "헬리오시티",
      "파크리오"
    ],
    "metrics": [
      "households"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "헬리오시티",
    "파크리오"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C09
질문
~~~~
에테르노청담이랑 PH129 가격 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 에테르노청담: 최근 거래가 218.0억원
- PH129: 최근 거래가 155.0억원
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 에테르노청담, PH129; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "에테르노청담",
      "PH129"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "에테르노청담",
    "PH129"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C10
질문
~~~~
현대2차랑 신현대11차 연식 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 현대2차: 최근 거래가 정보 없음, 2006년 준공
- 신현대11차: 최근 거래가 정보 없음, 1983년 준공
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 현대2차, 신현대11차; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "현대2차",
      "신현대11차"
    ],
    "metrics": [
      "built_year"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "현대2차",
    "신현대11차"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C11
질문
~~~~
청담자이랑 아이파크 교통이랑 학교 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 청담자이: 최근 거래가 정보 없음, 가까운 역 청담역(563m), 가까운 학교 서울봉은초등학교(469m)
- 아이파크: 최근 거래가 정보 없음, 가까운 역 봉은사역(438m), 가까운 학교 서울봉은초등학교(261m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 청담자이, 아이파크; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "청담자이",
      "아이파크"
    ],
    "metrics": [
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": [
      "education",
      "transport"
    ]
  },
  "resultNames": [
    "청담자이",
    "아이파크"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C12
질문
~~~~
은마랑 선경1차 가격 세대수 연식 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 은마: 최근 거래가 34.0억원, 4424세대, 1979년 준공
- 선경1차(1동-7동): 최근 거래가 60.0억원, 644세대, 1983년 준공
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 은마, 선경1차(1동-7동); 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "은마",
      "선경1차"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "은마",
    "선경1차(1동-7동)"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C13
질문
~~~~
도곡렉슬이랑 대치현대 어디가 더 대단지야 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 도곡렉슬: 최근 거래가 정보 없음, 3002세대
- 대치현대: 최근 거래가 정보 없음, 630세대
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 도곡렉슬, 대치현대; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "도곡렉슬",
      "대치현대"
    ],
    "metrics": [
      "households"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "도곡렉슬",
    "대치현대"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C14
질문
~~~~
상지리츠빌카일룸2차랑 마크힐스이스트윙 가격 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 상지리츠빌카일룸2차: 최근 거래가 100.0억원
- 마크힐스이스트윙: 최근 거래가 84.0억원
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 상지리츠빌카일룸2차, 마크힐스이스트윙; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "상지리츠빌카일룸2차",
      "마크힐스이스트윙"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "상지리츠빌카일룸2차",
    "마크힐스이스트윙"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C15
질문
~~~~
아크로리버파크랑 신반포2 교통 접근성 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 아크로리버파크: 최근 거래가 정보 없음, 가까운 역 신반포역(376m)
- 신반포2: 최근 거래가 정보 없음, 가까운 역 고속터미널역(542m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 아크로리버파크, 신반포2; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "아크로리버파크",
      "신반포2"
    ],
    "metrics": [
      "nearest_station"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": [
      "transport"
    ]
  },
  "resultNames": [
    "아크로리버파크",
    "신반포2"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C16
질문
~~~~
청담삼익아파트랑 청담자이 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 청담삼익아파트: 최근 거래가 40.0억원, 가까운 역 청담역(677m), 가까운 학교 서울봉은초등학교(384m)
- 청담자이: 최근 거래가 24.9억원, 708세대, 2011년 준공, 가까운 역 청담역(563m), 가까운 학교 서울봉은초등학교(469m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 청담삼익아파트, 청담자이; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "청담삼익아파트",
      "청담자이"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year",
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "청담삼익아파트",
    "청담자이"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C17
질문
~~~~
한양1차랑 한양2 가격과 연식 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 한양1차(영동한양): 최근 거래가 54.9억원, 1977년 준공
- 한양2: 최근 거래가 79.8억원, 1978년 준공
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 한양1차(영동한양), 한양2; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "한양1차",
      "한양2"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "built_year"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "한양1차(영동한양)",
    "한양2"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C18
질문
~~~~
개포주공5단지랑 개포주공7단지 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 개포주공5단지: 최근 거래가 38.8억원, 940세대, 1983년 준공, 가까운 역 개포동역(266m), 가까운 학교 서울개포유치원(163m)
- 개포주공7단지: 최근 거래가 23.5억원, 900세대, 1983년 준공, 가까운 역 대모산입구역(277m), 가까운 학교 돌샘유치원(57m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 개포주공5단지, 개포주공7단지; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "개포주공5단지",
      "개포주공7단지"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "households",
      "built_year",
      "nearest_station",
      "nearest_school"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": []
  },
  "resultNames": [
    "개포주공5단지",
    "개포주공7단지"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C19
질문
~~~~
래미안대치팰리스랑 은마 초등학교 가까운지 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 래미안대치팰리스: 최근 거래가 정보 없음, 가까운 학교 서울대도초등학교(446m)
- 은마: 최근 거래가 정보 없음, 가까운 학교 서울대곡초등학교(319m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 래미안대치팰리스, 은마; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "래미안대치팰리스",
      "은마"
    ],
    "metrics": [
      "nearest_school"
    ],
    "school_type": "초등학교",
    "school_name": null,
    "infra_preferences": [
      "education"
    ]
  },
  "resultNames": [
    "래미안대치팰리스",
    "은마"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~

### C20
질문
~~~~
잠실엘스랑 트리지움 가격이랑 교통 비교해줘
~~~~

답변
~~~~~~
조회된 데이터 기준으로 비교하면 다음과 같습니다.
- 잠실엘스: 최근 거래가 33.0억원, 가까운 역 종합운동장역(404m)
- 트리지움: 최근 거래가 32.3억원, 가까운 역 잠실새내역(503m)
상권, 학군 평판, 미래 가격 전망은 제공된 데이터만으로는 확인할 수 없습니다.
~~~~~~

평가(응답결과가 잘 나왔는지)
~~
판정: PASS (0.0s)
좋았던 점: top-level success=true; handler=comparison; feature success=true; 비교 결과 2건: 잠실엘스, 트리지움; 답변이 비교 대상명을 언급함
문제/수정 후보: 없음
criteria/result 요약:
{
  "handler": "comparison",
  "success": true,
  "criteria": {
    "apartment_names": [
      "잠실엘스",
      "트리지움"
    ],
    "metrics": [
      "latest_price",
      "pyeong",
      "price_per_pyeong",
      "nearest_station"
    ],
    "school_type": null,
    "school_name": null,
    "infra_preferences": [
      "transport"
    ]
  },
  "resultNames": [
    "잠실엘스",
    "트리지움"
  ],
  "resultCount": 2,
  "missingApartmentNames": [],
  "message": "아파트 비교 데이터를 조회했습니다."
}
~~
