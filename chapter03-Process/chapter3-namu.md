# 3. Process

## 프로세스?

- 정의: Program in execution. 실행 중인 프로그램. 메모리에 올라와 실행되고 있는 프로그램.
- 프로세스의 문맥(context)
    - 특정 시점에서 프로세스를 관찰했을 때,
        - 어디까지 수행을 했는지
        - 어떤 독자적인 주소 공간(코드, 데이터, 스택)을 확보하고 있는지
        - CPU의 프로그램 카운터가 메모리의 코드 영역의 어떤 Instruction을 가리키고 있는지
    - 위와 같이 프로세스의 현재 상태를 나타내는데 필요한 모든 정보를 아우르는 것을 말한다.
    1. CPU 수행 상태를 나타내는 하드웨어 문맥
        1. Program Counter - 어떤 Instruction을 가리키고 있는지
        2. 각종 레지스터 - 어떤 값을 가지고 있는지
    2. 프로세스의 주소 공간(메모리 - 코드, 데이터, 스택)
    3. 프로세스 관련 커널 자료 구조(PCB - Process Control Block)
        1. PCB
        2. Kernel stack - 프로세스가 시스템 콜을 했을 때 운영체제가 수행해야 하는 코드가 쌓여있는, 프로세스별로 구분되어있는 별도의 커널 스택
- 프로세스는 현대 컴퓨터 시스템에서 단독으로 실행되지 않는다. 시분할을 통한 멀티 태스킹이 기본이기 때문에, 하나의 프로세스가 CPU를 점유하고, 다시 놓아주는 것을 반복하는데, CPU가 재할당 되었을 때 이전까지의 작업 문맥을 알고있지 못하면 안되기에 중요한 개념이다.
    ![image](https://user-images.githubusercontent.com/72993238/163520763-6c86385a-1b4c-473e-8128-f6f0841d1a7c.png)

    

## 프로세스의 상태 (Process State)

- 프로세스는 상태(state)가 변경되며 수행된다.
    - Running
        - CPU를 잡고 instruction을 수행중인 상태
    - Ready
        - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 준비되어있음. CPU만 얻으면 실행가능한 상태)
    - Blocked (or wait, sleep)
        - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
        - Process 자신이 요청한 event(예: I/O)가 즉시 만족되지 않아 이를 기다히는 상태
            - 예) 디스크에서 file을 읽어와야 하는 경우 등
    - New: 프로세스가 생성중인 상태
    - Terminated: 프로세스가 종료 중인 상태 (수행(execution)이 끝난 상태)

