---
title: "RxJava 예제 > Observable Example Code"
date: 2022-09-27
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, RxJava, RxKotlin, Observable]
---

# RxJava 예제 > Observable Example Code

RxJava와 RxKotlin의 Observable을 만들고 구독하는 방법(just, fromIterable, empty, never, range, create, defer, Single, dispose)을 실습한 예제 코드이다.

```kotlin
import io.reactivex.rxjava3.core.Observable
import io.reactivex.rxjava3.core.Single
import io.reactivex.rxjava3.disposables.CompositeDisposable
import io.reactivex.rxjava3.kotlin.subscribeBy
import java.io.File
import java.io.FileNotFoundException
import kotlin.math.roundToInt

fun main(args: Array<String>) {

    exampleOf("just") {
//        val observable = Observable.just(1, 2, 3)
        val observable : Observable<List<Int>> = Observable.just(listOf(1))

        // list, set, map
    }

    exampleOf("fromIterable") {
        val observable: Observable<Int> =
            Observable.fromIterable(listOf(1,2,3))
    }

    val sequence = 0 until 3
    val iterator = sequence.iterator()
    while (iterator.hasNext()) {
        println(iterator.next())
    }

    exampleOf("subscribe") {
        val observable = Observable.just(3,2,1) // Observable Single ->

        // subscriber

        observable.subscribe { // ---> Disposable ---> Subscriber ---> Observable이 발행한 데이터를 받는다.
            println(it)
        }
            .dispose()
        observable.subscribe {
            println(it)
        }

    }

    exampleOf("empty") {
        val observable = Observable.empty<Unit>()
        // empty Observable은 element가 없으므로, onNext를 수행하지 않고
        // onComplete만을 실행한다...
        observable.subscribeBy(
            onNext = {
                println(it)
            },
            onComplete = {
                println("Completed!")
            }
        )
    }

    exampleOf("never") {

        val subscriptions = CompositeDisposable()

        val observable = Observable.never<Any>()
            // doOn.. 을 사용하면, side effects를 만들 수 있다.
            // 이는 Observable에 영향을 미치지 않는다.
            .doOnSubscribe {
                println("subscribed never observable")
            }
            .doOnDispose {
                println("I'm disposing...")
            }
            .doOnComplete {
                println("I'm completed...")
            }

        val disposable = observable.subscribeBy (
            onNext = { println("Next") },
            onComplete = { println("Completed") }
        )

        subscriptions.add(disposable)
        subscriptions.dispose()
    }

    exampleOf("range") {
        val observable: Observable<Int> = Observable.range(1, 10)

        observable.subscribe {
            val n = it.toDouble()
            val fibonacci = ((Math.pow(1.61803, n) -
                    Math.pow(0.61803, n)) / 2.23606).roundToInt()
            println(fibonacci)
        }
    }

    exampleOf("dispose") {
        val mostPopular: Observable<String> =
            Observable.just("A", "B", "C")

        // 이렇게 함으로써 발행된 disposable을 subscription(local variable)에 저장한다.
        val subscription = mostPopular.subscribe { // onNext
            println(it)
        }
            .dispose() // 구독 관계를 끊는다.
    }

    exampleOf("CompositeDisposable") {
        val subscriptions = CompositeDisposable()

        val observable = Observable.just("A", "B", "C")
        val disposable = observable
            .subscribe {
                println(it)
            } // .dispose()

        subscriptions.add(disposable)

        // 모든 동작을 마쳤다.

        subscriptions.dispose()
    // dispose를 제때 하지 않으면 leak이 발생한다.
        // observable을 많이 쓸 때 하나하나 .dispose해주기가 번거롭다.
    }

    exampleOf("create") {
        val disposables = CompositeDisposable()

        // create을 사용하면, Observable의 stream을 직접 만들어낼 수 있다...
        Observable.create<String> { emitter ->
            // 1
            emitter.onNext("1")
            emitter.onNext("2")
            emitter.onNext("3")

            // error!
            // emitter.onError(RuntimeException("Error"))

            emitter.onComplete()
            emitter.onNext("4")
            // dispose, complete

            // complete -> dispose
            // dispose, complete가 되기 전에 임의로 실행 가능하다.

            // 3
        }
            .subscribeBy (
                onNext = { println(it) },
                onComplete = { println("Completed") },
                onError = { println(it) }
            )
            .dispose()
    }

    exampleOf("defer") { // factory pattern. 조건에 따라서 발행되는 Observable의 데이터가 바뀌는것.

        val disposables = CompositeDisposable()
        // 1
        var flip = false
        // 2
        val factory: Observable<Int> = Observable.defer {
            // defer를 이용해서, condition에 따라 Observable이 다른 elements를 emit하도록 만들 수 있다.
            // 그래서 Observable Factory 라고 부르는 것으로 보인다.
            // 3
            flip = !flip
            // 4
            if (flip) {
                Observable.just(1, 2, 3)
            } else {
                Observable.just(4, 5, 6)
            }
        }

        for (i in 0..3) {
            disposables.add(
                factory.subscribe {
                    println(it)
                }
            )
        }
        // Observable Factory에서 Observable Operator를 이용해 Observable을 만들어내는 과정을 작성하고,
        // 이후에, 사전에 만들어 놓은 CompositeDisposable에 factory.subscribe {} (onNext) 로 만들어낸 disposable을
        // add 해준다.

        disposables.dispose()
    }

    // Observable의 형태 -> Single, Maybe, Completable
    // 1. onNext , onSuccess
    // 2. onError
    // 3. onComplete

    exampleOf("Single") {
        val subscriptions = CompositeDisposable()

        fun loadText(filename: String) : Single<String>  {
            return Single.create create@{ emitter ->

                // 1
                val file = File(filename)
// 2
                if (!file.exists()) { // file이 존재하지 않는다면. onError. create는 Observable의 동작 구현 가능.
                    emitter.onError(FileNotFoundException("Can’t find $filename"))
                    return@create
                }
// 3
                // Single은 onSuccess와 onError만 존재하는데,
                // 여기서 읽어온 ex File을 contents 변수에 저장하고
                // emitter.onSuccess(contents)로 넘겨주게 된다...
                val contents = file.readText(Charsets.UTF_8)

                emitter.onSuccess(contents)
            }
        }

        val observer = loadText("ex.txt") // Disposable ---> 구독자
            .subscribeBy (
                    onSuccess = {println(it)},
                    onError = { println("Error, $it") }
            )

        subscriptions.add(observer)
    }
}

fun exampleOf(description: String, action: () -> Unit) {
    println("\n--- Example of : $description ---")
    action()
}
```
