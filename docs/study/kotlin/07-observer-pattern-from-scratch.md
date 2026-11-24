---
title: "Observer Pattern from Scratch"
date: 2022-09-27
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, 디자인 패턴, Observer Pattern]
---

# Observer Pattern from Scratch

Kotlin에서 옵저버 패턴을 구현하려면 `Observer`와 `Observable` 인터페이스를 정의하고, 옵저버들이 옵저버블의 상태 변화를 감지하도록 할 수 있다. 아래는 간단한 예시이다.

## 1. 옵저버와 옵저버블 인터페이스 정의

```kotlin
// 옵저버 인터페이스
interface Observer {
    fun update(newState: String)
}

// 옵저버블 인터페이스
interface Observable {
    fun addObserver(observer: Observer)
    fun removeObserver(observer: Observer)
    fun notifyObservers()
}
```

## 2. 옵저버블 구현 클래스

```kotlin
class ConcreteObservable : Observable {
    private val observers = mutableListOf<Observer>()
    private var state: String = ""

    override fun addObserver(observer: Observer) {
        observers.add(observer)
    }

    override fun removeObserver(observer: Observer) {
        observers.remove(observer)
    }

    override fun notifyObservers() {
        for (observer in observers) {
            observer.update(state)
        }
    }

    fun setState(newState: String) {
        state = newState
        notifyObservers()
    }
}
```

## 3. 옵저버 구현 클래스

```kotlin
class ConcreteObserver(private val name: String) : Observer {
    override fun update(newState: String) {
        println("Observer $name: 상태가 '$newState'로 변경됨")
    }
}
```

## 4. 사용 예시

```kotlin
fun main() {
    val observable = ConcreteObservable()

    val observer1 = ConcreteObserver("A")
    val observer2 = ConcreteObserver("B")

    observable.addObserver(observer1)
    observable.addObserver(observer2)

    observable.setState("상태 1")
    observable.setState("상태 2")
}
```

위 코드에서는 `ConcreteObservable`이 상태를 관리하며, 상태가 변경될 때마다 등록된 옵저버들에게 알린다. `ConcreteObserver`는 상태 변경을 알리는 역할을 한다. 옵저버 패턴을 통해 객체 간의 의존성을 최소화하면서도 상태 변화를 적절하게 알릴 수 있다.
