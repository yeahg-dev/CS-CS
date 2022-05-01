## critical section 방지 조건

- Mutual Exclusion(상호 배제)
    - 하나의 프로세스가 critical section에서 코드 수행중이면 다른 프로세서는 critical section에 들어가면 안된다.
- Progress(진행)
    - 아무도 critical section에 없다면 코드 실행이 필요한 프로세스는 들어갈 수 있어야 한다.
- Bounded(유한 대기)
    - 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될때까지 다른 프로세스들이 critical section에 들어가는 횟수에 제한이 있어야한다.

### 알고리즘1

- turn: 차례를 나타내는 변수
- turn을 0으로 만들고 실행 시작
- while문을 실행하면서 turn이 0이 아니면 계속 반복문을 실행하다 차례가 되면(turn값이 0이 되면) critical section에 들어간다.
- critical section을 실행 후 turn을 1로 바꿔줘서 상대방 턴으로 돌린다.

→ Mutual Exclusion(상호 배제)은 만족하지만 progress 만족 못할수있다. 왜냐하면 일단 한번은 critical section에 들어갔다 나와야 turn 값이 1이 되기 때문이다. 극단적인 경우 하나의 프로세스는 critical section 실행이 빈번하고 다른 프로세스는 한번만 실행한다면 여러 번 실행이 필요한 프로세스는 한번 이후에는 실행되지 못한다.

### 알고리즘2

- Boolean 타입의 변수 flag를 사용한다(critical section에 들어가고자 하는 상태를 나타내는 값)
- flag 값을 변경한다

→ 양쪽 모두 critical section에 들어가고자 한다면 양쪽 모두 깃발을 들고 있기 때문에 아무도 못들어간다. 깃발을 내려놓으려면 critical section을 빠져나온 뒤에 깃발을 내려놓는 코드가 실행되는데, critical section에 아무도 접근하지 못하기 때문에 내려놓는 코드 또한 실행이 되지 않아서 문제가 생긴다.

### 알고리즘3(피터슨의 알고리즘)

- turn, flag를 모두 쓴다.
- critical section에 들어가고자할때 깃발을 들고, turn을 상대방 턴으로 바꿔놓는다
- while문의 조건으로 상대방이 깃발을 들고 있고 && 상대방 턴일때는 while문을 계속 돌며 기다리다가 반복문(대기상태)가 끝나면 critical section에 들어간다.
- critical section 코드를 빠져나올 때 나의 깃발을 내린다

→ Busy Wating(= spin lock)문제가 있다. 기다리는 동안 while문을 계속 돌며 wait 상태로 씨피유와 메모리를 사용하기 때문이다. while문 조건의 변경이 없는데도 반복적으로 조건 체크만 계속하면서 자원을 낭비한다.

### Synchronization Hardware

- 하드웨어적으로 Test&Modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제는 해결된다.
- Test&Modify을 lock 걸고 critical section 수행 후 락을 푼다.

## 세마포어

- 위 방식들을 추상화시킴
- 정수타입 변수를 가진다.
- 아래의 두가지 아토믹 연산에 의해서만 접근 가능
    - P(S)연산: while문을 S가 0이하인 동안 계속 돌면서 기다린다. 0이상이 되면 하나 빼기
    - V(S)연산: S 1 더하기

- 세마포어를 사용하면 세마포어 mutex 값을 1로 지정한다.
- critical section 실행 전에 P연산을 하고, 실행 후에 V연산을 수행한다.
- while문 조건에서 S가 0이하인 동안 busy wait는 계속 진행된다.
- Lock을 얻지못하면 block상태로 잠들어버리게한다.

### Block/Wakeup Implementation

- Block
    - 커널은 block을 호출한 프로세스를 suspend 시키고, 이 프로세스의 PCB를 세마포어에 대한 wait queue에 넣는다.
- WakeUp: 블락된 프로세스 P를 wake up 한다. 해당 프로세스의 PCB를 ready queue로 옮긴다.

### Implementation

- P연산: 자원을 획득하는 과정
    - S값을 뺀 뒤, 자원 여분이 있으면 획득, 자원이 없으면 블락상태인 것
- V연산: S값을 더하고 지원이 있으면 획득, 자원이 없는데 S값이 음수면 블락상태 인 것이므로 깨워준다.

→ S값이 음수면 누군가가 자원을 기다리는 block상태인 것이고, 양수면 자원의 여분이 있다는 것

## 뭐가 좋을까?

Busy-wait vs Block/wakeup

- 보통  Block/wakeup이 효율적이다. 자원을 의미없이 쓰는 경우가 적기 때문이다.
- 하지만  Block/wakeup도 block과 ready의 반복 과정에서 오버헤드가 발생한다.
- critical section이 매우 짧으면 Block/wakeup의 오버헤드가 Busy-wait 방식보다 커질 수 있음
- 따라서 critical section이 길면 Block/wakeup이 적당하고, 짧으면 Busy-wait방식이 적당하다.

## 두가지 세마포어

- Counting semaphore
    - 도메인이 0이상인 임의의 정수값
    - 주로 리소스 카운팅에 사용
- Binary semaphore(=mutex)
    
    

## 데드락과 기아현상

- 데드락: 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
- 기아현상: 프로세스가 서스팬드된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
