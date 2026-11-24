---
title: "객체기반SW설계"
---

# 객체기반SW설계

2학년 2학기에 수강한 과목의 수업 노트입니다. C++로 객체지향 프로그래밍을 다루는 과목으로, 클래스와 생성자, 동적 할당과 복사 생성자, 연산자 중복, 상속과 다형성, 파일 입출력, 예외 처리, template, STL을 주차별로 정리했습니다.

**주차별 노트**

- [1주차: auto](01-week-01-auto.md): string과 bool, type을 자동으로 추론해 주는 auto
- [2주차: cin과 getline](02-week-02-cin-getline.md): cin과 getline의 차이, cin.ignore()로 버퍼 비우기, range based for
- [4주차-1: OOP 특징, 생성자, Getter/Setter](03-week-04-1-oop-constructor.md): 캡슐화·정보 은닉·상속·다형성, 접근 지정자, UML, 생성자와 소멸자, 참조자로 객체 넘기기
- [4주차-2: 객체 배열, Vector, Array, SFML](04-week-04-2-object-array-vector.md): 객체 배열과 auto&, Vector의 선언·메모리 확장·begin/end, sort, 이차원 vector, STL array
- [5주차: SFML 설정, 라이브러리 링크](05-week-05-sfml-library-link.md): include·lib·bin 디렉터리의 역할, Link 과정, Static Library와 Dynamic Library, Debug 모드 설정
- [7주차: nullptr, 동적 할당, Smart Pointer, 복사 생성자](06-week-07-dynamic-allocation-smart-pointer.md): new와 delete를 쓰는 이유, Smart Pointer, 함수로 객체를 넘기고 반환하기, 복사 생성자와 대입 연산자
- [9주차: static 변수와 함수, Singleton](07-week-09-static-singleton.md): static 변수와 static 함수의 제약, Singleton 디자인 패턴, 연산자 오버로딩, 포인터 연산자 중복
- [10주차(1): friend, 연산자 중복, 상속](08-week-10-1-friend-operator-inheritance.md): 접근 지정자와 정보 은닉, friend 함수와 friend 클래스, &lt;&lt;와 &gt;&gt; 연산자 overloading, 상속 규칙
- [10주차(2): overriding, 다형성](09-week-10-2-overriding-polymorphism.md): overloading과 overriding의 차이, 상속 방식, 다중 상속, upcasting과 downcasting, virtual과 dynamic binding
- [Dynamic Cast](10-dynamic-cast.md): 안전한 downcasting을 위한 dynamic_cast의 사용 형식과 조건, 성공·실패 시 반환 값
- [11주차 (1): 순수 가상 함수, 파일 입출력](11-week-11-1-pure-virtual-file-io.md): 다형성 복습, 순수 가상 함수와 추상 클래스, ofstream과 ifstream
- [11주차 (2): 이진 파일, 임의 접근 파일](12-week-11-2-binary-random-access-file.md): 이진 파일 읽고 쓰기, 순차 접근과 임의 접근, seekg와 seekp, 예외 처리와 template 도입
- [12주차 (1): 예외 처리, template](13-week-12-1-exception-template.md): try와 catch, 여러 catch 두기, function template과 class template, STL의 구성, list와 vector 비교
- [12주차 (2): set, map, container adapter](14-week-12-2-set-map-container-adapter.md): set과 map, stack·queue·priority queue 어댑터, algorithm 함수, 람다 함수

**주요 개념 정리 > STL**

- [Vector List](15-stl-vector-list.md): Vector와 List에서 쓸 수 있는 메서드, iterator 순회, 원소 추가·제거·정렬
- [Set Map](16-stl-set-map.md): map을 pair의 vector로 옮겨 정렬하는 방법, map과 set에서 algorithm 함수를 쓰기 어려운 점
