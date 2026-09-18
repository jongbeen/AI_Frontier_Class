# DB for AI — 쉽게 정리한 노트

> 원본: DB_for_AI_-_이상구_교수님.pdf (이상구 교수, 서울대 IDS Lab) — 전체 102페이지
> 우리금융그룹 AI/데이터 전문가 과정 자료
> 페이지 번호(`P.n`)는 PDF 파일 기준이라 PDF 뷰어에서 바로 그 페이지로 이동할 수 있어.

## 전체 지도 (목차)

| 순서 | 주제 | PDF 범위 |
|---|---|---|
| I | 개요 (1. DB 역사) | `P.4~P.19` |
| II-2 | 관계형 DB | `P.20~P.31` |
| II-3 | SQL – 기본 | `P.32~P.51` |
| II-4 | SQL – 집계함수 | `P.52~P.61` |
| II-5 | SQL – 조인 | `P.62~P.71` |
| II-6 | 데이터 분석 (Star Schema, View) | `P.72~P.84` |
| III-7 | DB for ML – Feature Store | `P.85~P.88` |
| III-8 | LLM for DB – Text2SQL | `P.89~P.98` |
| III-9 | DB for LLM | `P.99~P.102` |

**한 줄 요약**: DB(관계형→DW→빅데이터→AI)가 어떻게 발전해왔는지 훑고, SQL(기본/집계/조인)을 실전 예제로 익힌 뒤, "AI 시대에 DB가 왜 여전히 중요한가"(Feature Store, Text2SQL, Vector DB, Agent 메모리)로 연결하는 강의야.

---

## §1. 개요 — DB의 역사와 AI로의 흐름 `P.4~P.19`

### 1-1. Computing = Data Processing `P.5`
거의 모든 전산 시스템(ERP, 웹커머스, 데이터마이닝, 추천 등)은 결국 데이터베이스를 쓰게 돼. "컴퓨팅 = 데이터 처리"라는 관점으로 이 강의 전체를 보면 돼.

### 1-2. File System vs DBMS `P.6`
- **File System**: OS의 핵심 하부 모듈. 각 애플리케이션이 각자 파일 관리, 동시성 제어, 보안, 복구를 다 알아서 해야 함 — 중복되고 비효율적.
- **DBMS(Database Management System)**: 이 저장/관리/처리 기능을 하나의 소프트웨어로 통합해서 여러 애플리케이션이 공유하는 구조.

> 💡 파일시스템 시대엔 앱마다 "저장하는 법"을 다 새로 짰다면, DBMS는 그걸 공통 인프라로 떼어낸 거라고 보면 돼.

### 1-3. [Tipping Point 1] 관계형 DB 등장 `P.7~P.8`
관계형 모델이 뜬 이유 4가지:
- 단순하고 직관적인 구조(테이블)
- 쉽고 강력한 SQL
- 자동 최적화로 빠른 처리
- 안정적인 트랜잭션 지원

여기서 핵심 개념이 **증거기반 의사결정(Evidence Based Decision Making)**이야. 셜록 홈즈 인용이 나오는데 — "데이터 없이 이론부터 세우면 안 된다"는 뜻으로, 데이터를 봐야 진짜 통찰이 나온다는 얘기.
두 가지 관점을 구분하는 게 중요해:
- **평면적/실행적 관점(tabular/transactional)**: 업무 처리용 — "이 주문 처리해줘"
- **다차원적/분석적 관점(multidimensional/analytic)**: 의사결정용 — "지역별/월별 매출 추이는?"

### 1-4. [Tipping Point 2] Data Warehouse `P.9`
업무시스템(OLTP, transaction system)에 흩어진 데이터를 ETL(Extract-Transform-Load)로 한 곳에 모아서 분석 전용으로 쓰는 저장소가 DW야. "Data Processing(거래처리) → Information Processing(분석)"으로 넘어가는 전환점.

### 1-5. 데이터 폭발과 [Tipping Point 3] Big Data `P.10~P.13`
- 데이터가 1.5년마다 2배씩 증가(모바일/소셜/센서 때문).
- 빅데이터가 뜬 이유 3가지: **Ubiquitous**(모든 분야에서 일어남) + **Feasible**(풍부한 데이터·컴퓨팅·분석기술로 대응 가능) + **Virtuous Cycle**(성공사례가 쌓이며 데이터의 가치가 인정받는 선순환).
- 이를 다루려고 **Hadoop**(분산처리 프레임워크, MapReduce 기반)과 **NoSQL**(단순한 key-value 모델, BASE 트랜잭션 모델)이 등장.

