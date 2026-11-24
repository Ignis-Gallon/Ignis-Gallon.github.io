---
title: "Class"
date: 2022-09-24
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, 클래스, 생성자, 상속]
---

# Class

## `클래스와 생성자`

- 코틀린에서 Class는 다음과 같이 선언하며, 생성자는 아래와 같이 표현할 수 있다.

```kotlin
Class User {
	var name = "정동교"

	constructor(name: String) {
		this.name = name
	}
	fun someFun() {
		println("name : $name")
	}

	class SomeClass {
	}
}
```

- 코틀린 에서는 이와 같이 constructor 키워드를 이용해 생성자를 만들 수 있다. 또한, 클래스 안에 또 다른 클래스를 만들 수 있다.
- 클래스는 객체를 생성해 사용하며 객체로 클래스의 멤버에 접근한다.

```kotlin
val user = User("동교")
user.someFun()
```

클래스 객체를 선언하는 방법

---

## `생성자`

- 코틀린 클래스는 생성자를 `주 생성자`와 `보조 생성자`로 구분한다. 한 클래스 안에 주 생성자만 선언할 수도 있고 보조 생성자만 선언할 수도 있다. 물론, 둘 다 선언할 수도 있다.

→ `주 생성자`

```kotlin
class User constructor() {

}
```

주 생성자는 constructor 키워드로 클래스 선언부에 선언한다. 주 생성자 선언은 필수는 아니며 한 클래스에 하나만 가능하다.

- constructor keyword

```kotlin
class User() {

}
```

주 생성자를 선언할 때 `constructor` 키워드는 생략할 수 있다.

→ `주 생성자의 매개변수`

```kotlin
class User(name: String, count: Int) {

}
```

주 생성자를 선언할 때 필요에 따라 매개변수를 선언할 수도 있다.

- 주 생성자의 본문 - init 영역

```kotlin
class User(name: String, count: Int) {
	init {
		println("I am init")
	}
}
```

`init 영역`은 주 생성자뿐만 아니라 잠시 후에 다루는 보조 생성자로 객체를 생성할 때도 실행된다. 하지만 보조 생성자는 클래스 안에 선언하므로 { }를 이용해 본문을 지정할 수 있다. 따라서 init 영역은 일반적으로 `주 생성자`의 `본문`을 구현하는 용도로 사용한다.  

- init 키워드로 주 생성자의 본문 지정

```kotlin
class User(name: String, count: Int) {
	init {
		println("I am init")
	}
}

fun main() {
	val user = User("정동교", 10)
}

---------------------------------------
// 실행 결과
// I am init
```

User라는 클래스에 주 생성자를 선언하고 클래스 본문에 init 영역을 두어 주 생성자의 본문을 작성했다. 이렇게 하면 main() 함수에서 User 클래스의 객체를 생성할 때 init 영역을 작성한 코드가 자동으로 실행된다. 즉, 주 생성자로 전달받은 매개변수가 있고, 주 생성자의 본문을 작성하는 init 영역을 클래스 내부와 별개로 설정할 수 있는 것이다.

-  생성자의 매개변수를 init 영역에서 사용하는 예

```kotlin
class User(name: String, count: Int) {

	init { // 성공
		println("name : $name, count : $count")
	}

	fun someFun() {
		println("name : $name, count : $count")
	}

}
```

이와 같은 코드는 실행 가능할까? 결론부터 말하면, 아니다. 왜냐면 생성자의 매개변수는 지역변수이므로 다른 함수에서는 사용할 수 없다. 만약 생성자의 매개변수를 클래스의 멤버 변수처럼 다른 함수에서 사용해야 한다면 다음처럼 해야한다.

-  생성자의 매개변수를 다른 함수에서 사용하는 예

```kotlin
class User(name: String, count: Int) {

	var name: String
	var count: Int
	init {
		this.name = name
		this.count = count
	}

	fun someFun() {
		println("name : $name, count : $count")
	}
}

fun main() {
	val user = User("정동교", 10)
	user.someFun()
}

---------------------------------------
// 실행 결과
// name : 정동교, count : 10
```

