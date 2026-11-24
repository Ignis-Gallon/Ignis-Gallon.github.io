---
title: "Window Thread Synchronization"
date: 2023-05-19
last_modified_at: 2023-05-19
category: major
tags: [운영체제, Thread, Mutex, Windows API]
---

# Window Thread Synchronization

```c
#include <Windows.h>
#include <stdio.h>

// 1)
// 프로그램에서 HANDLE이라고 하는 것들은 
// 여러 구조체나 여러 사안들을 가지는 것을 대표하는
// 고유의 번호라고 생각하면 된다.
HANDLE hMutex;
int g_var; // global variable

DWORD WINAPI MyThread_1(LPVOID arg);
DWORD WINAPI MyThread_2(LPVOID arg);

int main(int argc, char* argv[]) {

	HANDLE hThread[2]; // handle 변수 앞에는 h를 붙여주는 것이 좋다.
	DWORD ThreadId[2]; // Thread를 만들때 필요한 것.
	// 이렇게 하면 두 개의 Thread를 생성할 준비가 됨.
	
	// 3)
	// Thread를 만들기 전에 Mutex를 사용할 것이므로,
	// 사용하겠다고 먼저 선언을 해야 한다.
	hMutex = CreateMutex(NULL, FALSE, NULL); // 기본적인 형태로 선언

	hThread[0] = CreateThread(NULL, 0, MyThread_1, NULL, 0, &ThreadId[0]);
	hThread[1] = CreateThread(NULL, 0, MyThread_2, NULL, 0, &ThreadId[1]);

	// Multi-Thread의 Template
	WaitForMultipleObjects(2, hThread, TRUE, INFINITE);

	CloseHandle(hMutex);

	// 내가 만든 프로그램은 앱 위에서 돌아가는 거라고 볼 수 있기 때문에
	// 운영체제에 안좋은 영향을 발생시키면 안된다.
	// 그러므로 프로그램을 깔끔하게 동작시키는 것이 좋으므로, return 0를 해주어야 한다.
	return 0;
}

// 2)
// Thread를 보호하면서 동작시키는 방법이 여러가지가 있는데,
// 이번에는 Mutex를 이용해서 시도해본다.

// 4)
// Mutex를 공유하는 것들은 한 번에 하나의 것만 들어갈 수 있도록
// 그 영역을 Critical Section으로서 보호하게 된다.

// Start Routine 지정.
DWORD WINAPI MyThread_1(LPVOID arg) {

	for (;;) {
		WaitForSingleObject(hMutex, INFINITE); // 얘를 갖는 동안은 기다리는 것.
			g_var = 1;
			Sleep(1);
			// 이렇게 하면 이 안의 프로그램은 묶여져서 보호된다.
			// 보통 Sleep은 Thread를 대기열에 집어넣어 버려서 Context Switching이 생긴다.
			// 그러면 Thread Ready Queue에 있던 것이 올라오게 된다.
			printf("Thread 1 : g_var = %d\n", g_var);
		ReleaseMutex(hMutex);
	}

	return 0;
}

// Start Routine 지정.
DWORD WINAPI MyThread_2(LPVOID arg) {

	for (;;) {
		WaitForSingleObject(hMutex, INFINITE);
			g_var = 2;
			Sleep(1);
			printf("Thread 2 : g_var = %d\n", g_var);
		ReleaseMutex(hMutex);
	}
	return 0;
}
```
