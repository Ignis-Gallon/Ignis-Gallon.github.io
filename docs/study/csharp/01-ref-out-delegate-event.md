---
title: "ref/out, Delegate/Event"
date: 2022-12-19
last_modified_at: 2023-01-05
category: study
tags: [C#, ref, out, Delegate, Event]
---

# ref/out, Delegate/Event

## Out/Ref

Java와 C# 모두에서 개체를 참조하는 메서드에 “매개 변수는 항상 참조로 전달” 되는 반면, 기본 데이터 형식 매개 변수는 값으로 전달한다.

C#에서 값 형식을 참조로 전달하려면 ref 또는 out 키워드 중 하나를 지정해야 한다. 이 두 키워드의 차이점은 `매개 변수 초기화`에 있다.

- ref : ref 매개 변수는 사용하기 전에 초기화된다.
- out : out 매개 변수는 이를 전달하기 전에 명시적으로 초기화할 필요가 없고 이전의 값은 모두 무시한다.

---

- ref

호출되는 메서드에서 매개 변수로 호출되는 변수의 값을 영구적으로 변경하려면, 값 형식 매개 변수에 이 키워드를 지정한다. (Call By Reference를 의미하는 듯 하다.) 호출에 사용되는 변수의 값을 전달하는 대신 이 키워드를 지정하면 변수에 대한 참조 자체가 전달된다.

---

- out

ref 키워드와 매우 비슷한 효과를 가지며, out을 사용하여 선언된 매개 변수의 수정된 내용은, 메서드 외부에서도 볼 수 있다.

out 매개 변수의 모든 초기 값이 메서드 내에서 무시되며,

out 매개 변수는 메서드를 진행하는 동안 할당해야 한다.

(즉, 사전에 어떠한 값이 들어가 있더라도 메서드 내에서 다시 새로운 값을 할당해주어야 한다는 뜻이다.)

```csharp
class TestOut {

	private static void Add(int i, int j, out int result) {
	
		result = i + j;
		return;
	}

	static void Main() {

		int total = 20;
		System.Console.WriteLine("Original value of 'total' : {0}", total);
		
		Add(33, 77, out total);
		System.Console.WriteLine("Value after calling Add() : {0}", total);
	}
}
```

---

> 정리하자면,  
> ref/out은 모두 메서드 내에서 매개 변수를 참조한다는 데서 공통점을 갖지만, ref는 call by value와 같이 사용되고 out은 반드시 메서드 내에서 새롭게 초기화를 진행해야 한다는 데서 차이점이 있다.

## Delegate/Event

- `Delegate`란?

C/C++을 접해본 사람들은 Function Pointer에 대한 개념을 들어봤을 것이다. C#에서 `Delegate`는 C/C++의 함수 포인터와 매우 유사한 성격을 지닌다. C#에서 Delegate는 간접 접근의 한 방법으로 사용되며, 이는 Delegate의 사전적인 의미인 ‘대표, 사절, 파견 위원’등과 유사하다.

- delegate의 형식

```csharp
delegate 반환형 델리게이트명(매개변수..);
```

델리게이트를 이용하면 메서드를 넘겨줄 수 있다. 델리게이트는 메서드를 참조하는 것이고, 참조하는 메서드가 달라진다면, 델리게이트 역시 달라진다. (매개변수의 데이터 형식과 반환형은 참조할 메서드의 매개변수의 데이터 형식과 반환형에 맞추어주어야 한다.)

- delegate 사용 예제

```csharp
namespace ConsoleApp1
{
    delegate int PDelegate(int a, int b);

    class Program
    {
        static int Plus(int a, int b)
        {
            return a + b;
        }

        static void Main(string[] args)
        {
            // Plus 메서드 자체를 PDelegate 타입의 변수에 대입함.
            // 이로써, delegate를 instance화 한다.
            PDelegate pd1 = Plus;

            // pd1처럼 명시적으로 메서드를 넣어줄 수도 있고,
            // Anonymous method를 만들어서 pd2처럼 사용할 수도 있다.
            PDelegate pd2 = delegate (int a, int b)
            {
                return a / b;
            };

            Console.WriteLine(pd1(5, 10));
            Console.WriteLine(pd2(10, 5));
        }
    }
}
```

⇒ 위의 예처럼, PDelegate는 메서드를 참조하는 기능을 한다. 반환형이 int이고, 매개변수가 int, int인 메서드를 전달받아 변형하여 사용한다.

Delegate는 주로 코드에서 어떠한 작업을 하고 싶은데, 그 작업이 명확하지 않은 경우에 주로 사용한다.  C#에서 자주 쓰이는 대표적인 예가 Thread이다.

```csharp
class Program 
{
	static void Main(string[] args)
	{
		Thread thread = new Thread(new ThreadStart(Prn));
		thread.Start();
	}

	static void Prn()
	{
		Console.WriteLine("Printing... Anywhere");
	}
}
```

위 코드에서 Thread가 시작됨과 동시에 Prn이라는 함수를 호출하고, Printing… Anywhere라는 문자를 찍는 작업을 할 수 있는 것은, `ThreadStart`라는 `Delegate` 때문이다.  
⇒ Thread는 그 자체가 무엇을 수행해야 할 지는 모르지만, Delegate를 통해 해당 작업을 수행.

- ThreadStart라는 Delegate는 아래와 같이 선언되어 있다.

```csharp
namespace System.Threading
{
	[ComVisible(true)]
	public delegate void ThreadStart();
}
```

Delegate라는 Type은 위에서 설명한 것과 같이 대리자, 역할 위임의 수행을 하기 때문에 특정 동작에 대한 정보를 담고있는 Type이라고 할 수 있다.

Delegate의 사용법은 위의 Thread 샘플과 같은 방식으로 사용한다. 해당 Delegate Type과 return 및 파라미터 인자들까지 그 형태가 같아야 한다.

Delegate의 또 다른 특징은 `여러 작업`을 `동시에` 담아둘 수도 있으며(`Delegate Chain`), 동시에 여러 번의 작업을 실행할 수도 있다는 점이다.

---

- `델리게이트 체인`(Delegate Chain)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    delegate void PDelegate(int a, int b);
    class Program
    {
        static void Plus(int a, int b)
        {
           Console.WriteLine("{0} + {1} = {2}", a, b, a + b);
        }
        static void Minus(int a, int b)
        {
            Console.WriteLine("{0} - {1} = {2}", a, b, a - b);
        }
        static void Division(int a, int b)
        {
            Console.WriteLine("{0} / {1} = {2}", a, b, a / b);
        }
        static void Multiplication(int a, int b)
        {
            Console.WriteLine("{0} * {1} = {2}", a, b, a * b);
        }
        static void Main(string[] args)
        {
            PDelegate pd = (PDelegate)Delegate.Combine(new PDelegate(Plus), new PDelegate(Minus),
                new PDelegate(Division), new PDelegate(Multiplication));

            pd(20, 10);
        }
    }
}
```

⇒ 위의 내용처럼 하나의 delegate를 통해서 총 네 개의 메서드를 수행한다.

---

- 이벤트(Event)

C#에서는 delegate를 이용해 이벤트를 처리할 수 있다. 이벤트의 기본적인 선언 형식은 다음과 같다.

```csharp
한정자 event 델리게이트 이름;
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApplication42
{
    // MyEventHandler라는 delegate가 선언되었음.
    public delegate void MyEventHandler(string message);

    class Publisher
    {
        // event 한정자로 수식한 델리게이트의 인스턴스 선언.
        public event MyEventHandler Active;

        public void DoActive(int number)
        {
            if (number % 10 == 0)
                Active("Active!" + number);
            else
                Console.WriteLine(number);
        }
    }

    class Subscriber
    {
        static public void MyHandler(string message)
        {
            Console.WriteLine(message);
        }

        static void Main(string[] args)
        {
            Publisher publisher = new Publisher();
            publisher.Active += new MyEventHandler(MyHandler);

            for (int i = 1; i < 50; i++)
                publisher.DoActive(i);
        }
    }
}
```

## Delegate를 사용하는 이유

- Delegate란? : 메서드를 담을 수 있는 타입이다.

    - 관례적으로 Delegate 타입의 이름은 끝에 Delegate라는 접미사를 붙인다.
    - JS의 함수를 변수처럼 사용하는 개념과 유사하다. (Higher-order function)
    - C, C++ 개발자들에게는 간단하게 함수 포인터라고 한다.

- Delegate를 사용하는 이유

    - 이벤트 핸들링을 위해서 사용하며, 콜백을 위해 사용하기도 한다.
    - Delegate를 잘 사용하면 이벤트 규격(함수의 형태)만 잘 정의하면 필요한 정보를 오브젝트에 쉽게 전달할 수 있다는 장점이 있다.

- Delegate 생성 예제

```csharp
public class DelegateExample 
{
	delegate void ExampleDelegate();

	void Start()
	{
		ExampleDelegate del = Hello;
		del();
	}

	void Hello()
	{
		Debug.Log("Hello");
	}
}
```
