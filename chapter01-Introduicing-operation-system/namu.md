# 1강

# 운영체제란 무엇인가?

컴퓨터 하드웨어 바로 위 계층에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

1. 하드웨어와 어떻게 소통할것인가
2. 각종 소프트웨어나 사용자와 어떻게 소통할것인가

### 협의의 운영체제(커널)

- 운영체제의 핵심 부분으로, 부팅 이후에 항상 메모리에 상주하는 부분

### 광의의 운영체제

- 커널 뿐 아니라 각종 주변 시스템 유틸리티(필요한 최소한의 소프트웨어)를 포함한 개념
- 커널에는 포함되지 않는 독립적인 프로그램들.

→ 전공자 입장에서는 운영체제란 주로 커널을 일컫는다.

## 운영체제의 목적

1. **컴퓨터 시스템의 자원을 효율적으로 관리 → 자원 관리자.**
    - **자원이란? CPU(프로세서), Memory(기억장치), 입출력 장치 등(하드웨어 자원)**
        - 주어진 자원으로 최대한의 성능을 내도록 (효율성)
        - 사용자 간의 형평성 있는 자원 분배
    - 프로세스, 파일, 메시지 등(소프트웨어 자원)을 관리
    - 사용자 및 운영체제 자신의 보호
    
2. **컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공**
- 운영체제는 동시 사용자 / 프로그램들이 각각 독자적 컴퓨터에서 수행되는 것 같은 ‘환상'을 제공
- 하드웨어를 직접 다루는 복잡한 부분을 운영체제가 대행
    

## 운영체제의 분류 - 동시 작업 가능 여부

- 단일 작업(single tasking)
    - 한 번에 하나의 작업만 처리
    
    예) MS-Dos 프롬프트 상에서는 한 명령의 수행을 ㄲ씉내기 전에 다른 명령을 수행시킬 수 없음
    
- 다중 작업(multi-tasking)
    - 동시에 두 개 이상의 작업 처리
    - 예) UNIX, MS Windows 등에서는 한 명령의 수행이 끝나기 전에 다른 명령이나 프로그램을 수행할 수 있음

## 운영체제의 분류 - 사용자의 수

- 단일 사용자(single user)
    - 예) MS-DOS, MS Windows
- 다중 사용자(multi user)
    - 예) UNIX, NT server
    - 사용자 간의 형평성이나, 각 사용자 간의 보안 등을 고려함

## 운영체제의 분류 - 처리 방식

- 일괄 처리(batch processing)
    - 작업 요청의 일정량을 모아서 한꺼번에 처리
    - 작업이 완전 종료될 때까지 기다려야 함
    예) 초기 Punch Card 처리 시스템 / OMR 카드 채점기같은 방식
    - 현대 운영체제에서는 찾아보기 어려운 방식
    당시에는 여러 입력이 모일 때까지 기다렸다가 처리하는게 효율적이었음
- 시분할(time sharing)
    - 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
    - 일괄 처리 시스템에 비해 짧은 응답 시간을 가짐. 예) - UNIX
    - **interactive**한 방식
    사용자의 입력에 바로 반응하는 것처럼 보임. 하지만 응답이 일정하거나 데드라인이 지켜지지는 않음. 사람이 느끼기에 빠른(즉각적인) 응답처럼 보이도록 하는 것이 목적
- 실시간(Realtime OS)
    - 정해진 시간 안에 어떤 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
    - 특수한 목적을 위해 필요한 시스템
    - 예 - 원자로 / 공장 제어, 미사일 제어, 반도체 장비, 로보트 제어
- 실시간 시스템의 개념 확장
    - Hard realtime system(경성 실시간 시스템)
    → 위에서 설명된 데드라인이 엄격히 지켜져야 하는 목적
    - Soft realtime system
    → 영화 상영 등, 데드라인은 존재하지만 어겨진다고 아주 심각한 결과를 초래하지는 않는 목적
        - 우리가 사용하는 영상 재생 어플리케이션이 realtime OS 위에서 실행되는 것은 아님.
        - 시분할 시스템 위에서 실시간성을 보장하기 위한 노력이 이루어지고 있음
    
    ### 운영 체제의 분류
    
    - 동시작업 가능성
    - 사용자의 수
    - 처리 방식
    

## 몇 가지 유사한 용어들

