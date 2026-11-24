---
title: "mysqldump 사용 시 주의사항"
date: 2023-12-18
last_modified_at: 2023-12-31
category: study
tags: [MySQL, mysqldump, WSL]
---

# mysqldump 사용 시 주의사항

> 🌟 MySQL 및 mysqldump 사용 시 주의사항 🌟  
>   
> 1) MySQL에서 table_rows, table_capacity 등은 옵티마이징 과정에서의 추측값으로, 정확하지 않을 수 있음. 정확히 알아내려면 직접 Query를 작성해서 확인해야 함.  
>   
> 2) MySQL에서 Import 할 때, .sql 파일의 이름이 숫자로 시작하면 상위 버젼에서 동작 불가능  
>   
> 3) Local Host 주소가 DHCP를 사용해서 바뀔 수 있음

## WSL 및 MySQL Command

- mysql ubuntu 로그인 방법

```bash
sudo mysql -u root
```

⇒ mysql 설치 후 root로 로그인

- mysql ubuntu 원격 접속

```bash
mysql -u USER_NAME -p SCHEMA_NAME -h DB_IP -P PORT_NUMBER
```

⇒ 입력 후에 패스워드 입력 하면 접속됨