> 💡 NoSQL의 BASE(Basically Available, Soft state, Eventual consistency)는 관계형 DB의 엄격한 ACID 트랜잭션과 대비되는 개념이야 — "당장은 좀 느슨해도, 결국엔 일관성이 맞춰진다"는 철학.

### 1-6. [Tipping Point 4] AI – Machine Learning `P.14`
컴퓨터 비전, 자연어처리, 자율주행, 생성형 AI(GPT, Sora 등)로 이어지는 흐름. 한 줄로 요약하면 **"기계학습은 컴퓨터 입장에서의 증거기반 의사결정"** — 즉 사람이 데이터 보고 판단하듯, 기계도 데이터(feature)를 보고 판단하는 것.

### 1-7. Feature Store `P.15`
기계학습에 쓰이는 데이터(feature)를 체계적으로 관리하는 저장소 개념. 학습 단계와 운영(서빙) 단계를 모두 지원(MLOps). *이 개념은 뒤 §7에서 다시 자세히 나와.*

### 1-8. [Tipping Point 5] 생성형 AI와 RAG `P.16~P.18`
- **LLM의 한계**: 입출력이 텍스트뿐이라 ① 지식 단절성(학습 시점 이후 정보 모름) ② 1회성 질의응답(대화 맥락 기억 못함/제한적) ③ 프라이버시·보안 문제 — 그래서 회사 업무에 바로 쓰기 어려움.
- **RAG(Retrieval Augmented Generation, 검색증강생성)**: LLM이 답하기 전에 회사 내부 문서(예: 인사규정)를 검색해서 그 내용을 참고하게 하는 방식. "우리회사 정보와 연결"하는 게 핵심.
- **RAG++**: 문서뿐 아니라 **정형 데이터(SQL DB)**까지 검색 대상으로 확장한 것. Text-RAG(문서DB 검색)와 SQL-RAG(Text2SQL 기술로 DB 조회)를 결합.

> 💡 김철수씨 휴가 예시: "인사규정상 몇 년차는 며칠"이라는 일반 규정(Text-RAG)과 "김철수 개인의 실제 사용 연차"(SQL-RAG, DB 조회)를 같이 봐야 정확한 답이 나온다는 게 RAG++의 핵심이야.

### 1-9. Text2SQL `P.19`
자연어 질문("A지점에 S카드 여신잔액 있는 고객 정보 알려줘!")을 SQL로 변환해서 DB를 직접 조회하는 기술. *이 강의 후반부(§8)의 핵심 주제로 이어져.*

---

## §2. 관계형 데이터베이스 `P.20~P.31`

### 2-1. 관계형 데이터 모델 용어 `P.21~P.22`
같은 개념을 부르는 이름이 여러 개 있어서 헷갈리기 쉬운데, 이렇게 정리하면 돼:

| 공식 용어 | 다른 이름 | 뜻 |
|---|---|---|
| attribute | 속성, 컬럼, 필드 | 표의 열 |
| record | 레코드, 행, 튜플 | 표의 한 줄(실세계의 개체 하나) |
| relation | 테이블, 릴레이션, 관계 | 표 전체 |

### 2-2. 스키마 & 인스턴스 `P.23`
- **Schema**: 테이블이 어떤 속성(컬럼)으로 구성되는가 — "테이블의 골격/설계도"
- **Instance**: 그 골격에 실제로 채워진 데이터 값들 — "테이블의 현재 상태"

> 💡 스키마는 엑셀의 "헤더 행"이고, 인스턴스는 그 아래 실제로 채워진 "데이터 행들"이라고 생각하면 돼.

### 2-3. 관계형 DB = 테이블들의 집합 `P.24`
대학교 예제 DB에 나오는 테이블들: instructor(교수), course(교과목), department(학과), section(개설강좌), teaches(담당교수), prereq(선수과목). 이 테이블들이 서로 연결되면서 "대상 정보 전체"를 표현해.

