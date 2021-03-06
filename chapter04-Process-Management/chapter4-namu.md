# 4. Process Management

## 프로세스 생성 (Process Creation)

- 부모 프로세스가 자식 프로세스 생성
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
    - 운영체제로부터 받는다
    - 부모와 공유한다
- 부모의 공간과 PCB를 복제한다, 하지만 효율성을 위해서 COW를 사용하기도 한다. 
우선은 자원을 공유하고, write가 발생하면 실제 복제가 이루어지는 것.
- 자원의 공유
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부를 공유하는 모델
    - 전혀 공유하지 않는 모델 - 원칙적으로 공유하지 않음. 
    자식을 낳으면, 그때부터는 각자 자원을 놓고 경쟁하는 관계가 된다.
- 수행(Execution)
    - 부모와 자식은 공존하며 수행되는 모델
    - 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델
    
- 주소 공간 (Adress space)
    - 자식은 부모의 공간을 복사함 (binary and OS data)
    - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스의 예
    - fork() 시스템 콜이 새로운 프로세스를 생성
        - 부모를 그대로 복사(OS data except PID + binary)
        - 주소 공간 할당
    - fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

## 프로세스 종료 (Process Termination)

자식 프로세0스는 항상 부모 프로세스보다 먼저 죽어야 한다. 

→ 자식 프로세스가 죽기 전까지는 부모프로세스는 죽을 수 없다.

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (exit)
    - 자식이 부모에게 output data를 보냄 (via wait).
    - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (abort)
    - 자식이 할당 자원의 한계치를 넘어섬
    - 자식에게 할당된 태스크가 더이상 필요하지 않음
    - 부모가 종료(exit)되는 경우
        - 운영체제는 부모 프로세스가 종료되는 경우 자식이 더이상 수행되도록 두지 않는다
        - 단계적인 종료(손자가 먼저 종료 → 자식 종료 → 부모 종료)

## fork() 시스템 콜

- a Process is created by `fork()` system call.
    - creates a new address space that is a duplicate of the caller
- 자식 프로세스는 pid 0을 부여받는다. 이로 인해 실행 흐름의 구분이 가능해 진다.
    
    ```c
    int main()
    { ind pid;
    	pid = fork();
    if (pid == 0) /* this is child */
    		printf("\n Hello, I am child!\n");
    else if (pid > 0) /* this is parent */
    		printf("\n Hello, I am parent!\n");
    ```
    

## exec() 시스템 콜

- A process can excecute a different program by the exec() system call.
    - replaces the memory image of the caller with a new program

```c
int main()
{ ind pid;
	pid = fork();
	if (pid == 0) /* this is child */
	{		printf("\n Hello, I am child! Now I'll run date \n");
			execlp("/bin/date", "bin/date", (char *) 0);
	}
	else if (pid > 0) /* this is parent */
	{		printf("\n Hello, I am parent!\n");
}
```

- 생성된 자식 프로세스에서 execlp를 만나는 순간, 이 프로세스에는 새로운 프로그램이 덮어씌워진다. 그래서 exec 아래의 코드는 실행되지 않는다.

## wait() 시스템 콜

- 프로세스 A가 wait() 시스템 콜을 호출하면
    - 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다(block상태)
    - Child Process가 종료되면 커널은 프로세스 A를 깨운다 (ready상태)

## exit() 시스템 콜

- 프로세스의 종료
    - 자발적 종료
        - 마지막 statement 수행 후 exit() 시스템 콜을 통해
        - 프로그램에 명시적으로 적어주지 않아도 main함수가 리턴되는 위치에 컴파일러가 넣어줌
    - 비자발적 종료
        - 부모 프로세스가 자식 프로세스를 강제 종료시킴
            - 자식 프로세스가 한계치를 넘어서는 자원 요청
            - 자식에게 할당된 태스크가 더이상 필요하지 않음
        - 키보드로 kill, break 등을 친 경우
        - 부모가 종료하는 경우
            - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨
            

## 프로세스와 관련된 시스템 콜

- fork() - create a child (copy)
- exec() - overlat new image
- wait() - sleep until child is done
- exit() - frees all the resources, notify parent

## 프로세스 간 협력

- 독립적 프로세스 (Independent process)
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스(Cooperating process)
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘(IPC: Interprocess Communication)
    - 메시지를 전달하는 방법
        - message Passing: 커널을 통해 메시지 전달
    - 주소 공간을 공유하는 방법
        - shared memory: 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
        - thread: thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능
        
        ## Message Passing
        
        - Message system
            - 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 (커널을 통해)통신하는 시스템
        - Direct Communication
            - 통신하려는 프로세스의 이름을 명시적으로 표시
                
               ![image](https://user-images.githubusercontent.com/72993238/163817674-b7e294cb-9d7f-4285-9fe2-c007e4bbba90.png)
               
        - Indirect Communication
            - mailbox (또는 port)를 통해 메시지를 간접 전달. 수신자가 다수 될 수도 있음

               ![image](https://user-images.githubusercontent.com/72993238/163817709-ac98e85d-28fb-465a-aae2-b9d872e266aa.png)
               
           ![image](https://user-images.githubusercontent.com/72993238/163817767-92083fef-8d9f-4b30-b80a-ee165506077e.png)


                
