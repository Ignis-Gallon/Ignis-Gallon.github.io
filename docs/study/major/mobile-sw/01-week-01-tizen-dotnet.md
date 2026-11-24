---
title: "1주차: Tizen 개요, .NET 플랫폼"
date: 2024-03-04
last_modified_at: 2024-03-11
category: major
tags: [모바일SW, Tizen, .NET, C#, Xamarin]
---

# 1주차: Tizen 개요, .NET 플랫폼

## &lt;수업(2024-03-04 월)&gt;

### 개요

타이젠(*Tizen*) OS를 사용하는 프로그래밍을 다루며, Base가 되는 것은 Xamarin(*Markup language*)과 C#이다. Tool을 설치하는 과정 자체가 매우 복잡하다.

- **수업**

    - Xamarin과 C#은 마이크로소프트에서 밀고 있는 언어
    - Xamarin을 이용해 UI를 만들고, C#을 이용해서 그 UI를 제어한다. 타이젠이 깔려있는 삼성 스마트 TV에서 해당 프로그램을 돌려볼 수 있을 것이다.
    - 가전 사업에서는 타이젠을 아주 밀고 있고, On Device App(Edge Device)을 밀고 있기 때문에, 이후에는 삼성의 모든 제품들이 스마트 가전제어가 될 것이다. (*Smart Things*)  
        ⇒ 무선 사업부는 안드로이드, 가전은 타이젠의 흐름.
    - Xamarin과 C#으로 안드로이드/iOS/윈도우즈 어플도 동시에 개발하는 것이 가능하다.

    > 이번 수업의 가장 큰 목표는 Multi-language를 사용하여 프로그램을 만들어보는 경험을 가지는 것이다.

- **수업 환경**

    - Visual Studio/Xamarin/Android, iOS, Windows/Tizen/C#/OOP

- **Xamarin**

    - Xamarin은 Layer를 이용하므로, Expand/Shrink가 자유롭다. 따라서 UI에 대한 오류는 거의 발생하지 않는다.

- **향후 진로**

    - 임베디드 SW 엔지니어
    - Application 개발자
    - 웹 개발자
    - AI 개발자(Core는 연구이고, 서비스로 넘어가려면 Web/App으로 넘어가기도 함.)

> AI 관련 분야가 대두되고 있는 중. 대부분 웹서비스 개발이지만, Native App도 가능하다.  
> ⇒ 코어 기술 연구개발, 응용 서비스 개발, 응용 서비스용 활용 기술 개발, 모델 개발, 앱/웹/서비스 등

- **개발 Tool**

    - Visual Studio 2019 (XAML과 C#을 컴파일하고 결합)
    - Tizen SDK
    - Tizen Emulator (PC에서 Tizen 프로그램을 띄워주는 것)

---

## &lt;수업(2024-03-05 화)&gt;

### Tizen Overview

- Linux 기반의 운영체제로, 삼성전자, 인텔이 공동으로 개발한 오픈 소스 모바일 운영체제이다.
- Native API는 C 언어를 이용한 임베디드 시스템 소프트웨어로 개발하기 유용하다.
- Web API는 HTML5, CSS, Javascript를 사용하여 간단한 애플리케이션을 만들 수 있도록 함.
- 소프트웨어 개발 키트(SDK)를 통해 각종 tool과 API를 제공

### Tizen Application Types

- Native Application(C#)

    - Native Application은 Native API를 사용하며, Linux C를 기반으로 메모리와 성능의 이점.

- Web Application

    - Web Application을 만들 때는 Web API를 사용.
    - Javascript 언어 기반의 디바이스 접근을 위한 Tizen Device API 제공.

- Hybrid Application(Xamarin + C#)

- Tizen .NET Application

    - Framework. 기본적으로 .NET 위주로 돌아감.

    - 따라서 .NET Core를 설치하며, 다음과 같은 네 가지의 프로그램 개발 환경을 사용함.

        - **NET Standard API**
        - **Xamarin.Forms**
        - **Tizen.Wearable.CircularUI**
        - **TizenFX API**

> Tizen 프로그램을 빌드하면 .tpk 파일이 만들어지고, 이를 디바이스로 보내서 설치가 가능함.

### C#의 특징

- 형식 안정적인 객체 지향 언어
- 기존 프로그래밍 언어의 생산성을 개선하여 성능이 굉장히 높음.
- 윈도우, 맥, 리눅스, 안드로이드, 아이폰 등의 플랫폼에서 모두 동작 가능.

### .NET Platform

- 마이크로소프트가 만든 중간 모듈로서, 초기에는 윈도우에서만 동작했지만, 현재는 마이크로소프트가 활용할 수 있는 프로그래밍 언어를 연결 가능
- C#은 모노 플랫폼이라는 소프트웨어 플랫폼 위에서도 동작한다.

- C#의 기타 활용

    - 게임 프레임워크(게임 엔진): 유니티
    - 모바일 응용 프로그램 프레임워크: Xamarin

### 라이브러리 vs 프레임워크

- **라이브러리**는 이미 만들어져 있는 함수를 사용자가 가져다가 사용하는 것을 지원하는 모듈

- 프레임워크는 제어 역전(Inversion of Control)이 있는 대규모의 라이브러리

    - 프로그램의 초기화부터 종료까지의 흐름을 직접 관리
    - 기본 틀을 모두 제공해주어 개발자는 개발에 집중 가능

    - 현재는 “**대규모의 라이브러리 = 프레임워크**”로 많이 혼용되고 있음.

        - .NET 프레임워크는 .NET 플랫폼과 클래스 라이브러리가 합쳐진 하나의 제품 이름이다.

---

### 실제 예시

- TizenXamlApp8 / TizenXamlApp8.Tizen 이런 식으로 솔루션 내에 두 개의 프로젝트가 위치하게 되는데, 항상 아래 것(.Tizen)이 우선이다.

    - 즉, 위의 것을 코딩하면, 아래 것(.Tizen)이 종속적으로 만들어지는 형식임.

- Emulator를 설치하면 IDE 좌측 상단에 Emulator 동작 칸이 생김.
- **Tizen Package Manager**: 설치할 때 사용됨
- **Tizen Emulator Manager**: 애뮬레이터를 선택해서 돌리기 위해 사용됨
