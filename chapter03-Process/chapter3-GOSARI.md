# chapter 3. Process

> 아래 글에서 사용된 이미지는 [해당 링크](http://www.kocw.net/home/search/kemView.do?kemId=1046323)의 강의에서 발췌하였습니다.

## 프로세스의 개념

> process is a program in execution
> 

컴퓨터에서 연속적으로 실행(메모리에 올라간)되고 있는 컴퓨터 프로그램

<img src = "https://i.imgur.com/3Y6Nz9k.png" width="70%" height="70%">

**프로세스의 문맥(context)**

- CPU 수행 상태를 나타내는 하드웨어 문맥
    - Program Counter
    - registers
- 프로세스의 주소 공간
    - code, data, stack
- 프로세스 관련 커널 자료 구조
    - PCB(Process Control Block): 프로세스 별로 관리하기 위한 컨트롤 블럭. 자료구조의 형태로 데이터 영역에 가지고 있다.
    
<img src = "https://i.imgur.com/K79i2Hx.png" width="40%" height="40%">

- kernel stack: 커널은 여러 프로세스가 공유하는 코드라고 볼 수 있고, 어떤 프로세스가 호출했는지를 알기 위해서 커널의 스택은 프로세스 별로 존재한다.

## 프로세스의 상태(Process State)

- **Running**: CPU를 사용하며 인스트럭션을 수행 중인 상태
- **Ready**: 필요한 부분을 물리적 메모리에 올려둔 것으로 모든 준비가 끝난 채로 CPU를 기다리는 상태
- **Blocked(wait, sleep)**: CPU를 주어도 당장 인스트럭션을 수행할 수 없는 상태
    - Process 자신이 요청한 event(예: I/O) 가 즉시 만족되지 않아 이를 기다리는 상태
    - 예) 디스크에서 file을 읽어와야 하는 경우
- **New**: 프로세스가 막 생성된 상태
- **Terminated**: 수행(execution)이 끝난 상태

## 프로세스 상태도

<img src = "https://i.imgur.com/hTmSFwx.png" width="70%" height="70%">

<img src = "https://i.imgur.com/TXND68g.jpg" width="70%" height="70%">

## 문맥 교환(Context Switch)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- 예) 여러 이유로 실행 중이던 프로세스 A가 CPU를 프로세스 B에게 넘겨주어야 할 때, 다시 CPU에 올라갔을 때 이어서 동작할 수 있도록 프로세스 문맥을 프로세스 A PCB에 넘겨받게 된다. 다시 CPU에 올라갈 때는 해당 정보들을 하드웨어에 다시 복원시키는 과정을 거친다.
- 복사되는 정보로는 `register`, `program counter`가 있다.

<img src = "https://i.imgur.com/qOuLamt.png" width="70%" height="70%">

- system call이나 interrupt 발생시 반드시 context switch가 발생하는 것은 아니다.

<img src = "https://i.imgur.com/b1Z30pr.jpg" width="70%" height="70%">

→ (1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 저장하지만, 문맥 교환을 하는 (2)의 경우가 오버헤드가 훨씬 크다.

## 프로세스 스케줄링 큐

- **Job queue**: 현재 시스템 내에 있는 모든 프로세스의 집합
- **Ready queue**: 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
    - PCB에 있는 pointer가 다음 PCB를 가리키는 것으로 ready queue는 나열되어 있다.
- **Device queues**: I/O device의 처리를 기다리는 프로세스의 집합

→ 프로세스들은 각 큐들을 오가며 수행된다. Ready queue와 device queue에 동시에 존재할 수 없다.

## 스케줄러(Scheduler)

- Long-term scheduler(장기 스케줄러, job scheduler)
    - 시작 프로세스 중 어떤 것들을 ready queue에 보낼지 결정
    - 프로세스에 memory(및 각종 자원)을 주는 문제를 담당
    - 메모리에 올라가있는 프로세스의 수를 제어
    - time sharing system에는 보통 장기 스케줄러가 존재하지 않고, 무조건 ready queue로 들어간다.
- Short-term scheduler(단기 스케줄러, CPU scheduler)
    - 어떤 프로세스르르 다음 번에 running 시킬지 결정
    - 프로세스에 CPU를 주는 문제를 담당
    - 충분히 빨라야 함
- Medium-term scheduler(중기 스케줄러, Swapper)
    - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫒아냄
    - 메모리에 올라가있는 프로세스의 수를 제어
    - 중기 스케줄러가 프로세스에게서 메모리를 빼앗기 때문에 생기는 프로세스 상태를 `suspended`라고 한다. 쫒겨난 프로세스 정보는 디스크로 이동한다.
    
    ### suspended(stopped)
    
    - 외부적인 이유로 프로세스의 수행이 정지된 상태
    - 프로세스는 통째로 디스크에서 swap out 된다.
    - 예) 사용자가 프로그램을 일시 정지시킨 경우, 메모리에 너무 많은 프로세스가 올라와 있는 경우


<img src = "https://i.imgur.com/B44xSDG.jpg" width="70%" height="70%">

- Blocked: 자신이 요청한 event가 만족되면 Ready
- Suspended: 외부에서 resume 해 주어야 Active
- 프로세스가 동작하다가 시스템 콜을 하여 커널 모드에서 러닝하게 되었다고 하여 운영체제가 러닝을 하고 있다고 말할 수는 없다. 커널의 코드를 사용한다고 하여도 해당 프로세스가 여전히 러닝 중인 것으로 말한다.

## 스레드(Thread)

- 다중 스레드로 구성된 테스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 테스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다.
- 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
- 스레드를 사용하는 병렬성을 높일 수 있다.
- 스레드가 가져오는 이점
    - 각각의 스레드가 다른 일을 맡기 때문에 높은 응답성을 가짐
    - 리소스를 공유하기 때문에 낮은 오버헤드를 가짐
    - 프로세스를 생성하는 것보다 스레드를 생성하는 편이 낮은 오버헤드를 가짐
    

## 스레드의 구성

- Kernel Threads: 커널의 지원을 받는 스레드
    - 커널은 해당 스레드가 여러 개라는 것을 알고 있다.
    - 하나의 스레드에서 다른 스레드로 CPU가 넘어간느 것 또한 커널이 스케쥴링해주듯 넘겨준다.
- User Threads: 라이브러리의 지원을 받는 스레드
    - 커널은 해당 스레드가 프로세스 내부에 여러 개 있다는 것을 모른다.
    - 라이브러리의 지원을 받아서 프로세스가 직접 관리한다.
