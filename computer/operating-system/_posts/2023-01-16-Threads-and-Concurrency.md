---
title: "[운영체제] Threads & Concurrency(진행중)"
---

이 장의 목표:
- 쓰레드의 기본 구성 요소 확인 및 쓰레드와 프로세스 구분.
- 멀티쓰레딩 프로세스의 장단점 이해.
- 쓰레드 풀, fork-join 와 Grand Central Dispatch의 차이점을 통한 implicit threading 확인.
- 윈도우와 리눅스의 쓰레드 이해하기.
- Pthreads, Java, 그리고 윈도우 API를 통한 멀티쓰레드 앱 만들기. 

# 개요

> **쓰레드(Thread)**: CPU 사용의 기본 단위.

![프로세스와 쓰레드](https://imgur.com/0r8MXNZ.png)

쓰레드의 구성 요소:
- 쓰레드 ID.
- PC(Program Counter).
- 레지스터 집합.
- 스택.

쓰레드간 공유하는 자원:
- 코드 부분(code section).
- 전역 변수(data section).
- 운영체제 자원.
  - 열린 파일(open files).
  - 신호(signal).

## 쓰레드의 사용 배경

한 프로세스가 비슷한 작업을 반복해서 계속 처리해야 하는 경우.
- 같은 프로세스를 만드는 것은 시간과 자원 면에서 비용(overhead)이 큼.
- 쓰레드를 생성하는 것이 효울적.

예시:
- 사진 썸네일을 만드는 프로그램의 경우, 서로 다른 사진들 각각에 대해 쓰레드를 만들어서 작업.
- 웹 브라우저에서 한 쓰레드는 HTML 렌더링을 담당하고 다른 쓰레드는 네트워크로부터 데이터를 수신함.
- 워드프로세서에서 한 쓰레드는 작업을 화면에 보여주고, 다른 한 쓰레드는 사용자의 키 입력을 처리, 또 다른 쓰레드는 맞춤법 검사를 수행.

## 쓰레드의 장점

1. **반응성(Responsiveness)**: 프로그램의 특정 부분이 중지되거나 작업 처리 시간이 오래 걸리더라도 사용자와 계속 반응할 수 있음.
2. **자원 공유(Resource Sharing)**: 같은 주소 공간 안에서 서로 다른 작업을 하는 쓰레드를 가질 수 있음.
- 프로세스끼리 자원을 공유하는 방법은 공유 메모리(shared memory) 또는 메시지 전달(message passing)밖에 없음.
3. **경제성(Economy)**: 프로세스를 생성할 때보다 쓰레드를 생성할 때 걸리는 시간 및 사용되는 메모리가 적음.
- Context switching도 쓰레드가 프로세스보다 빠름.
4. **확장성(Scalability)**: 쓰레드는 서로 다른 CPU에서 실행될 수 있음.
- 단일쓰레드 프로세스는 하나의 CPU에서만 실행됨.

# 멀티코어 프로그래밍

> - **동시적(concurrent) 시스템**: 한 번에 많은 일(task)을 진행시킴.
> - **병렬적(parallel) 시스템**: 동시에 많은 일을 수행할 수 있음.

## 멀티코어 프로그래밍의 어려움

멀티코어 프로그램을 만들 때 마주치는 어려움.
- **작업 확인(Identifying tasks)**: 하나의 작업을 서로 독립적이고 동시적으로 실행가능한 작업으로 분할.
- **균형(Balance)**: 작업들은 같은 가치와 양을 갖는 작업들로 분할되어야 함.
- **데이터 분할(Data splitting)**: 프로그램의 데이터 역시 작업들과 같이 분할되어야 함.
- **데이터 의존성(Data dependency)**: 특정 데이터에 접근할 수 있는 작업(task)들로 어떤 것들이 있는지 확인해야 함.
- **테스트와 디버깅(Testing and debugging)**: 단일쓰레드 프로그램과 달리 실행 경로가 다양하기 때문에 테스트와 디버깅의 난이도 증가.

## 병렬성의 종류

병렬성에는 크게 두 가지 종류가 존재.
- **데이터 병렬성(Data parallelism)**: 데이터를 여러개로 분할한 뒤 각각의 CPU에서 같은 작업들을 수행.
- **작업 병렬성(Task parallelism)**: 서로 다른 역할을 하는 작업들을 각각의 CPU에 분배.
  - 서로 다른 쓰레드가 같은 데이터에서 작업 가능.

# 멀티쓰레딩 모델

쓰레드는 사용자 수준의 쓰레드와 커널 수준의 쓰레드가 존재.
- **사용자 쓰레드(user thread)**: 사용자 수준에서 관리하는 쓰레드.
- **커널 쓰레드(kernel thread)**: 운영체제가 직접 관리하는 쓰레드.
- [참고](https://www.ibm.com/docs/da/aix/7.2?topic=processes-kernel-threads-user-threads)

사용자 쓰레드와 커널 쓰레드 사이의 관계에 따라 다양한 모델이 가능.

## Many-to-One 모델

다수의 사용자 쓰레드를 하나의 커널 쓰레드에 대응.
- 쓰레드 하나가 블로킹 시스템 콜을 호출하면 전체 프로세스가 중지.
- 쓰레드들이 멀티 코어 시스템을 활용할 수 없음.

## One-to-One 모델

하나의 사용자 쓰레드를 하나의 커널 쓰레드에 대응.
- 쓰레드 하나가 블로킹 시스템 콜을 호출하더라도 다른 쓰레드는 계속 작업할 수 있음.
- 멀티 코어 시스템에서 병렬적으로 작업 가능.
- 쓰레드가 많아지면 시스템에 부담이 될 수 있음.
  - 대부분의 시스템에서 코어의 개수가 증가함에 따라 이 문제로부터 자유로워짐.
- 리눅스, 윈도우에서 사용.

## Many-to-Many 모델

다수의 사용자 쓰레드를 많으면 사용자 쓰레드 개수만큼의 커널 쓰레드에 대응.
- 대응되는 커널 쓰레드의 개수는 프로그램 또는 컴퓨터 성능에 따라 달라짐.
- 멀티 코어 시스템에서 병렬적으로 작업 가능.
- 시스템 부담에 대한 걱정 없이 원하는 만큼 쓰레드 생성 가능.
- 구현이 어려움.

# 쓰레드 라이브러리

쓰레드 라이브러리 제공 방법.
- 커널 수준의 지원 없이 사용자 수준의 쓰레드 라이브러리 제공.
  - 라이브러리의 모든 코드와 데이터는 사용자 공간에 존재.
- 운영체제의 직접적인 지원을 받는 커널 수준의 라이브러리 구현.
  - 라이브러리의 모든 코드와 데이터는 커널 공간에 존재.

쓰레드 라이브러리 예시.
- Pthreads는 사용자 수준과 커널 수준의 쓰레드 라이브러리 모두 지원.
- 윈도우는 커널 수준의 쓰레드 라이브러리 제공.
  - POSIX와 윈도우 쓰레드는 전역적으로 생성된 데이터를 공유.
- JAVA는 JAVA 프로그램이 생성 및 관리할 수 있는 쓰레드 API 제공.
  - JAVA는 전역 데이터에 대응되는 개념이 없음.
  - 쓰레드끼리 공유할 수 있는 데이터를 명시적으로 설정해야 함.

**비동기적 쓰레딩(Asynchronous Threading)**
- 부모 쓰레드가 자식 쓰레드를 생성한 뒤, 다시 자기 작업을 시작.
- 주로 부모 쓰레드와 자식 쓰레드 사이에 데이터가 거의 공유되지 않음.

**동기적 쓰레딩(Synchronous Threading)**
- 부모 쓰레드가 자식 쓰레드를 생성한 뒤 자식 쓰레드가 종료될 때까지 대기.
- 주로 쓰레드끼리 의미있는 양의 데이터를 공유.

## Pthreads

> **Pthreads: POSIX 표준이 정의하는 쓰레드 생성 및 동기화와 관련된 API.**

- 쓰레드의 행동에 관한 명세(specification)이며, 구현(implementation)은 아님.
- 리눅스, MacOS 등 대체로 유닉스 계열의 OS에서 구현.

```c
#include <pthread.h>
#include <stdio.h>

#include <stdlib.h>
int sum;
void *runner(void *param); // threads call this function

int main(int argc, char *argv[]) {
  pthread_t tid; // the thread identifier
  pthread_attr_t attr; // set of thread attributes

  // set the default attributes of the thread
  pthread_attr_init(&attr);
  // create the thread
  pthread_create(&tid, &attr, runner, argv[1]);
  // wait for the thread to exit
  pthread_join(tid, NULL);

  printf("sum = %d\n", sum);
}

// the tread will execute in this function
void *runner(void *param) {
  int i, upper = atoi(param);
  sum = 0;

  for (i = 1; i <= upper; i++)
    sum += i;

  pthread_exit(0);
}
```
- `pthread_t`: 쓰레드 식별 정보.
- `pthread_attr_t`: 쓰레드의 속성 정보 저장.
- `pthread_attr_init(&attr)`: 쓰레드의 속성 정보를 설정.
- `pthread_create`: 쓰레드 생성.
- `pthread_join`: 자식 쓰레드가 종료될때까지 대기.
- `pthread_exit`: 자식 쓰레드의 종료.

## 윈도우 쓰레드

## JAVA 쓰레드

# Implicit Threading

동시성과 병렬성을 고려할 때, 멀티 쓰레딩 프로그램 개발은 난이도가 매우 높음.

> **암묵적 쓰레딩(Implicit threading)**

- 쓰레딩과 관련된 개발자의 작업을 컴파일러나 런타임 라이브러리에게 전달.
- 개발자는 병렬적으로 실행 가능한 작업(task)을 확인만 해주면 됨.

## 쓰레드 풀

쓰레드를 자주 생성할 때의 문제점.
- 쓰레드를 생성하는 데에 시간이 오래 걸림.
  - 쓰레드가 작업을 마치면 그냥 버려짐.
- 쓰레드를 지나치게 많이 생성하면 시스템 자원을 많이 소모함.

> **쓰레드 풀(Thread pool)**: 프로그램 시작과 동시에 쓰레드들을 미리 생성.

- 쓰레드를 생성하는 대신, 쓰레드 풀에 인자들을 전달.
  - 작업 가능한 쓰레드가 있다면 바로 작업 실행.
  - 작업 가능한 쓰레드가 없다면 큐에서 대기.
- 쓰레드 풀에 전달되는 작업은 비동기적으로 실행 가능.

쓰레드 풀의 장점.
- 쓰레드 생성에 필요한 시간이 단축됨.
- 전체 쓰레드의 개수를 제한함으로써 시스템 자원을 효율적으로 사용.
- 다양한 방식으로 작업 처리 가능.
  - 특정 시간 후, 또는 주기적으로 작업을 실행시킬 수 있음.

쓰레드 개수 결정 방법.
- 쓰레드의 개수는 시스템 자원 등을 고려해서 설정됨.
- 복잡한 쓰레드 풀의 경우, 동적으로 쓰레드의 개수를 결정.
  - 메모리 사용을 줄일 수 있음.

윈도우의 쓰레드 풀 API.
- `Thread_Create()`과 비슷한 방식으로 쓰레드 풀 사용.
- `QueueUserWorkItem(LPTHREAD_START_ROUTINE Function, PVOID Param, ULONG Flags);`
  - 쓰레드 풀에 있는 쓰레드가 `Function`을 실행.
  - `Function`에 `Param` 인자를 전달.
  - `Flags`를 통해 쓰레드 생성 및 관리 방법을 쓰레드 풀에 전달.

### Java Thread Pools

## Fork Join

## OpenMP

## Grand Central Dispatch

## Intel Thread Building Blocks

# Threading Issues

# Operating-System Examples
