---
title: "[운영체제] Main Memory(진행중)"
---

이 장의 목표:
- 논리주소와 물리주소의 차이 및 MMU의 역할.
- 메모리 할당을 위한 다양한 방법.
- Internal fragmentation과 external fragmentation의 차이.
- TLB를 포함한 페이징 시스템에서 논리주소의 변환.
- Hierarchical paging, hashed paging, 그리고 inverted page table.
- IA-32, x86-64, 그리고 ARMv8 아키텍쳐에서의 주소 변환.

# Background

CPU가 명령어를 읽는 과정:
1. CPU는 PC(Program Counter)에 저장된 값(다음 실행될 명령어의 주소)을 MAR(Memory Address Register)에 복사 및 control bus에 `read` 명령어 전송.
2. 이에 대한 응답으로 메모리는 PC가 가리키고 있던 데이터를 전송.
3. CPU는 응답받은 데이터를 MDR(Memory Data Register)에 저장.
4. CPU는 MDR에 저장된 명령어를 IR(Instruction Register)에 복사.
5. PC는 값을 증가시켜 다음 명령어를 가리킴.

이때 CPU(또는 PC)가 보는 주소는 논리주소.

## Basic Hardware

CPU는 메모리와 레지스터에 저장된 값만 읽을 수 있음.

메모리에 접근할 때는 속도 뿐만 아니라 안정성까지 보장되어야 함.
> **Base register**: 물리주소의 가장 작은 값을 저장.\\
> **Limit register**: 주소공간의 크기를 저장.

Base register와 limit register는 운영체제에 의해서만 load될 수 있음.
- Base register와 limit register은 kernel mode에서만 수정 가능.
- Kernel mode는 오직 운영체제만 실행 가능.
- 따라서 운영체제가 kernel mode로 실행되면 메모리 위에 다른 데이터를 덮어 쓸 수 있음.
  - Context switch에서, 실행중인 프로세스의 상태를 메모리에 저장하고 다른 프로세스를 실행.

## 주소 변환(Address Binding)

명령어 및 데이터가 주소로 변환되는 과정:
1. Compile time
- 프로세스가 위치하게 될 메모리 주소를 compile time에 알고 있으면 컴파일러는 absolute code를 생성.
  - 주소의 starting location이 바뀌게 되면 코드를 다시 컴파일해야 함.
- 프로세스가 위치하게 될 메모리 주소를 모르면 컴파일러는 symbolic address를 relocatable address로 변환(bind).
  - 예) `count` 변수 &rarr; "14 bytes from the beginning of this module."
2. Load time
- 링커(linker) 또는 로더(loader)가 relocatable address를 absolute address로 변환(bind).
  - 예) "14 bytes from ..." &rarr; 74014
- 주소의 starting address가 바뀌게 되면 코드를 reload해야 함.
3. Execution time
- 프로세스가 실행 중일 때 메모리 주소가 바뀔 여지가 있으면 주소 변환(binding)은 프로세스의 실행 이후에 이뤄짐.
- 별도의 하드웨어가 필요.
- 대부분의 운영체제가 이 방식을 사용.

## 논리 및 물리 주소

> **논리 주소(logical address)**: CPU에 의해 생성된 메모리 주소.\\
> **물리 주소(physical address)**: 메모리 유닛(memory-address register)에 의해 보여지는 메모리 주소.

- Compile time 또는 load time에 변환(bind)된 논리 주소와 물리 주소는 같음.
- Execution time에 변환(bind)된 논리 주소와 물리 주소는 다름.
  - 이때 논리 주소를 가상 주소(virtual address)라고 부르기도 함.
  - **Memory-management unit(MMU)**: 가상 주소와 물리 주소를 변환해주는 하드웨어.
  - 프로그램은 절대로 물리 주소에 접근할 수 없음.

## Dynamic Loading

## Dynamic Linking and Shared Libraries

# Contiguous Memory Allocation

> **Contiguous memory allocation**: 각각의 프로세스는 메모리의 일정 부분을 차지. 프로세스들이 차지하는 메모리 섹션은 서로 인접함.

- 가장 기본적인 메모리 할당 방식.

## 메모리 보호

프로세스의 잘못된 메모리 접근 방지.
- Relocation register: 논리 주소를 물리 주소로 변환.
- Limit register: 논리 주소의 적절성 검증.
  - 논리 주소가 limit register의 값보다 작을 때 논리 주소에 relocation register의 값을 더해줌.

