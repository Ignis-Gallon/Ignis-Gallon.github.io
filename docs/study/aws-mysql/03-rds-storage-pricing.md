---
title: "RDS(스토리지 확인, 온디맨드와 예약형)"
date: 2024-01-07
last_modified_at: 2024-01-07
category: study
tags: [AWS, RDS, 스토리지, 요금]
---

# RDS(스토리지 확인, 온디맨드와 예약형)

작성일 2024-01-07
{ .post-date }

## &lt;현재 스토리지 확인&gt;

[[RDS] 스토리지 사용률 확인](https://passwd.tistory.com/entry/RDS-스토리지-사용률-확인)

AWS 콘솔에서는 기본적으로 RDS 스토리지 사용량 또는 사용률에 대한 지표를 제공하지 않는다. 대신 여유 스토리지 공간 (MB/초) 지표를 제공한다.

따라서 총 스토리지 용량과 여유 스토리지 공간 용량을 이용해, 사용량 및 사용률을 별도로 계산해야 한다.

---

## &lt;RDS 관련 정보&gt;

[Amazon RDS for MySQL 요금 – Amazon Web Services](https://aws.amazon.com/ko/rds/mysql/pricing/?pg=pr&loc=2&refid=fa2d6ba3-df80-4d24-a453-bf30ad163af9)

### 온디맨드 DB 인스턴스 vs 예약형 DB 인스턴스

[온디맨드 인스턴스 대 예약 인스턴스 - 인스턴스 유형 비교 - AWS](https://aws.amazon.com/ko/compare/the-difference-between-on-demand-instances-and-reserved-instances/)

[Aurora용 온디맨드 DB 인스턴스 - Amazon Aurora](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/USER_OnDemandDBInstances.html)

- **온디맨드 인스턴스와 예약형 인스턴스의 차이점**

**온디맨드 인스턴스**와 **예약형 인스턴스**는 AWS 클라우드에서 프로비저닝할 수 있는 두 가지 요금 모델이며, 기능적으로는 두 유형이 동일하다. 단, 요금만 다르다. **온디맨드 인스턴스**의 경우 장기 약정 없이 컴퓨팅 용량에 대해 시간당 또는 초당(최소 60초) 비용을 지불한다. 사용한 만큼만 지불하면 되고, 워크로드 변화에 따라 인스턴스가 **자동으로 스케일 업** 또는 **스케일 다운**된다(*Auto Scaling*).

반면, **예약형 인스턴스**는 할인된 요금과 용량 예약(선택 사항)을 제공한다. 고정된 기간 동안 예약형 인스턴스를 동급 온디맨드 인스턴스보다 낮은 초당 또는 시간당 요금으로 임대하게 된다.

- **온디맨드 DB 인스턴스**

Amazon RDS 온디맨드 DB 인스턴스는 DB 인스턴스 클래스를 기반으로 청구된다. (*ex. db.t3.small 또는 db.m5.large*)

DB 인스턴스가 사용 가능하면 즉시 DB 인스턴스에 대한 청구가 시작되며, 요금은 시간 단위로 고시되지만, 청구서는 초 단위로 계산되고 시간을 10진수 형식으로 표시한다.

Amazon RDS 사용량은 1초 단위로 청구되며 최소 청구 시간은 10분이다. 컴퓨팅 또는 스토리지 용량 조정과 같은 청구 대상 구성 변경의 경우 최소 시간인 10분에 대해 요금이 부과된다. DB 인스턴스를 삭제하거나 DB 인스턴스에 장애가 발생하여 DB 인스턴스가 종료될 때까지 청구가 계속된다.

DB 인스턴스 요금이 더 이상 부과되지 않도록 하려면 추가 DB 인스턴스 시간에 대해 청구되지 않도록 인스턴스를 중지하거나 삭제해야 한다.

### 다중 AZ DB 인스턴스/클러스터

- [다중 AZ DB 인스턴스](04-multi-az-db-instance.md)
- [다중 AZ DB 클러스터](05-multi-az-db-cluster.md)
