---
title: "RxJava 예제 > Subject Example Code"
date: 2022-09-27
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, RxJava, RxKotlin, Subject]
---

# RxJava 예제 > Subject Example Code

작성일 2022-09-27
{ .post-date }

RxJava와 RxKotlin의 Subject 네 가지(PublishSubject, BehaviorSubject, ReplaySubject, AsyncSubject)를 실습한 예제 코드이다.

```kotlin
import io.reactivex.rxjava3.disposables.CompositeDisposable
import io.reactivex.rxjava3.kotlin.subscribeBy
import io.reactivex.rxjava3.subjects.AsyncSubject
import io.reactivex.rxjava3.subjects.BehaviorSubject
import io.reactivex.rxjava3.subjects.PublishSubject
import io.reactivex.rxjava3.subjects.ReplaySubject

fun main(args:Array<String>) {

    exampleOf("PublishSubject") {
        val subscriptions = CompositeDisposable()
        val publishSubject = PublishSubject.create<Int>()

        publishSubject.onNext(0)

        val subscriptionOne = publishSubject // disposable return
            .subscribe {  // subscribe { }, subscribeBy ()
                println(it)
        }

        subscriptions.add(subscriptionOne)

        publishSubject.onNext(1)
        publishSubject.onNext(2)
        publishSubject.onNext(3)

        val subscriptionTwo = publishSubject
            .subscribe { int ->
                println("2) $int")
            }

        publishSubject.onNext(4)

        subscriptionOne.dispose() // 여기부터 subscriptionOne이 dispose된다.

        publishSubject.onNext(5)
        publishSubject.onComplete()
        publishSubject.onNext(5)

        subscriptionTwo.dispose() // 여기부터 subscriptionTwo가 dispose된다.

        val subscriptionThree = publishSubject.subscribeBy( // subscriptionThree가 publishSubject를 새롭게 구독한다.
            // subscriber의 동작은, subscribe level에서 정의된다... 여기서 subscriptionThree는 disposable type.
            onNext = { println("3) $it") },
            onComplete = { println("3) Complete") }
        )

        publishSubject.onNext(6)
    }

    // Subject
    // 1. PublishSubject
    // 2. BehaviorSubject
    // 3. ReplaySubject
    // 4. AsyncSubject

    // 1
    exampleOf("BehaviorSubject") {
        // 2
        val subscriptions = CompositeDisposable()
        // 3
        val behaviorSubject =
            BehaviorSubject.createDefault("Initial value")

        behaviorSubject.onNext("X")
        // default value를 Initial value로 설정했지만,
        // onNext("X")를 해주었기 때문에,

        val subscriptionOne = behaviorSubject.subscribeBy(
            onNext = { println("1) $it") },
            onError = { println("1) $it") }
        )
        subscriptions.add(subscriptionOne)

        // 1
        behaviorSubject.onError(RuntimeException("Error!"))
// 2
        subscriptions.add(behaviorSubject.subscribeBy(
            onNext = { println("2) $it") },
            onError = { println("2) $it") }
        ))
        // 위와 같이 짜면, subscriptionOne은 onError가 동작됐기 때문에, onError event를 전달받고,
        // subscriptionTwo(실제로 이름으로 지정되지는 않았지만)는 가장 최근에 일어난 event를 전달받기 때문에
        // One과 동일하게 onError를 띄운다.
    }

    exampleOf("BehaviorSubject State") {

        val subscriptions = CompositeDisposable()
        val behaviorSubject = BehaviorSubject.createDefault(0)

        with(behaviorSubject) {
            this.value?.let { println(it) }
        }

        subscriptions.add(behaviorSubject.subscribeBy {
            println("1) $it")
        })

        behaviorSubject.onNext(1)
        behaviorSubject.value?.let {
            println(it)
        }

        subscriptions.dispose()
    }

    exampleOf("ReplaySubject") {

        val subscriptions = CompositeDisposable()
        // 1
        val replaySubject = ReplaySubject.createWithSize<String>(2)
        // 2
        replaySubject.onNext("1")

        replaySubject.onNext("2")

        replaySubject.onNext("3")
        // 3
        subscriptions.add(replaySubject.subscribeBy(
            onNext = { println("1) $it") },
            onError = { println("1) $it") }
        ))

        subscriptions.add(replaySubject.subscribeBy(
            onNext = { println("2) $it") },
            onError = { println("2) $it") }
        ))

        // replaySubjet는 emitted된 item들을 replay하므로,
        // 처음에 만들어질 때 지정된 length만큼 이전에 emit됐던
        // 아이템을 가져온다.

        replaySubject.onNext("4")
        replaySubject.onError(RuntimeException("Error!"))

        subscriptions.add(replaySubject.subscribeBy(
            onNext = { println("3) $it") },
            onError = { println("3) $it") }
        ))
    }

    exampleOf("AsyncSubject") {
        val subscriptions = CompositeDisposable()
        // 1
        val asyncSubject = AsyncSubject.create<Int>()
        // 2
        subscriptions.add(asyncSubject.subscribeBy(
            onNext = { println("1) $it") },
            onComplete = { println("1) Complete") }
        ))
        // 3
        asyncSubject.onNext(0)
        asyncSubject.onNext(1)
        asyncSubject.onNext(2)
        asyncSubject.onNext(3)
        // 4
        asyncSubject.onComplete()
        subscriptions.dispose()
    }

}
```
