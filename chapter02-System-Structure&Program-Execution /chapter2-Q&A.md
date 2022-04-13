# 2. System Structure & Program Execution

운영체제를 공부하기 앞서,

하드웨어가 어떻게 동작하는지, 하드웨어 위에서 어떻게 소프트웨어가 돌아가는지 알아보는 챕터.

## 컴퓨터 시스템 구조

![image](https://user-images.githubusercontent.com/72993238/163132618-5cb31be2-212c-4c3c-a7c3-a91c038abd47.png)

![image](https://user-images.githubusercontent.com/72993238/163132753-dbb2841f-daec-409b-b9d3-6f15a5096862.png)

컴퓨터 → { CPU와 메모리 } 이를 호스트라고도 한다.

Disk를 포함한 키보드, 모니터, 프린터 등은 크게 분류하여 I/O Device에 속한다.

- 메모리: CPU의 작업 공간.
CPU는 매 클럭 사이클마다 메모리에서 instruction(기계어)를 하나씩 읽어서 실행.
하드웨어의 Instruction set(기계어 집합)에 따라 다르고, 약 4바이트 정도의 Instruction.
매  Instruction의 마지막엔 다음 Instruction의 메모리 주소가 있다.
- I/O Device
    - Input Device - 키보드
    - Output Device - 모니터, 프린터
    - 하드 디스크는 보조 기억장치이기도 하지만, Input 및 Output device로도 바라볼 수 있다.
    - 각각의 I/O Device들은 작은 CPU인 Device Controller가 붙어있어서, 이것에 의해 제어된다.
        - Device Controller도 CPU이기에 작업 공간인 메모리가 필요하다. 이를 Local Buffer라고 한다.
- CPU와 I/O Device는 작업 속도가 많이 차이난다.

- CPU 내부에는, Memory보다 빠르면서 작업을 저장할 수 있는 공간인 register가 있다.
- CPU 내부에 mode bit 이라는 것이 있는데, 현재 CPU에서 실행되는 것이 운영체제인지 사용자 프로그램인지를 구분해 주는 것.
- Interrupt line
    
    → I/O Device로부터 입력을 받았을 때 CPU가 알아챌 수 있는 방법.
    
- CPU는 메모리와만 소통할 뿐, 직접 I/O Device와 소통하지 않는다.
- Input이 필요할 때, CPU는 입출력장치의 device controller에 정보를 요청한 후, 응답이 돌아올 때 까지 놀고 있지 않고 다른 프로그램을 관할하게 된다.
- 특정 프로그램이 CPU를 독점하는 것을 막기 위해 시분할을 하는데,
    - 이 시분할을 위해 Timer라는 하드웨어가 필요하다.
    
    ### 시분할 시나리오
    
    - 한 프로그램(혹은 OS)가 다른 프로그로그램에 CPU를 넘겨줄 때, Timer에 아주 작은 시간을 세팅한 후 다른 프로그램에 CPU를 넘긴다. Timer에 설정된 시간이 끝나면, CPU에 Interrupt를 건다.
    CPU는 매 Instruction을 실행한 후 Interrupt Line을 체크한다.
    그래서, Timer로부터 Interrupt가 걸리면, 다른 프로그램으로 CPU를 다시 넘기는 구조이다.
    일반적으로, OS → 프로그램 A → OS → 프로그램 B ... 이런 식으로 OS돌려받고 다시 Timer 세팅 후 할당하는 과정이 반복되는 듯하다.
        - Timer Interrupt가 없더라도 I/O작업을 해야 할 떄는 즉시 자진하여 CPU를 OS에 넘긴다.
        각 프로그램은 직접 I/O에 접근할 수는 없기 때문. OS가 이를 요청받아 입출력장치에 작업을 요청하고, 응답이 오기 전까지는 다른 프로그램에 CPU를 넘기고, 아까 요청한 작업 응답이 돌아왔을때(==Interrupt가 걸렸을 때) 이를 필요로 한 프로그램의 메모리에 결과 데이터를 복사하여 넘겨주고, 아까 CPU를 자진반납한 프로그램에게 CPU를 다시 할당한다. 남은 시간만큼 프로그램이 실행된 후, 다시 OS에 CPU를 할당한다.
    
    ### Mode Bit
    ![image](https://user-images.githubusercontent.com/72993238/163132852-d19562da-b410-4de6-a01e-bbb5cd80172d.png)
    
    modeBit이 0일 때 == 모니터모드 == 커널 모드 == 시스템 모드
    
    → 모든 권한을 갖고 있음
    
    modeBit이 1일 때 → 권한 제어를 걸고 CPU를 사용자 프로그램에 넘김.
    
    ### 타이머
    ![image](https://user-images.githubusercontent.com/72993238/163132893-8162d52c-8c2b-4f2d-87df-902db74edc89.png)
     
    
    ### Device Controller
    ![image](https://user-images.githubusercontent.com/72993238/163132947-f0724866-0262-4834-a6ce-1ea994c94e36.png)
    
    ### -CPU가 너무 많은 Interrupt를 당하게 된다?
    
    → DMA Controller.
    
    - DMA Controller를 두어, CPU 뿐만 아니라 DMA 컨트롤러도 입출력장치의 로컬 버퍼에서 DMA가 데이터를 가져와  메모리에 접근할 수 있게 한다. 메모리에 모두 썼으면 그 때 CPU에 Interrupt를 걸면, CPU가 매번 로컬 버퍼에 접근하는 오버헤드를 줄일 수 있다. CPU와 DMA가 동시에 메모리에 접근할 때의 Race Condition을 제어하기 위해 Memory Controller가 존재한다.
    

## 입출력의 수행
![image](https://user-images.githubusercontent.com/72993238/163133012-3e1a24d6-cc7a-4273-91f0-17708294f6d0.png)

- 시스템 콜?
    - 사용자 프로그램이 일반적인 instruction이 아닌, OS에게 무언가를 요청하는 것. ( I/O 요청 등)
    - 일반적인 함수 호출과 달리, OS의 함수에 직접 접근할 수는 없기에, 자신의 Instruction 후에 Interrupt를 건다. (software interrupt == trap)

## Interrupt?

- 일반적으로 말하는 Interrupt는 입출력장치로부터 들어온 하드웨어 인터럽트.
- Timer로부터도 Interrupt가 온다. 시분할을 위함
- I/O를 요청하기 위해서도 사용자 프로그램이 직접 Interrupt를 거는데, 이를 소프트웨어 인터럽트, 혹은 트랩이라 함.
- “현대의 운영체제는 인터럽트에 의해 구동됨”
![image](https://user-images.githubusercontent.com/72993238/163133051-625883ad-aca5-4ff1-ba3c-3e778520aa13.png)

- 인터럽트에는 다양한 종류가 있고, 그 인터럽트마다 어떤 일을 해야할지가 각각 다른데, 이것을 인터럽트 처리 루틴이라 한다.
- 이것이 저장되어 있는 주소를 인터럽트 벡터라고 한다.

## 동기식 입출력과 비동기식 입출력
![image](https://user-images.githubusercontent.com/72993238/163133235-81dfc4cf-cce0-47e6-bda1-e25d83048e47.png)
![image](https://user-images.githubusercontent.com/72993238/163133269-8e29f209-5f4d-4459-a5ff-203fbb71ba10.png)

- 일반적으로 Read는 Synchronous 방식, Write는 Asynchronous하게 구현한다. (물론 다르게 구현할 수도 있지만.)

## DMA ( Direct Memory Access )

- 원래는 메모리와 통신할 수 있는 것은 오직 CPU.
- 워낙 많고 다양한 I/O, 각 I/O마다 워낙 빈번한 Input이 있기에, 매번 Interrupt를 걸고, CPU가 이를 처리하게 하면 막대한 오버헤드가 일어남.
- 이를 방지하기 위해서 DMA 가 로컬 버퍼를 CPU의 중재 없이 메모리로 직접 쓴다.
    ![image](https://user-images.githubusercontent.com/72993238/163133325-c6707778-0499-48de-b598-a5f82289d4db.png)
    
- 작업 완료 시에만 CPU에 인터럽트를 발생시킴.

## 서로 다른 입출력 명령어
![image](https://user-images.githubusercontent.com/72993238/163133383-68a17002-aeb2-424a-aaed-471b04227550.png)

- 메모리와는 별개로, 특별한 Instruction을 통해 I/O를 수행하는 방법과
- 입출력장치에도 메모리 주소의 연장선상의 주소를 부여하고, 메모리에 접근하듯 입출력장치에 접근하는 방식

으로 나뉜다. (Memory-Mapped I/O)

## 저장장치 계층 구조
![image](https://user-images.githubusercontent.com/72993238/163133439-77c2a1d5-c62c-4c76-af78-9bd3bc1676bc.png)

- 최근에는 플래시 메모리 방식으로 보조기억장치가 바뀌고 있음.(SSD)
- Primary 영역의 메모리들은 주로 휘발성, 보조 매체는 주로 비휘발성.
- Primary(Executable) 메모리는 바이트 단위 접근이 가능하여 실행 가능함
- Secondary 메모리는 섹터 단위 접근을 해야 하기에 실행 불가능함.
- 아래로 갈수록 처리 속도가 느리고, 위로 갈수록 빠르다.
- 위로 갈수록 용량이 작고, 아래로 갈수록 크다.
- 캐시 메모리는, 빠른 속도의 CPU를 느린 속도의 메모리 및 그 하위 저장장치와의 속도 보완을 위해 캐싱을 활용하는 계층이다.

## 프로그램의 실행(메모리 load)

- 보통 프로그램은 실행 파일 형태로 디스크에 저장되어 있는데, 실행 시에 이것이 메모리에 올라가서 프로세스가 된다.
- 메모리에 올라가기 이전에 Virtual Memory에 올라간다.
    - 각 프로그램마다 독자적인 코드, 데이터, 힙, 스택으로 이루어진 가상 메모리가 할당된다.
    
    이 가상 메모리가 물리적 메모리에 할당되는 것이다.
    
- 물리적 메모리의 효율적 운용을 위해, 가상 메모리의 꼭 필요한 부분(실행 중인 부분)만 물리적 메모리에 올라갔다가 해제되는 식으로 작동한다. 메모리에 올라가지 않은 부분은 디스크의 Swap area에 머문다.
    - 디스크의 Swap area는 주기억장치 용량의 한계를 극복하기 위한 보조 장치이지, 영구 저장을 위한 장치는 아니다.
    - 그렇기에 Swap area는 프로세스가 죽으면 쓸모없는 데이터가 된다.
- 각 프로그램마다 주어진 0번지부터의 주고가 있고, 물리적 메모리의 0번지 부터의 주소가 있다. 
가상에서 물리로 올라갈 때 **주소 변환**이 일어난다.
    - 이 메모리 주소 변환을 도와주는 하드웨어가 따로 있다.
- 커널 또한 하나의 프로그램이기에 가상 메모리 공간이 있다.
    ![image](https://user-images.githubusercontent.com/72993238/163133482-5e9a0614-82ce-4988-a62b-5fd73af26872.png)
    
- PCB - Process Control Block
    - 프로그램마다 이를 관리하기 위한 자료 구조가 필요함.

## 사용자 프로그램이 사용하는 함수
![image](https://user-images.githubusercontent.com/72993238/163133552-f4de1f46-2f55-470b-ae0b-13cf98c9e790.png)

함수는 기계어 단계로 내려가더라도 유지됨.

코드레벨을 벗어난 컴퓨터 구조를 논하더라도 함수라는 용어가 쓰이는 이유임.

## 프로그램의 실행
![image](https://user-images.githubusercontent.com/72993238/163133622-cf629a28-643a-4813-93f8-d18417e79745.png)

프로그램은 그 생애주기에서 유저 모드와 커널 모드를 계속 오간다.

CPU 관점에서는, 위 그림 이외에도 Timer Interrupt에 걸리면 커널 모드로 돌아왔다가 다른 프로그램에 CPU를 넘긴다.
