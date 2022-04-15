Chapter3. Process

# 4/15 Chapter 3

## Q. 타임쉐어링에서는 시작과 동시에 모두 레디상태가 되는데, 그럼 잡큐가 존재하지 않는 걸까?

잡 큐는 모든 프로세스의 집합이기 때문에 프로세스가 ready, running이 아닐 경우도 있기 때문에 잡큐는 항상 존재할 것 같다.

## Q. suspended가 앱라이프사이클의 개념과 비슷한 기능일까?

비슷한 개념이라고 생각이 든다... 프로그램(=앱)이라고 생각한다면 →

### Q_1. suspended ready를 앱에서 백그라운드 상태라도 봐도 될까?[Managing Your App's Life Cycle | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle)
![image](https://user-images.githubusercontent.com/72993238/163523780-40248737-7a97-46b4-8ff0-66ae3a9c6901.png)
![image](https://user-images.githubusercontent.com/72993238/163523787-02e0c6c0-4045-4095-861a-dfe05e1ebc3a.png)

### Q_2. 앱라이프사이클에서 백그라운드 상태가 되었을 때 일정 시간 후 inout 가능한 시점에서 프로세스는 어떤 상태일까?

- Not Running(Terminated) : 앱이 시스템에 의해 완전히 종료된 상태입니다. 혹은 아직 실행되지 않았을 때도 Not Running 상태입니다.
- Inactive(Foreground) : 앱이 실행 중인 상태입니다. 하지만 이벤트를 받지는 않습니다. Active 상태로 넘어가기 전에 앱은 반드시 이 상태를 거칩니다. 알림 같은 특정 알림창이 화면을 덮어서 앱이 event를 받지 못하는 상태가 여기에 해당됩니다.
- Active(Foreground) : 앱이 실행 중이고 이벤트를 받을 수 있는 상태입니다. Foreground 상태에 있는 앱들은 보통 이 상태입니다.
- Background : 앱 사용중에 다른 앱을 실행하거나 홈 화면으로 나갔을 때 상태입니다. 백그라운드에서 동작하는 코드를 추가하면 suspended 상태로 넘어가지 않고 백그라운드 상태를 유지하게 됩니다. 처음부터 background 상태로 실행되는 앱은 inactive 대신 background 상태로 진입합니다. 음악을 실행하고 홈 화면으로 나가도 음악이 나오는 상태가 이 경우에 해당됩니다.
- Suspended : 앱이 background 상태에서 추가적인 작업을 하지 않으면 곧바로 suspended 상태로 진입합니다. 앱을 다시 실행할 경우 빠른 실행을 위해 메모리에만 올라가 있습니다. 메모리가 부족한 상황이 되면 iOS는 suspended 상태에 있는 앱들을 메모리에서 해제시켜서 메모리를 확보합니다.

## Q. 스레드에서 힙의 공유는 어떻게 되지?

스레드는 각각 스택을 따로 할당받고, Code, Data, Heap 영역은 공유한다. 같은 프로세스 안에 있는 스레드들은 힙 공간을 공유한다. 반면에 프로세스는 다른 프로세스의 메모리에 접근할 수 없다. 스레드는 별도의 레지스터와 스택을 갖고 있지만, 힙 메모리는 서로 읽고 쓸 수 있다. 한 스레드가 프로세스 자원을 변경하면, 다른 이웃 스레드도 그 변경 결과를 즉시 볼 수 있다.

Q. suspend와 block시에 메모리를 가지고 있을까?

block은 메모리에 올라와있고, suspend는 메모리에서 디스크로 이동된다.
