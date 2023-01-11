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

# Process Scheduling

## Scheduling Queues

## CPU Scheduling

## Context Switch

# 프로세스에 관한 Operation

프로세스는 동시성(concurrency)이 보장되어야 하고 동적으로 생성 및 종료되어야 함.

## 프로세스 생성

한 프로세스는 새로운 프로세스들을 생성할 수 있음.
- 생성된 프로세스들은 부모-자식 관계를 형성.
- 프로세스는 고유한 **PID(Process Identifier)**를 부여받음.
- 리눅스에서 루트 프로세스는 `systemd`.

자식 프로세스의 자원 할당.
- 운영체제로부터 새로운 자원을 할당받을 수 있음.
- 부모 프로세스와 자원을 공유할 수 있음.
- 부모 프로세스가 받은 입력 데이터도 자식 프로세스에게 전달 가능.

프로세스 생성 후 실행에 관한 경우의 수.
- 부모 프로세스와 자식 프로세스가 동시적으로 실행.
- 자식 프로세스가 특정 작업을 마칠 때까지 부모 프로세스 대기.

프로세스 생성 후 주소 공간에 관한 경우의 수.
- 자식 프로세스가 부모 프로세스를 그대로 복사.(`fork`)
- 자식 프로세스는 새로운 프로그램을 불러옴.(`exec`)

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

## Process Termination

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