![image](https://user-images.githubusercontent.com/72993238/163520829-5c5b3285-5554-4cfb-8384-dc6008c8b196.png)
![image](https://user-images.githubusercontent.com/72993238/163520867-97107e3f-5828-46c2-b7df-91739a8136e2.png)

각 프로세스는 Timer Interrupt에 의해서 Running 상태에서 Ready Queue의 맨 뒤로 가는 것을 반복한다. 또한, I/O 및 Event Wait에 의해서 I/O Queue의 맨 뒤로 가, Blocked상태가 되기도 한다.

이 모든 일은 커널(운영체제)의 메모리 주소에서 자신의 Data 영역에서 각 Queue를 관리 제어한다.

그림과는 달리 항상 순서대로(라운드-로빈) 이루어지는 것은 아니고, 프로세스 별 우선순위에 따라서 순서를 바꾸어가며 실행하기도 한다.

## Process Control Block(PCB)

- PCB
    - 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
    - 다음의 구성요소를 가진다(구조체로 유지)
    1. OS가 관리상 사용하는 정보
        - Process state, Process ID
        - scheduling information, priority
    2. CPU 수행 관련 하드웨어 값
        - Program Counter, registers
    3. 메모리 관련
        - Code, data, stack의 위치 정보
    4. 파일 관련
        - Open file descriptors...
![image](https://user-images.githubusercontent.com/72993238/163521437-5cec25e0-a955-46fb-ac7f-027942223fa7.png)


## Context Switch ( 문맥 교환 )

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
    - 뺏겼다가 다시 얻었을 때, 문맥을 기억했다가 그 시점으로부터 다시 시작하는 방식으로 이루어진다.
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴
![image](https://user-images.githubusercontent.com/72993238/163520971-5fe56e17-7686-41df-8295-41fe813f7651.png)

- System call이나 Interrupt 발생시 반드시 컨텍스트 스위치가 일어나는 것은 아님
    - 프로세스에서 단순히 OS로 CPU를 넘기는 것은 컨텍스트 스위치가 아님. 하지만 OS의 판단으로 다른 프로세스에 CPU를 넘기게 되면 컨텍스트 스위치가 일어남.
![image](https://user-images.githubusercontent.com/72993238/163521022-9e862d82-2606-4eea-adf5-3a72bd57c1f9.png)

## 프로세스를 스케줄링 하기 위한 큐

- Job Queue - 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready Queue - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device Queues - I/O device 처리를 기다리는 프로세스의 집합
- 프로세스들은 각 큐들을 오가며 수행된다.
    - 실제로는 각 큐에 프로세스들의 PCB를 줄세우는 것

    ![image](https://user-images.githubusercontent.com/72993238/163521082-91e79383-35dd-4370-b18c-1ebfb07b68f1.png)
    ![image](https://user-images.githubusercontent.com/72993238/163521755-3cb83698-4de2-4855-a4e2-c692e09bd750.png)


## 스케줄러 ( Scheduler )

순서를 정해주는..

- Long-term Scheduler (장기 스케줄러 or job scheduler)
프로세스 상태가 new→ready로 넘어갈 때 메모리에 올라가는 것을 admitted받아야 하는데, 이것을 Long-term scheduler가 관장함
    - 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
    - 프로세스에 memory(및 각종 자원)을 주는 문제
    - degree of Multiprogramming을 제어 - 동시에 메모리에 몇 개의 프로세스를 올려놓을지를 결정
    - time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)
    - 일반적으로 현재 사용되는 OS에는 장기 스케줄러가 없다. 
    장기 대신에, 보통 Medium-term Scheduler를 사용하여 degree of Multiprogramming을 제어한다.
- Short-term Scheduler (단기 스케줄러 or  CPU scheduler)
    - 어떤 프로세스를 다음번에 running시킬지 결정
    - 프로세스에 CPU를 주는 문제
    - 충분히 빨라야 함(millisecond 단위)
- Medium-Term Scheduler (중기 스케줄러 or Swapper)
    - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
    - 프로세스에게서 memory를 뺏는 문제
    - degree of Multiprogramming을 제어
    
    중기 스케줄러로 인해 프로세스 상태에 하나가 추가된다. 바로 Suspended
    

## 프로세스의 상태 (Process State) (ver.중기 스케줄러)

- 프로세스는 상태(state)가 변경되며 수행된다.
    - Running
        - CPU를 잡고 instruction을 수행중인 상태
    - Ready
        - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 준비되어있음. CPU만 얻으면 실행가능한 상태)
    - Blocked (or wait, sleep)
        - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
        - Process 자신이 요청한 event(예: I/O)가 즉시 만족되지 않아 이를 기다리는 상태
            - 예) 디스크에서 file을 읽어와야 하는 경우 등
    - Suspended (stopped)
        - 외부적인 이유로 프로세스의 수행이 정지된 상태
        - 프로세스는 통째로 디스크에 swap out된다.
        - 예) 1. 사용자가 프로그램을 일시 정지시킨 경우(break key)
        2. 시스템이 여러 이유로 프로세스를 잠시 중단시킴
        (메모리에 너무 많은 프로세스가 올라와 있을 때)
        - * Blocked - 자신이 요청한 event가 만족되면 Ready
        - * Suspended - 외부에서 resume해 주어야 Active
    ![image](https://user-images.githubusercontent.com/72993238/163521853-61032af7-b40c-42ed-ac16-80193e6c720f.png)

    - 유의할 점 - 한 사용자 프로그램 프로세스가 시스템콜 등으로 커널에 CPU를 넘긴 상태에서도
    해당 프로세스는 Running이라고 봐야 함. 다만 usermode가 아니라 monitor mode인 것.
    이런 경우에 OS가 Running 모드라는 식으로 말하지는 않는다.
![image](https://user-images.githubusercontent.com/72993238/163521926-d30c8913-fd50-4148-90cf-e1ad2af13c1e.png)

# Thread

- 한 프로세스 내부에 CPU의 수행 단위가 여러 개 있는 것을 쓰레드라 부름
- 프로세스가 하나 주어지면 다음과 같은 주소 공간과 PCB가 형성된다.
    
    ![image](https://user-images.githubusercontent.com/72993238/163522002-2b29d63b-690d-4ea8-a383-e7753963aa89.png)
    ![image](https://user-images.githubusercontent.com/72993238/163522378-20c9af1a-1b58-48f0-8fea-41dd933d6c01.png)

    
    같은 일을 하는 프로세스를 여러 개 띄워놓고 싶다면, 주소 공간은 하나만 띄워놓고 각 세부 프로세스마다 다른 코드를 실행할 수 있도록, 즉 CPU 수행 단위 (Instruction set)만 다르게 가져갈 수 있도록 하는 개념이 쓰레드. 
    프로세스 하나에서 공유할 수 있는 것은 최대한 공유하되, 자신만의 수행 단위를 저장하는 Stack과 이를 가리키는 program counter 및 reguster만 다르게 가져갈 수 있도록 한다.
    
    ---
 
- A thread( or lightweight process) is a basic unit of CPU Utilization
- Thread의 구성
    - program counter
    - register set
    - stack space
- Thread가 동료 thread와 공유하는 부분(=task)
    - code section
    - data section
    - OS resources
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고있는 task로 볼 수 있다.

---

- 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked (waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다. - 빠른 응답성.
- 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)가 성능 향상을 얻을 수 있다. 공유할 수 있는 부분을 공유함으로서 메모리 효율성 재고
- 스레드를 사용하면 병렬성을 높일 수 있다. (CPU가 여러 개인 환경 - 멀티프로세서 에서만 해당함)

---
![image](https://user-images.githubusercontent.com/72993238/163522891-584d4c26-9fb4-43af-b8bf-5f90c10dc67e.png)
![image](https://user-images.githubusercontent.com/72993238/163522919-5c3b3c71-bde1-4445-9f23-bdc9c131eb40.png)

## 쓰레드 사용의 장점

- Responsiveness - 응답성
    - eg) multi-threaded Web - network 요청에 의해 thread가 blocked되어도, 다른 스레드가 display를 연속적으로 작업해 준다.
- Resource Sharing - 자원 공유
    - 복수의 스래드가 코드, 데이터 영역 및 자원을 공유한다
- Economy - 경제성
    - 프로세스를 생성하거나 컨텍스트 스위칭하는 오버헤드 대신, 쓰레드 생성하거나 쓰레드 간의 스위치하는 것이 훨씬 가벼운 작업이다.
    - Solaris의 경우 위 두가지 작업의 오버헤드는 각각 30배, 5배
- Utilization of MP Archetectures
    - 병렬 프로그래밍 시 각 쓰레드가 서로다른 CPU에서 병렬적으로 수행 가능

## 쓰레드 구현 방법
![image](https://user-images.githubusercontent.com/72993238/163523004-bd3eeda9-b0f1-4587-adcc-bef037ecc58a.png)