### 2-4. Keys (키) `P.25~P.26`
- **식별자(identifier)**: 각 레코드를 유일하게 구분할 수 있는 컬럼(또는 컬럼 조합). 예: 주민등록번호, 학번. 여러 컬럼 조합도 가능(예: <주소, 이름>).
- **Primary Key(기본키, PK)**: 여러 식별 가능한 키 중 "대표"로 정한 하나. 테이블마다 1개.
- **Foreign Key(외래키, FK)**: 다른 테이블의 PK를 참조하는 컬럼. 예: `instructor.dept_name`은 `department.dept_name`에 실제로 존재하는 값만 쓸 수 있음 → 이게 테이블 간 "연결고리" 역할을 해.

> 💡 FK는 엑셀로 치면 "다른 시트의 값만 골라 쓸 수 있게 드롭다운을 걸어놓은 셀"이라고 보면 이해가 쉬워. 데이터 정합성을 지켜주는 장치야.

### 2-5. Database Design `P.30~P.31`
어떤 컬럼을 가진 어떤 테이블들로 구성할지 설계하는 문제. 목표는 세 가지: ① 필요한 정보를 다 표현할 수 있어야 하고 ② 중복을 최소화하고 ③ 운영/조작이 쉬워야 함. 슬라이드의 `instructor` vs `instructor + dept` 비교 예시는, 학과 정보를 교수 테이블에 다 때려박을지 아니면 분리할지의 트레이드오프를 보여줘(중복 vs 조인 필요성).

---

## §3. SQL – 기본 `P.32~P.51`

### 3-1. 질의(Query)와 질의어(Query Language) `P.33`
- **질의**: DB에서 정보를 추출하는 질문(information need)
- **질의어**: 그 질의를 표현하는 언어. 넓은 의미로는 조회뿐 아니라 삽입/삭제/수정까지 포함.

### 3-2. SQL의 역사 `P.36~P.37`
1970년 E.F. Codd가 관계형 모델을 제안했고, IBM이 System R 프로젝트(1974)로 구현하면서 SEQUEL(→SQL)이라는 질의어를 만들었어. 1986년 ANSI/ISO 표준으로 채택.

SQL은 크게 두 종류로 나뉘어:
- **DDL(Data Definition Language)**: 스키마 생성/수정/삭제 — `create table`, `drop table`, `alter table`
- **DML(Data Manipulation Language)**: 데이터 조작/조회 — `insert`, `delete`, `update`, `select` (이 중 **select**가 압도적으로 많이 쓰임)

### 3-3. SELECT 문의 기본 구조 `P.38`
```
select <컬럼 리스트>
from <테이블 리스트>
where <조건>
```
- `select` 절: 출력할 컬럼 지정
- `from` 절: 어떤 테이블(들)을 쓸지
- `where` 절: 어떤 레코드를 고를지(조건, 생략 가능 → 전체 레코드)

### 3-4. SELECT 절 활용 `P.39~P.41`
```sql
-- 전체 컬럼 (와일드카드 *)
select * from instructor

-- 중복 제거
select distinct dept_name from instructor

-- 산술식도 컬럼처럼 사용 가능
select ID, name, salary/12 from instructor
```
`*`는 "모든 속성"을 뜻하는 와일드카드고, `distinct`는 중복된 결과값을 하나로 합쳐줘. 산술식(+,-,*,/)을 select 절에 바로 쓸 수 있다는 것도 포인트야 — 컬럼명 자체도 "산술식의 한 종류"로 취급된다는 개념.

### 3-5. WHERE 절과 연산자 `P.42`
각 레코드가 결과에 포함되려면 만족해야 하는 조건. `from`절 테이블의 레코드 하나하나에 대해 평가돼.
- 비교 연산자: `>`, `<`, `=`, `<=`, `>=`, `!=`
- 논리 연산자: `and`, `or`, `not`

```sql
-- 연봉 80000 이상인 CS 교수 이름
select name from instructor
where dept_name = 'Comp. Sci.' and salary >= 80000
```

### 3-6. FROM 절과 카티션 곱(Cartesian Product) `P.44`
테이블을 두 개 이상 나열하면, 양쪽 레코드의 **가능한 모든 조합**으로 이루어진 거대한 임시 테이블이 만들어져(카티션 곱). `where`절과 `select`절은 이 거대한 테이블에 대해 작동해.