특징.
- CPU 스케쥴러가 프로세스를 실행(context switch)할 때, dispatcher가 relocation register와 limit register을 설정.
- CPU에 의해 생성된 모든 주소를 확인하기 때문에 잘못된 메모리 접근 차단 가능.

## 메모리 할당

**Variable-partition scheme**:
- 프로세스에게 알맞은 크기의 메모리를 할당.
- 운영체제는 메모리의 사용 정보를 파악.
  - **Hole**: 할당 가능한 메모리 조각.
- 할당 가능한 메모리 hole이 존재하지 않는 경우
  - 프로세스의 실행을 종료하고 에러 메시지 전송.
  - 프로세스를 wait queue에 보냄.

프로세스에 Hole을 할당하는 방법
- **First fit**: 할당 가능한 hole중 처음 발견한 hole을 할당.
- **Best fit**: 할당 가능한 hole중 가장 작은 hole을 할당.
- **Worst fit**: 할당 가능한 hole중 가장 큰 hole을 할당.
- 일반적으로 First fit, Best fit, Worst fit 순으로 성능(속도 및 메모리 사용)이 좋음.

## 메모리 단편화

> **외부 단편화(external fragmentation)**: 사용 가능한 메모리가 작은 조각들로 파편화되어 있음.

- 전체 메모리의 크기와 프로세스의 크기에 따라 외부 단편화의 중요성이 달라짐.
- **50%-Rule**: 전체 할당된 메모리의 50%가 외부 단편화로 인해 사용이 불가능.
- 만약 메모리 할당 후 크기가 매우 작은 hole이 남는 경우, 처음부터 hole이 남지 않도록 메모리를 할당.
  - 크기가 매우 작은 hole을 관리할 때 발생하는 오버헤드를 줄임.
  - 외부 단편화 문제가 **내부 단편화(internal fragmentation)** 문제로 바뀜. 

외부 단편화의 해결 방법.
- **Compaction**: 사용 가능한 메모리 조각을 하나의 조각으로 모음.
  - 메모리 할당이 동적이고 assembly time이나 load time이 아닌 execution time에 실행될 때 가능.
- **Paging**: 논리 주소와 물리 주소를 동일한 크기의 메모리 조각으로 나눔.

# 페이징(Paging)

**페이징(Paging)**을 통해 프로세스가 non-contiguous한 물리 주소를 가질 수 있음.
- 외부 단편화와 그로 인한 compaction이 발생하지 않음.

## Basic Method

페이징(paging):
- 물리 주소와 논리 주소를 동일한 크기로 나눔.
- **프레임(frame)**: 동일한 크기로 나누어진 물리 주소 조각
- **페이지(page)**: 동일한 크기로 나누어진 논리 주소 조각

논리 주소의 변환
- 프로세스별로 **페이지 테이블(page table)**이 존재.
- CPU가 보는 주소를 두 부분(address(32bit) = **page number**(20bit) \| **page offset**(12bit))으로 분리.
  1. address(32bit) = page number(20bit) \| page offset(12bit)
  2. page table[page number] = frame number
  3. frame number \| page offset

페이지 크기
- 하드웨어에 의해 결정.
- 페이지의 크기는 보통 4KB 또는 8KB.
  - Internal fragmentation을 줄이려면 페이지의 크기가 작아야 함.
  - 그러나 페이지의 크기가 작아지면 page table에서 오버헤드 발생.
- 다양한 크기의 페이지를 지원하는 CPU와 운영체제도 존재.

운영체제의 역할
- **프레임 테이블(frame table)**
  - 운영체제가 관리하는 프레임 정보를 저장.
  - 프레임의 사용 여부 및 (프레임이 사용중이라면) 프레임을 사용중인 프로세스 정보.
- 프로세스 각각의 페이지 테이블 정보를 저장.

## Hardware Support

## Protection

## Shared Pages

# Structure of the Page Table

# Swapping

# Example: Inter 32- and 64-bit Architectures

# Example: ARMv8 Architecture

# 참고 문헌

[1] Silberschatz, A., Galvin, P. B., & Gagne, G. (2018). *Operating System Concepts*, 10th ed. John Wiley & Sons.\\
[2] https://en.wikipedia.org/wiki/Instruction_cycle
