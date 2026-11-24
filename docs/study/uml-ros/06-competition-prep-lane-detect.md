---
title: "대회 준비와 lane_detect.py"
date: 2023-07-02
last_modified_at: 2023-07-04
category: study
tags: [ROS, 자율주행, OpenCV, 차선 인식]
---

# 대회 준비와 lane_detect.py

작성일 2023-07-02 · 최종 수정일 2023-07-04
{ .post-date }

2023-07-02 ~

- 트랙 정보 x
- Issue : 직진에서는 가는데, 코너 부분에서 ROI 영역을 벗어나면서 인식 불가능

---

1. SLAM 이용

    장애물이 없으면 좌표 특정이 힘들기 때문에 goal을 설정하기는 힘들지만 가능하기는 함.  
    그런데 만약 반사 등으로 인해 LIDAR 사용이 안되면 이 방식은 이용할 수 없을 수도 있음.

2. 기존의 코드에서 곡선 검출이 안되던 것을 해결

    ROI 등을 수정하여 회전하여 수정하거나 기존 코드를 활용하여 주행. 후자는 어느 정도 적당한 수준으로 설정되어 있어야 함.

3. 하드 코딩 방식

    직진, 코너링 등을 할 수 있는 메소드를 생성, 현장에서 파라미터를 조절하며 주행을 완수할 수 있도록 만든다. 

4. 젯봇 써보기

---

[경진대회 수업 자료](07-class-materials.md)

---

## lane_detect.py

### Parameter Server

A parameter server is a shared, multi-variate dictionary that is accessible via network APIs. 

Nodes use this server to store and retrieve parameters at runtime.

⇒ 런타임 중에 parameter 값을 저장하고 검색할 수 있도록 해주는 역할. network API를 사용하여 통신이 가능하다. 일종의 shared preferences의 역할을 한다고 보임.

따라서 실행 중에 필요한 여러 parameter들을 수정하고 이용할 수 있음.

- 사용가능한 parameter types

    - 32-bit integers
    - booleans
    - strings
    - doubles
    - iso8601 dates
    - lists
    - base64-encoded binary data

- private parameters

The ROS naming convention refers to ~name as a **private** name. These private names primarily are for parameters specific to a single Node. ⇒ 이름 충돌을 막기 위해 주로 사용됨.

### Parameters

The ROS Parameter Server can store strings, integers, floats, booleans, lists, dictionaries, iso8601 dates, and base64-encoded data. Dictionaries must have string keys.

- Getting Parameters

Fetch value from the parameter server. you can optionally pass in a default value to use if the parameter is not set. Names are resolved relative to the node’s namespace. if you use get_param() to fetch a namespace, a dictionary is returned with the keys equal to the parameter values in that namespace. 

⇒ KeyError is raised if the parameter is not set.

```python
global_name = rospy.get_param("/global_name") # 절대경로
relative_name = rospy.get_param("relative_name") # 상대경로
private_param = rospy.get_param('~private_name') # private value
default_param = rospy.get_param('default_param', 'default_value')

# fetch a group (dictionary) of parameters
gains = rospy.get_param('gains')
p, i, d = gains['p'], gains['i'], gains['d']  
```

- Parameter existence

```python
if rospy.has_param('to_delete'):
	rospy.delete_param('to_delete')
```

---

- Server

The ‘Server’ class from the ‘dynamic_reconfigure.server’ module is used to create a dynamic reconfigure server. It takes two arguments: the configuration class and the callback function.

The callback function ‘reconfigure_callback’ is called whenever a parameter is changed through the dynamic reconfigure server. It receives the updated configuration as an argument, allowing you to perform any necessary actions or updates based on the new parameter values.

⇒ 즉, callback function은 dynamic_reconfigure server에 대하여 어떠한 parameter의 value가 수정될 경우, 그 값을 argument로 전달받고 정해진 역할을 수행하는 함수이다.

---

### cv2

- cv2.moments(_img)

모멘트(moments)는 영상의 형태를 표현하는 일련의 실수 값이다.

OpenCV의 cv2.moments() 함수는 이미지 모멘트를 계산하고, 이를 사전형 자료에 담아 리턴한다. 모멘트의 종류는 세 가지이며, 아래와 같이 총 24개의 값을 가진다.

**공간 모멘트(Spatial Moments)**

m00, m10, m01, m20, m11, m02, m30, m21, m12, m03

**중심 모멘트(Central Moments)**

mu20, mu11, mu02, mu30, mu21, mu12, mu03

**평준화된 중심 모멘트(Central Normalized Moments)**

nu20, nu11, nu02, nu30, nu21, nu03

cv2.moments() 함수의 인자는 1개로, 1xN 또는 Nx1 크기의 Numpy Array이다.

cv2.findContours() 함수는 이미지에서 contour를 찾은 후, 리스트형 자료에 담아 리턴한다.

하나의 Contour는 1xN 크기의 Numpy Array이다.

⇒ 따라서 cv2.moments() 함수는 다음과 같이 활용할 수 있다.

- cv2.findContours() 함수로 이미지 contour들을 찾음
- 찾은 contour에서 이미지 모멘트를 구하고자 하는 contour 1개를 정함
- 이 contour를 cv2.moments() 함수의 인자로 전달하여 이미지 모멘트를 구함

여기서 m00은 폐곡선의 면적을 뜻함.

---

### HSV, HLS

- HLS(Hue, Lightness, Saturation)

    - 채도(Hue) : 지정된 색상에 회색이 얼마나 포함될 것인가를 결정하는 수치. 이 값은 퍼센티지(%)의 성격을 띄며, 그 범위는 0~100이다. 값이 작을수록 회색의 반영 비율이 높아지며, 결국 색상 자체가 흐릿해진다.

    - 명도(Lightness) : HSV 체계에서는 명도가 0이면 완전한 검정색이 되고, 100이면 그 색상이 원래의 밝기 그대로 나타나게 된다. 하지만 HLS 체계에서는 명도가 100이면 그 색상의 원래 밝기로 나타나는 것이 아니라 흰색(White)으로 나타나게 된다.

        즉, HSV 체계에서는 명도가 0이면 완전한 검정색이 되고 100이면 그 색상이 원래의 밝기 그대로 나타나는 개념인 것과 달리 HLS 체계에서는 명도가 100이면 그 색상의 원래 밝기로 나타나는 것이 아니라 흰색(White)이 나타나게 된다. 즉, HLS 체계에서는 가장 밝은(명도가 가장 높은) 값이 흰색이 된다는 차이점이 있다.
