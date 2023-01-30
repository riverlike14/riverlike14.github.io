---
title: \[운영체제\] Synchronization Tools (진행중)
---

이 장의 목표
- 임계 구역(Critical-section) 문제 및 경쟁 상태(race condition)에 대한 이해.
- Memory barrier, `compare-and-swap` 연산, atomic 변수 등 임계 구역 문제의 하드웨어적 해결책.
- 뮤텍스 락, 세마포어, 모니터, 조건 변수 등 임계 구역 문제의 해결책.
- 점유 경쟁의 수준(low, moderate, high)에 따른 임계 구역 문제의 해결책.

# 배경

하나의 프로세스가 일을 온전히 마치지 못한 상태로 다른 프로세스가 실행될 때 의도치 않은 결과가 발생할 수 있음.

> **경쟁 상태(Race condition)**: 둘 이상의 프로세스가 같은 자원을 공유하면서 작업을 진행할 때, 그 결과가 작업의 순서에 의존하는 상태.

## 생산자-소비자 문제(producer-consumer problem)

생산자는 다음과 같은 작업을 실행
- ```c
  while (true) {
    // produce an item in next_produced
    while (count == BUFFER_SIZE)
      ; // do nothing
    buffer[in] = next_produced;
    in = (in + 1) % BUFFER_SIZE;
    count++;
  }
  ```

소비자는 다음과 같은 작업을 실행
- ```c
  while (true) {
    while (count == 0)
      ; // do nothing
    next_consumed = buffer[out];
    out = (out + 1) % BUFFER_SIZE;
    count--;
    // consume the item in next_consumed
  }
  ```

한편, 기계 수준에서 `count++`와 `count--`는 다음과 같이 동작
- `count++`
  - ```c
    register1 = count
    register1 = register1 + 1
    count = register1
    ```
- `count--`
  - ```c
  register2 = count
  register2 = register2 - 1
  count = regiser2
  ```

이때, 기계어 수준에서 아래와 같은 순서로 명령이 진행되면 문제 발생.
- ```
  register1 = count
  register1 = register1 + 1
  register2 = count
  register2 = register2 - 1
  count = register1
  count = regiser2
  ```
- 생산자와 소비자 프로세스 모두 `count` 변수를 수정하기 때문에 의도치 않은 결과 발생.

# 임계 구역(Critical-Section) 문제

> **임계 구역(Critical Section)**: 프로세스가 다른 프로세스와 공유하고 있는 데이터.

- 한 프로세스가 임계 구역 내에서 실행중이면 다른 어떠한 프로세스도 임계 구역에서 실행되면 안됨.
- 프로세스는 임계 구역에 들어가기 전에 권한을 획득해야 함.

임계 구역 문제를 해결하기 위한 세 가지 필요조건.
1. **Mutual exclusion**: 어떤 프로세스가 임계구역 내에서 실행중이면, 다른 어떠한 프로세스도 임계 구역 내에서 실행될 수 없음.
2. **Progress**: 임계 구역에서 진행중인 프로세스가 없고, 임계 구역에 진입하려는 프로세스가 있으면 임계 구역에 즉시 진입할 수 있어야 함.
3. **Bounded waiting**: 한 프로세스가 임계 구역 접근을 요청한다면, 임계 구역에서 실행중인 다른 프로세스들에게 임계 구역 접근에 횟수 제한이 생김. 따라서 요청 처리가 보장됨.

임계 구역 문제의 해결 방법

- 싱글 코어 환경
  - 공유 자원에 대한 변경이 완료되기 전에 인터럽트를 발생시키지 않으면 쉽게 해결.
- 멀티 코어 환경
  - 인터럽트를 발생시키지 않으면 메시지가 모든 프로세서에게 전달됨.
  - 이 메시지로 인해 임계 영역으로의 접근이 지연되고, 결과적으로 시스템 효율 감소.

> - **선점형 커널(Preemptive kernel)**: 커널 모드로 진행될 때 다른 프로세스가 선점 가능.
> - **비선점형 커널(Nonpreemptive kernel)**: 커널 모드로 진행될 때 다른 프로세스가 선점 불가능.

- 비선점형 커널은 커널 데이터에 대한 경쟁 상태(race condition)로부터 자유로움.
  - 커널에서는 하나의 프로세스만 실행되기 때문.
- 선점형 커널은 반응이 빠름.
  - 실시간 프로그램에 더 적합.

# Peterson's Solution

# Hardware Support for Synchronization

# 뮤텍스 락(Mutex Locks)

뮤텍스 락(Mutex Locks)
- **Mut**ual **Ex**clusion.
- Critical-section 문제를 해결할 수 있는 가장 간단한 소프트웨어 수준의 툴.
- Critical section 보호 및 Race condition 방지.

---

예시 코드
> - `available`: 뮤텍스 락의 사용 가능 여부를 알려주는 Boolean 변수.
> - `acquire()`: 뮤텍스 락을 획득.
> - `release()`: 뮤텍스 락을 반환.

```c++
while (true) {
    acquire lock;
        critical section;
    release lock;
        remainder section;
}

void acquire() {
    while (!available)
        ; // busy wait
    available = false;
}

void release() {
    available = true;
}
```

---

뮤텍스 락의 특징
- `acquire()`함수와 `release()`함수 모두 atomic하게 구현되어야 함.
- 뮤텍스 락의 contention이 높을수록(락을 acquire하려는 쓰레드가 많을수록) 성능이 안좋아짐.

뮤텍스 락의 문제점 - busy waiting
- 뮤텍스 락은 busy waiting(예시 코드의 10행)을 이용.
- 스핀 락(Spin lock)이라고도 불림.
- CPU를 계속 점유하므로 성능 저하.
  - 단, context switch가 일어나지 않음.
  - 따라서 context switch가 두 번 실행되기 전에 lock을 획득할 수 있다면 스핀 락을 이용.

# Semaphores

# Monitors

# Liveness

# Evaluation

# 참고 문헌

[1] Silberschatz, A., Galvin, P. B., & Gagne, G. (2018). *Operating System Concepts*, 10th ed. John Wiley & Sons.
