---
title: "[운영체제] Processes(진행중)"
---

이 장의 목표:
- 프로세스의 구성 요소 및 운영체제 내에서 나타나는 프로세스의 형태.
- 운영체제 내에서 프로세스의 생성 및 소멸 과정.
- 메모리 공유 및 메시지 패싱을 통한 프로세스간의 통신 과정.
- 파이프와 POSIX 공유 메모리를 사용하는 프로세스간의 통신 프로그램 디자인.
- 소켓과 remote procedure calls을 활용한 클라이언트-서버 통신.
- 리눅스 운영체제와 소통하는 커널 모듈 디자인.

# 프로세스의 개념

## 프로세스

> **프로세스(Process)**: 현대 컴퓨터 시스템에서 작업의 단위.

프로세스의 현재 상태는 *PC(Program counter)*와 *레지스터*에 저장된 값으로 표현.

1. Text: 프로그램 코드.
2. Data: 전역 변수.
3. Heap: 런타임 시 동적 할당되는 메모리.
4. Stack: 함수를 실행할 때 변수를 일시적으로 저장하는 공간.
- 함수 파라미터, 지역 변수, 반환 주소 값 등을 저장.

**프로그램(Program)**: 명령어 집합을 저장하고 있는 파일.

## 프로세스 상태

<p align="center">
  <img alt="프로세스 상태" src="https://imgur.com/USXMxSb.png" />
</p>

- New: 프로세스 생성.
- Running: 명령어가 실행중.
- Waiting: 프로세스가 특정 이벤트(I/O 등)의 발생을 기다림.
- Ready: CPU에게  할당 대기.
- Terminated: 프로세스 종료.

## Process Control Block

> **Process Control Block(PCB)**: 운영체제에게 보여지는 프로세스의 모습.

- Process state: new, ready, waiting, halted 등.
- Program counter: 다음 실행되는 명령어의 주소를 가리킴.
- CPU register: CPU 레지스터의 상태 정보 저장.
  - 프로세스가 중단된 후 다시 실행될 때 작업을 이어서 할 수 있어야 함.
- CPU-scheduling information: 프로세스 우선순위, 스케쥴링 큐 포인터 등 스케쥴링과 관련된 정보.
- Memory-management information: Base 레지스터와 Limit 레지스터의 값, 페이지 테이블 또는 세그먼트 테이블 등 메모리 관리에 사용되는 정보.
- Accounting information: CPU 사용 시간, 시간 제한, 프로세스 번호 등.
- I/O status information: 프로세스에게 할당된 I/O 디바이스 목록, open file 목록 등.

## 쓰레드

> **쓰레드(Thread)**: 프로세스 내에서 실행되는 작업 흐름의 단위.

# 프로세스 스케쥴링

**프로세스 스케쥴러(process scheduler)**의 두 가지 목적.
1. *Multiprogramming*: CPU가 실행해야 하는 프로세스가 항상 존재함으로 인해 자원 사용의 극대화.
2. *Time sharing*: 많은 프로세스가 동시에 진행되는 것처럼 보이게 함.

## 스케쥴링 큐

