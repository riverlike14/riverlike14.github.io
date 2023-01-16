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

# Multicore Programming

# Multithreading Models

# Thread Libraries

# Implicit Threading

# Threading Issues

# Operating-System Examples
