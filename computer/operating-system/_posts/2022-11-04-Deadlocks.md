---
title: \[운영체제\] Deadlocks (진행중)
---

(작업 목록)
- System Model
- Deadlock Characterization
- Deadlock Prevention
- Deadlock Avoidance
- Deadlock Detection
- Recovery from Deadlock

# System Model

# Deadlock in Multithreaded Applications

POSIX mutex lock 함수.
- `pthread_mutex_init`: block되지 않은 mutex를 초기화.
- `pthread_mutex_lock`: mutex를 acquire.
- `pthread_mutex_unlock`: mutex를 release.
- `pthread_mutex_trylock`: mutex를 acquire. 실패하더라도 block되지 않음.

## Deadlock Example

Deadlock :
- 쓰레드가 block되어서 작업이 진행되지 않음.

```c
void *do_work_one(void *param) {
    pthread_mutex_lock(&first_mutex);
    pthread_mutex_lock(&second_mutex);

    /* Do some work */

    pthread_mutex_unlock(&second_mutex);
    pthread_mutex_unlock(&first_mutex);

    pthread_exit(0);
}

void *do_work_two(void *param) {
    pthread_mutex_lock(&second_mutex);
    pthread_mutex_lock(&first_mutex);

    /* Do some work */

    pthread_mutex_unlock(&first_mutex);
    pthread_mutex_unlock(&second_mutex);

    pthread_exit(0);
}
```


## Livelock Example

Livelock : 
- 쓰레드가 block되지는 않지만 작업이 반복적으로 실패함.
- 쓰레드가 실패한 작업을 다시 시도할 때, 약간의 랜덤한 시간 간격을 주면 해결 가능.

```c
void *do_work_one(void *param) {
    int done = 0;

    while (!done) {
        pthread_mutex_lock(&first_mutex);
        if (pthread_mutex_trylock(&second_mutex)) {

            /* Do some work */

            pthread_mutex_unlock(&second_mutex);
            pthread_mutex_unlock(&first_mutex);
            done = 1;
        }
        else
            pthread_mutex_unlock(&first_mutex);
    }

    pthread_exit(0);
}

void *do_work_two(void *param) {
    int done = 0;

    while (!done) {
        pthread_mutex_lock(&second_mutex);
        if (pthread_mutex_trylock(&first_mutex)) {

            /* Do some work */

            pthread_mutex_unlock(&first_mutex);
            pthread_mutex_unlock(&second_mutex);
            done = 1;
        }
        else
            pthread_mutex_unlock(&second_mutex);
    }

    pthread_exit(0);
}
```

# Deadlock Characterization

## Necessary Conditions

Deadlock의 4가지 필요조건
- Mutual exclusion : 1가지 이상의 자원이 non-sharable인 형태로 점유되어 있어야 함.
- Hold and wait : 쓰레드는 1가지 이상의 자원을 점유한 채로 다른 자원을 요청해야 함.
- No preemption : 자원은 preempted될 수 없음.
- Circular wait : 쓰레드들이 요청하는 자원을 점유하고 있는 쓰레드들 사이에 싸이클이 형성되어야 함.

## Resource-Allocation Graph

<p align="center">
  <img alt="Resource-allocation graph" src="https://imgur.com/VZgtQ94.png" />
</p>

System Resource-Allocation Graph
- 방향 그래프.
- 정점은 쓰레드 또는 자원 두 종류로 구분.
  - 자원 정점은 인스턴스의 개수까지 고려.
- 간선
  - Request edge(쓰레드 &rarr; 자원) : 쓰레드가 자원을 요청.
  - Assignment edge(자원 &rarr; 쓰레드) : 쓰레드가 자원을 점유.

Deadlock의 확인
- 싸이클이 존재하지 않으면 deadlock은 발생하지 않음.
- 싸이클이 존재하는 경우
  - 자원 정점들의 인스턴스가 하나뿐인 경우 deadlock 발생.
  - 그 외의 경우에는 장담할 수 없음.

# Methods for Handling Deadlocks

Deadlock을 대하는 3가지 태도.
1. Deadlock을 신경쓰지 않음.
  - 대부분의 운영체제는 deadlock까지 신경쓰지 않음.
2. Deadlock이 발생하지 않는 프로토콜을 사용.
  - Deadlock prevention : deadlock의 4가지 필요조건 중 하나를 만족시키지 못하게 함.
  - Deadlock avoidance : 운영체제는 추가적인 정보를 바탕으로 쓰레드에게 자원 할당 여부를 결정.
3. Deadlock을 발견했을 때 문제를 해결.

# Deadlock Prevention

Deadlock의 4가지 필요조건 중 하나라도 만족하지 않으면 deadlock을 방지할 수 있음.

## Mutual Exclusion

## Hold and Wait

## No Preemption

## Circular Wait

# Deadlock Avoidance

# Deadlock Detection

# Recovery from Deadlock

# 참고 문헌

[1] Silberschatz, A., Galvin, P. B., & Gagne, G. (2018). *Operating System Concepts*, 10th ed. John Wiley & Sons.
