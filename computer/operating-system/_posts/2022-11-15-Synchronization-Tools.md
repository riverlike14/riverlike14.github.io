---
title: \[운영체제\] Synchronization Tools (진행중)
---

# Background

# The Critical-Section Problem

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
