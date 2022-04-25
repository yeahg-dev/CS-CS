# Chapter 5️⃣ CPU Scheduling

## 1. CPU and I/O burst in Program Execution
- CPU burst : CPU를 사용하는 단계
- I/O burst: I/O 작업을 하는 단계

어떤 프로그램이든 CPU burst와 I/O burst를 번갈아 함

### 프로세스의 특성 분류

- I/O bound process
    - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 프로세스
    - CPU burst 짧게 자주 일어남
- CPU-bound process
    - CPU로 하는 계산 위주의 프로세스
    - CPU burst가 길게 종종 일어남

### 🌟 CPU 스케줄링의 핵심 역할

- interactive한 job이 사용자 입장에서 원활하게 진행될 수 있도록 조정
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용하도록

> 1. 누구에게 우선적으로 줄 것인지
2. 시간을 어떻게 분배할 지, 중간에 뺏을지 말지

가 중요!

## 2. CPU Scheduler & Dipatcher
- 별도의 프로그램이 아닌, 운영체제 내에 있는 기능

### CPU 스케줄러

> Ready상태의 프로세스 중 다음에 CPU에게 줄 프로세스를 고름

### Dispatcher

> CPU의 제어권을 선택된 프로세스에게 실제 넘기는 일 수행
기존 프로세스의 컨텍스트 저장, 선택된 프로세스의 컨텍스트 불러옴
이 과정을 context switch라고 함

- 우선순위 기반 스케줄링 // blocked → Ready

🚀 CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우

1. `Running` → `Blocked` (예: I/O 작업 시스템콜 호출)
2. `Running` → `Ready` (예: CPU 할당시간 만료로 timer interrupt가 들어올 때)
3. `Blocked` → `Ready` (예: I/O 작업 완료후 인터럽트)
4. `Terminate`

## 3. Scheduling Algorithm

### 분류
- non-preemptive(비선점형) : 강제로 빼앗지 않는 방법 (일 끝날 때까지 기다려줌)
- preemptive(선점형) : 강제로 빼앗는 방법 (현재 운영체제에서 주로 사용✅ )

### Scheduling Criteria(성능 척도)

== performance Index == performance measure

시스템 입장에서의 척도

1. **CPU Utilization (이용률)** 
    
    전체시간중 일한 시간의 비율
    
2.  **Throughput(처리량)**
    
     주어진 시간에 Process 몇 개를 완료 했느냐
    

프로그램 입장에서의 척도

1. **Turnaround Time(소요 시간, 반환 시간)**
    
    CPU burst에 들어와서 나갈때까지 걸린 시간
    
2. **Waiting Time(대기 시간)**
    
    CPU burst에 들어와서 레디큐에서 기다린 시간의 총합
    
3. **Response Time(응답 시간)**
    
    I/O에서 CPU를 처음 얻기까지 기다린 시간 (time-sharing 환경에서)

### 1. FCFS (First-Come First-Served)
- 먼저 도착한 프로세스 먼저 처리하는 알고리즘 (non-preemptive)
- 그닥 효율적이지 않음
- 앞에 어떤 프로세스가 있느냐에 따라 waiting time을 결정
- convoy effect

---
### 2. SJF(Shortest-Job-First)
프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄함

- **NonPreemptive SJF**
  현재 CPU에서 진행중인 프로세스가 끝나야 스케쥴링
  
- **Preemptive SJF**
  새로운 프로세스가 들어오는 시점에 스케줄링
  - 단점 : CPU사용시간이 긴 프로세스는 영원히... 일하지 못할 수도...(stravation 기아 현상)

- CPU 사용시간을 미리 정확하게 알 수는 없음, 과거를 바탕으로 추측만 가능

---
### 3. Priority Scehduling
- 가장 높은 우선순위의 프로세스에게 CPU를 할당
    - 숫자가 작을수록 높은 우선순위
- 단점: 기아현상, 낮은 우선순위의 프로세스는 절대 실행되지 않을 수 있음
- 해결책: Aging (경로우대), 시간이 흐를 수록 프로세스의 우선도를 높임

