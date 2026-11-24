---
title: "프로젝트: 워치 앱 화면 구상"
date: 2024-05-31
last_modified_at: 2024-05-31
category: major
tags: [모바일SW, Xamarin.Forms, 워치 앱, 프로젝트]
---

# 프로젝트: 워치 앱 화면 구상

작성일 2024-05-31
{ .post-date }

**참고**

- **동그란 이미지**

    [Xamarin Forms #24: How to create Circle Image](https://www.youtube.com/watch?v=g3inKo8fbcI&list=PL0CKdD7TV8pG0kNyfWO5LFqeU6J6Zj_49&index=24)

- **스위치**

    [Xamarin Forms #40: Switch](https://www.youtube.com/watch?v=XU2mSuIpXfw&list=PL0CKdD7TV8pG0kNyfWO5LFqeU6J6Zj_49&index=40)

- **SearchBar**

    [Xamarin Forms #31 ListView (Search Bar)](https://www.youtube.com/watch?v=QHB6qtYEKzM&list=PL0CKdD7TV8pG0kNyfWO5LFqeU6J6Zj_49&index=31)

- **Carousel Page**

    [Xamarin Forms #34 Navigation: Carousel Page](https://www.youtube.com/watch?v=FWt-7MrFPlA&list=PL0CKdD7TV8pG0kNyfWO5LFqeU6J6Zj_49&index=34)

- **Editor(숫자 입력)**
- **TapRecognizer**

---

## &lt;방향성&gt;

1. StackLayout을 사용하여 최하단에 워치 이미지를 띄운다.
2. 워치 이미지와 동일한 사이즈로 내부에 들어갈 이미지를 준비한다.  
    이때, 반드시 투명 배경으로 준비한다.

    1. Watch Page

        1. 디지털 시계: 중앙 할아버지, 알람 아이콘, 음표 아이콘, 배터리 아이콘

            > 디지털 시계를 구현할 때는 시간 표시는 모두 제거하기  
            > 할아버지, 알람 아이콘, 음표 아이콘, 배터리 아이콘은 별도로 배치하기  
            > ⇒ 할아버지, 알람 아이콘, 음표 아이콘은 StackLayout 활용. 배터리는 AbsoluteLayout으로 할아버지 내부로 넣기  
            > 시간 표현은 AbsoluteLayout으로 위치 지정.

        2. 아날로그 시계: DoubleTap으로 디지털/아날로그 전환(만약 안되면 Convert Button 추가)

            > 아날로그 시계에서는 시간 표현 추가하기. 이때 숫자는 복잡하므로 넣지 말고, 사전에 준비된 선 이미지로 적용. (안그럼 복잡해짐…)  
            > 또한 콧수염은 제거하고, 단순한 시침 분침으로 변환. (콧수염으로 표현 어려움…)

    2. Instant Alarm Page: 현재 시간과 날짜를 표시하고, 알람을 울릴 시간을 설정할 수 있도록 한다. 알람을 울리는 시간은 최대한 editor를 사용해보고, 안되면 +. - 버튼을 사용한다.

        > 인스턴트 알람은 어떤 페이지에 있든지 시간이 되면 울려줄 수 있어야 한다. 그러므로 최상위 클래스에서 처리가 필요할 것으로 생각됨.

    3. Metronom Page: 콧수염 이미지와 막대 이미지가 필요함. Anchor를 걸고 회전하는 기능을 추가할 필요가 있음.

        > BPM을 설정할 수 있어야 함. 가능하다면 Editor 사용, 불가능하다면 + - 버튼을 사용

> 화면 간의 전환은 CarouselView를 사용한다.  
> ⇒ CarouselView를 사용해 구현 中

---

## &lt;참고&gt;

### RotateTo()

RotateTo() 메서드의 각도는 이전 값으로 계속 정의가 남아있다. 따라서 360도를 회전하고 나면 현재 각도는 360도이다. 추가 1바퀴를 위해서는 다음 회전을 720으로 주거나 Image.Rotation = 0으로 초기화 해야 한다.