> 💡 이게 조인(join)의 원리적 배경이야 — `where` 조건으로 이 조합 중 "실제로 관련 있는 것"만 걸러내는 게 다음 §3-7에서 나오는 조인이야.

### 3-7. 조인(Join) — where절 방식 `P.45~P.47`
```sql
-- 각 교수의 이름과 그가 가르친 과목 ID
select name, course_id
from instructor, teaches
where instructor.ID = teaches.ID
```
서로 다른 테이블의 공통 컬럼(여기선 ID)을 `where`절에서 맞춰줌으로써 관련 있는 레코드끼리만 연결하는 게 조인이야.

**Rename(별명, alias)**: 같은 테이블을 두 번 조인해야 할 때(자기 자신과 비교) 필수적으로 필요해.
```sql
-- Einstein 교수보다 연봉 높은 교수들
select distinct T.name
from instructor as T, instructor as S
where T.salary > S.salary and S.name = 'Einstein'
```
여기서 `instructor`를 T, S 두 개의 "가상 인스턴스"처럼 다루는 거야 — `as`는 생략 가능(`instructor as T` ≡ `instructor T`).

---

## §4. SQL – 집계함수 `P.52~P.61`

### 4-1. 집계함수(Aggregate Functions)란 `P.55`
데이터 집합에서 **하나의 대표값**을 뽑아내는 함수:

| 함수 | 뜻 |
|---|---|
| `avg` | 평균 |
| `min` / `max` | 최소값 / 최대값 |
| `sum` | 합계 |
| `count` | 개수 |

```sql
select avg(salary), min(salary), max(salary) from instructor
```

### 4-2. 그룹 없이 vs 그룹별로 `P.53~P.54`
```sql
-- 전체 평균 연봉 (한 줄 결과)
select avg(salary) from instructor

-- 학과별 평균 연봉 (그룹별 결과)
select dept_name, avg(salary)
from instructor
group by dept_name
```

### 4-3. GROUP BY 규칙 `P.58`
지정된 컬럼 값이 같은 레코드들끼리 묶어서 그룹별로 집계함수를 적용. **중요한 규칙**: `group by`절에 나열된 컬럼과 `select`절에서 집계되지 않은(집계함수를 안 쓴) 컬럼이 일치해야 해.

> 💡 예를 들어 `select dept_name, avg(salary) ... group by dept_name`에서, `dept_name`은 group by에도 있고 select에도(집계 안 된 채) 있어서 OK. 만약 select에 `name`까지 넣으면, name은 그룹 내에서 여러 값이 있을 수 있어서 DB가 "어떤 name을 보여줘야 할지" 알 수 없어 에러가 나.

### 4-4. count(distinct ...) `P.56`
```sql
-- 2021년 봄학기에 강의한 CS 교수가 몇 명인지 (중복 제거)
select count(distinct instructor.ID)
from teaches, instructor
where instructor.ID = teaches.ID
  and semester = 'Spring' and year = '2021'
  and dept_name = 'Comp. Sci.'
```
같은 교수가 여러 과목을 가르치면 `teaches`에 여러 번 나오니까, `distinct`로 중복 제거한 뒤 세어야 정확한 "인원 수"가 나와.

### 4-5. ORDER BY `P.59`
검색 결과를 정렬. 위치는 항상 맨 마지막.
```sql
select * from instructor order by name          -- 오름차순(기본)
select * from instructor order by dept_name desc, salary asc  -- 여러 컬럼, 내림/오름 혼합 가능
```

---

## §5. SQL – 조인(Join) `P.62~P.71`

앞(§3-7)에서 `where`절로 조인하는 법을 봤는데, 여기선 명시적인 `join` 키워드를 쓰는 방법들을 배워. 결과는 동일하지만 가독성과 편의성이 다름.

### 5-1. Join – ON `P.63~P.64`
```sql
select * from instructor join teaches on (instructor.ID = teaches.ID)
```
이건 다음과 완전히 같아:
```sql
select * from instructor, teaches where instructor.ID = teaches.ID
```
`on` 절로 조인 조건을 명시적으로 표현하는 방식이고, `from`절에서 조인 결과 전체를 "하나의 테이블"로 취급해서 다른 테이블과 또 조인할 수도 있어.

