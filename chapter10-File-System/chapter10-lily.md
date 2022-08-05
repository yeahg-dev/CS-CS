## File and File System

- `File`
    - “A named collection of related information” 이름을 통해서 접근
    - 일반적으로 비휘발성의 보조기억장치에 저장
    - 운영체제는 다양한 저장 장치(디스크)를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌 (device specific file)
    - Operation
        - create, read, write, reposition(lseek), delete, open, close등
- `File metadata (file attribute)`
    - 파일의 자체 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자등
- `File system`
    - 운영체제에서 파일을 관리하는 부분
    - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
    - 파일의 저장 방법 결정
    - 파일 보호등

## Directory and Logical Disk

- `Directory`
    - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 file
    - 그 디렉토리에 속한 파일 이름 및 파일 attribute들
    - operation
        - search for a file, create a file, delete a file
        - list a directory, rename a file, traverse the file system
- `Partition (= Logical Disk)`
    - 하나의 물리적 디스크 안에 여러 파티션을 두는게 일반적
    - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
    - 목적 :
      - 각각의 파티션에 `file system` 을 깔거나, `swapping` 등 다용도로 사용할 수 있음
      - 별도의 디스크 저장장치처럼 구분해서 사용 가능. 따라서 서로 다른 OS가 같은 디스크를 사용할 수도 있음. 운영체제가 각 파티션을 별도의 디스크로 취급하기때문


## `open()` : 파일의 metadata를 메모리에 올리는 함수

- 루트 디렉토리의 위치는 미리 알려져 있음.
- `open()` 은 시스템콜임
- 각 디렉토리의 metadata를 순차적으로 메모리에 올려와서 최종 파일의 위치를 얻어내는 방식. `file descriptor` 를 리턴
- 읽어온 디렉토리의 metadata들은  `system-wide open file table`에 저장됨
- 각 프로세스마다 오픈한 파일들의 디스크 포인터를  PCB에 배열(`per-process file descriptor table`)로 저장해둠.  따라서 후엔 `index`만 알면 PCB에서  `file descriptor` → `open file table` 을 통해 파일의 위치정보를 읽어와서 빠르게 read/write 요청을 할 수 있음
- 추가적으로 프로세스마다 `file offset` 이 필요함. 접근 중인 파일 위치 정보를 저장하기 위함. `file offset table` 을 별도로 둠
- 파일을 읽어와서 먼저 운영체제의 공간에 올린 후, 카피를 사용자 프로그램에게 전달. (buffer caching)
- buffer cahce는 운영체제가 파일에 접근한 프로세스의 수와 같은 정보를 알고 있기 때문에 LRU, LFU 알고리즘을 적용 가능

## 파일 보호 : 접근 권한을 어떻게 표현할 것인지

> 각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?
> 
- `Access Control Matrix`
    - 메모리 낭비적이라서 주체별로(사용자/파일) linked-list로 만들어서 관리
    - `Access control list` : 파일별로 누구에게 어떤 접근 권한이 있는지 표시
    - `Capabilty` : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시
- `Grouping` ✅ 운영체제가 사용하는 방법
    - 전체 user를 owner, group, public의 세 그룹으로 구분
    - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
- `Password`
    - 파일마다 password를 두는 방법(디렉토리 파일에 두는 방법도 가능)
    - 모든 접근 권한에 대해 하나의 password: all or nothing
    - 접근 권한 별 password : 암기 문제, 관리 문제
  
## File System의 Mounting

> 다른 파티션의 파일 시스템에 접근하는 방법 → Mounting 기법
> 

루트 파일 시스템에 다른 파티션의 파일 시스템의 `루트`를 이어둠


## Access Methods : 파일 접근 방법

- 순차 접근 (sequential access)
    - 카세트 테이프 방식처럼
    - 읽거나 쓰면 offset은 자동적으로 증가
- 직접 접근 (direct access, random access)
    - LP 레코드 판과 같이 접근하도록 함
    - 파일을 구성하는 레코드를 임의의 순서로 접근 가능
    - 직접 접근이라도 순차 접근만 가능하도록 설계 가능

<br>

# File System Implementation

## Allocation of File Data in Disk

파일데이터를 디스크에 어떻게 할당할지 방법들!

- 디스크에 저장할 때는 동일한 섹터 단위(= 논리적 블록)로 나누어 저장

### 1️⃣ Contiguous Allocation

연속한 섹터에 저장하는 것

장점

- 빠른 I/O가 가능
    - 접근 시간의 대부분 디스크헤드 이동시간이 차지
    - 한번의 seek/rotation으로 많은 바이트 transfer
    - realtime file용으로 (데드라인이 있고, 빠른 I/O가 필요한 작업)
    - 또는 이미 run중이던 process의 swapping용 (프로세스의 주소 공간, 공간효율성보다 시간효율성이 더 중요)
- Direct access(=random access)가능

단점

- 외부 조각 발생
- 파일의 크기를 증가시키기가 어려움(file grow)
    - 파일 생성시 얼마나 큰 hole을 배당할 것인가
    - grow 가능성을 고려해서 넉넉하게 공간을 할당해두면 낭비가 발생 (내부조각 발생)

### 2️⃣ Linked Allocation

- 파일의 시작 위치만 디렉토리가 저장하고 있음
- 각 블럭은 `내용` + `다음 블럭의 위치`를 가지고 있음

장점

- 외부조각이 발생하지 않음

단점