클래스의 멤버 함수(메서드)에서 생성자의 매개변수를 이용하고자, 클래스의 멤버 변수를 선언하고 주 생성자의 본문인 init 영역에서 매개변숫값을 클래스의 멤버 변수에 대입했다.

- 생성자의 매개변수를 클래스의 멤버 변수로 선언하는 방법

```kotlin
Class User(val name: String, val count: Int) {
	fun someFun() {
		println("name : $name, count : $count")
	}
}

fun main() {
	val user = User("정동교", 10)
	user.someFun()
}
```

원래 함수는 매개변수를 선언할 때 var나 val 키워드를 추가할 수 없다. (함수는 매개변수가 모두 val로 선언된다.) 그런데, 주 생성자에서만 유일하게 var나 val 키워드로 매개변수를 선언할 수 있으며 이렇게 하면 클래스의 멤버 변수(attiribute)가 된다. 따라서 위 코드에서 생성자의 매개변수 name과 count를 someFun()이라는 메서드에서 사용할 수 있다.

→ `보조 생성자`

- 보조 생성자는 클래스의 본문에 constructor 키워드로 선언하는 함수이다. 클래스 본문에 선언하므로, 여러 개를 추가할 수 있다.
- 보조 생성자도 생성자이므로, 객체를 생성할 때 자동으로 호출된다. 그리고 보조 생성자는 클래스 본문에 선언하므로 생성자 본문을 중괄호 { }로 묶어서 객체 생성과 동시에 실행할 영역을 지정할 수 있다. (주 생성자의 init과 마찬가지로)
- 보조 생성자 선언

```kotlin
class User {

	constructor(name: String) {
		println("constructor(name: String) call...")
	}
	constructor(name: String, count: Int) {
		println("constructor(name: String, count: Int) call...")
	}
}

fun main() {
	val user1 = User("정동교")
	val user2 = User("정동교", 10)
}

---------------------------------------
// 실행 결과
// constructor(name: 정동교) call...
// constructor(name: 정동교, count: 10) call...
```

- 보조 생성자에 주 생성자 연결

```kotlin
class User(name: String) {
	constructor (name: String, count: Int) : this(name) {
		// TODO
	}
}

fun main() {
	val user = User("정동교", 10)
}
```

주 생성자가 있을 때 보조 생성자를 만들게 된다면, 보조 생성자에서 주 생성자를 호출해주어야 한다.  
보조 생성자는 객체를 생성할 때 호출되며, 이 때 클래스 내에 주 생성자가 있다면 this() 구문을 이용해 주 생성자를 호출해야 한다.

만약 주 생성자가 있는 상태에서 보조 생성자를 여러 개 선언한다면 보조 생성자에서 this()로 다른 보조 생성자를 호출할 수도 있다. 그런데 이 때에도 보조 생성자로 객체를 생성한다면 어떤 식으로든 주 생성자가 호출되어야 한다.

- 보조 생성자가 여럿일 때 생성자 연결

```kotlin
class User(name: String) {
	constructor(name: String, count: Int) : this(name) {
		// TODO
	}
	constructor(name: String, count: Int, email: String) : this(name, count) {
		// TODO
	}
}

fun main() {
	val user = User("정동교", 10, "user@example.com")
}

// 매개 변수 타입이 맞는 보조 생성자로 클래스가 선언되며, this() 키워드를 통해
// 다중 생성된 보조 생성자를 타고 올라가면서 결국 주 생성자에 다다르게 된다.
```

주 생성자에서는 constructor와 init을 생략하고 프로퍼티를 생성할 수 있는데, 보조 생성자에서는 안되는 것인가? 결론부터 말하자면 안되는 것으로 보인다. 보조 생성자는 프로퍼티를 반드시 생성자에서 지정해주어야 한다. 다음 코드를 예시로 보자.

