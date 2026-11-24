---
title: "10주차: C# 문법, 프로젝트 계획"
date: 2024-05-11
last_modified_at: 2024-05-30
category: major
tags: [모바일SW, C#, 박싱, 컬렉션, 예외 처리]
---

# 10주차: C# 문법, 프로젝트 계획

작성일 2024-05-11 · 최종 수정일 2024-05-30
{ .post-date }

## &lt;프로젝트 계획서&gt;

제목: 동작이 화려하고 내용이 정밀한 동그란 워치 페이스 만들기 (동작하는 시계)

⇒ 갤럭시 워치 스타일

---

목표로 하는 워치 페이스의 모양을 구체적 그림으로 제시한다. 타겟으로 하는 결과물에 가장 유사한 기존 화면을 이용해서 예측된 결과를 제시하고, 실제의 목표 결과는 그림 툴이나 손으로 그려서 제시한다.

![](../../../assets/img/study/major/mobile-sw/08-week-10-csharp-project-plan/01.webp)

![](../../../assets/img/study/major/mobile-sw/08-week-10-csharp-project-plan/02.webp)

---

## &lt;C# 강의&gt;

### Object 형식

C#에서 사용되는 모든 데이터 타입은 Object로부터 시작된다. 즉, int, char, float 등과 같은 데이터 타입도 모두 Object이다.

**Object**는 값을 Heap에 보관하고, 주소는 Stack에 저장한다. 만약 어떠한 **Object** 타입 데이터에 값을 보관한다면 이를 **박싱**(Boxing)이라고 한다.

반대로 Heap의 값 형식 데이터를 값 형식 객체에 할당할 경우, 값만 꺼내 **Stack**에 저장한다. 이를 **언박싱**(Unboxing)이라고 한다.

```csharp
class Program
{
	static void Main(string[] args)
	{
		int num = 10;
		
		object obj = num;
		
		int num2 = (int)obj;
	}
}
```

값 형식 객체인 num을 값 형식 데이터 10으로 초기화했다. 이후에는 값 형식 데이터 10을 obj에 저장하는데, obj는 object이므로, 값은 Heap에, 주소는 Stack에 저장하는 Boxing을 수행한다. 이후에는 object 내의 값 형식 데이터 10을 꺼내서 값 형식 객체 num2에 저장하므로 이를 Unboxing이라고 할 수 있다. (*object의 값 형식 데이터를 Heap에서 꺼내서 값 형식 객체에 저장하는 경우 언박싱이라 함.*)

> 만약 객체에 넣은 값 형식 데이터의 타입과 다른 형식으로 언박싱을 하려고 하면 문제가 생기므로, 같은 형식으로 언박싱을 해야 한다.

> 모든 데이터는 object 형식을 상속받아 사용하기 때문에, object가 가진 모든 메서드를 사용할 수 있다. (ToString, Equals 등…)

### Equals

- equals()는 값 형식이면 값 비교(즉, 스택의 값 비교), 참조형식이면 스택에 있는 주소값을 비교한다. (정확히는 그 객체 이름이 암묵적으로 리턴해주는 값을 비교함.)

- 위에서 num1, num2가 값이면 두 값을 비교한다. 그런데 만약 num1, num2가 object 형이면, object 형이 암묵적으로 리턴해 주는 값으로 비교하게 된다.

    일반적으로 참조형은 주소를 리턴하는데, object형은 값을 반환한다. 따라서 만약 새롭게 만든 두 개의 object를 비교하고자 한다면, 기본적으로 스택에 있는 주소를 비교하게 된다. 따라서 새로 만든 클래스는 equals()가 반드시 False를 반환하게 된다.

```csharp
int num1=10, num2=10;
num1.equals(num2) // True
num1 == num2 // True
// 비교 연산자 ==은 Stack에서 직접 값을 꺼내서 비교
// int는 object가 아니므로 값이 직접적으로 stack에 존재.
```

- **정리**

.equals()는 해당 데이터가 암묵적으로 반환하는 값을 비교함. 즉, 값 형식 객체와 일반 객체 모두가 암묵적으로 반환하는 값으로 비교된다는 뜻이다.

```csharp
object num1=10, num2=10;
num1.equals(num2) // True, 암묵적으로 반환되는 값을 비교하기 때문
num1 == num2 // False, Stack에서 값을 꺼내서 비교하기 때문.
// Object는 Stack에 주소가 있고, 값 형식 객체는 값이 들어있는 것으로 보인다.
```

> 그런데 new로 생성된 객체는 equals()가 주소로 바로 비교하게 된다.

```csharp
class Animal
{
	string name;
	public Animal(string name)
	{
		this.name = name;
	}

}

class Program
{
	static void Main(string[] args)
	{
		Animal a = new Animal("사자");
		Animal b = new Animal("사자");
		
		Console.writeline(a.Equals(b));
	}
}
```

new를 통해 생성한 객체는 equals() 함수가 Stack에 있는 주소값을 비교하므로 결과는 **False**이다.

```csharp
Animal a;
a = new Animal("...");
```

> 윗 줄의 의미: Animal Class의 객체가 만들어질 건데, 그 객체를 참조하는 a를 생성한다. 이것은 앞으로 만들어질 객체를 가리키는 것이기 때문에 실제 객체가 만들어진 것은 아니다. 실제 객체는 나중에 Heap에 저장하게 되는데, new를 통해 Animal을 만들면 a가 해당 객체를 가리킨다.

> 정리하자면, Animal a;를 할 경우 언젠가 실제로 만들어질 Animal 객체를 참조할 a를 만들고, 그것을 임시로 Stack에 우선 저장한다. 그리고 이후에 new를 통해서 Animal 객체가 Heap에 만들어지게 되고, 이 주소를 Stack에 있던 a에 넘겨주는 것이다.

### this() 생성자

여러 개의 생성자를 만들 때, 이전 생성자와 동일하면서 새로운 내용을 추가하고 싶다면 this()를 통해 지정된 생성자를 연결받고 내용을 추가할 수 있다. (생성자 Overloading)

```csharp
class Dog
{
	public string color;
	public string name;
	public int age;
	
	Dog()
	{
		this.color = "Brown";
	}
	
	Dog(string name) : this()
	{
		this.name = name;
	}
}
```

### static 키워드

static은 한정자이다. 단독으로 사용할 수 없고 다른 키워드에 붙여서 사용해야 한다. 일반적으로 어떠한 클래스의 메서드는 그 클래스의 객체를 만들 때 동작하는데, 앞에 static을 붙이면 그 클래스의 소속이 된다. 따라서 클래스의 인스턴스를 실제로 만드는 것과는 별개로 동작하게 된다.

```csharp
class Animal
{
	public static int count = 0;
	string name;
	
	public Animal (string name)
	{
		this.name = name;
		Console.WriteLine(count);
	}
	
	public static void StaticMethod()
	{
		Console.WriteLine("This message is printed on static method.")
	}
}

class Program
{
	static void Main(string[] args)
	{
		Animal.StaticMethod();
		Animal.count = 10;
	}
}
```

### 상속

```csharp
class Child : Parent
{
	// ...
}
```

현재 클래스는 this를 통해 내부 요소에 접근이 가능하고, base를 쓰면 부모 class에 접근할 수 있다. 만약 어떤 클래스에 sealed 키워드를 붙여서 만들면, 이 클래스는 나중에 다른 클래스로 상속되지 못한다. SDK 등을 만들 때 주로 사용한다. 추가로 protected 키워드는 나와, 나를 상속받은 다른 클래스에서만 쓸 수 있도록 만드는 것이다.

### 데이터 타입 변환

- “12345” ↔ 12345

문자열 12345를 숫자 12345로 바꿀 때, 단순히 (int)”12345”를 하면 안된다. 문자열 12345는 실제로 메모리 내에서 저장되는 값이 숫자 12345와는 다르기 때문이다. 따라서 다른 방식으로 변환한다.

문자: Parse(), Convert.ToInt32()

숫자: 문자 해결책: ToString()

### 상수와 열거 형식

상수는 Text 공간에 있어서 값을 바꿀 수 없는 것들이다. 안정성을 높여준다.

```csharp
const int a = 3;
const double b = 3.14;
const string c = "abcdef";
```

define은 compile 단계에서 바꿔주는 것일 뿐, const와는 다르다.

### 열거형

종류는 같지만 다른 값을 갖는 상수

- enum 열거형식명: 기반자료형 {상수1, 상수2, 상수3, …}
- enum 열거형식명 { 상수1 = 값1, 상수2 = 값2, … }

> 기본값을 지정하지 않으면 0, 1, 2, …로 시작한다. 중간에 값을 바꿔줄 수 있다.

### Nullable

Null이 가능한 변수 형식으로, 데이터가 없는 상태를 표현하기 위해 사용할 수도 있다.

```csharp
int? a = null;
float? b = null;
double? c = null;
```

주로 DB 등과 같이 데이터가 없는 상황을 전달해야 하는 외부 프로그램과의 연동을 위해 사용

### var 형식

C#은 강력한 형식 언어이므로, 의도치 않은 형식의 데이터를 읽거나 할당하는 일을 차단한다. 하지만 대충 사용하고 싶다면 keyword var를 사용하여 약한 형식 검사를 이용할 수 있다. **선언과 동시에 초기화가 필수적이며**, **지역 변수로만 사용이 가능**하다. 

### foreach

배열이나 컬렉션에 주로 사용한다.

- **배열**: 여러 개의 데이터를 담는 코드 요소
- **컬렉션**: 배열과 비슷하나, 데이터 저장 및 액세스 방식이 다름

```csharp
int[] arr = new int[]{0, 1, 2, 3, 4};

foreach (int a in arr) {
	Console.WriteLine(a);
}
```

### 배열의 기본

```csharp
int[] scores = new int[5];
```

for/foreach로 순회 가능

- **System.Array**

C#에서 배열은 System.Array 형식에서 파생된 것이다. Array 클래스의 주요 메소드와 프로퍼티.

> Sort()  
> BinarySearch&lt;T&gt;()  
> IndexOf()  
> TrueForAll&lt;T&gt;()  
> FindIndex&lt;T&gt;()  
> Resize&lt;T&gt;()  
> Clear()  
> ForEach&lt;T&gt;()  
>   
> GetLength()  
>   
> Length  
> Rank

- **다차원 배열**

```csharp
int[,] arr = new int[2, 3]{ {1, 2, 3}, {4, 5, 6} };
int[,] arr2 = new int[,]{ {1, 2, 3}, {4, 5, 6} };
int[,] arr3 = { {1, 2, 3}, {4, 5, 6} };

int[, ,] array = new int[4, 3, 2]
{
	{ {1, 2}, {3, 4}, {5, 6} }, ...
}
```

- **가변 배열**

```csharp
int[][] jagged = new int[3][];
jagged[0] = new int[5] {1, 2, 3, 4, 5};
jagged[1] = new int[] {10, 20, 30};
jagged[2] = new int[] {100, 200};
```

### 컬렉션 기본

- **컬렉션**: 같은 성격의 데이터 모음을 담는 자료 구조
- 배열도 컬렉션의 자료 구조 중 하나이다.
- ArrayList
- Queue
- Stack
- Hashtable

등의 자료구조를 가지고 있다.

- **ArrayList**

배열과 유사한 컬렉션이다. 배열 처럼 [] 연산자를 이용하며, 특정 위치 요소에 데이터를 할당할 수 있다. 용량을 미리 지정할 필요가 없고, 필요에 따라 용량 증가/감소가 가능하다.

Add(), RemoveAt(), Insert() 등을 사용할 수 있다.

```csharp
ArrayList list = new ArrayList();
list.Add(10);
list.Add(20);
list.Add(30);

list.RemoveAt(1);
list.Insert(1, 25);
```

- **Queue**

Enqueue(), Dequeue() 등을 통해 Queue 사용이 가능하다.

- **Stack**

Push(), Pop() 등을 통해 Stack 사용이 가능하다.

### Try/Catch

```csharp
static void DoSomething(int arg)
{
	if (arg < 10)
		Console.WriteLine("arg : {0}", arg);
	else
		throw new Exception("arg가 10보다 크다.");
}

static void Main()
{
	try
	{
		DoSomething(13);
	}
	catch (Exception e)
	{
		Console.WriteLine(e.Message);
	}
}
```

- **finally**

finally는 예외를 처리하다보면 어떤 것이 생길지 모르므로, 문제가 생길 경우 자원이 적절하게 해제되지 않을 가능성이 있다. 따라서 finally를 사용하여 중요한 코드를 수행해준다.

단, finally 안에서 예외가 발생할 경우 catch가 불가능하다.