### 5-2. Join – USING `P.65~P.66`
양쪽 테이블에 **이름이 같은 컬럼**으로 조인할 때 더 간결하게 쓸 수 있는 방법.
```sql
select * from section join teaches using (course_id, sec_id, semester, year)
```
`on`과의 차이: `using`은 동치(=) 조건에 해당하는 "컬럼 이름"만 나열하면 되고(양쪽에 같은 이름 필요), 조인 후엔 그 컬럼이 **하나만 남아**. `on`은 조건을 완전히 명시해야 하고 양쪽 컬럼이 각각 남음.

### 5-3. Natural Join `P.67~P.68`
양쪽 테이블에서 **이름이 같은 컬럼을 자동으로 찾아서** 조인. `using`에서 컬럼 이름 나열조차 생략한 버전이라고 보면 돼.
```sql
select * from instructor natural join teaches
```

> 💡 세 방식(`on` / `using` / `natural join`) 비교: `on`은 "내가 직접 조건을 다 써주는" 가장 명시적인 방법, `using`은 "이름 같은 컬럼들만 짚어주는" 중간, `natural join`은 "이름 같은 컬럼을 DB가 알아서 찾는" 가장 편하지만 위험한(의도치 않은 컬럼까지 엮일 수 있는) 방법이야. 실무에선 안전하게 `on`을 쓰는 경우가 많아.

### 5-4. 여러 테이블 조인 실전 예제 `P.69~P.71`
```sql
-- Einstein 교수가 301동에서 강의한 과목을 들은 학생들 학번(중복 제거)
select distinct takes.ID
from takes, section, teaches, instructor
where takes.course_id = section.course_id
  and takes.sec_id = section.sec_id
  and takes.semester = section.semester
  and takes.year = section.year
  and section.course_id = teaches.course_id
  and section.sec_id = teaches.sec_id
  and section.year = teaches.year
  and teaches.ID = instructor.ID
  and instructor.name = 'Einstein' and building = '301'
```
테이블이 많아질수록 연결고리(FK 관계)를 하나씩 다 맞춰줘야 하는 걸 보여주는 예제야. `natural join`과 `using`을 쓰면 훨씬 짧아져:
```sql
select distinct ID, name
from student natural join takes
     natural join section
     join course C using (course_id)
where year = 2020 and C.dept_name = 'CS'
```

---

## §6. 데이터 분석 `P.72~P.84`

### 6-1. 다중 그룹 집계 실전 `P.74~P.76`
```sql
-- 학생의 학과별, 년도별 평점평균
select S.dept_name, T.year, avg(grade)
from takes T, student S
where T.ID = S.ID
group by S.dept_name, T.year
```
`group by`에 컬럼을 여러 개 넣으면 그 조합별로 세부 그룹이 나뉘어. "학과×년도"처럼 다차원으로 쪼개서 보는 게 바로 §1-3에서 언급했던 "다차원적/분석적 관점"의 실제 구현이야.

### 6-2. Star Schema `P.77~P.79`
DB 상의 어떤 **사건(event)**을 중심에 놓고, 그 사건에 참여하는 여러 개체(dimension)의 정보들을 FK로 연결한 구조. 편의점 예시(브랜드별 매출, 점포별 판매 등), 신용카드 예시(고객 세그먼트별 카드 사용 패턴) 모두 "중심 팩트 테이블 + 여러 차원 테이블"의 형태야.

> 💡 Star Schema는 이름 그대로 가운데 팩트 테이블(거래/판매 기록)이 있고, 그 주변에 별처럼 차원 테이블(상품, 매장, 시간, 고객 등)이 붙어있는 구조야. "브랜드별×도시별 매출"처럼 여러 각도로 데이터를 잘라볼 수 있게 설계된 거지.

### 6-3. View(가상 테이블) `P.81~P.84`
기존 테이블들로부터 만들어지는 **가상의 테이블**. 실제로 데이터를 저장하는 게 아니라, 쓸 때마다 기존 테이블에서 다시 계산돼.
```sql
create view instructor_2 as
  select ID, name, dept_name
  from instructor
```
**View의 용도 두 가지**:
1. 테이블 내용의 일부를 숨김 (보안 효과)
2. 자주 쓰는 복잡한 조인 질의를 View로 만들어두고 간편하게 재사용