```kotlin
class E (val name: String) {

    var age: Int = 1
    var height: Int = 2

    init {
        println("call Init Block!")
    }

    constructor(name: String, age: Int) : this(name) {
        this.age = age
        println("call Name, Age Constructor!")
    }

    constructor(name: String, age: Int, height: Int) : this(name, age) {
        this.height = height
        println("call Name, Age, Height Constructor!")
    }
}

fun main() {
    
    val e = E("정동교", 23, 30000)
    
}
```

이 코드의 실행 순서는 `보조 생성자2 → 보조 생성자1 → 주 생성자(init)` 이다. 이 예시처럼 보조 생성자는 반드시 보조 생성자 내에서 프로퍼티를 지정해주어야 한다는 것을 알 수 있다.

- 생성자를 주 생성자와 보조 생성자로 구분하는 이유는 무엇인가요?

    객체를 여러 가지 형태로 생성할 수 있도록 생성자의 매개변수를 다양하게 구성하기 위하여
    보조 생성자가 사용되며, 보조 생성자로 객체를 구성할 때에는 반드시 주 생성자가 실행되게
    만들어야 한다는 규칙이 있다. 이는 생성자의 매개변수를 다양하게 구성할 때 생성자의 공통된
    코드는 주 생성자에 작성하라는 의미로 이해할 수 있다.

---

## `상속`

- `상속`과 `생성자`

    클래스를 선언할 때 다른 클래스를 참조해서 선언하는 것을 `상속`이라고 한다. 코틀린에서는 어떤 클래스를 상속받으려면 선언부에 콜론(:)과 함께 상속받을 클래스 이름을 입력한다. 이렇게 하면 기존 클래스를 재사용할 수 있다.

```kotlin
open class Super { // 상속할 수 있게 open 키워드 이용
}

class Sub : Super() { // Super를 상속받아 Sub 클래스 선언
}
```

- 상속 관계에서 상속 대상이 되는 클래스를 상위 클래스(Super Class)라고 한다.
- 상속받는 클래스를 하위 클래스(Sub Class)라고 한다.
- 만약 다른 클래스에서 상속할 수 있게 클래스를 선언하려면 class 키워드 앞에 open을 붙인다.
- 상위 클래스를 상속받은 하위 클래스의 생성자에서는 상위 클래스의 생성자를 호출해야 한다.

```kotlin
open class Super(name: String) {
}

class Sub(name: String): Super(name) { // Super Class의 Constructor를 호출해준다.
}

// 만약 Super Class에 보조 생성자만 있다면 다음과 같은 방식으로 생성자를 호출할 수 있다.

open class Super(name: String) {
}
class Sub: Super {
	constructor(name: String) : Super(name) {
	}
}
```

- `오버라이딩(overriding)` - `재정의`

    상속이 주는 최고의 이점 : 상위 클래스에 정의된 멤버(변수, 함수)를 하위 클래스에서 자신의 멤버처럼 사용할 수 있다는 것이다.

- 상속 관계인 두 클래스

```kotlin
open class Super {
	var superData = 10
	fun superFun() {
		println("I am superFun : $superData")
	}
}

class Sub: Super()

fun main() {
	val obj = Sub()   // Sub Class 선언
	obj.superData = 20
	obj.superFun()
}
```

- 위 코드에서 Super Class에는 superData라는 변수와 superFun() 이라는 함수를 선언했다. 그리고 Sub Class에는 별도의 멤버를 선언하지 않고 Super Class를 상속받았다. 이렇게 하면 Super Class에 선언한 멤버를 Sub Class가 사용할 수 있다. 따라서 main 함수에서 Sub 클래스의 객체를 생성한 후 이 객체를 이용해 superData와 superFun() 함수를 사용했다.

    → `상속의 이점` , 결국 생산성의 증대!

    그런데, 때로는 상위 클래스에 정의된 멤버를 하위 클래스에서 재정의해야 할 수도 있다.  
    즉, 상위 클래스에 선언된 변수나 함수를 같은 이름으로 하위 클래스에서 다시 선언하는 것이다. 이를 `오버라이딩(overriding)` 이라고 한다.

    `변수`도 `오버라이딩 기법`으로 재정의할 수 있지만, 주로 `함수`를 `재정의`하는 데 사용한다. 함수는 본문의 실행 영역에 특정한 로직을 작성한다. 상위 클래스의 함수가 수행하는 로직을 그대로 이용할 수도 있지만, 같은 함수명으로 하위 클래스에서 새로운 로직을 추가하고 싶을 때 오버라이딩을 사용한다.

