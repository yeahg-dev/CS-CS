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

# 물리적 메모리 관리 방법

- 낮은 주소영역 - 커널 코드
- 높은 주소영역 - 사용자 프로세스 영역

크게 2가지 기법으로 분류 가능

1. 연속 할당 
2. 불연속 할당 (✅ 현대에 사용)

## 1. 연속 할당 (Contignous Allocation)

- 고정 분할 방식
- 가변 분할 방식

### 1️⃣ 고정 분할 방식

> 사용자 프로그램이 들어갈 메모리 영역을 미리 분할해둠

- 내부 조각, 외부 조각 발생 가능
- 분할당 하나의 프로그램 적재
- 융통성이 없음
    - 동시에 메모리에 load되는 프로그램의 수가 고정됨
    - 최대 수행 가능 프로그램 크기 제한

> 외부조각 / 내부조각
> 
> - **외부 조각 (external fragment)**
>     
>     프로그램 크기보다 분할의 크기가 작아서 사용이 되지 않은 공간
>     
> - **내부 조각 (internal fragment)**
>     
>     프로그램에게 할당은 되었지만, 프로그램의 크기가 분할의 크기보다 작아서 사용이 되지 않은 공간

<br>

### 2️⃣ 가변 분할 방식

- 프로그램 실행 순서대로 메모리에 차곡차곡 쌓아서 올리는 방식
- 프로그램의 크기가 다 다르기 때문에 사용하지 못하는 외부 조각이 발생하게 됨

### **Hole**

> 가용 메모리 공간

- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
- 운영체제는 다음의 정보를 유지 - 할당 공간 / 가용 공간(hole)
- hole을 사용하기 위한 방법, 프로세스가 도착하면 수용가능한 hole을 할당 ⬇️

### Hole에 프로그램을 할당하는 방법 3️⃣가지 (Dynamic Storage-Allocation Problem)

: 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

1. **First-fit**
    - 사이즈가 n 이상인 것 중 **최초**로 찾아지는 hole에 할당
2. **Best-fit**
    - 사이즈가 n이상인 **가장 작은** hole을 찾아서 할당
    - hole의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트 탐색해야함
    - 많은 수의 아주 작은 hole들이 생성됨
3. **Worst-fit**
    - 가장 큰 hole에 할당
    - 모든 리스트를 탐색해야함
    - 상대적으로 아주 큰 hole들이 생성됨

🦁  실험적으로 first- fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐

### Compaction

> hole들을 묶어서 한군데로 밀어서 아주 큰 hole을 만들어서, 외부조각 문제를 해결하는 한 가지 방법

- 비용이 많이 드는 복잡한 방법
- 런타임 바인딩이 지원되어야만 수행 가능

<br>

## 2. 불연속 할당

💡 현대 컴퓨터에서 사용하는 메모리 할당 방법

### 1️⃣ Paging 기법
> 프로그램을 구성하는 virtual memory를 동일한 크기의 page로 자름,
virtual memory의 내용이 page단위로 비연속적으로 저장됨
일부는 backing storage에, 일부는 physical memory에 저장
> 
- 물리적 메모리를 동일한 크기의 `frame` 으로 나눔
- 논리적 메모리를 동일한 크기의 `page` 으로 나눔 (`frame`과 같은 크기)
- 주소바인딩이 복잡해짐... (page table이용)
- 외부조각 발생 안함
- 내부조각 발생 가능

### 📑 page table

> 논리적 메모리의 `page`가 각각 물리적 메모리의 몇 번 `frame`에 올라가있는지 표현
> 
- page table은 main memory에 저장
- 각 프로그램 마다 page table이 있음, 그리고 용량이 큼
- `Page-table base register(PTBR)` 가 page table을 가리킴
- `Page-table length register(PTLR)` 가 테이블 크기를 보관
- 모든 메모리 접근 연산에 **2번의 메모리 접근**이 필요
    - page table에 접근, 실제 메모리에 접근
    - 두번 접근한다는 것은 **시간적 비용이 큼**
- 속도 향상을 위해 `associative register` 혹은 `translation look-aside buffer(TLB)`라 불리는 고속의 lookup hardware cahce 사용
- 프로그램이 실제로 사용하는 코드영역은 일부분임에도 모든 공간에 대한 page table을 만들어야하는 것이 비효율적, **공간적 오버헤드가 큼** ➡️ 이를 2단계 page table이 해결

### Associative Register (TLB)

- 빠른 접근을 위해 **parallel search**가 가능
- TLB에는 page table 중 일부만 존재
- 만약 해당 page가 associative register에 있는 경우 곧바로 frame을 얻음
- 그렇지 않은 경우, main memory에 있는 page table로부터 frame을 얻음.
- TLB는 context switch때 flush

### 📑 📑 Two-Level Page Table

