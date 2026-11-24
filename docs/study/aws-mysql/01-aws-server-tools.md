---
title: "AWS(서버 운영 도구 메모)"
date: 2023-02-28
last_modified_at: 2023-02-28
category: study
tags: [AWS, WinSCP, PuTTY, pm2]
---

# AWS(서버 운영 도구 메모)

작성일 2023-02-28
{ .post-date }

- WinSCP : 로컬 호스트(서버로 돌아가는 PC)에 원격으로 접속할 수 있는 프로그램
- Putty : WinSCP에서 terminal을 열어주는 프로그램

---

- pm2 list : 현재 실행되고 있는 프로세스를 확인하는 명령어
- pm2 start (svr.js name) : 서버 시작
- pm2 stop (svr.js name) : 서버 중단
- pm2 delete (svr.js name) : 서버 중단, 목록에서 지워버림
- pm2 monit : 현재 동작하고 있는 서버의 상태를 모니터링 할 수 있음

사용하고 있는 포트가 1000번 이하라면 pm2 명령어 앞에 sudo를 붙여야 제대로 동작한다.