`takes_detail` 예제처럼 여러 테이블(takes, section, course, student, teaches, instructor)을 다 조인해서 만든 View를, 이후에는 마치 단일 테이블처럼 간단하게 조회할 수 있어(`P.84`의 Exercise 참고).

---

## §7. DB for ML – Feature Store `P.85~P.88`

### 7-1. Feature Engineering `P.86`
기계학습은 과거 데이터를 학습해서 모델을 만들고, 그 모델로 새 입력에 대한 결과를 예측/생성하는 것. 이때 **원시 데이터(raw data)를 ML이 바로 쓸 수 있는 형태로 가공하는 과정**이 feature engineering이야. Product feature table과 customer feature table을 Join 등으로 합쳐서 최종 training dataset을 만드는 흐름이 슬라이드에 나와.

### 7-2. Feature Store의 역할 `P.87~P.88`
Feature Store는 여러 ML 팀이 쓰는 feature들을 **중앙에서 관리·공유**하는 저장소야. 두 가지 문제를 해결해:
- 학습 단계와 추론(운영) 단계에서 feature 계산 로직이 서로 다르게 구현되는 걸 방지(일관성 보장)
- 서로 다른 ML 팀이 비슷한 feature를 중복해서, 혹은 다르게 정의하는 걸 방지

**Feature table**은 entity(예: 고객, 상품)마다 PK와 feature들로 구성된, 소스 데이터 기반의 **materialized view**(실제로 계산되어 저장된 뷰)야. §6-3의 View는 "그때그때 계산"이지만, materialized view는 "미리 계산해서 저장"해둔다는 차이가 있어(속도를 위해).

---

## §8. LLM for DB – Text2SQL `P.89~P.98`

### 8-1. Text2SQL이 왜 필요한가 `P.90~P.91`
§6까지 배운 것처럼, 원하는 정보를 얻으려면 결국 복잡한 SQL을 짜야 해(다중 조인, group by 등). 일반 사용자는 SQL을 모르니까, **자연어 질문을 자동으로 SQL로 변환**해주는 게 Text2SQL이야.

### 8-2. Text2SQL이 어려운 이유 5가지 `P.92~P.94`
1. **자연어의 모호성**: "각 호텔의 평가를 알려줘"가 별점인지, 평점인지, 둘 다인지 불명확
2. **Schema Linking – 테이블/컬럼 명**: 자연어 단어가 실제 컬럼명과 매칭이 안 될 수 있음(예: "거래일"이 `DT`인지 다른 컬럼인지)
3. **Schema Linking – 데이터 값**: gender가 'M'/'F'인지 0/1인지 '남'/'여'인지, "최우수 고객"이 status='A'인지 type='최우수'인지 등 실제 저장된 값의 형태를 알아야 함
4. **Schema Linking – Join Path**: "Einstein 교수의 학생들"이라는 질문도 수강생인지, 지도학생인지, 같은 학과 학생인지에 따라 완전히 다른 조인 경로가 필요
5. **전문 지식(Domain Knowledge)**: 수율 계산식, "특목고"의 정의, "연체/부실"의 정의처럼 업무 도메인 지식이 있어야 정확한 SQL을 짤 수 있음

> 💡 이 5가지는 딱 이 강의 §2~§6에서 배운 내용과 정확히 대응돼: ①은 질의 자체의 모호함, ②③은 §2의 스키마/데이터, ④는 §5의 조인, ⑤는 §6의 도메인 분석 지식. 즉 Text2SQL이 어려운 이유는 결국 "DB를 잘 아는 사람의 암묵지"를 기계가 대신 알아야 하기 때문이야.

### 8-3. Text2SQL Pipeline과 벤치마크 `P.95~P.96`
일반적인 Text2SQL 파이프라인 구조가 있고, 대표 벤치마크로 **BIRD-SQL**을 소개해 — 12,751개의 질문-SQL 쌍, 95개의 대형 DB(총 33.4GB), 37개 이상의 전문 도메인을 커버하는 대규모 평가셋이야.

### 8-4. 분석용 Text2SQL `P.97`
§6-3에서 배운 주제별 View에 대해 자연어로 질의하는 방식. "엑셀 스프레드시트에 자연어로 명령하듯" 쓸 수 있게 하는 게 목표.

