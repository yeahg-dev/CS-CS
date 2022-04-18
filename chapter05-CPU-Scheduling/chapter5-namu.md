# 5. CPU Scheduling

## CPU and I/O Bursts in Program Execution

<img width="240" src="https://user-images.githubusercontent.com/72993238/163818079-6eb425d9-2d7f-48fa-8c5a-658e3954b244.png">

어떤 프로그램이든 CPU burst와 I/O burst가 번갈아가면서 일어남. 프로그램의 특성에 따라 각각의 빈도가 다르다.

## CPU-burst Time의 분포

<img width="840" src="https://user-images.githubusercontent.com/72993238/163818104-4fc9ba8d-21be-4250-9e40-89552de0c77c.png">
여러 종류의 job(=process)이 섞여있기 떄문에 CPU 스케줄링이 필요하다.

- Interactive job에게 적절한 response 제공 요망
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

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
- 1, 4에서의 스케줄링은 nonpreemptive(→강제로 빼앗지 않고 자진 반납)
- 다은 스케줄링은 preemptive(→강제로 빼앗음)
