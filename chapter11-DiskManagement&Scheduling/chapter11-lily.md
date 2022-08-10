# Disk Management

## Disk Structure

- **logical block**
    - 디스크 외부에서 보는 디스크의 단위 정보 저장 공간들
    - 주소를 가진 1차원 배열처럼 취급
    - 정보를 전송하는 최소 단위
- **sector**
    - logical block이 물리적인 디스크에 매핑된 위치
    - 디스크를 관리하는 최소 단위
    - sector0은 최외관 실린더의 첫 트랙에 있는 첫 번째 섹터 (boot에 사용)
- logical block과 sector는 매핑되어 있음

## Disk Managemnet

- **physical formatting** (Low-level formatting)
    - 디스크를 디스크 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    - 각 섹터는 `header` + `실제 data`(보통 512byte) + `trailer` 로 구성
    - header와 trailer는 sector number, ECC(Error-Correcting Code)등의 정보가 저장, 디스크 컨트롤러가 직접 접근 및 운영
    - ECC 는 데이터의 요약본 (hash함수 적용)
    - header, trailer를 같이 읽어서 data의 결함이 있는지 확인 가능, ECC 용량에 따라 수정도 가능
- **Partioning**
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정 (예로 C, D 드라이브 나누는 것) 
    - OS는 이것을 독립적 disk로 취급 (logical disk)
- **Logical formatting**
    - 파일 시스템을 만드는 것
    - FAT, incode, free space등의 구조 포함
- **Booting**
    - ROM(비휘발성) 에 있는 `small boostrap loader` 의 실행
    - sector 0(boot block)을 load하여 실행
    - sector 0은 `full boostrap loader program`
    - OS를 디스크에서 load하여 실행

## Disk Scheduling

- **Access time**의 구성
    - `Seek time`
        - 헤드를 해당 실린더(트랙)로 움직이는데 걸리는 시간
        - 가장 큰 시간 구성 요소
    - `Rotational latency`
        - 헤드가 원하는 섹터에 도달하기 까지 걸리는 회전 지연 시간
    - `Transfer time`
        - 실제 데이터의 전송 시간 (read/write)
- **Disk bandwidth**
    - 단위 시간당 전송된 바이트의 수
- **Disk Scheduling**
    - seek time을 최소화하는 것이 목표
    - seek time = seek distance

# Disk Scheduling Alogorithm

논리 블럭에 매핑된 섹터를 참고

## 1. FCFS (First Come First Service)

- 큐에 먼저 들어온 요청 부터 처리
- 매우 비효율적

## 2. SSTF (Shortest Seek Time First)

- 현재 헤드 위치에서 가장 가까운 요청을 처리
- stravation 문제

## 3. SCAN

- 엘리베이터 스케줄링
- 디스크의 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동
- 비교적 공정, 헤드 이동 시간 효율적
- 문제점 : 실린더 위치에 따라 대기 시간이 다름 (가장자리에 있으면 불합리)

### 4. C-SCAN

- Circular-Scan
- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- 이동거리는 길어지지만, SCAN보다 균일한 대기 시간을 제공

### 5. N-SCAN

- SCAN의 변형 알고리즘
- 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service

### 6. LOOK and C-LOOK

- SCAN과 달리 헤드가 진행중이다가 그 방향에 더 이상 기다리는 요청이 없을시 즉시 반대 로 이동

## 알고리즘의 결정

- SCAN, C-SCAN, LOOK, C-LOOK이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인것으로 알려짐
- File 할당 방법에 따라 디스크 요청이 영향을 받음
- 따라서 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 **OS와 별도의 모듈**로 작성되는 것이 바람직

## Swap-Space Management

- Disk를 사용하는 두 가지 이유
    1. memory의 volatile한 특성 → file system에 저장
    2. 프로그램 실행을 위한 memory 공간 부족 → swap space(space area)로 사용
- **Swap-space**
    - Virtual memory system에서는 디스크를 memory의 연장 공간으로 사용
    - 파일시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
        - 공간 효율성보다는 속도 효율성이 우선
        - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
        - 따라서 block의 크기 및 저장 방식이 일반 파일 시스템과 다름 (용량이 큼)

## RAID

RAID (Redundant Array of Independent Disks)

- 여러 개의 디스크를 묶어서 사용
- 사용 목적
    - 디스크 처리 속도 향상
        - 여러 디스크에 block의 내용을 분산 저장
        - 병렭적으로 읽어 옴(interleaving, striping)
    - 신뢰성 향상
        - 동일 정보를 여러 디스크에 중복 저장
        - 하나의 디스크가 고장시 다른 디스크에서 읽어옴(Mirroring, shadowing)
        - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성 높일 수 있음
