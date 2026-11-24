---
title: "5주차: SFML 설정, 라이브러리 링크"
date: 2022-10-05
last_modified_at: 2022-10-27
category: major
tags: [객체기반SW설계, C++, SFML, 라이브러리, DLL]
---

# 5주차: SFML 설정, 라이브러리 링크

## `SFML`

개발할 때는 include directory와 lib directory가 중요하다.

- `include`

    C++의 header file은 확장자가 .cpp SFML 프로그래밍에 필요한 header file들이 정의되어 있다. header file은 함수의 프로토타입(function declaration)과 class definition의 모음이다. 즉, 함수와 클래스가 어떻게 생겼는지를 제공한다.

- `lib`

    실제 프로그래밍 할 때 사용하는 모든 함수들은 library 안에 들어가 있지만, 이는 기업의 지적재산권이므로, 소스 코드가 공개되지 않는다. 즉, 이 소스 코드를 compile해서 binary code로 묶어놓은 것이 library이다. 즉, 함수의 실제 구현체를 제공한다. 

    → 내가 작성한 코드와 라이브러리를 묶는 과정을 Link라고 한다. 링크를 거치면 .exe 생성.  

> 똑같은 라이브러리를 여러 곳에서 Link하면, 메모리 낭비가 발생한다. 또한, 라이브러리가 버전 업이 된다면 각각의 실행파일에 Link된 Library를 모두 바꿔주기가 힘들다. 이를 해결하기 위해서 Static Library대신 Dynamically Linked Library를 이용한다. (DLL은 Windows 개념)  
>
> SFML Library는 컴퓨터에 한 copy만 존재하며, 각각의 프로그램에 Static Library 대신 DLL을 이용하여 실제 Library를 필요할 때마다 불러오면 된다.  
>
> 프로그램이 시작되면 DLL이 있는 곳으로 가서 SFML Library를 메모리에 올려준다.

- `bin(binary)`  

    Dynamically Linked Library, 남들이 짜놓은 프로그램을 Library라고 한다.  
    즉, Library가 내 프로그램에 Link되는 것이다. Dynamically는 동적이라는 의미이며, 이를 이해하기 위해서는 반대의 개념을 잘 알아야 한다.

> 1. Static : 내 프로그램에 정적으로 달라붙어서 한 몸이 되는 것이다.
> 2. Dynamic : 다른 사람의 프로그램에 대한 포인터를 프로그램에 포함시켜서, 필요할 때마다 포인터로 라이브러리를 링크하여 공유(Share)할 수 있는 방식의 Library Link.  
>     → Memory 관점에서 이점을 얻을 수 있다.
>
> Static Library를 이용하면, 파일을 옮겨줄 때 그냥 하나의 프로그램만 이동해주면 된다는 장점이 있다.

- `Configuration`

    Debug mode : 개발 시에 사용하는 모드

    Release mode : 배포 시에 사용하는 모드

```cpp
#include <SFML/Graphics.hpp>

using namespace sf;

int main() {

	// SFML은 대부분 windows 정의부터 시작하며, sf:: 는 namespace를 의미한다.
	// VideoMode( , )는 내가 만든 게임의 화면 크기이다. (가로, 세로)
	// RenderWindow class의 Constructor를 호출한다.
	// VideoMode 또한 Class이다.
	// 화면에 그려주는 것을 Render라고 하며, 게임 화면을 그려주는 창이라는 뜻으로 RenderWindow를 사용한다.
	RenderWindow window(VideoMode(200, 200), "First Example");

	// Constructor에서 하나의 argument를 받는데, 이는 반지름이다.
	CircleShape shape(100.0f);

	// 원을 그리는데, 원이 색깔로 꽉 채워진 원이라는 뜻이다.
	shape.setFillColor(Color::Green);

	// 아두이노와 개발하는 방식이 비슷한데, 아두이노는 setup이 끝나면 infinite loop을 돈다.
	// 게임 프로그래밍도 마찬가지로 infinite loop을 만들어서 동작을 구현한다.

	while (window.isOpen()) {

		Event e; // event란, 마우스를 움직이거나 창을 건드리는 등 사람이 조작하는 행위를 전부 Event라고 본다.

		// click event, drag event 등등... 다양한 event가 존재한다.
		// window.pollEvent()는 사용자가 event를 발생시켰는지 조사한다는 뜻이다.
		while (window.pollEvent(e)) {
			if (e.type == Event::Closed) {
				window.close();
			}
		}

		window.clear();

		// draw(object) 한다고 그림이 바로 그려지지 않는다. draw를 하면 그래픽 카드 메모리에 객체가 로딩되고,
		// display()를 실행하면 그림이 그려진다.
		window.draw(shape);
		window.display();

	}

	return 0;
}
```

- Project Properties에서 C/C++ 의 일반(general)에서 경로를 SFML 폴더의 include로 지정해준다.
- 이 상태에서는 LNK error. 즉, Link error가 발생한다. 라이브러리 위치를 지정해준다. Project Properties에 들어가서 Linker의 General에서 추가 SFML lib 디렉터리를 지정해준다. 단, 이렇게만 해주면 어떤 라이브러리와 Link 해야되는지를 알지 못하므로, 경로를 지정해준다. Properties에 들어가서 Linker - Input으로 간다. 여기서 dependencies라는 용어가 나오는데, 이는 굉장히 범용적으로 사용되는 용어이다. (외부 라이브러리를 사용한다는 뜻)
- 이제, 사용할 라이브러리 명을 입력해주는데, Debug Mode이므로, -d 라이브러리를 사용한다.

sfml-graphics-d.lib  
sfml-window-d.lib  
sfml-system-d.lib

→ release mode library를 이용해도 돌아는 가지만, 프로그램이 죽는다.

여기까지 하면 빌드는 되어야 한다.

- Windows(OS)가 .dll을 찾는 경로는 어디일까요? 기본에 없으면 path를 지정해주어야한다.

일반-디버깅-환경 : visual studio 돌릴 때만 환경변수를 바꿔주고 싶다. 이러면 visual studio에서 환경 변수를 bin 경로로 설정해주면된다. Merge Environment를 No로 설정한다. Yes로 하면 시스템이 통합된다. 우리는 visual studio를 실행할 때만 경로 설정을 바꿀 것이기 때문에 Merge System을 No로 설정해주어야 한다. Working Directory는 홈 디렉토리가 어디인지 알려준다. 이것은 중요한데, 게임은 실행되면 오디오 파일이나 이미지 파일들을 다 읽어오는데, 그 경로를 알려주는 것이 Working Directory이다. 원하는 경로로 설정한다. (나는 repos에서 SFML 최상위 폴더로 지정해주었다.)

이제 실행 될 것이다.
