---
title: "AWS와 MySQL"
---

# AWS와 MySQL

서버를 운영하고 데이터베이스를 다루면서 찾아본 내용을 적어 둔 노트입니다. 2022년 9월에 SQL 연습 문제를 풀며 쓴 풀이, 2023년 2월의 서버 운영 도구 메모, 2023년 12월부터 2024년 1월 사이 DB 백업 작업을 하면서 AWS 문서와 블로그를 보고 정리한 RDS와 mysqldump 관련 글을 묶었습니다.

**AWS**

- [AWS(서버 운영 도구 메모)](01-aws-server-tools.md): 원격 접속 프로그램과 터미널 프로그램의 용도, 프로세스 관리 도구(pm2) 명령어
- [RDS에서 데이터를 내보내는 방법](02-rds-export.md): 이전 서비스(DMS)와 덤프 도구(mysqldump)의 비교, 선택할 때 고려할 점
- [RDS(스토리지 확인, 온디맨드와 예약형)](03-rds-storage-pricing.md): 스토리지 사용량을 확인하는 방법, 온디맨드와 예약형 인스턴스의 요금 차이와 청구 단위
- [다중 AZ DB 인스턴스](04-multi-az-db-instance.md): 가용 영역과 리전, 프로비저닝, 데이터 이중화, 고가용성
- [다중 AZ DB 클러스터](05-multi-az-db-cluster.md): Writer와 Reader 인스턴스, 세 종류의 엔드포인트, 엔드포인트의 추상화

**MySQL**

- [MySQL: 프로그래머스 Lv. 2 풀이](06-mysql-programmers-lv2.md): SQL 연습 문제 11개의 풀이 과정. NULL 처리, 날짜 형 변환, 이름 검색, 개수 세기
- [mysqldump 사용 시 주의사항](07-mysqldump-notes.md): MySQL과 mysqldump를 쓸 때 주의할 점, WSL의 Ubuntu에서 MySQL에 로그인하고 원격 접속하는 명령

RDS와 mysqldump 글을 쓰게 된 작업은 프로젝트 글 [대용량 DB 백업 자동화](../../projects/server/06-db-backup-automation.md)에 정리했습니다.