- 직접 접근 불가, 순차 접근만 가능
- Reliability 문제
    - 한 sector가 고장나서 pointer가 유실되면 나머지 부분을 잃음
    - Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림 512byte/sector, 4byte/sector

변형

- File-allocation table(FAT) 파일 시스템
    - 포인터를 별도의 위치에 보관하여 reliabilty와 공간 효율성 문제 해결

### 3️⃣ Indexed Allocation
- 파일 조각 저장되어있는 위치와 순서를 저장하는 index block을 디렉토리가 관리

장점

- 외부 조각이 발생하지 않음
- 직접 접근이 가능

단점

- 아무리 작은 파일이라도 블럭이 2개가 필요 (파일이 작은 경우 공간 낭비, 그런데 실제 많은 파일들이 작음)
- 큰 파일의 경우 하나의 블럭만으로 index를 저장하기 부족
    - 해결 방안
    - linked scheme : index를 연결
    - multi-level index : 인덱스가 또 다른 인덱스 블럭을 가리키기 하는 것 (2단계 paging과 같은 원리)

<br>

# 실제 파일 시스템의 구현

### 1️⃣ UNIX 파일 시스템의 구조

- **Boot block**
    
    부팅에 필요한 정보(boostrap loader), 어떠한 파일 시스템을 사용하던 0번 block은 부팅에 필요한 정보를 저장하도록 약속
    
- **Super block**
    
    파일 시스템에 관해 총체적인 정보를 담고 있음
    
    (예. 각 블럭들의 range)
    
- **Inode list**
    
    파일 이름을 제외한 파일의 모든 메타 데이터를 저장
    
    `inode` 가 핵심!
    
- **Data block**
    
    파일의 실제 내용을 보관
    

- 파일의 위치는 어떻게 저장?
    - Indexed allocation 방법
    - 파일의 크기가 작으면 direct block에 index 저장 (직접 접근 가능)
    - direct block에 저장할 수 있는 크기보다 클 때는 single indirect, double indirect, triple indirect 사용
    - 대부분의 파일은 작기 때문에 효율적인 방법

### 2️⃣ FAT File System

- Window 일부나 모바일 시스템에서 사용함
- Boot block
- FAT
    - 파일의 메타 데이터의 일부(위치 정보)를 저장
    - 데이터 블럭의 다음 위치 정보를 table로 저장
    - 중요해서 2카피 이상을 저장한다고
- Root directory
- Data block
    - 디렉토리가 접근 권한, 소유주, 파일의 이름, 첫 번째 위치 저장
    - linked allocation으로 저장, 하지만 직접 접근 가능 (FAT이 메모리에 올라가 있기 때문에 중간의 블럭의 위치 정보를 빠르게 얻을 수 있음)

## Free-Spcae Management

### Bit map or bit vector

- 블럭의 사용 여부를 1과 0으로 표시
- bit map은 부가적인 공간을 필요로함
- 연속적인 n개의 빈 블럭을 찾은데 효과적

### Linked list

- 비어있는 블럭을 Linked list로 관리
- 첫번째 블럭의 주소만 알고 있으면 됨. 따라서 공간 낭비가 없음
- 연속적인 공간을 찾는 것은 비효율적 (실제로 사용하긴 좀..)

### Grouping

- llinked list의 변형
- 첫번째 빈 블럭이 n개의 포인터를 가짐

### Counting

- 빈 블럭의 첫번째 위치, 연속적으로 몇개가 빈 블럭인지 갯수를 저장
- 연속적인 빈 블럭을 찾기 효율적

<br>

## Directory Implementation

- linear list
    - (파일 이름, 파일의 메타데이터)를 리스트 형식으로 저장
    - 구현이 간단
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요(time-consuming)
- hash table
    - linear list + hashing
    - 파일명을 hash함수로 변환, linear list의 위치로 바꾸어줌
    - 검색 시간을 없앰
    - collision 발생 가능

- File의 metadata 보관 위치
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - inode, FAT등
- 긴 파일 이름의 지원
    - entry는 일반적으로 고정 크기
    - entry보다 긴 파일 이름은 entry마지막 부분에 이름의 뒷 부분이 위치한 곳의 포인터를 저장
    - 이름의 나머지 부분은 동일한 directory file의 일부에 존재

<br>

## VFS and NFS

- VFS(Virtual File System)
    
    사용자가 파일 시스템의 종류에 상관없이 동일한 인터페이스를 사용할 수 있도록 지원
    
- NFS(Network File System)
    - 분산 시스템에서는 네트워크를 통해 파일이 공유 될 수 있음
    - NFS는 분산 환경에서의 대표적인 파일 공유 방법
    - 서버와 클라이언트에 모두 필요

<br>

## Page Cahce and Buffer Cache

### Page Cache

- Virtual memory에서의 page frame을 caching의 관점에서 설명하는 용어
- Memory-mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용
- 프로세스의 주소공간을 구성하는 page가 swap area에 있냐 page cache에 있냐

### Buffer Cache

- 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
- File 사용의 locality 활용
    - 한 번 읽어본 block에 대한 후속 요청시 buffer cahce 사용
- 모든 프로세스가 공용으로 사용
- 대체 알고리즘 필요 (LRU, LFU등)

### Unified Buffer Cache

- 최근 OS에서는 기존의 buffer cache가 page cache에 통합됨

### Memory-Mapped I/O

- file의 일부를 virtual memory에 mapping 시킴
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게함
