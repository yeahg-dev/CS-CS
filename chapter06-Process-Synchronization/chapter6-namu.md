# 6. Process Synchronization

## 데이터의 접근
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108339-6bc0cdce-310b-4fcc-b7ab-d733a856e19f.png">

## Race Condition

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108360-bff7fe46-3309-40a4-b91b-2f7be64053c5.png">

- **저장 공간**을 공유하는 **실행 주체**가 여럿 있는 경우 레이스 컨디션의 가능성이 있음.

저장 공간: Memory, Address Space / 실행 주체: CPU, Process

- Multiprocesser system에서 발생 가능
- 같은 공유 메모리를 사용하는 프로세스들 간에 발생 가능
- 커널 내부 데이터를 접근하는 루틴들 간 (예: 커널모드 수행 중 인터럽트로 커널보드의 다른 루틴 수행 시)

## OS에서 race condition은 언제 발생하는가?

1. Kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

## OS에서 race condition(1/3) - interrupt handler v.s. kernel

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108395-267737dc-a703-4390-abe9-fa3cda3c288a.png">

- 커널모드 running중 interrupt가 발생하여 인터럽트 처리루틴이 수행
- → 양쪽 다 커널 코드이므로 kernel address space 공유
- 중간에 interrupt 발생 시 문제가 생기는 경우, 해당 연산 완료까지는 interrupt를 받지 않도록 block

## OS에서 race condition(2/3) - preempt a process running in kernel?

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108403-d39d50b1-0763-4b07-849a-ab7ccc0317cd.png">

- 두 프로세스의 address space 간에는 data sharing이 없음
- 그러나 system call을 하는 동안에는 kernal address space의 data를 access하게 됨(share)
- 이 작업 중간에 CPU를 preempt해가면 race condition 발생

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108421-33d9e91d-63a9-4f89-ad99-346bccf4adcc.png">

→ 해결책: 커널 모드에서는 CPU를 preempt하지 않음. 커널 모드에서 사용자 모드로 돌아갈 때 preempt.

- 이로 인해 time sharing이 정확하게 지켜지지는 않지만, 큰 문제는 아니다.

## OS에서 race condition(3/3) - multiprocessor
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108435-f0549960-feba-4144-8381-a3dd50625f3c.png">

- interrupt enable/disable이 아닌, 데이터 별로 lock과 unlock하는 방법.
1. 한 번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법 (비효율적)
2. 커널 내부에 있는 각 공유 데이터에 접근할 떄마다 그 데이터에 대한 lock / unlock을 하는 방법

## Process Synchronyzation 문제

- 공유 데이터(shared data)의 동시 접근(concurrent access)는 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process)간의 실행 순서(orderly execution)를 정해주는 메커니즘 필요
- Race condition
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- Race condition을 막기 위해서는 concurrent process는 동기화(synchronize)되어야 한다
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108453-5a105424-a950-49a4-a9c9-059f10ad550b.png">

## The Critical-Section Problem 임계구역 문제

- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재
- problem - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다
    
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108467-bcb703d0-31ae-4507-a680-c7a45049d0d3.png">


## 프로그램적 해결법의 충족 조건

- Mutual Exclusion - 상호 배제. 상호 배타성
    - 프로세스 Pi가 임계구역 부분을 수행 중이면 다른 모든 프로세스들은 그들의 임계구역에 들어가면 안된다.
- Progress - 진행
    - 아무도 임계구역에 들어가있지 않은 상태에서 임계구역에 들어가고자 하는 프로세스가 있으면 임계구역에 들어가게 해 주어야 한다
- Bound Waiting - 유한 대기
    - 프로세스가 임계구역에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 임계구역에 들어가는 횟수에 한계가 있어야 한다.
- 가정
    - 모든 프로세스의 수행속도는 0보다 크다
    - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다
    
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108517-a4fca82a-264a-4e36-a5d6-2e36115f11a5.png">
<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108521-a11f183f-6ccf-48e8-86cf-1bbc809c2c74.png">

→ 위 코드는 - Progress 조건을 만족할 수 없을 수 있다. 누군가가 들어갔다가 나와야지만 내 차례가 오기 때문. 또한, 두 프로세스간 진입 빈도가 다를 경우를 다뤄주지 못한다.

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108548-f0466c37-508a-4e53-a30f-2eb324160fb4.png">

→ 위 코드 또한 양쪽 다 flag를 들고 있는 상황에서 임계구역에 들어가지 않았다면 아무도 들어가지 못하는 상황 발생. Progress를 만족하지 못함.

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108580-ec938102-f73d-46c4-8ffe-924484d4813c.png">

→ 조건을 모두 만족하는 알고리즘!

- 하지만 문제가 있다 → Busy Waiting(=spin lock) - 계속 CPU와 memory를 쓰면서 wait. while문만 계속 돌고 있는 비효율적인 방법.

<img width="740" alt="title" src="https://user-images.githubusercontent.com/72993238/166108599-f524797d-2d13-40f8-b0db-2d988ea1b002.png">

- 기존의 문제는, 고수준 언어에서 코드는 여러 세부 instruction으로 이루어져있기 때문에 하나의 코드흐름 중간에 interrupt당할 가능성이 있다.
- 하드웨어적으로 load, modify, store를 하나의 instruction으로 atomic하게 묶어버리면 중간에 interrupt당할 일은 없음. 간단한 해결이 가능.

## Semaphores
