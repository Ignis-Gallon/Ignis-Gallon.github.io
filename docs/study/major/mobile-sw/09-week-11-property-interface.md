---
title: "11주차: Property, Interface"
date: 2024-05-30
last_modified_at: 2024-05-30
category: major
tags: [모바일SW, C#, Property, Interface]
---

# 11주차: Property, Interface

## &lt;C# 강의&gt;

### Property

클래스를 구현할 때, public 필드를 이용하면 외부에서 접근이 용이하므로 프로그램 작성과 운용이 편리할 것이라고 생각되지만, 자칫 잘못하면 의도치 않게 데이터가 오염될 수 있다.

하지만 은닉성을 키운다면, 데이터 접근이 너무 힘들고 귀찮아지므로 코드가 매우 길어진다.

Property를 이용하면 별도의 메서드를 만들지 않고도 Getter/Setter를 손쉽게 만들 수 있다.

```csharp
class MyClass
{
	private int myField;
	public int MyField
	{
		get
		{
			return myField;
		}
		set
		{
			myField = value;
		}
	}
}

// ...

MyClass obj = new MyClass();
obj.MyField = 3;
Console.WriteLine(obj.MyField);
```

또한, 만약 property의 get/set을 기본 용도로만 사용할 것이라면 자동 구현도 가능하다.

```csharp
public string Name
{
	get; set;
}

// ...

public class NameCard
{
	public string Name{ get; set; } = "UnKnown";
}
```

### Property와 Initializer

```csharp
MyClass myClass = new MyClass()
{
	property1 = value1,
	property2 = value2,
	property3 = value3
};
```

### Interface의 Property

Interface란, 상속받는 클래스가 내용을 정의하고, 선언때는 모양만 가지고 있는 것을 말한다. 그런데 Interface가 Property를 선언할 경우, 해당 인터페이스를 상속하는 클래스는 반드시 프로퍼티를 구현해야 한다.

```csharp
using System;

namespace PropertiesInterface
{
	interface INamedValue
	{
		string Name
		{
			get;
			set;
		}
		
		string Value
		{
			get;
			set;
		}
	}
}

class NameValue : INamedValue
{
	public string Name
	{
		get;
		set;
	}
	
	public string Value
	{
		get;
		set;
	}
}
```
