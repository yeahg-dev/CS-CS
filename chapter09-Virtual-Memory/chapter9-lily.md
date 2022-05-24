# Chapter 9️⃣ Virtual Memory

현대 컴퓨터에서는 대부분 paging기법을 사용함

프로그램의 모든 page를 메모리에 올려두지 않고, 실제로 필요할 때 page를 메모리에 올리는 방식을 사용.

## 1️⃣ Demand Paging

> 실제로 필요할 때 Page를 메모리에 올리는 것

- I/O 양의 감소
- Memory 사용량 감소
- 빠른 응답 시간 (디스크 I/O 감소에 따른)
- 더 많은 사용자 수용

**Valid / Invalid bit의 사용**

- `Invalid` 의 의미
    - 사용되지 않는 주소 영역인 경우
    - 페이지가 물리적 메모리에 없는 경우
- 처음에는 모든 page entry가 invalid로 초기화
- address translation 시에 `invalid bit` 으로 설정되어 있으면 ⇒ “page fault” 라고 함

### Page Fault

: 요청한 페이지가 메모리에 없는 경우 

> 운영체제가 page fault에 대한 처리 루틴(page fault handler)를 수행

1. invalid page를 접근하면 MMU가 trap을 발생 시킴. (page fault trap)
2. 커널모드로 들어가서 **page fault handler**가 invoke됨
3. 다음과 같은 순서로 처리
    1. invalid reference (bad address, protection violation)? 유효한 참조인가를 확인 
        
        → 유효하지 않으면 프로세스 중단
        
    2. empty frame을 얻어옴 (없으면 뺏어옴: replace)
    3. 해당 page를 디스크에서 메모리로 읽어옴
        1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt당함 (block됨)
        2. disk read가 끝나면 page table entry를 기록. 그리고 invalid bit ➡️ valid bit 변경
        3. read queue에 process를 insert → dispatch later
    4. 프로세스가 다시 CPU를 얻고 runnning
    5. 중단되었던 instruction을 재개시킴!
  

### Demand Paging의 성능

Page Fault Rate 0 ≤ p ≤ 1.0

p = 0 : page fault가 없다

p = 1 : 참조할 때마다 page fault가 있다.


### Free frame이 없는 경우

- Page replacement
    - 어떤 frame을 빼앗아 올지 결정해야함
    - 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
    - 동일한 페이지가 여러번 메모리에서 쫓겨났다가 다시 들어올 수도 있음
- Replacement Algorithm
    - 🌟 **page fault rate를 최소하는 것이 목표**
    - 알고리즘의 평가
        - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사함으로써

> 운영체제의 역할 : **page fault rate를 0에 가깝도록하는 것이 목표.**

### Replacement Algorithm

1. **Optimal Algorithm**

> 가장 최적의 알고리즘, 미래의 참조를 안다고 가정함

어떠한 알고리즘도 Optimal Algorithm보다 더 좋은 알고리즘을 만들 수 없음.

**2. FIFO Algorithm**

> 먼저 들어온 것을 먼저 내쫓는 방법
> 
- FIFO Anomaly (Beleady’s Anomaly)
    
    page frame이 늘어나면 page fault rate가 높아짐, 성능이 안좋아지는 이상한 경우가 있음
    

**3. LRU(Least Recently Used) Algorithm**

> 가장 오래 전에 참조된 것을 지우는 방법
> 

linked list로 구현

**4. LFU(Least Frequently Used) Alogorithm**

> 참조 횟수가 가장 적은 페이지를 지우는 방법

최저 참조 횟수 page가 여러개 있는 경우

- LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다.
- 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있다.
- 장단점
    - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있다.
    - 참조 시점의 최근성을 반영하지 못한다
    - LRU보다 구현이 복잡하다.
