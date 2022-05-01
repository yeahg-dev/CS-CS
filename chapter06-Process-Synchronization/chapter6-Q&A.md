# Chapter 6. Process Synchronization

## Block/ Wakup 방식에서 semaphore를 획득하지 못한 프로세스는 획득한 프로세스의 뒤에 PCB의 형태로  줄줄이 소세지가 된다고 설명되었는데, 여기서 PCB는 무엇인가?

PCB는 OS관리를 위한 커널의 자료구조이며 정보 블럭이라고 설명할 수 있다. PCB는 Program Counter를 가지고 있어서 다음 차례의 프로세스를 가리키는 형태로 저장된다.
아래는 PCB에 포함되는 정보로, Program Counter 항목을 통해 위의 내용을 확인할 수 있다.

![](https://i.imgur.com/95ew1yR.png)
