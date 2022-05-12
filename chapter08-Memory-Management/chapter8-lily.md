# Chapter 8️⃣ Memory Management

# Memory address

## Logical 🆚 Physical Address
- **Logical address (= virtual address)**
    
    각 프로세스마다 독립적으로 가지는 주소 공간
    
    각 프로세스마다 0번지 부터 시작
    
    CPU가 보는 주소는 logical address임
    
- **physical address**
    
    메모리에 실제 올라가는 위치
    

- **주소 바인딩**
    
    논리적 주소를 물리적 주소로 변환하는 과정을 주소바인딩이라고 함
    
- **Symbolic Address** : 프로그래머는 숫자로 된 주소를 사용하지 않음. 프로그래머가 사용하는 함수, 변수명을 의미적 주소라고 함

## 논리적 주소를 물리적 주소로 변환하는 방법 3️⃣가지
### 1️⃣ Complie time binding

- 물리적 메모리 주소가 컴파일 시점에 정해짐
- 그래서 시작 위치가 변경될 때 컴파일을 다시 해야함. 따라서 비효율적 (현재는 잘 사용하지 않음)
- 이렇게 생산된 코드를 absoulte code(절대코드)라고 함

### 2️⃣ Load time binding

- 컴파일 타임에는 논리적 주소까지만 결정
- 런타임에 동적으로 물리적 주소를 결정함 (주소 바인딩)

### 3️⃣ Run time binding (=Execution time binding)

- 런타임에 주소 바인딩
- 실행중에도 물리적 주소가 바뀔 수 있음.
- CPU가 주소를 참조할 때 마다 binding을 점검 (address mapping table)
- 현대의 컴퓨터가 사용하는 방법
- 하드웨어적인 장치가 필요 (MMU)

> **MMU(Memory-Management Unit)**
>logical address를 physical address로 매핑해주는 hardware device
>
>**💟 MMU scheme**
> 사용자 프로세스가 CPU에서 수행되며 생성하는 모든 주소값(logical address) + base register를 해서 physical address를 구함.
> 
> - relocation register (base register) : 해당 프로세스가 사용하는 물리적 주소의 시작 위치
> - limit register :  논리적 주소의 크기(범위), 악의적 프로그램으로부터 다른  프로그램의 메모리를 접근하지 못하도록 하기위해.
> 
> 따라서 사용자 프로그램은 logical address만 알면되며, 실제 physical address는 볼 수 없으며 알 필요도 없게 된다.


# 몇 가지 용어

### 1️⃣ Dynamic Loading
- 프로세스 전체를 미리 다 올리는 것이 아닌, 해당 루틴이 불려질 때 루틴을 처리하는 코드를 메모리에 로드하는 것.
- 운영체제가 제공하는 라이브러리를 사용해서 개발자가 직접 구현  (paging은 운영체제가 하는 것, 구분됨)
- 운영체제가 자율적으로 프로그램을 메모리 올리고 내리고 하는 것을 지칭할때도 용어를 사용하기도 함

### 2️⃣ Overlays
- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 운영체제의 지원이 없음. (dynamic loading과의 차이점)
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현, 따라서 프로그래밍이 매우 복잡함

### 3️⃣ Swapping
- 프로세스를 일시적으로 메모리에서 backing store, swap area(하드 디스크)로 쫓아내는 것
- 중기 스케줄러는 swap out할 프로그램을 결정함 
- **swap in** : backing store에서 메모리로 프로세스를 불러오는 것
- **swap out :** 메모리에서 backing store로 프로세스를 쫓아내는 것
    - priority가 낮은 프로세스를 swapped out시킴
    - priority가 높은 프로세스를 메모리에 올려 놓음
- swapping이 효율적으로 되려면 run-time binding이 적합함.
- swap time은 transfer time임 (swap되는 양에 비례하는 시간)

보통 시스템에서는 디스크 헤드가 이동하는 시간인 seek time이 대부분을 차지하지만, swapping시스템에서는 양이 많기 때문에 transfer time이 더 비율을 더 많이 차지함

### 4️⃣ Dynamic Linking
- Linking을 런타임(execution time)에 하는 방법
- 라이브러리 코드가 실행 파일에 포함되지 않고, 실행 시 라이브러리 호출이 일어날 때 stub을 참고해서 라이브러리의 위치를 찾음
    - stub : 라이브러리 루틴의 위치를 찾기 위한 코드
- 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고, 없으면 디스크에서 읽어옴
- dynamic linking을 하는 라이브러리를 shared library라고 부름.

> **Static linking**
> 
> 라이브러리가 프로그램의 실행 파일 코드에 포함되는 방법
> 실행 파일의 크기가 커짐
> 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비가 일어남.
