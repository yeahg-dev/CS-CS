# Chapter 5. CPU Scheduling

## CPU and I/O Bursts in Program Execution

<img src="https://i.imgur.com/6NbatzL.png" width="50%"/>


모든 프로세스는 I/O와 CPU를 번갈아 작업한다.

- I/O bound job: I/O 작업이 잦은 job
- CPU bound job: CPU가 길게 작업을 하고, 간혹 I/O가 일하는, 계산 위주의 job

> CPU Scheduling의 핵심은 누구에게 우선해서 CPU를 할당하고 얼마만큼의 시간을 준 후에 뺏을 것인가 이다.
> 

## CPU-busrt Time의 분포

<img src="https://i.imgur.com/HtlAV4h.png" width="50%"/>

그래프를 보았을 때, I/O작업이 CPU를 많이 소모한다고 볼 수는 없다. 실제 사용시간이 긴 것은 CPU bound job이다.

- CPU Scheduler
    - 운영체제 안에서 스케쥴링하는 코드. 하드웨어나 소프트웨어의 개념이 아니다.
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
- Dispatcher
    - 스케줄러와 마찬가지로 코드로써 존재하며 하드웨어나 소프트웨어가 아니다.
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    - 이 과정을 context switch(문맥 교환)라고 한다

## **Scheduling Criteria**

Performance Index ( = Performance Measure, 성능 척도 )

### 1. 시스템 입장에서의 성능 척도

- CPU utilization (이용률)
    - 비싼 자원인 CPU가 놀지 않고 일한 퍼센테이지
- Throughput(처리량)
    - 주어진 시간동안 몇 개의 작업을 완료했는지에 대한 척도

### 2. 프로그램 입장에서의 성능 척도

- Turnaround time (소요 시간, 반환 시간)
    - CPU를 쓰러 들어와서  CPU 버스트가 다 끝날 때가지 걸린 시간
    - CPU 버스트 중, CPU를 빼앗기고 다시 얻기까지 소요된 대기시간과 사용시간을 모두 더하여 계산한다.
- Waiting time(대기 시간)
    - Ready Queue에서 CPU가 할당되기를 기다린 시간
    - CPU 버스트 중, CPU를 빼앗기고 다시 얻기까지 여러 번의 대기의 소요시간을 모두 더하여 계산한다.
- Response Time (응답 시간)
    - Ready Queue에 들어와서 처음으로 CPU를 얻기까지 걸린 시간
    - 사용자 응답과 긴밀하게 관련되어 있기 때문에 별도 도입되어 이야기된다.

---

## Scheduling Algorithm

- **Nonpreemptive(비선점형):** CPU 제어권을 자진반납한다.
    - Running → Blocked (예: I/O 요청하는 시스템 콜)
    - Terminate
- **Preemptive(선점형):** CPU 제어권을 강제로 빼앗긴다.
    - Running → Ready (예: 할당 시간 만료로 Timer interrupt)
    - Blocked → Ready (예: I/O 완료 후 인터럽트)

### 1. FCFS (First-Come Firest-Served)

먼저 온 순서대로 처리되는 `비선점형 스케쥴링`. 효율적이지 않다.

앞에 긴 시간이 필요한 프로세스가 도착해버려서 대기 시간이 길어져버리는 걸 콘보이 이펙트라고 한다.

<img src="https://i.imgur.com/Rzrh7YK.png" width="50%"/>

- Convoy Effect: 앞에 긴 시간이 필요한 프로세스가 먼저 도착해버려서 대기시간이 길어져버리는 성능 문제

### 2. SJF (Shortest Job First)

CPU 버스트 타임이 가장 짧은 프로세스를 제일 먼저 스케줄링하는 방법. 가장 짧은 평균 대기시간을 가진다.

- **Nonpreemptive Scheduling (비선점형)**

<img src="https://i.imgur.com/8fxTtlt.png" width="50%"/>

- **Preemptive Scheduling (선점형)**

<img src="https://i.imgur.com/2tCUNHa.png" width="50%"/>


비선점형과 선점형의 차이는 `언제 스케줄링을 할 것인가` 이다.

- 비선점형은 한 프로세스의 CPU 버스트가 끝나고 스케줄링이 이루어진다.
- 선점형은 새로운 프로세스가 Ready Queue에 들어왔을 때 스케줄링이 이루어진다.

### SJF의 문제점

1. starvation(기아 현상)
    - CPU 사용시간이 너무 긴 프로세스는 영원히 CPU를 할당받지 못할 수 있다.
2. 미리 확인 불가능한 CPU burst time
    - exponential averaging: 실제 시스템에서는 과거의 burst time을  토대로 추정한다.

### 3. Priority Scheduling

우선순위가 높은 프로세스에게 먼저 스케쥴링하는 방식

- 비선점과 선점으로 나누어 생각할 수 있다.
- SJF는 Priority Scheduling 중 하나이다. (predicted next CPU burst time)

**Aging: 기아현상을 해결하는 방법.** 기다리는 만큼 우선순위를 높여줘서 할당받을 수 있도록 한다.

### 4. RR (Round Robin)

각 프로세스에게 동일한 CPU 할당 시간  `time quantum`을 세팅하고, 시간이 끝나면 빼앗기는 선점형 스케줄링 방식.

- 할당 시간이 끝나면 프로세스는 선점(Preempted)당하고 ready  queue의 제일 뒤에 가서 다시 줄을 선다.

**time quantum 크기에 따른 Performance**

- 너무 작은 타입 컨텀을 주는 것(q small) 또한 문맥교환으로 인해 오버헤드가 커질 수 있다
- 큰 타임 컨텀을 세팅하면 FIFO의 모습을 가진다.

> 현대의 스케쥴링 알고리즘은 RR을 기반으로 한다.
>
