---
title: "DB"
---

# DB

2023년 8월에 YouTube 강의 재생목록 [데이터베이스](https://www.youtube.com/playlist?list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe)를 보면서 정리한 노트입니다. 관계형 데이터 모델의 개념에서 시작해 SQL, join, 함수 종속과 정규화, index까지 다룹니다. 글 제목의 숫자는 원래의 강의 번호이며, 강의 가운데 골라서 정리한 노트이기 때문에 번호가 건너뜁니다.

- [2 (relation 개념)](01-relation.md): 수학에서 말하는 relation과 Cartesian Product, relational data model, relation schema, key, constraints
- [3 (sql 주요 개념)](02-sql-concepts.md): SQL 용어, attribute의 자료형, unique·check 제약, foreign key와 reference option, ALTER TABLE
- [8 (join)](03-join.md): implicit join과 explicit join, inner join과 outer join, equi·natural·cross·self join, join 예제
- [22 (functional dependency)](04-functional-dependency.md): 함수 종속의 정의, 스키마의 의미로 FD를 판단해야 하는 이유, FD의 종류
- [23 (정규화-1)](05-normalization-1.md): normal form의 단계, 임직원 월급 계좌 테이블 예제, 1NF와 2NF
- [24 (정규화-2)](06-normalization-2.md): transitive FD와 3NF, BCNF, 2NF 참고사항, 역정규화(denormalization)
- [25 (index)](07-index.md): full scan과의 성능 차이, index를 거는 문법, B tree 기반 동작 방식, covering index, hash index, full scan이 나은 경우