![프로세스 스케쥴링 다이어그램](https://imgur.com/d1e36Bh.png)

1. 프로세스는 가장 먼저 *ready queue*에 들어감.
- ready queue는 보통 linked list로 구현됨.
- 각 PCB의 포인터는 ready queue 안에 있는 다음 PCB를 가리킴.
2. 필요한 경우 프로세스는 *wait queue*에 들어감.
- 프로세스가 I/O 요청을 하면 이 프로세스는 I/O wait queue에 들어감.
- 프로세스가 자식 프로세스를 만들면 자식 프로세스가 종료될 때까지 wait queue에 들어감.
- 인터럽트가 발생하거나 사용 시간이 만료된 프로세스는 CPU에서 다시 ready queue로 들어감.
3. 프로세스가 종료될 때까지 위 과정 반복.

## CPU 스케쥴링

CPU 스케쥴러는 ready queue에 있는 프로세스에게 CPU 자원을 할당함.
- 프로세스당 100ms이하의 시간 자원 배분.

스왑(swapping)
- 프로세스를 디스크에 저장하고 메모리에서 제거.
  - Degree of multiprogramming(현재 메모리에 있는 프로세스의 개수) 감소.
- 주로 메모리가 부족할때 사용.

## Context Switch

> **Context Switch**: 현재 프로세스의 상태를 PCB에 저장하고, 실행할 프로세스의 상태를 PCB에서 불러옴.

- 상태(state)로는 CPU 레지스터, 프로세스 상태, 메모리 관리 정보 등.
- Context switch 시간은 순수한 비용.
  - 메모리 속도, 복사되어야 할 레지스터의 개수 등에 따라 달라짐.
  - 보통 수 &micro;초.

# 프로세스에 관한 Operation

프로세스는 동시성(concurrency)이 보장되어야 하고 동적으로 생성 및 종료되어야 함.

## 프로세스 생성

한 프로세스는 새로운 프로세스들을 생성할 수 있음.
- 생성된 프로세스들은 부모-자식 관계를 형성.
- 프로세스는 고유한 **PID(Process Identifier)**를 부여받음.
- 리눅스에서 루트 프로세스는 `systemd`.

자식 프로세스가 자원을 할당 받는 방법:
- 운영체제로부터 새로운 자원을 할당받을 수 있음.
- 부모 프로세스와 자원을 공유할 수 있음.
  - 부모 프로세스가 받은 입력 데이터도 자식 프로세스에게 전달 가능.

프로세스 생성 후 가능한 실행 방법:
- 부모 프로세스와 자식 프로세스가 동시적으로 실행.
- 자식 프로세스가 특정 작업을 마칠 때까지 부모 프로세스 대기.

프로세스 생성 후 가능한 주소 공간 사용 방법:
- 자식 프로세스가 부모 프로세스를 그대로 복사.
- 자식 프로세스는 새로운 프로그램을 불러옴.

```c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid;

    // fork a child process
    pid = fork();

    if (pid == 0) { // child process
        execlp("/bin/ls", "ls", NULL);
    } else { // parent process, pid > 0
        wait(NULL); // wait for the child to complete
        printf("Child Complete");
    }

    return 0;
}
```
- UNIX `fork()` 시스템 호출을 활용한 새로운 프로세스 생성.
- `fork()`는 호출한 프로세스를 새로운 메모리에 복사.
- `exec()`는 호출된 프로그램이 메모리에 남음.

## 프로세스의 종료

프로세스는 `exit()` 시스템 콜을 호출하면서 종료.
- 만약 부모가 (`wait()` 시스템 콜을 통해) 대기하고 있으면 부모에게 상태값을 반환.
- 사용중이던 모든 자원은 운영체제에게 반환.

한 프로세스가 다른 프로세스를 강제로 종료시킬 수도 있음.
- 보통 부모 프로세스 외에는 이러한 시스템 콜을 호출할 수 없음.
  - 자식 프로세스가 할당받은 자원보다 많은 자원을 사용하는 경우.
  - 자식 프로세스가 더 이상 필요 없는 경우.
  - 부모 프로세스가 종료될 때 운영체제가 자식 프로세스의 종료를 강제하는 경우.
- *cascading termination*: 어떤 프로세스가 종료될 때 모든 자식 프로세스를 종료시키는 현상.

프로세스는 `exit()` 시스템 콜을 통해 exit status를 전달.
```c
exit(1); // exit status: 1
```

부모 프로세스는 `wait()` 시스템 콜을 통해 자식 프로세스의 pid와 exit status를 전달받음.
```c
pid_t pid;
int status;

pid = wait(&status);
```

**좀비 프로세스(zombie process)**
- 프로세스가 종료되었는데 부모 프로세스가 `wait()` 시스템 콜을 호출하지 않은 경우.
- 부모 프로세스가 `wait()` 시스템 콜을 호출하면 프로세스 테이블에서 제거됨.

**고아 프로세스(orphan process)**
- 자식 프로세스보다 부모 프로세스가 먼저 종료되는 경우.
- 리눅스에서는 `systemd` 프로세스가 고아 프로세스의 새로운 부모 프로세스로 설정.
  - `systemd` 프로세스는 주기적으로 `wait()` 시스템 콜을 호출.
  - `systemd` 외의 다른 프로세스들도 같은 기능을 수행할 수 있음.

# Interprocess Communication

# IPC in Shared-Memory Systems

# IPC in Message-Passing Systems

## Naming

## Synchronization

## Buffering

# Examples of IPC Systems

## POSIX Shared Memory

## Mach Message Passing

## Windows

## Pipes

# Communication in Client-Server Systems

## Sockets

## Remote Procedure Calls

# 참고 문헌
[1] Silberschatz, A., Galvin, P. B., & Gagne, G. (2018). *Operating System Concepts*, 10th ed. John Wiley & Sons.\\
