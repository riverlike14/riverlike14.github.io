---
title: "[컴퓨터 구조] Machine-Level Representation of Programs"
---

# A Historical Perspective

# 프로그램 인코딩

1. C 전처리기: `#include` 명령어와 `#define` 명령어 처리.
2. 컴파일러: 소스 코드에서 어셈블리 코드 생성.
3. 어셈블러: 어셈블리 코드에서 오브젝트 파일 생성.
4. 링커: 여러 오브젝트 파일을 합쳐서 실행 파일 생성.

## 기계 수준의 코드

두 가지 중요한 추상화:
- 명령어는 ISA(Instruction Set Architecture; 명령어 집합)에 의해 정의됨.
- 메모리 주소는 가상 주소(virtual addresses).

C 언어와 다른 기계어:
- PC(Program Counter): 다음 실행될 명령어 주소를 가리킴.
- 레지스터: 메모리 주소, 정수 값 등을 저장.
- Condition Code Register: 가장 마지막에 실행된 명령어의 여러 결과를 저장.
- Vector Register: 정수 또는 실수 값을 저장.

## 코드 예시

`mstore.c` 소스 코드 작성.
```c
// mstore.c
long mult2(long, long);

void multstore(long x, long y, long *dest) {
    long t = mult2(x, y);
    *dest = t;
}
```

`gcc -Og -S mstore.c` 명령어로 `mstore.s` 어셈블리 파일 생성.
```nasm
; mstore.s
multstore:
  pushq   %rbx
  movq    %rdx, %rbx
  call    mult2@PLT
  movq    %rax, (%rbx)
  popq    %rbx
  ret
```

`gcc -Og -c mstore.c` 명령어로 `mstore.o` 오브젝트 파일 생성.
```
...
... 5348 3f3f 3f00 0000 0048 3f03 5b3f ...
...
```

`objdump -d mstore.o` 명령어로 어셈블리 명령어 재확인.
```
0000000000000000 <multstore>:
   0:	53                   	push   %rbx
   1:	48 89 d3             	mov    %rdx,%rbx
   4:	e8 00 00 00 00       	call   9 <multstore+0x9>
   9:	48 89 03             	mov    %rax,(%rbx)
   c:	5b                   	pop    %rbx
   d:	c3                   	ret
```
- x86-64 명령어는 1바이트에서 최대 15바이트.
  - 자주 사용되거나 인자가 적은 명령어에 크기가 작은 바이트 명령어 대응.
- 하나의 기계어(바이트 명령어)는 하나의 어셈블리 명령어에 대응.
- 디스어셈블러는 기계어(바이트 명령어)를 읽고 어셈블리 코드를 제공.
- 디스어셈블러가 만든 어셈블리 코드와 `gcc`가 만든 어셈블리 코드는 다를 수 있음.

실행파일을 만들기 위해 메인 함수 작성.
```c
// main.c
void multstore(long, long, long *);

int main() {
    long d;
    multstore(2, 3, &d);
    printf("2 * 3 --> %ld\n", d);
    return 0;
}

long mult2(long a, long b) {
    long s = a * b;
    return s;
}
```

`gcc -Og -o prog main.c mstore.c`명령어로 실행 파일 생성.
`objdump -d prog` 명령어로 어셈블리 명령어 재확인.
```
00000000000011ab <multstore>:
    11ab:	53                   	push   %rbx
    11ac:	48 89 d3             	mov    %rdx,%rbx
    11af:	e8 ef ff ff ff       	call   11a3 <mult2>
    11b4:	48 89 03             	mov    %rax,(%rbx)
    11b7:	5b                   	pop    %rbx
    11b8:	c3                   	ret
```
- 명령어 주소에 관한 정보를 링커가 수정.
  - 명령어가 저장된 주소 값(0~d &rarr; 11ab~11b8)이 다름.
  - `call`이 실행하는 명령어 주소가 `mult2`로 바뀜.

# 데이터 형식

인텔 아키텍처에서 사용하는 데이터 용어
- `word`: 16비트 데이터
- `double words`: 32비트 데이터
- `quad words`: 64비트 데이터

|C declaration|Intel data type|Assembly-code suffix|Size(bytes)|
|:-|:-|:-:|:-:|
|`char`|Byte|`b`|1|
|`short`|Word|`w`|2|
|`int`|Double word|`l`|4|
|`long`|Quad word|`q`|8|
|`char *`|Quad word|`q`|8|
|`float`|Single Precision|`s`|4|
|`double`|Double Precision|`l`|8|

- GCC에 의해 만들어진 어셈블리 코드는 피연산자의 크기를 나타내는 접미사를 포함.
- `int`와 `double`의 접미사 모두 `l`이지만 서로 완전히 다른 명령어 집합에서 사용되기 때문에 혼동의 염려는 없음.


# 정보 접근

![정수 레지스터](https://i.imgur.com/1rYUGij.png)

- x86-64 CPU는 위 16가지의 레지스터를 포함.
- 명령어는 low-order 레지스터에 저장된 크기가 다른 데이터에도 실행 가능.
- 4바이트 데이터를 생성하는 명령어는 남은 4바이트를 0으로 설정.
- 레지스터마다 고유의 역할이 있음.
  - stack pointer(`%rsp`): 런타임 스택의 마지막 위치를 가리킴.
  - 어떤 명령어들은 특정한 레지스터를 읽음.

## 피연산자 형식

|Type|Form|Operand value|Name|
|:-:|:-|:-|:-|
|Immediate|$Imm|Imm|Immediate|
|Register|$r_a$|R[$r_a$]|Register|
|Memory|Imm|M[Imm]|Absolute|
|Memory|($r_a$)|M[R[$r_a$]]|Indirect|
|Memory|Imm($r_b$)|M[Imm + R[$r_b$]]|Base + displacement|
|Memory|($r_b$,$r_i$)|M[R[$r_b$] + R[$r_i$]]|Indexed|
|Memory|Imm($r_b$,$r_i$)|M[Imm + R[$r_b$] + R[$r_i$]]|Indexed|
|Memory|(,$r_i$,s)|M[R[$r_i$] &middot; s]|Scaled indexed|
|Memory|Imm(,$r_i$,s)|M[Imm + R[$r_i$] &middot; s]|Scaled indexed|
|Memory|($r_b$,$r_i$,s)|M[R[$r_b$] + R[$r_i$] &middot; s]|Scaled indexed|
|Memory|Imm($r_b$,$r_i$,s)|M[Imm + R[$r_b$] + R[$r_i$] &middot; s]|Scaled indexed|

- 상수, 레지스터 또는 메모리에서 데이터를 읽고 레지스터 또는 메모리에 데이터 저장.
- *상수(Immediate)*: `$`표시 뒤에 정수 표현. (`$-577` 또는 $`0x1F` 등)
- *레지스터(Register)*: 레지스터에 저장된 값을 읽음.
- *메모리(Memory)*: 메모리 주소(*effective address*)에 저장된 값을 읽음.
- *Scale factor*: 1, 2, 4 또는 8만 가능.


## Data Movement Instructions

## Data Movement Example

## Pushing and Popping Stack Data

# Arithmetic and Logical Operations
# Control
# Procedures
# Array Allocation and Access
# Heterogeneous Data Structures
# Combining Control and Data in Machine-Level Programs
# Floating-Point Code

# 참고문헌
[1] Bryant, R. E., David Richard, O. H., & David Richard, O. H. (2016). *Computer Systems: A Programmer's Perspective*, 3rd ed. Pearson.
