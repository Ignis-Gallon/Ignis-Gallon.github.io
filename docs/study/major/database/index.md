---
title: "데이터베이스"
---

# 데이터베이스

3학년 2학기에 수강한 데이터베이스 수업의 노트입니다. 데이터베이스의 개념과 DBMS 구조에서 시작해 ER 모델을 이용한 개념적 설계, 관계형 데이터 모델과 관계 대수, SQLite로 실습한 SQL, 함수적 종속성과 정규화까지 다룹니다.

**데이터베이스 설계**

- [데이터베이스 개요](01-database-overview.md): 텍스트 파일로 저장할 때의 문제와 DB를 쓰는 이유, 데이터베이스의 특징, 사용자 분류, DBMS의 장점
- [DBMS 아키텍처와 스키마](02-dbms-architecture-schema.md): 데이터 모델의 분류, 스키마와 인스턴스, 3단계 스키마 아키텍처, DBMS 언어와 아키텍처
- [ER Diagram](03-er-diagram.md): Company 예제로 본 Entity와 Attribute 유형, 관계 타입과 제약 조건, Weak Entity Type, ER 표기법
- [관계형 데이터 모델과 제약 조건](04-relational-model-constraints.md): Relation, Tuple, Attribute, Domain, Null, 키와 무결성 제약 조건, 갱신 연산 시의 위반 처리
- [ER-관계 사상에 의한 관계 DB 설계](05-er-to-relational-mapping.md): Strong Entity, Weak Entity, 관계, 다치 애트리뷰트, n차 관계의 사상 규칙과 Book 예제 매핑
- [관계 대수 (Relational Algebra)](06-relational-algebra.md): Select, Project, 카티션 곱, Natural Join, Theta Join, 집합 연산, 재명명 연산, 질의 트리
- [Open API](07-open-api.md): Naver Geocoding을 이용한 RESTful API 실습을 짧게 적은 메모

**SQL**

- [SQL-2](08-sql-2.md): check 제약과 on update cascade, SELECT와 DISTINCT, LIKE, 산술식, 정렬, 별명(Alias), 집합 연산자
- [SQL-3](09-sql-3.md): 테이블 별명, 중첩 질의와 IN, ALL, ANY, 상관 중첩 질의, EXISTS, 집단 함수
- [SQL-4](10-sql-4.md): Max, Min, Count, Group By, Having, NULL 처리, SELECT 문 구조, INSERT, DELETE, UPDATE, View 만들기
- [SQL-5](11-sql-5.md): 뷰를 질의에 활용하기와 뷰 수정의 제약, 동시 접근 문제와 고장 감내성, Rollback, Transaction의 성질

**정규화**

- [함수적 종속성과 정규화](12-functional-dependency-normalization.md): 삽입, 삭제, 수정 이상, 함수적 종속성과 추론 규칙, Closure, 1NF부터 BCNF까지의 정규형