- `오버라이딩`의 예

```kotlin
open class Super {
	open var someData = 10
	open fun someFun() {
		println("I am super class function : $someData")
	}
}

class Sub: Super {
	override var someData = 20 // 변수도 override가 가능하다.
	override fun someFun() {
		println("I am sub class function : $someData")
	}
}

fun main() {
	val obj = Sub()
	obj.someFun()
}
```

- 상위 클래스에서 open 키워드를 설정하지 않으면, 오버라이드 할 수 없다.

- `접근 제한자`

    `접근 제한자` 란, 클래스의 멤버를 외부의 어느 범위까지 이용하게 할 것인지를 결정하는 키워드이다. 변수나 함수를 클래스로 묶지 않고 소스 파일의 최상위에 선언할 수 있으며, 이렇게 선언한 변수나 함수도 접근 제한자로 이용 범위를 지정할 수 있다.

| 접근 제한자 | 최상위에서 이용 | 클래스 멤버에서 이용 |
|---|---|---|
| public |  모든 파일에서 가능 | 모든 클래스에서 가능 |
| internal | 같은 모듈 내에서 가능 | 같은 모듈 내에서 가능 |
| protected | 사용 불가 | 상속 관계의 하위 클래스에서만<br>사용 가능 |
| private | 파일 내부에서만 이용 | 클래스 내부에서만 이용 |

- `public` : 접근 제한이 없다. 즉, 원하는 곳 어디든 접근할 수 있다. 코틀린에서는 변수, 함수, 생성자 등을 선언할 때 접근 제한자를 생략하면 public이 기본이다.
- `internal` : 같은 모듈 내에서 접근할 수 있다. 모듈이라는 개념은 그래들이나 메이븐 같은 빌드 도구에서 프로젝트 단위 또는 같은 세트 단위를 가리킨다.
- `protected` : 클래스의 멤버에서만 선언할 수 있고, 최상위에 선언되는 변수나 함수는 protected로 선언할 수 없다. protected로 선언한 클래스의 멤버는 해당 클래스 내부와 그 클래스를 상속받는 하위 클래스에 접근할 수 있다.
- `private` : 해당 클래스 내부에서만 접근할 수 있다. 최상위에서 private 으로 선언하면 해당 파일 내부에서만 접근할 수 있다.

```kotlin
open class Super {
	var publicData = 10
	protected var protectedData = 20
	private var privateData = 30
}

class Sub: Super() {
	fun subFun() {
		publicData++
		protectedData++
		privateData++ // 오류! Super Class에서 private으로 선언되었으므로 참조 불가
	}
}

fun main() {
	val obj = Super()
	obj.publicData++
	obj.protectedData++ // 오류! 
	obj.privateData++ // 오류!
}
```

---

## `클래스의 종류`

- `데이터 클래스`

    데이터 클래스는 data 키워드로 선언하며, 자주 사용하는 데이터를 객체로 묶어준다. 데이터 클래스는 VO(value-object) 클래스를 편리하게 이용할 수 있게 해준다.

- `데이터 클래스 선언`

```kotlin
class NonDataClass(val name: String, val email: String, val age: Int)

data class DataClass(val name: String, val email: String, val age: Int)
```

- `데이터 클래스 객체 생성`

```kotlin
fun main() {
	val non1 = NonDataClass("정동교", "user@example.com", 23)
	val non2 = NonDataClass("정동교", "user@example.com", 23)

	val data1 = DataClass("정동교", "user@example.com", 23)
	val data2 = DataClass("정동교", "user@example.com", 23)
}
```