> ✅ 개선 : page table의 공간을 줄임으로써 해결!! (사용되는 메모리 주소에 대한 Page table만 생성해서 메모리에 올려둠) → `Outer page table` 도입
👻 단점 : 여러번 page table에 접근해야하기 때문에 **시간적 오버헤드가 생김
➡️ TLB**

- page table 자체를 Page로 구성
- 사용되지 않는 주소 공간에 대한 outer page table의 엔트리값은 NULL, 대응하는 inner page table은 없음
- 즉, outer page table은 대응되는 논리적 메모리크기 만큼 만들어지지만, 사용이 안되는 주소에 대한 inner page table은 만들어지지 않음
- `outer page table`: page table의 page와 매칭되는 index 저장
- `inner page table`: 물리적 주소에 대한 frame의 위치에 대한 정보 저장
- `page offset` : frame으로부터 offset

### 🚀 **Multilevel Paging and Performance**

- 2단계 이상의 page table을 사용할 수도 있음
- address space가 더 커지면 다단계 페이지 테이블이 필요하게 됨
- multi level 일수록 메모리 접근에 대한 **시간적 비용이 커짐** (예를 들어 4단계 page table의 경우, 주소 변환을 위해 4번, 실제 메모리에 접근 1번, 총 5번의 메모리 접근이 필요함)

> ✅ 개선 : **TLB**를 사용하면 오버헤드(접근 시간)를 줄 일 수 있음.

### ☑️ Memory Protection (feat. `bit`)

page table의 각 entry마다 저장되어 있는 부가적 정보

- `Protection bit`
    
    page에 대한 접근 권한, 연산에 대한 권한 표시(read/write/read-only)
    
    예를들어 code영역은 read-only로 지정
    
- `Valid-invalid bit`
    - `vallid` : 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함
    - `invalid` :  해당 주소의 frame에 유효한 내용이 없음을 뜻함 (접근 불허) , 프로세스가 그 주소 부분을 사용하지 않는 경우 / 해당 페이지가 메모리에 올라와 있지 않고 swap area(backing store)에 있는 경우

### ⤴️ Inverted Page Table

- page table의 문제점
    - **공간적 오버헤드가 크다**
    - 모든 프로세스별로 logical address에 대응하는 모든 page에 대 page table entry가 존재
    - 대응하는 page가 메모리에 있든 아니든간에 page table에는 entry가 존재해야함

> ✅ 개선 : 시스템안에 page table이 단 하나 존재. (공간을 적게 사용)  page table의 entry가 물리적 메모리의 frame의 갯수 만큼 존재
👻 단점 : 모든 entry를 검색해야하기 때문에 **시간적 오버헤드가 큼**
➡️ pararell search가 가능한 associative register 사용
> 
- logical address (`pid` 와 `page` `d`)가 주어지면, 모든 entry를 검색해야함.

### 📜 Shared Page

만약 여러개의 프로그램이 같은 코드로 프로그램을 돌릴때 

**Shared code 에 대해서는 1개만 메모리에 올리는 것.**

- **shared code**
    - Re-entrant Code ( = pure code)
    - read only로 셋팅해야함.
    - 모든 프로세스의 동일한 logical address space에 위치해야함.
- **Private code and data**
    - 각 프로세스들은 독자적으로 메모리에 올림

### 2️⃣ Segmentation

> 주소공간을 의미 단위로 자름 (예를 들어, 코드/데이터/스택, 함수단위)


크기가 균일하지 않음 

- logical address는 `segment-number`, `offset`으로 구성
- **segment table**
    - `base` : 물리적 주소에서 segment의 시작 위치
    - `limit` : segment의 길이, (의미 단위로 자르기 때문에 동일하지 않음)
- **segment table base register (STBR)**
    
    물리적 메모리에서의 segment table의 위치를 저장
    
- **segment table length register (STLR)**
    
    프로그램이 사용하는 segment의 수를 저장
    

**변환 과정**

1. STLR와 비교하여 프로세스의 유효한 세그먼트인지 확인
2. 세그먼트의 offset이 limit 보다 작은지 확인
3. 정상이라면 segment 시작위치(base) + offset 으로 주소 변환

### ☑️ Allocation

- segment의 길이가 균일하지 앟기 때문에 가변분할 방식에서와 동일한 외부조각이 생기는 문제점이 있음.
- first fit / bset fit과 같은 할당방식으로 해결

> 💡 의미단위로 적용을 해야할 경우 paging segment 방식이 적합함
> 

### ☑️ Protection

- 각 세그먼트 별로 protection bit이 있음
- 의미단위로 권한을 부여할 때 적합

### ☑️ Sharing

- shared segment
- same segment number

> 💡 테이블의 메모리 사용량은 Segment방식이 Paging기법보다 적음

<br>

### 3️⃣ Segmentation with Paging 
segment를 여러개의 Page로 구성되는 기법

<br>

---
### 메모리 관리에서 운영체제가 하는 역할은?
주소변환에 운영체제가 하는 역할이 없음.

모두 하드웨어가 해야하는 역할임.