- Multitasking - 동시성. context switching을 통해, 매 순간 프로그램이 하나씩만 처리되지만, 계속 번갈아가며 진행되기에 마치 동시에 진행되는 것 처럼 보이는 방식
- Multiprogramming - 동시성과 비슷한 개념이지만, 동시에 여러 프로그램이 메모리에 올라가 있음을 강조하는 개념
- Time sharing - 시분할. CPU를 강조한 측면으로, CPU가 시간적으로 분할되어 사용된다는 의미를 강조.
- Multiprocess - 여러 프로그램(프로세스) 가 동시에 진행된다.

---

- **Multiprocessor** - 하나의 컴퓨터에 CPU가 여러 개 붙어 있음을 의미. 위 내용과는 다른 개념. 
하나의 프로세서에서의 시분할 개념이 아닌, 하드웨어적으로 병렬적으로 처리되는 상황에 대한 개념.

# 운영체제의 예

## 유닉스(UNIX)

- 주로 대형 컴퓨터를 위해 만들어진 운영체제(서버...)
- 코드 대부분을 C언어로 작성
어셈블리어에 가까운 언어로 운영체제를 프로그래밍하기 어려워서 C언어를 개발.
그렇기에 C언어는 기계어와도 가깝고, 사람과도 가까운 언어.
- 높은 이식성 - Portability. 
하나의 컴퓨터에서 돌아가는 UNIX를 다른 컴퓨터의 기계어에도 이식하기 쉬움. 
(C언어로 작성되었기에 다른 기계어에 맞게 컴파일만 하면 됨)
- 최소한의 커널 구조
커널에는 최소한의 기능만 탑재.(메모리 부담 감소)
- 복잡한 시스템에 맞게 확장 용이
- 소스 코드 공개 (공개 소프트웨어 정신)
- 프로그램 개발에 용이
- 다양한 버전
    - System V, FreeBSD, SunOS, Solaris
    - Linux 
    공개 소프트웨어 정신으로 만들어진 운영체제
    개인용 PC OS로 주로 쓰임. 안드로이드 또한 Linux 커널 기반으로 작성됨

## MS DOS(Disk Operating System)

- MS사에서 1981년 IBM-PC를 위해 개발
- 단일 사용자용 운영체제, 메모리 관리 능력의 한계(주기억장치 최대용량: 640KB)
    - 하드웨어의 빠른 발달로 Deprecated.
    - 이 한계 위에서 기능이 추가되다 보니 OS의 코드가 엉망이 됨.

## MS Windows

- MS사의 다중 작업용 GUI 기반 운영체제
- Plug and Play, 네트워크 환경 강화
- DOS용 응용프로그램과의 호환성 제공
- 불안정성
- 풍부한 지원 소프트웨어

## Handheld device를 위한 OS

- PalmOS, PocketPC(WinCE), TinyOS

이들 외에도 애플의 OS, 스마트폰을 위한 OS가 많이 개발되었음.

# 운영 체제의 구조

### CPU

누구한테 얼마나 어떻게 CPU를 주는 것이 효율적일까? → CPU 스케줄링

### Memory

한정된 메모리를 어떻게 쪼개어 쓰지? → 메모리 관리
1/N 로 나누는 것이 아닌, 효율적인 정책을 책정.

### Disk

디스크에 파일을 어떻게 보관하지? → 파일 관리

디스크의 형태에 따라 달라지는 방식, 디스크 스케쥴링 정책 등을 고려

- 디스크 스케줄링 - 선착순이 아닌, 디스크의 head가 가장 효율적으로 이동할 수 있도록 하는 정책
(엘레베이터, 우편 배달 등)

### I/O device

- 각기 다른 입출력장치와 컴푸터 간에 어떻게 정보를 주고 받게 하지? → 입출력 관리
- CPU보다 워낙 느린 입출력장치. 이들과의 소통을 어떻게 할 것인지?
    - 독립적으로 작동하다가, 입력이 들어오면 CPU에 interupt를 건다. CPU가 중간에 최대한 방해받지 않도록.

### 프로세스 관리 (소프트웨어 관리)

- 프로세스의 생성과 삭제
- 자원 할당 및 반환
- 프로세스 간 협력

### 그 외

- 보호 시스템
- 네트워킹
- 명령어 해석기(command line interpreter)

# 운영체제 공부의 태도

- 본 과목은 OS사용자 관점이 아니라 OS개발자 관점에서 수강해야 함
- 대부분의 알고리즘은 OS 프로그램 자체의 내용
- 인간의 신체가 뇌의 통제를 받듯 컴퓨터 하드웨어는 운영체제의 통제를 받음으며 그 운영체제는 사람이 프로그래밍하는 것이다
- 본인을 하나의 운영체제라고 생각하고 본인의 할 일이 무엇인지를 생각해 보면 내용 이해에 용이