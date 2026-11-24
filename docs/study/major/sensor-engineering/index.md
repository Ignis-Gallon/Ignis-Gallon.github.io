---
title: "센서공학"
---

# 센서공학

3학년 1학기에 수강한 과목입니다. 앞부분에서는 센서의 기본 특성과 광센서, 자기센서, 초음파센서를 배우고, 뒷부분에서는 자율주행자동차에 쓰이는 GPS, LIDAR, SONAR와 ROS, Localization, Stanley 경로 추종, 차선 검출을 다룹니다. 주차별 수업 노트와 과제를 하며 정리한 메모를 묶었습니다.

- [1주차(센서의 기본)](01-week01-sensor-basics.md): 강의 도입(4차 산업 혁명과 AI), 센서의 정의와 구조, 감도·직선성·히스테리시스 같은 정특성과 동특성
- [2주차(광센서/자기센서/위치-변위센서)](02-week02-optical-magnetic-position-sensors.md): 광도전 셀과 포토다이오드, 트랜지스터 응용 회로, 홀 센서, 포텐쇼미터와 로터리 인코더
- [3주차(초음파센서)](03-week03-ultrasonic-sensor.md): 음파의 주파수 구분, 데시벨과 음압 레벨, 압전 현상, 소나, SAW 센서, IMU
- [4주차(자율주행자동차 기본)](04-week04-autonomous-vehicle-basics.md): 자동차에서 모빌리티로 바뀌는 흐름, ADAS, 자율주행자동차의 기능과 자율주행 레벨 0 ~ 5
- [5주차(자율주행자동차의 기본-2)](05-week05-autonomous-vehicle-basics-2.md): 자율주행 기술 동향, 센서 구성과 V2X, 인지·판단·제어, LIDAR 기반과 Camera 기반 자율주행, 전기차 플랫폼
- [6주차-1(GPS)](06-week06-1-gps.md): GPS의 구성과 측위 원리, GNSS, 단독 측위·DGPS·RTK, GPS 오차
- [6주차-2(LIDAR)](07-week06-2-lidar.md): Virtual Lidar, LIDAR의 동작 원리와 제품별 사양, FOV와 Angular Resolution, TOF
- [7주차-1 (LIDAR 실습)](08-week07-1-lidar-practice.md): One layer LIDAR 데이터를 회전 변환으로 그리는 실습 1, 16채널 LIDAR로 3차원 Map을 만드는 실습 2
- [7주차-2 (SONAR TOF)](09-week07-2-sonar-tof.md): 충돌 시간(ttc) 식, 움직이는 차량과 벽 사이의 초음파 송수신 시간, Radar와 센서별 특성 비교
- [과제1](10-assignment1.md): 아두이노 실습 과제를 하며 찾아본 volatile 키워드, 풀업·풀다운 저항, ADC 값 환산
- [9주차(ROS)](11-week09-ros.md): ROS의 개념과 Node·Topic·Service, GPS와 LIDAR 드라이버 실행, rosbag, 차량의 치수와 구동 방식, MDPS
- [10주차(Driving Models)](12-week10-driving-models.md): EPS의 C 타입과 R 타입, Understeer와 Oversteer, ESP, Ackerman Geometry, 후륜의 이탈궤적, SLAM
- [11주차(Odometric Localization)](13-week11-odometric-localization.md): Map Matching과 Loop Closure, Dead Reckoning, 속도와 각속도로 위치를 추정하는 식, 실습 메모
- [12주차(Dead-Reckoning & Stanley)](14-week12-dead-reckoning-stanley.md): Euler·Runge-Kutta·Exact 방식의 차이, Stanley 알고리즘의 yaw error와 lateral distance error, 시뮬레이션 코드
- [과제3](15-assignment3.md): GPS 데이터와 Point Cloud의 Time Stamp가 맞지 않는 문제를 적은 짧은 메모
- [13주차(Lane Line Detection)](16-week13-lane-line-detection.md): LDWS와 LKAS, Canny Edge Detection의 네 단계(Gaussian Filter, Sobel Mask, Non-maximum Suppression, Hysteresis Thresholding)
- [14주차 (Hough Transform)](17-week14-hough-transform.md): Hough Domain에서 직선을 나타내는 방법, 예제, MATLAB의 hough·houghpeaks·houghlines로 차선 고르기
