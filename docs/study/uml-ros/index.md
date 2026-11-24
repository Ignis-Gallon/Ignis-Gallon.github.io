---
title: "UML, ROS, 자율주행"
---

# UML, ROS, 자율주행

2023년 6월부터 9월 사이에 쓴 노트입니다. ROS 묶음은 6월의 ROS 교육에서 날마다 적은 기록과, 7월에 자율주행 경진대회를 준비하며 정리한 차선 인식 코드와 수업 내용입니다. 자이트론 교육은 7월 말 자율주행 대회 교육에서 배운 ROS와 주행 실습을 적은 글이고, UML은 9월에 유튜브 강의 두 개를 보고 클래스 다이어그램을 정리한 노트입니다.

**UML**

- [Class Diagram (유튜브 강의 정리)](01-class-diagram-youtube.md): UML의 개념, 클래스의 구성요소(이름, 속성, 오퍼레이션), 가시성 기호, 연관·의존·구현·상속과 aggregation, composition 표기
- [클래스 다이어그램 (널널한 개발자)](02-class-diagram-neolneolhan.md): UML의 필요성, 접근 제어자와 스테레오타입, 클래스 간 관계, 시퀀스 다이어그램, 객체지향 5원칙(SOLID)

**ROS**

- [ROS 3일차](03-ros-day-3.md): Publisher와 Subscriber, rosrun으로 노드 실행하기, catkin_make 뒤 setup 파일을 source하는 이유, zsh 설치 후 생긴 문제, turtlesim 실습
- [ROS 4일차](04-ros-day-4.md): 루프백·포트·IP와 ifconfig, 가상 환경의 bridge 네트워크 설정, ROS_MASTER_URI와 ROS_IP, 로봇 연결과 LIDAR의 /scan 토픽
- [ROS 5일차](05-ros-day-5.md): ranges 배열의 인덱스를 각도로 바꾸기, 장애물의 크기와 위치를 추정해 정지·회피하기, HSV와 Perspective를 이용한 차선 인식
- [대회 준비와 lane_detect.py](06-competition-prep-lane-detect.md): 코너에서 ROI를 벗어나 인식이 안 되던 문제와 주행 방식 구상, ROS Parameter Server, cv2.moments, HSV와 HLS
- [수업 자료](07-class-materials.md): 초음파 센서의 거리 계산식, 가변 저항의 동작 원리, 기어 모터와 모터 드라이버를 아두이노로 제어하기, 실습 과제

**자율주행 대회 관련**

- [자이트론 교육](08-xytron-training.md): ROS의 패키지·노드·토픽과 실행·catkin 명령어, Publisher·Subscriber 예제와 과제, 초음파 센서 주행, OpenCV 영상처리, 카메라 주행
