---
title: "Dynamic Cast"
date: 2022-11-20
last_modified_at: 2022-11-20
category: major
tags: [객체기반SW설계, C++, dynamic_cast, downcasting]
---

# Dynamic Cast

Dynamic Cast는 C++에서 안전한 downcasting을 사용하기 위해 활용된다. downcasting은 subclass type의 pointer로 superclass의 instance를 가리키는 방법인데, 이렇게 되면 pointer가 subclass type이기 때문에 실제로 메모리가 할당되지 않은 구역을 접근하게 되면서 문제가 발생할 수 있다. 이를 방지하기 위해 Dynamic Cast 기능을 사용하는 것이다.

포인터나 레퍼런스를 기본 클래스 → 파생 클래스로의 다운 캐스팅과, 다중 상속에서 클래스 간의 안전한 타입 캐스팅에 사용된다. 안전하지만 느리다. 이 연산자를 사용하려면 하나 이상의 virtual 함수가 존재해야 한다.

- 사용법

**dynamic_cast&lt;new_type&gt;(expression)**

ex) dynamic_cast&lt;childClass\*&gt;(pParent\*);

성공할 경우 : new_type의 value를 return 합니다.

실패할 경우(new_type = pointer) : null pointer

실패할 경우(new_type = reference) : bad_cast (exception 처리됩니다.)

```cpp
#include <iostream>
using namespace std;
class Blog
{
public:
    Blog() { cout << "Blog()\n"; };
    virtual ~Blog() { cout << "~Blog()\n"; };
 
    void Show()
    {
        cout << "This is Blog Class\n";
    }
};
 
class Tistory : public Blog
{
public:
    Tistory() { cout << "Tistory()\n"; };
    virtual ~Tistory() { cout << "~Tistory()\n"; };
 
    void Show()
    {
        cout << "This is Tistory Class\n";
    }
};
 
int main(void)
{
    Blog* pBlog = new Blog();
 
    //출력됩니다.
    pBlog->Show();
 
    Tistory* pTistory = dynamic_cast<Tistory*>(pBlog);
    if (pTistory == nullptr)
    {
        //티스토리 클래스의 포인터가 nullptr이 나올때.
        cout << "Runtime Error\n";
    }
    else
    {
        pTistory->Show();
    }
    
    delete pBlog;
    system("pause");
    return 0;
}
```

→ 이 예제는 오류가 발생한다. 왜냐면, new Blog()를 통해서 생성되었기 때문에, 당연하게도 Tistory 생성자는 불리지 않았다. 그렇기 때문에 해당 타입으로 포인터를 만들 수 없다.

```cpp
#include <iostream>
using namespace std;
class Blog
{
public:
    Blog() { cout << "Blog()\n"; };
    virtual ~Blog() { cout << "~Blog()\n"; };
 
    void Show()
    {
        cout << "This is Blog Class\n";
    }
};
 
class Tistory : public Blog
{
public:
    Tistory() { cout << "Tistory()\n"; };
    virtual ~Tistory() { cout << "~Tistory()\n"; };
 
    void Show()
    {
        cout << "This is Tistory Class\n";
    }
};
 
int main(void)
{
    Blog* pBlog = new Tistory();
 
    //출력됩니다.
    pBlog->Show();
 
    Tistory* pTistory = dynamic_cast<Tistory*>(pBlog);
    if (pTistory == nullptr)
    {
        //티스토리 클래스의 포인터가 nullptr이 나올때.
        cout << "Runtime Error\n";
    }
    else
    {
        pTistory->Show();
    }
    
    delete pBlog;
    system("pause");
    return 0;
}
```
