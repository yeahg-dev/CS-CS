# 컴퓨터 시스템 구조

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/18bff22c-3a3a-4afb-ab60-78027292c763/Untitled.png)

## 컴퓨터의 구조

- CPU
    - 레지스터
    - mode bit: cpu 사용의 주체를 구분한다.
        
        0 - 모니터모드: OS코드 수행
        
        1 - 사용자모드: 사용자 프로그램 수행
        
    - interrupt line
- Memory
    
    CPU의 작업 공간
    
- timer
    - 하나의 프로그램이 cpu를 독점적으로 쓰지 못하게 제어한다.(타임 인터럽트)
    - 매 클럭 틱마다 1씩 감소한다.
    - 타이머 값이 0이 되면 타이머 인터럽트가 발생한다.
    - 타임 쉐어링을 구현하기 위해 이용한다.
    - 현재 시간 계산에도 이용한다.
    
- I/O device
    - device controller가 각각 붙어있어서 디바이스를 전담하는 각각의 CPU가 된다.
    - local buffer(기기의 작업공간)를 가진다.
    - 입력이 들어오면 cpu의 인터럽트라인에 전달되어 cpu가 권한을 갖는다.
    - 하드 디스크, 모니터, 키보드 등 입출력 기기가 이에 해당한다.

### 디바이스 드라이버?

디바이스가 작동하기 위한 별도의 코드를 가지고있다.

### 사용자프로그램의 입출력 방법?

1. 시스템콜: OS에게 입출력 요청(프로그램 → CPU → 입출력요청)
2. 트랩을 사용하여 인터럽트 벡터의 특정 위치로 이동
3. 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
4. 올바른 입출력 요청인지 확인 후 수행
5. 입출력 완료 시 제어권을 시스템 콜 다음 명령어로 이동

❓ 
**인터럽트 벡터?**  
해당 인터럽트의 처리 루틴 주소를 가지고 있음

**인터럽트 처리 루틴?** 
인터럽트 핸들러라고도 하며, 해당 인터럽트를 처리하는 커널 함수

**인터럽트?**  하드웨어 인터럽트

**트랩?**  소프트웨어 인터럽트


### DMA 컨트롤러

- cpu와 마찬가지로 메모리에 접근할 수 있다.(메모리 컨트롤러가 중간에서 제어)
- cpu는 자기 일을 하고 있고 I/O의 작업은 DMA컨트롤러가 관리하며 메모리 작업을 한다.
- 입출력으로 인한 cpu의 잦은 인터럽트 방지 역할을 한다.



## 동기식 입출력 / 비동기식 입출력

### 동기식

→ 입출력 작업이 완료된 뒤 제어가 사용자 프로그램에 넘어감

- 방법 1
    - 입출력이 끝날때까지 씨피유를 낭비시킴
    - 매 시점 하나의 입출력만 일어날 수 있음
- 방법 2
    - 입출력이 완료될 때까지 프로그램에게서 씨피유를 빼앗음
    - 입출력 처리를 기다리는 대기열에 프로그램을 줄세움
    - 다른 프로그램에게 씨피유를 줌
    

### 비동기식

입출력 시작후 끝나기를 기다리지 않고, 제어가 사용자 프로그램에 넘어감

### DMA

- 빠른 입출력장치를 메모리에 가까운 속도로 처리할 수 있도록 사용한다.
- CPU의 중재 없이 디바이스 컨트롤러가 디바이스의 버퍼 내용을 메모리에 올릴 수 있게 한다.

## 저장장치 계층 구조

### 휘발성

- 씨피유
- 레지스터
- 캐시메모리
- 메인메모리

### 비휘발성

- 자기디스크
- 광디스크
- 자기테이프

→ 상단으로 갈수록 빠르고 용량이 적고 비싸다.

## 프로그램의 실행 과정(메모리 로드)

파일시스템의 실행파일 → 가상메모리 → (주소변환) → 물리메모리

❓ 
**가상메모리?**
0번지부터 시작하는 프로그램만의 독자적인 주소 공간


## 커널 주소공간의 내용

### 코드

- 시스템콜 인터럽트 처리 코드
- 자원관리 코드
- 편리한서비스 제공 코드

### 데이터

- 운영체제가 사용하는 자료구조들 정의

### 스택
