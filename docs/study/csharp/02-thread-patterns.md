---
title: "Thread 사용 패턴"
date: 2022-12-20
last_modified_at: 2023-01-25
category: study
tags: [C#, Thread, 멀티스레드, 폴링]
---

# Thread 사용 패턴

작성일 2022-12-20 · 최종 수정일 2023-01-25
{ .post-date }

## Thread

[https://frozenpond.tistory.com/23](https://frozenpond.tistory.com/23)

프로세스란, 메모리에 적재되어 실제로 실행되는 프로그램을 말하며, 스레드란 프로세스 내부에서 생성되는 실제로 작업을 하는 주체를 말한다. 하나의 프로세스는 여러 개의 스레드를 가질 수 있으며, 같은 프로세스 안에 있는 스레드들은 코드, 데이터, 힙 영역을 공유하고 스택과 레지스터 값은 저장하는 공간은 따로 할당된다는 특징이 있다. (힙 영역의 데이터를 공유시 주의 필요.)

스레드는 new Thread 키워드로만 생성되는 것은 아니며, C#으로 데스크탑 애플리케이션을 만들면 스레드가 화면을 보여주기 위해 계속 일하게 되고, 버튼에 대한 요청은 스레드가 진행하며 api 서버를 만들면 http request 하나하나는 스레드가 실행시켜준다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    internal class ThreadExample
    {
        static void Main(string[] args)
        {
            Thread myThread = new Thread(Func);
            myThread.Start();

            for (int i = 0; i < 3; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
            Console.WriteLine("메인쓰레드 종료");
        }

        private static void Func()
        {
            for (int i = 0; i < 10; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
        }
    }
}
```

⇒ 코드를 실행하면, 메인 스레드가 3까지 센 뒤 종료되고, 서브 스레드는 이미 스레드가 할당되었기 때문에 10까지 센 후에 프로그램이 종료된다.

- 스레드에 매개변수 전달하기

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    internal class ThreadExample
    {
        static void Main(string[] args)
        {
            Thread myThread = new Thread(Func);
            myThread.Start(7);

            for (int i = 0; i < 3; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
            Console.WriteLine("메인쓰레드 종료");
        }

        private static void Func(object obj)
        {
            int num = (int)obj;

            for (int i = 0; i < num; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
        }
    }
}
```

⇒ 스레드에 매개변수를 전달할 때는 스레드 생성 시에 전달하는 것이 아니라, Start() 키워드를 수행할 때, 매개 변수를 object로 넣어준다. 이후, 스레드 내부에서 매개변수를 사용할 때는 object type을 원하는 type으로  casting해서 사용해야 한다.

- 스레드에 2개 이상의 매개변수 전달하기

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    internal class ThreadExample
    {
        static void Main(string[] args)
        {
            Thread myThread = new Thread(Func);
            ThreadParam tp = new ThreadParam(2, 6);
            myThread.Start(tp);

            for (int i = 0; i < 3; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
            Console.WriteLine("메인쓰레드 종료");
        }

        private static void Func(object obj)
        {
            ThreadParam tempParam = obj as ThreadParam;
            int num = tempParam.param1 + tempParam.param2;

            for (int i = 0; i < num; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
        }
    }

    class ThreadParam
    {
        public int param1;
        public int param2;

        public ThreadParam(int num1, int num2)
        {
            this.param1 = num1;
            this.param2 = num2;
        }
    }
}
```

⇒ 별도의 class를 선언하고, 객체를 만들어서 전달한 뒤, parsing해서 쓴다.

---

- 백그라운드 스레드 : 스레드를 백그라운드로 만들면, 프로세스가 종료될 때 프로세스가 백그라운드 스레드를 종료시켜버린다. (프로세스가 스레드 종료를 관리한다.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    internal class ThreadExample
    {
        static void Main(string[] args)
        {
            Thread myThread = new Thread(Func);
            // thread를 백그라운드 스레드로 생성함.
            myThread.IsBackground = true;
            myThread.Start();

            for (int i = 0; i < 3; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
            Console.WriteLine("메인쓰레드 종료");
        }

        private static void Func()
        {
            for (int i = 0; i < 10; i++)
            {
                Console.WriteLine(i + 1);
                Thread.Sleep(100);
            }
        }
    }

    //class ThreadParam
    //{
    //    public int param1;
    //    public int param2;

    //    public ThreadParam(int num1, int num2)
    //    {
    //        this.param1 = num1;
    //        this.param2 = num2;
    //    }
    //}
}
```

- 스레드 기다리기1 (블락시키기) : 서브 스레드를 실행시키고, 메인 스레드를 블락시켜서 서브스레드가 끝날 때까지 기다리는 방법이다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    class Program 
    { 
        static void Main(string[] args) 
        { 
            Thread myThread = new Thread(Func); 
            myThread.Start(); 
            for (int i = 0; i < 3; i++) 
            { 
                Console.WriteLine(i+1); 
                Thread.Sleep(100); 
            } 
            myThread.Join(); 
            Console.WriteLine("메인 스레드 종료"); 
        } 
        private static void Func() 
        { 
            for (int i = 0; i < 10; i++) 
            { 
                Console.WriteLine(i+1); 
                Thread.Sleep(100); 
            } 
        }
    }
}
```

⇒ 위의 예처럼, 서브 스레드를 메인 스레드를 블락시켜서 서브 스레드가 끝날 때까지 기다리도록 만들 수 있는데, 이는 메인 스레드에서 생성한 스레드에 대하여 Join()을 수행하면 된다.

(리눅스에서 pthread_join(mythread[i], NULL);을 사용하는 것과 같다.)

- 스레드 기다리기2 (블락시키기) : 작업 스레드를 실행시키고, 메인 스레드를 블락시키며, 작업 스레드가 끝날 때까지 기다리는 방법이다. 메인 스레드는 ewh.WaitOne()에서 블락되어 해당 스레드가 끝날 때까지 대기한다. WaitOne은 스레드 쪽보다는 비동기 작업의 IAsyncResult와 관련이 깊지만, Thread에서도 사용은 가능하다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    class Program 
    { 
        static void Main(string[] args) 
        { 
            EventWaitHandle ewh = new EventWaitHandle(false, EventResetMode.ManualReset); 
            Thread myThread = new Thread(Func); 
            myThread.Start(ewh); 
            Console.WriteLine("서브스레드 시작"); 
            for (int i = 0; i < 3; i++) 
            { 
                Console.WriteLine(i+1); 
                Thread.Sleep(100); 
            } 
            ewh.WaitOne(); 
            Console.WriteLine("메인쓰레드 종료"); 
        }
        private static void Func(object obj) 
        { 
            EventWaitHandle ewh = obj as EventWaitHandle; 
            for (int i = 0; i < 10; i++) 
            { 
                Console.WriteLine(i + 1); 
                Thread.Sleep(100); 
            } 
            ewh.Set();
        }
    }
}
```

- 폴링 : 가장 보편적인 스레드 사용법. 서브 스레드를 만들고, 서브 스레드에서 돌아갈 무한 루프를 0.1초 간격으로 체킹한다. (프로세스 내에서 생성된 스레드는 데이터 메모리를 공유하므로, 전역 static bool 변수 사용)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    class Program 
    { 
        static bool myFlag = false; 
        static void Main(string[] args) 
        { 
            Thread myThread = new Thread(Func); 
            myThread.Start(); 
            for (int i = 0; i < 3; i++) 
            { 
                Console.WriteLine(i+1); 
                Thread.Sleep(100); 
            } 
            myFlag = true; 
            Console.WriteLine("메인스레드 종료"); 
        } 
        private static void Func() 
        { 
            while (true) 
            { 
                if(myFlag) 
                { 
                    Console.WriteLine("폴링 성공"); 
                } 
                Thread.Sleep(100); 
            } 
        }
    }
}
```

- 스레드 강제종료

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace ConsoleApp2
{
    class Program 
    { 
        static bool myFlag = false; 
        static void Main(string[] args) 
        { 
            Thread myThread = new Thread(Func); 
            myThread.Start(); 
            for (int i = 0; i < 3; i++) 
            { 
                Console.WriteLine(i + 1);
                Thread.Sleep(100); 
            } 
            myThread.Abort(); 
            myFlag = true; 
            Console.WriteLine("메인스레드 종료"); 
        } 
        private static void Func() 
        { 
            while (true) 
            { 
                if (myFlag) 
                { 
                    Console.WriteLine("폴링 성공"); 
                } 
                Thread.Sleep(1000);
            } 
        }
    }
}
```

⇒ Abort 키워드로 서브 스레드를 강제종료 시킬 수 있다. myFlag는 True가 되어도, 이미 폴링 스레드가 종료되었으므로 “폴링 성공”은 출력되지 않는다.

- 데이터 공유를 할 때 특수한 경우, 스레드 동기화가 필요.

[https://frozenpond.tistory.com/24](https://frozenpond.tistory.com/24)
