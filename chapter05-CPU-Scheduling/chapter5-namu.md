# 5. CPU Scheduling

## CPU and I/O Bursts in Program Execution

<img width="240" src="https://user-images.githubusercontent.com/72993238/163818079-6eb425d9-2d7f-48fa-8c5a-658e3954b244.png">

어떤 프로그램이든 CPU burst와 I/O burst가 번갈아가면서 일어남. 프로그램의 특성에 따라 각각의 빈도가 다르다.

## CPU-burst Time의 분포

<img width="840" src="https://user-images.githubusercontent.com/72993238/163818104-4fc9ba8d-21be-4250-9e40-89552de0c77c.png">

여러 종류의 job(=process)이 섞여있기 떄문에 CPU 스케줄링이 필요하다.

- I/O bound Job으 == Interactive job에게 적절한 response 제공 요망
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용해야 함
- 어떤 프로세스에 CPU를 주고나서 뺏지 않고 작업이 끝날때까지 기다리기로 한다면, 그것이 너무 오래 걸리는 경우에는 인터액션이 밀리는 이슈가 발생함. 그래서  공평성과 효율성을 모두 고려해야 함.

## 프로세스의 특성 분류

- 프로세스는 그 특성에 따라 다음 두 가지로 나눔
    - I/O-bound process
        - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
        - (many short CPU bursts)
    - CPU-bound process
        - 계산 위주의 job
        - (few very ln CPU bursts)

## CPU Scheduler & Dispatcher

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
    - 운영체제 기능의 일부이다.
- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    - 이 과정을 context switch(문맥 교환)라고 한다
- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다
    1. Running → Blocked (예: I/O 요청하는 시스템 콜)
    2. Running → Ready (예: 할당 시간 만료로 Timer interrupt)
    3. Blocked → Ready (예: I/O 완료 후 인터럽트)
    4. Terminate
- 1, 4에서의 스케줄링은 nonpreemptive(→강제로 빼앗지 않고 자진 반납, 비선점형)
- 다른 스케줄링은 preemptive(→강제로 빼앗음, 선점형)
- **현대적인 CPU 스케줄링은 preemtive한 선점형 스케줄링을 대부분 쓰고 있다. (타이머 인터럽트를 통해 강제로 회수)**

## Scheduling Criteria 
== Performance Index ( = Performance Measure, 성능 척도 )

### 시스템 입장에서의 성능 척도

- CPU utilization (이용률)
    - 전체 시간 중 CPU가 일한 시간의 비율.
    - 가급적 CPU를 바쁘게 하라
- Throughput(처리량)
    - 주어진 시간동안 처리한 일.
    - 가급적 많은 일을 처리하도록 하라

---

### 고객(프로세스) 입장에서의 성능 척도

- Turnaround time (소요 시간, 반환 시간)
    - CPU를 모두 사용하고 다시 반납하기까지 걸린 시간
- Waiting time(대기 시간)
    - Ready queue에서 기다리는데 걸린 시간
        - 선점형 스케줄링의 경우, 하나의 CPU burst 동안에도 여러번 기다리는 시간이 발생하는데
        이를 모두 더한 시간이 대기 시간이라고 한다.
- Response Time (응답 시간)
    - 처음으로 CPU를 얻기까지 걸리는 시간
    

## Scheduling Algorithms

### 1. FCFS (First Come First Served)

<img width="350" alt="title" src="https://user-images.githubusercontent.com/72993238/165328719-76ace244-987c-4a4a-aa73-a19d15a4217a.png">
- 비선점형이기에 비효율적이다.
- 뒤에 도착한 짧은 P2,  P3가 먼저 도착했다면?

<img width="350" alt="title" src="https://user-images.githubusercontent.com/72993238/165328860-1dcf66ad-e940-4849-8e81-9f5d19ab38f1.png">

- Convoy 효과: 긴 프로세스 뒤에 짧은 프로세스가 오래 기다리게 되는 현상

### 2. SJF (Shortest Job First), SRTF(Shortest Remaining Time First)

- CPU를 짧게 쓰는 프로세스에게 먼저 할당하는 방식

<img width="500" alt="title" src="https://user-images.githubusercontent.com/72993238/165328893-34826d7c-a914-4c2c-bfb7-bcda13343117.png">

- 주어진 프로세스들에 대해 최소한의 평균 대기시간이 보장된다. (Preemtive의 경우)
- SJF 중에서도 선점형이면 SRTF라고 한다. 전체 시간이 아닌, 남은 시간 기준으로 결정하는 것.
- Non-Preemtive 예시

    <img width="350" alt="title" src="https://user-images.githubusercontent.com/72993238/165328930-5444951d-7810-429a-b346-b0b0ac1ef40f.png">
    
- Preemtive 예시

    <img width="350" alt="title" src="https://user-images.githubusercontent.com/72993238/165328959-7f71ca25-b4c8-4534-82e2-e61c49dcd7b5.png">
    
- 선점/비선점의 차이 중에는, “언제 스케줄링을 할것인가"도 있다.
    - Preemtive의 경우에는 새로운 일이 도착할 때마다 다시 스케줄링해야 하기 때문
    - Non-preemtive는 작업이 끝나고 CPU의 반납이 이루어질 때만 다시 하면 된다.
- SJF의 문제점 1: **Starvation**
    - **CPU 사용시간이 긴 프로세스는 영원히 할당받지 못하는 현상**
