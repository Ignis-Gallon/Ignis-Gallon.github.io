---
title: "3주차(Various Sorting)"
date: 2022-09-29
last_modified_at: 2022-10-16
category: major
tags: [알고리즘, 정렬, 선택 정렬, 삽입 정렬, 버블 정렬]
---

# 3주차(Various Sorting)

- `정렬 알고리즘의 안정성(stability)`

    → 동일한 키 값을 갖는 레코드들의 상대적인 위치가 정렬 후에도 바뀌지 않음.

## # `Selection Sort`

**`Selection Sort`**

- `비교 횟수`  
    (n-1) + (n-2) + … + 1 = n(n-1)/2 = O(n^2)

- `이동 횟수`

    한 번 교환하기 위해 3번의 이동 필요 (swap 함수 작업 시) 이 필요하므로,  
    3(n-1) → 마지막 한 개는 swap 을 진행하지 않음.

- T(n) = O(n^2 + 3(n-1)) = `O(n^2)`

- `특징`

    1. 알고리즘이 매우 간단하다.
    2. 효율적인 알고리즘이 아니며, `안정성`을 만족하지 않는다.

```c
#define SWAP(x, y, t) ((t) = (x), (x) = (y), (y) = (t))

void selection_sort(int list[], int n) {
	int i, j, least, tmp;
	for (i = 0; i < n-1; i++) {
		least = i;
		for (j = i + 1; j < n; j++) {
			if (list[least] > list[j])
				least = j;
			SWAP(list[i], list[least], temp);
	}
}
```

## # `Insertion Sort`

**`Insertion Sort`**

- `복잡도 분석`

    - 최선의 경우 `O(n)` :  이미 정렬되어 있는 경우 → n-1번 (외부루프)
    - 최악의 경우 `O(n^2)` : 역순으로 정렬되어 있는 경우

```c
void insertion_sort(int list[], int n) {
	int i, j, key;
	for (i = 1; i < n; i++) {
		key = list[i];
		for (j = i-1; j >= 0 && list[j] > list[i]; j--)
			list[j+1] = list[j];
		list[j+1] = key;
	}
}
```

- `특징`

    1. `많은 이동 필요` → `레코드가 크면 불리하다.`  
        (만약, 레코드가 크고 리스트가 `내림차순`에 가까울 수록 `이동 횟수`가 굉장히 많아짐)
    2. `안정된 정렬 방법`, 대부분 `정렬`되어 있는 경우 매우 `효율적`이다.  
        (최상의 경우, `O(n)`의 시간 복잡도로 사용될 수 있음)

## # `Bubble Sort`

**`Bubble Sort`**

- `비교 횟수`

    최상, 평균, 최악의 경우 모두 일정하다.

- `이동 횟수`

    - 역순으로 정렬된 경우(최악) : 이동 횟수 = 3 * 비교 횟수 (swap 함수 포함)  
        → 3*(1 + 2 + … + n-1) = 1.5(n^2 - n)
    - 이미 정렬된 경우(최선의 경우) : 이동 횟수 = 0
    - 평균의 경우 : `O(`$n^2$`)`

- `레코드의 이동 과다`

    - 이동 연산은 비교 연산보다 더 많은 시간이 소요됨

```c
#define SWAP(x,y,t) ((t)=(x), (x)=(y), (y)=(t))

void bubble_sort(int list[], int n) {
	int i, j, temp;
	for (i=n-1; i>0; i--) {
		for (j=0; j<i; j++) {
			if (list[j] > list[j+1]) {
				SWAP(list[j], list[j+1], temp);
			}
		}
	}
}
```

- `Bubble Sort` : `O(`$n^2$`)` comparisons and `O(`$n^2$`)` swaps
- `Selection Sort` : `O(`$n^2$`)` comparisons and `O(`$n$`)` swaps