---

## §9. DB for LLM `P.99~P.102`

### 9-1. Vector DB `P.100`
LLM/RAG 시대엔 텍스트를 임베딩(벡터)으로 바꿔서 "의미가 비슷한" 문서를 찾아야 해. 이때 쓰이는 검색 방식:

| 방식 | 특징 |
|---|---|
| K-NN (정확한 최근접 탐색) | 정확한 top-k 결과, 하지만 너무 느림 |
| ANN (근사 최근접 탐색) | 약간 부정확할 수 있지만 매우 빠름 |
| Hybrid Retrieval | 키워드 검색(BM25 등)과 벡터 검색을 병합, 메타데이터 필터로도 활용 |

### 9-2. DB for Agents `P.101`
AI 에이전트가 반복적으로 작업(Loop)을 수행하려면 DB 기반의 메모리가 필요해. 세 가지 메모리 유형:
- **Semantic memory**: 사실 및 상식(일반 지식)
- **Episodic memory**: 과거의 사건과 경험(대화 이력 등)
- **Procedure memory**: 태스크를 처리하는 절차/방법

> 💡 이건 §1-8의 RAG 개념이 한 단계 더 발전한 형태로 볼 수 있어 — RAG가 "질문할 때마다 문서를 찾아오는 것"이었다면, Agent 메모리는 "에이전트가 스스로 경험/절차/지식을 DB에 쌓아가며 활용하는 것"이야.

---

## 한눈에 보는 핵심 정리표

| 조인 방식 | 조건 명시 | 컬럼 이름 조건 | 결과 컬럼 | 장단점 |
|---|---|---|---|---|
| `where` 절 방식 | `where a.x = b.x` | 자유 | 양쪽 컬럼 모두 남음 | 가장 원초적, 모든 DB에서 동작 |
| `join ... on` | `on (a.x = b.x)` | 자유 | 양쪽 컬럼 모두 남음 | 가장 명시적, 실무 권장 |
| `join ... using` | 컬럼명만 나열 | 양쪽 이름 같아야 함 | 컬럼 1개만 남음 | 간결하지만 이름 일치 필요 |
| `natural join` | 자동 매칭 | 이름 같은 컬럼 전부 자동 | 컬럼 1개만 남음(자동) | 가장 짧지만 의도치 않은 매칭 위험 |

| 시대/개념 | 핵심 문제의식 | 해결책 |
|---|---|---|
| 관계형 DB | 데이터를 단순·안정적으로 저장/질의 | SQL, transaction |
| Data Warehouse | 여러 업무시스템 데이터를 분석에 활용 | ETL, 통합 저장 |
| Big Data | 폭증하는 비정형/대용량 데이터 처리 | Hadoop, NoSQL |
| DB for ML | ML이 쓸 feature를 일관되게 관리 | Feature Store |
| LLM for DB | 자연어로 DB 조회 | Text2SQL |
| DB for LLM | LLM이 최신/사내 정보 활용 | RAG, Vector DB |

---

## 스스로 확인해볼 질문 (복습용)

1. Data Warehouse가 등장하게 된 배경은 무엇이고, OLTP 시스템과 어떤 관계에 있을까? (`P.9` 참고)
2. `where`절 방식 조인과 `natural join`은 결과가 같아 보이는데, 왜 실무에서는 `natural join`을 조심해서 써야 할까? (`P.67~P.68` 참고)
3. `group by`절에 나열된 컬럼과 select절의 컬럼이 일치해야 하는 이유는 뭘까? 만약 어긴다면 어떤 문제가 생길까? (`P.58` 참고)
4. Feature Store에서 말하는 "materialized view"는 §6에서 배운 일반 View와 무엇이 다를까? (`P.81`, `P.88` 참고)
5. Text2SQL이 어려운 5가지 이유 중, 만약 우리은행 내부 데이터에 적용한다면 어떤 이유가 가장 심각한 문제가 될 것 같아? 왜 그럴까? (`P.92~P.94` 참고)
6. RAG++(`P.18`)와 §9-2의 Agent 메모리(`P.101`)는 둘 다 "LLM에 외부 정보를 붙여준다"는 점에서 비슷한데, 구조적으로 어떻게 다를까?