---
### 4. Round Robin
- preemptive
- 각 프로세스는 동일한 크기의 할당 시간을 가짐(일반적으로 10-100 millisecond)
- 할당시간이 지나면 프로세스는 선점당하고, ready queue의 제일 뒤로 가서 다시 줄을 섬
- 장점: response time이 짧음
    - 어떤 프로세스도 (n-1)q time unit이상 기다리지 않음

---
### 5. Multilevel Queue
- Ready queue를 여러 개로 분할
- CPU에 사용가능한 자리가 나면 우선순위가 높은 큐부터 프로세스를 디큐하여 스케줄링
- 큐간에 이동이 불가
- 우선순위가 낮은 큐는 평생 CPU를 못 얻을 수 있음 (기아현상) 
➡️ 개선한 알고리즘 : Multilevel Feedback Queue

><높은 우선 순위>
>- `system processes`
>- `interactive processes`
>- `interactive editing processes`
>- `batch processes` (CPU만 오래 사용하는 프로세스)
>- `student processes`
>
><낮은 우선 순위>

><두개의 큐로 분류하는 방법>
> - foreground (interactive)
> - background (batch - no human interaction)

- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground - RR
    - background - FCFS
- 큐에 대한 스케줄링이 필요
    1. CPU를 어떤 큐에 줄 것인지
    2. 큐에 있는 프로세스 중 누구에게 사용권을 줄 것인지

---
### 6. Mulitlevel Feedback Queue
- 프로세스가 큐간에 이동이 가능

일반적인 방식 :
- 처음 들어오는 프로세스는 일단 우선순위 가장 높은 큐로 넣음. 우선순위가 높은 큐일수록 할당시간이 짧음. 할당된 시간 내에 프로세스가 완료되지 못할 경우, 아래 큐로 강등됨. (CPU사용시간이 짧은 I/O burst 잡의 경우 많이 기다리지 않고 바로바로 실행될 수 있음)
- 위의 큐가 빌 때만 아래 큐를 실행

## 4. 다양한 상황에서의 CPU Scheduling
### Multi-Processor Scheduling
- **Homogeneous processor(동등한 프로세서?)인 경우**
    - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스의 경우 복잡해짐
- **Load Sharing**
    - 일부 프로세서에 Job이 몰리지 않도록 하는 메커니즘이 필요
    - 프로세서 내 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- **Symmetric Multiprocessing(SMP)**
    - 각각의 프로세스가 동등한 지위
    - 각 프로세서가 각자 알아서 스케줄링 결정
- **Asymmetric Multiprocessing**
    - 하나의 프로세서가 다른 프로세서까지 관리
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

### Real-Time Scheduling
데드라인을 보장해야하는 job을 다룰 때 사용하는 스케줄링,
주기적으로 update되어야하는 job이 여기에 해당
- **Hard real-time systems**
    정해진 시간 안에 반드시 끝내도록 스케줄링 해야함
    
- **Soft real-time computing**  
    일반 프로세스에 비해 높은 priority를 갖도록 해야 함

### Thread Scheduling
스레드를 구현하는 방법 2가지

- **Local Scheduling**
    - User level thread, 운영체제는 스레드의 존재를 모름
    - 어떤 스레드에게 CPU를 줄지 프로세스가 결정함
- **Global Scheduling**
    - Kenrel level thread, 운영체제가 스레드 존재를 알고잇음
    - 일반 프로세스와 마찬 가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄링할 지 결정


## 7. 알고리즘 평가
- **Queueing models**
    - 확률 분포로 주어지는 arrival rate와 service rate등을 통해 각종 performance index 값을 계산
    - 예전에 많이 사용한 방법, 이론적으로 사용
- I**mplementaion(구현)과 Measurement(성능 측정)**
    - 실제 시스템에 알고리즘 구현하여 실제 작업에 대해서 성능을 측정 비교
    - 기존의 운영체제의 소스코드를 수정하기 때문에 실제로 구현하기 어려움
- **Simulation(모의 실험)**
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
    - trace: 실제 프로그램으로 추출한 인풋 데이터

