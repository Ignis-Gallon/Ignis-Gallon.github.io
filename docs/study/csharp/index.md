---
title: "C#"
---

# C#

2022년 11월부터 2023년 1월 사이에 C#과 Windows Forms를 공부하면서 정리한 노트입니다. 블로그와 Microsoft 문서의 예제를 따라 실행해 보며 ref/out, Delegate, Thread, 비동기 호출, 레지스트리 API를 적었습니다.

- [ref/out, Delegate/Event](01-ref-out-delegate-event.md): 값 형식을 참조로 넘기는 ref와 out의 차이, Delegate와 Event, Delegate를 쓰는 이유
- [Thread 사용 패턴](02-thread-patterns.md): 스레드 생성, 매개변수 전달, 백그라운드 스레드, 대기, 폴링, 강제 종료
- [Invoke와 BeginInvoke](03-invoke-begininvoke.md): Delegate의 동기 호출과 비동기 호출, EndInvoke와 콜백을 다룬 예제 네 개
- [레지스트리 API](04-registry-api.md): Windows 레지스트리의 개념과 키·값을 만들고 읽는 메서드, XmlTextReader
- [C# 스터디 2022.11.26](05-csharp-study-2022-11-26.md): 콘솔 애플리케이션과 Windows Forms의 차이, Main 함수, 이벤트 핸들러