- `객체의 데이터를 비교하는 equals() 함수`

    VO 클래스는 데이터를 주요하게 다루므로, 객체의 데이터가 서로 같은지 비교할 때가 많다. 객체가 같은지가 아니라 객체의 데이터가 같은지를 비교하는 경우이다. 이 때는 equals() 함수를 사용한다.

```kotlin
println("non data class equals : ${non1.equals(non2)}")
println("data class equals : ${data1.equals(data2)}")

// 실행 결과

non data class equals : false
data class equals : true
```

- `equals() `함수로 일반 클래스의 객체를 비교하면 객체 자체를 비교하므로, 결괏값은 `false`

    → 이를 `Deep Comparison(깊은 비교)` 라고 한다. 기본적으로 primitive type은 equal 여부를 비교할 때 같은 값을 갖고 있는 변수면 같다고 표시하지만, non-primitive type 변수는 가지고 있는 값과 더불어서 가리키는 포인터(주소)까지 같아야만 equal이라고 표시하기 때문에, 서로 같은 Class instance를 선언하였더라도 false가 나오는 것이다.

    → 이와 반대로, 객체의 포인터(주소)는 비교하지 않고 가지고 있는 값만 비교하는 것을
    `Shallow Comparison(얕은 비교)` 라고 한다.

- 하지만 데이터 클래스의 객체를 비교하면, 객체 자체가 아니라 객체의 데이터를 비교하므로 `true` 가 나온다. 데이터 클래스는 데이터를 다루는 데 편리한 기능을 제공하는 것이 주목적이므로, 위의 코드처럼 주 생성자에 val, var 키워드로 매개변수를 선언해 클래스의 멤버 변수로 활용하는 것이 일반적이다.

    → 물론 데이터 클래스의 본문(Body)에도 변수나 함수를 추가할 수 있지만, 객체의 데이터를 비교할 때 이용하는 equals() 함수는 주 생성자에 선언한 멤버 변수의 데이터만 비교 대상으로 삼는다.

```kotlin
data class DataClass(val name: String, val email: String, val age: Int) {
	lateinit var address: String
	
	constructor(name: String, email: String, age: Int, address: String) :
		this(name, email, age) {
			this.address = address
		}
}

fun main() {
	val obj1 = DataClass("정동교", "user@example.com", 23, "Incheon")
	val obj2 = DataClass("정동교", "user@example.com", 23, "Busan")
	println("obj1.equals(obj2) : ${obj1.equals(obj2)}")
}

// 실행 결과

true
```

→ why true? : 두 객체의 일부 멤버 변숫값은 다르지만, 주 생성자에 선언한 멤버 변숫값이 같으면 true이다. 즉, 데이터 클래스의 equals() 함수는 `주 생성자의 멤버 변수가 같은지만 판단한다.`

- `객체의 데이터를 반환하는 toString() 함수`
- 데이터 클래스를 사용하면서 객체가 가지는 값을 확인해야 할 떄 사용된다.

```kotlin
fun main() {
	class NonDataClass(val name: String, val email: String, val age: Int)
	data class DataClass(val name: String, val email: String, val age: Int)
	val non = NonDataClass("정동교", "user@example.com", 23)
	val data = DataClass("정동교", "user@example.com", 23)
	println("non data class toString : ${non.toString()}")
	println("data class toString : ${data.toString()}")

// 실행 결과

non data class toString : com.example.test4.ch2.Test2Kt$main$NonDataClass@61bbe9ba
data class toString : DataClass(name=정동교, email=user@example.com, age=23)
```

일반 클래스로 생성한 객체의` toString()` 함수가 출력하는 값은 의미 있는 데이터가 아니다. 하지만, 데이터 클래스의 `toString()` 함수는 `객체가 포함하는 멤버 변수의 데이터를 출력`한다. 물론, `toString()` 함수 역시 `주 생성자의 매개변수에 선언된 데이터`만 `출력 대상`이다.

