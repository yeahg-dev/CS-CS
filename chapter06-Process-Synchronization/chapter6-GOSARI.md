# Chapter 6-1. Process Synchronization

## Initial Attempts to Solve Problem

```swift
do {
	entry section
	critical section
	exit section
	remainder section
} while (1);
```

- critical section: 공유데이터에 접근하는  코드
- remainder section: 공유하지 않는 자원을 사용하는 코드
- entry section: 접근 프로세스에 락을 걸어줌으로써 여러 프로세스가 한 번에 접근하는 것을 방지하는 코드

`여러 프로세스가 동시에 공유데이터에 접근하지 못하도록 제어`하는 방법이 `동기화(Synchronization)`이며, 이를 방법으로 채택하기 위한 충족 조건으로는 아래 세 가지가 있다.

1. Mutual Exclusion(상호 배제)
    - 프로세스 A가 critical section을 수행중이라면 다른 모든  프로세스는 그들의 critical section에 들어갈 수 없다.
2. Progress(진행)
    - 모든 프로세스가 critical section에 들어가있지 않는 상태임이 확인된다면, 진입을 희망하는 프로세스를 critical section에 들어가도록 허용해주어야 한다.
3. Bounded Waiting(유한 대기)
    - starvation이 생겨서는 안된다. → 기다리는 시간이 무한해서는 안된다.

---

### Algorithm 1

변수 `turn`을 두고 어떤 프로세스의 차례인지를 저장하여 프로세스의 접근을 허용하는 알고리즘.

```swift
do {
	while (turn != 0);
	critical section
	turn = 1;
	remainder section
} while (1);
```

문제점: 다른 프로세스가 들어갔다 나와야지만 해당 프로세스가 들어갈 수 있다.

ex) 프로세스 1은  자주 접근하고  싶고, 프로세스 2는 그렇지 않다면 프로세스 1의 접근이 요원해진다. 충족 조건 중 Process를 만족시키지 못하기 때문에 적합한 알고리즘이라고 볼 수 없다.

### Algorithm 2

변수 flag로 해당 프로세스가 critical section에 들어가고자 하는지에 대한 의사를 boolean으로 확인하는 알고리즘. 다른 프로세스의 flag 상태를 확인하고 critical section에 진입한다.

```swift
do {
	flag[i] = true;
	while (flag[i]);
	critical section
	flag[i] = false;
	remainder section
} while (1);
```

문제점: 둘 모두가 깃발을 들었을 때, while 문을 돌면서 상대의 상태때문에 크리티컬 섹션에 들어가지 못한다. 결과적으로 아무도 들어가지 못하는 문제가 발생한다.

### Algorithm 3 (Peterson’s Algorithm)

변수 turn과 flag를 모두 활용하여 while의 조건으로 두 가지르 모두 확인하는 알고리즘.

```swift
do {
	flag[i] = true;
	turn = j;
	while (flag[i] && turn  == j)
	critical section
	flag[i] = false;
	remainder section
} while (1);
```

문제점: Busy Waiting(=Spin Lock)

- while 문 조건을 확인하고 크리티컬 락에 접근하느라 CPU 할당시간을 크게 할애해버린다.

---

## Synchronization Hardware

- 하드웨어적으로 하나의 인스트럭션만 주어지면 critical section 문제는 쉽게 해결된다.
- critical section 문제의 원인은, 데이터를 읽는 것과 쓰는 것을 하나의 인스트럭션으로 처리하 수 없었기 때문이다. 인스트럭션 하나를 가지고 데이터를 읽는 작업과 쓰는 작업을 동시에 할 수 있다면 크리티컬 섹션 문제는 해결된다.

---

## Semaphores

프로그래머의 입장에서 공유자원에 접근하기 위해 매번 위와 같은 작업을 설계해야 한다면 대단히 번거로워지기 때문에 프로그래머에게는 추상 자료형인 Semaphore를 제공한다.

Semaphore에는 `공유자원을 획득하는 P 연산`과 `반납하는 V 연산`이 존재한다.

### **P 연산**

```swift
while (S<=0) do no-op;  // e.wait
S--;
```

- S가 양수가 될 때까지 기다렸다가 while문에 입장하는 모습을 가진다.
- 여전히 Busy waiting 문제가 생길 수 있는데, S(자원)이 없을 때 무한정 기다려야 하는 문제가 발생하기 때문이다.
- 이를 해결하는 방법으로는 ~~while문을 계속 돌면서 기다리게 하는 것이 아니라,~~ 공유 데이터에 접근이 가능해질 때까지 `sleep`으로 재우는 것이 있다.이것을 `Block & Wakeup` 방법이다.

### Block & Wakeup

프로세스가 semaphore를 쓰고나서 반납할 때 block된 프로세스 중 하나를 wakeup으로 깨우는 방법.

```swift
typedef struct {
	int value;          // Semaphore
	struct process *L;  // process wait queue
} semaphore;
```

획득을 못한 친구들은 PCB를 통해 semaphore 변수 Queue 뒤에 줄세워둔다.

### Block & Wakeup을 이용한 P 연산과 V 연산

semaphore를 반납하는 것 뿐만 아니라, 기다리고 있던 프로세스 하나를 wakeup 시키는 것까지 순차적으로 진행

```swift
// P(S)
S.value--;
if (S.value <0) {
	add this process to S.L
	block();
}

// V(S)
S.value++;
if (S.value <= 0) {
	remove a process P from S.L
	wakeup(P)
}
```

보통은  block wakeup 방식이 busy  wait보다 효율적이다. 하지만 전자도 오버헤드가 든다는 것을 염두해야 한다.

- Counting Semaphore: 자원이 다수인 경우
- Binary Semaphore (=mutex): 자원이 딱 하나인 경우

### semaphore를 사용할 때 주의할 점

- Deadlock: 둘 이상의 프로세스가 서로  상대방에 의해 충족될 수 있는 event를  무한히 기다리는 현상.
    - 해결: 자원을 획득하는 순서를 둘 이상의 프로세스에게 공통되게 제공
- Starvation:  indefinite blocking. 프로세스가 suspend된 이유에 헤당하는 세마포어 큐에서 빠져나가  수 없는 현상.