- SJF의 문제점 2: CPU 사용시간을 미리 알 수 없다.
    - Input에 따라 / 분기에 따라 시간이 길어지거나 짧아질 수 있기 때문.
    
    → 정확히 알 수는 없지만, 추정할 수는 있음. 과거의 CPU burst tIme 사용내역을 토대로 예측한다.
    
    - 다음 CPU Burst Time의 예측
        - 다음번 CPU Burst Time은, 추정(estimate)만 가능하다.
        - 과거의 CPU Burst Time을 이용해서 추정 (exponential averaging)

            <img width="230" alt="title" src="https://user-images.githubusercontent.com/72993238/165328994-91559c00-d05a-4604-b6b0-7414322a6d25.png">
            
            t는 실제 사용시간, 타우는 예측치.      
            → 다음 예측치 = 이전 실제 사용시간 * a + 이전 예측 사용시간 * (1-a)  
            a(알파)는 일정 비율을 뜻함

            <img width="350" alt="title" src="https://user-images.githubusercontent.com/72993238/165329015-4a5a277e-e4af-4aef-aedc-a702bd9368b8.png">
            
            식을 풀어보면 →  
             최근 것의 가중치가 가장 높고, 그 이전으로 갈수록 적은 가중치로 반영하여 다음 예측치를 구한다는 뜻이다.
            

### 3. Priority Scheduling

- a priority number(integer) is associated with each process
- highest priority를 가진 프로세스에게 CPU 할당
- smallest integer - highest priority
    - Preemtive
    - nonpreemtive
- SJF는 일종의 priority scheduling이다
    - priority = predicted next CPU burst time
- 문제점 - 낮은 우선순위는 Starvation
- 해결 방법 - Aging: 시간 경과에 따라 우선순위를 높여준다.

### 5. RR (Round Robin)

- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐(일반적으로 10-100 millisec)
- 할당 시간이 지나면 프로세스는 선점당하고, ready queue의 제일 뒤에 가서 다시 줄을 선다
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 cpu 시간의 1/n을 얻는다.
    - → 어떤 프로세스도 (n - 1)q time unit 이상 기다리지 않는다.
    - 어떤 프로세스가 오래 CPU를 사용할지 모르는 상황에서, 굳이 예측할 필요 없이, 짧은 일을 가진 프로세스가 짧게 쓰고 나갈 수 있게 해 주는 것이 장점.
    - 자신의 CPU사용시간과 대기시간이 비례하게 된다. 여러번 빼앗기면 여러번 기다리기 때문
- Performance
    - q large → FCFS
    - q small → context switch 오버헤드가 커진다
    - → 적당한 규모(10~100milsec)의 time quantum 할당이 필요하다.
    - 수행해야 할 작업이 균일하게 (q에 비해) 긴 프로세스들로만 이루어져 있다면, 불필요하게 긴 대기시간을 발생시켜 (overhead) 비효율을 야기하는 상황도 있다.

### 6. Multilevel Queue
<img width="640" alt="title" src="https://user-images.githubusercontent.com/72993238/166108187-e8cb8408-ac50-4e6b-950b-3fdf8f738a96.png">
- Ready queue를 여러 개로 분할
    - foreground(interactive)
    - background(batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground - RR
    - background - FCFS
- 큐에 대한 스케줄링이 필요
    - Fixed priority scheduling
        - serve all from foreground then from background
        - Starvation의 가능성
    - Time slice
        - 각 큐에 CPU time을 적절한 비율로 할당
        - 예 - Foreground에 RR로 80%, background에 FCFS로 20%

### 7. Multilevel Feedback Queue

<img width="640" alt="title" src="https://user-images.githubusercontent.com/72993238/166108205-7cd1980b-3fe8-43d1-8068-9879b196db91.png">

- 프로세스가 다른 큐로 이동 가능
- 에이징(aging)을 이와 같은 방식으로 구현할 수 있다.
- Multilevel-feedback-queue scheduler를 정의하는 파라미터들
    - Queue의 수
    - 각 큐의 scheduling algorithm
    - Process를 상위 큐로 보내는 기준
    - Process를 하위 큐로 내쫓는 기준
    - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
    
    ### 예시
    
    - 3개의 큐로 이루어진 멀티레벨 피드백 큐
        - Q0 - q: 8ms
        - Q1 - q: 16ms
        - Q2 - FCFS
    - Scheduling
        - new job이 queue Q0로 들어감
        - CPU를 잡아서 할당 시간 8ms동안 수행됨
        - 8ms동안 다 끝내지 못했으면 Q1로 내려감
        - Q1에서 기다렸다가 CPU를 받아 16ms동안 수행됨
        - 16ms동안 끝내지 못하면 Q2로 쫓겨남

## Multiple Processor Scheduling

- CPU가 여러개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous(균질한) processor인 경우
    - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
- Load sharing
    - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
    - 별개의 큐를 두는 방법 vs. 공동 큐를 사용하는 방법
- Symmetric Multiprocessing(SMP)
    - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

## Real-Time Scheduling

- Hard real-time systems
    - Hard-realtime system task는 정해진 시간 안에 반드시 끝내도록 스케줄링 해야 함
- Soft real-time computing
    - Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

## Thread Scheduling

- Local Scheduling
    - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
    - 프로세스가 CPU를 할당받으면, 프로세스가 알아서 내부 스레드를 스케쥴링한다.
- Global Scheduling
    - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케쥴러가 어떤 thread를 스케줄할지 결정

## Algorithm Evaluation

1. Queueing models
    - 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

<img width="240" alt="title" src="https://user-images.githubusercontent.com/72993238/166108235-fb378a75-564c-4aa7-b11e-76ddc90ef86d.png">

1. Implementation(구현) & Measurement(성능 측정)
    - 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교
2. Simulation (모의 실험)
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