- `오브젝트 클래스(Object Class)`

    코틀린에서 오브젝트 클래스는 `익명 클래스(Anonymous Class)` 를 만들 목적으로 사용한다. 클래스에 이름이 없으므로, `선언과 동시에 객체를 생성` 해야 한다. 그렇지 않으면 이후에 객체를 사용할 방법이 없다. (객체에 이름이 없기 때문에 참조가 불가능)

```kotlin
val obj = object {   // object class는 keyword 'object'를 이용하여 선언한다.
	var data = 10

	fun some() {
		println("data : $data")
	}
}

fun main() {
		obj.data = 20 // 오류 !
		obj.some() // 오류!
}
```

- 위의 코드에서 `object` 키워드를 사용해 멤버 변수와 메서드를 포함한 클래스를 선언했다. 이렇게 하면 클래스를 선언하자마자 객체가 생성되며, 그 객체를 `obj 변수에 저장`한다. 그런데,  obj객체로 클래스에 선언한 멤버에 접근하려고 시도하면 `오류`가 발생한다.

그 이유는 `클래스의 타입` 때문이다. object 키워드로 클래스를 선언했지만 타입을 명시하지 않았으므로, (Class의 이름이 존재하지 않으므로) 이 객체는 `코틀린의 최상위 타입`인 `Any`로 취급된다. 그런데 `Any` 타입 객체에는 data, some() 이라는 멤버가 없어서 오류가 발생한다.

→ Custom Class 라면 정의가 되어 있겠지만, 이름을 지정하지 않았기 때문에 obj의 타입이 Any로 지정되는 것이고, 이에 따라 Any에 data, some()이 없어서 오류가 발생한다!

그래서 `object { }` 형태로 `익명 클래스`를 선언할 수는 있지만, 보통은 `타입` 까지 함께 입력해서 선언한다. `오브젝트 클래스`의 `타입`은` object` 뒤에 `콜론`을 입력하고 그 뒤에 `클래스의 상위` 또는 `인터페이스`를 입력한다.

예를 들어, `object: A { }` 형태로 선언하면 클래스를 `A 타입` 으로 선언한 것이다. 만약 `A` 가  `클래스`면,  `A 클래스`를 상속받은 `하위 클래스`를 `익명`으로 선언한 것이며, `A`가 `인터페이스` 면 `A 인터페이스`를 구현한 `익명 클래스`를 `선언`한 것이다. 따라서 `object` 객체를 `A 타입`으로 이용할 수 있다.

```kotlin
open class Super {
	open var data = 10
	open fun some() {
		println("I am super some() : $data")
	}
}

val obj = object : Super() {
	override var data = 20 // Super Class의 멤버 변수를 재정의(override)
	override fun some() {
		println("I am object some() : $data")
	}
}

fun main() {
	obj.data = 30 // 성공
	obj.some() // 성공
}
```

- `컴패니언 클래스(Companion Class)`

    `컴패니언 클래스`는 멤버 변수나 메서드를 클래스 이름으로 접근하고자 할 때 사용한다. 일반적으로 클래스의 멤버는 `객체` 를 `생성해서 접근`해야 한다. 그런데 `컴패니언 클래스`는 이를 생성하지 않고서도 `클래스 이름`으로 `특정 멤버`를 이용할 수 있다.

    → 즉, 클래스 내에 선언된 멤버 변수나 메서드를 이용하려면, 기본적으로 `인스턴스`를 만들고나서 그 `인스턴스`를 통해서 `참조`해야만 하는데, 클래스 내에 `컴패니언 클래스`를 선언하면, `컴패니언 클래스`는 클래스의 `인스턴스`가 없더라도 `클래스 이름`으로 참조할 수 있게 된다.

    ```kotlin
    class MyClass {
    	companion object {
    		var data = 10
    		
    		fun some() {
    			println(data)
    		}
    	}
    }

    fun main() {
    	MyClass.data = 20
    	MyClass.some()
    }
    ```

    → MyClass의 instance를 생성하지 않고 MyClass라는 이름으로 companion object 변수를 참조
