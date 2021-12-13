## 임계 구역 문제를 해결하기 위한 충족 조건

- 상호 배제 (Mutual Exclusion)
    - 어떤 프로세스가 임계 구역 부분을 수행 중이면 다른 모든 프로세스는 그의 임계 구역에 들어가면 안 된다.
- 진행 (Progress)
    - 아무도 임계 구역에 있지 않은 상태에서 임계 구역에 들어가고자 하는 프로세스가 있으면 임계 구역에 들어가게 해 주어야 한다.
- 유한 대기 (Bounded Waiting)
    - 프로세스가 임계 구역에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 임계 구역에 들어가는 횟수에 한계가 있어야 한다.
    - ex) 세 개의 프로세스가 있을 때 두 개의 프로세스만 번갈아가며 임계 구역에 들어가는 것은 유한 대기 조건을 만족하지 못한 것이다.

위 조건은 모든 프로세스의 수행 속도는 0보다 크며, 프로세스 간의 상대적인 수행 속도는 가정하지 않는다.

## 임계 구역 문제 해결 알고리즘

### Algorithm 1

- Synchronization variable
    
    ```c
    int turn; 
    initially turn = 0; // 몇 번 프로세스가 들어갈 수 있는지를 알려주는 turn 변수
    
    ```
    

- Process P0
    - turn 변수가 0이 아닌 동안 while문을 계속 돌면서 자기 차례를 기다린다.
    
    ```c
    do {
        while (turn != 0);
        critical section
        turn = 1;
        remainder section
    } while (1);
    ```
    
    mutual exclusion은 만족하지만 progress는 만족하지 않는다. 반드시 한 번씩 교대로 임계 구역에 들어가야 하기 때문이다. 
    
    만약 프로세스 0은 임계 구역에 5번 들어가야 하고 프로세스 1은 한 번만 들어가면 된다고 가정해 보자. 프로세스 1은 한 번 임계 구역에 들어가고 나오면 끝나지만, 프로세스 0은 프로세스 1이 다시 들어갈 때까지 본인도 못 들어가므로 progess 조건이 성립하지 않는다. (과잉 양보)
    
    ### Algorithm 2
    
- Synchronization variable
    
    ```c
    boolean flag[2];
    initially flag[모두] = false; // critical section에 들어가고자 하는 의사 표시
    ```
    
- Process Pi
    
    ```c
    do {
        flag[i] = true;
        while (flag[j]);
        critical section
        flag[i] = false;
        remainder section
    } while (1);
    ```
    
    상대방이 임계 영역에서 빠져 나왔을 때 내가 들어간다.
    
    mutual exclusion은 만족하지만 progress는 만족하지 않는다. 프로세스 A가 임계 구역에 들어가려고 flag를 true로 바꾼 상황에서 context switch가 발생하여 프로세스 B에게 CPU가 넘어갔다고 하자. 이때 프로세스 B도 flag를 true로 바꿨는데 다시 context switch가 발생하여 프로세스 A가 CPU를 잡는다면 그 누구도 임계 구역에 들어갈 수 없다.
    
    ### Algorithm 3 (Peterson's Algorithm)
    
- Combined synchronization variables of algorithm 1 and algorithm 2
- Process Pi
    
    ```c
    do {
        flag[i] = true; // 내가 임계 구역에 들어가고 싶다고 알림
        turn = j; // 자신의 다음 차례를 프로세스 j로 바꿔 줌
        while (flag[i] && turn == j);
        critical section
        flag[i] = false;
        remainder section
    } while (1);
    ```
    
    상대방이 임계 구역에 들어가 있지 않고, 들어갈 준비도 하지 않는다면 내가 들어간다.
    
    세 조건을 모두 만족하지만, 계속 CPU와 메모리를 쓰면서 기다리기 때문에 busy waiting (spin lock)이 발생한다. 쉽게 말해 임계 구역에 들어가려면 상대방이 CPU를 잡고 flag 변수를 false로 바꿔주어야 하는데, 내가 CPU를 잡고 있는 상황에서 의미 없이 while문을 돌며 CPU 할당 시간을 낭비해야 한다.
    
- C++ 예시
    
    ```cpp
    #include <iostream>
    #include <thread>
     
    using namespace std;
     
    int cnt;
    bool flag[2] = { false, false };
    int turn = 0;
     
    void func0() {
        for (int i = 0; i < 10000; i++) {
            flag[0] = true;
            turn = 1;
            while (flag[1] == true && turn == 1) {}
     
            cnt++;
            printf("cnt1 :: %d\n", cnt);
     
            flag[0] = false;
        }
    }
    void func1() {
        for (int i = 0; i < 10000; i++) {
            flag[1] = true;
            turn = 0;
            while (flag[0] == true && turn == 0) {}
     
            cnt++;
            printf("cnt2 :: %d\n", cnt);
     
            flag[1] = false;
        }
    }
     
    int main() {
        thread t1(func0);
        thread t2(func1);
     
        t1.join();
        t2.join();
     
        cout << "cnt : :" << cnt << endl;
     
        return 0;
    }
    ```
    
    fun0만 보면 fun1은 자동으로 이해가 된다.
    
    1. flag[0] = true로 설정하여 0번 스레드가 임계 영역 진입을 하고 싶다고 표시한다.
    2. 이때 turn = 1으로 설정하여, 1번 프로세스가 먼저 임계 영역에 들어가라고 양보한다.
    3. 만약 이때 context switch가 되지 않았다면 while문에 갇히게 된다.
    4. 1번 프로세스가 모든 작업을 끝내면 turn = 0, flag[1] = false가 되므로 0번 프로세스가 임계 구역에 진입할 수 있다.
    
    ## 동기화 하드웨어
    
    임계 구역 문제가 발생한 근본적인 이유는 데이터를 읽고 쓰는 동작을 하나의 명령어로 수행할 수 없기 때문이다. 따라서 명령어 하나만으로 데이터를 읽는 작업과 쓰는 작업을 `atomic` 하게 수행하도록 지원하면 앞선 임계 구역 문제를 간단하게 해결할 수 있다.
    
    ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa43f5cd8-e5be-4ccf-83c2-22e143078997%2FUntitled.png?table=block&id=be880c4d-8649-449d-abbe-91096364ab92&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    
    위 그림은 a 변수를 읽은 후, 그 변수를 무조건 1로 설정하도록 명령어가 구성되어 있다. 
    
    ### Mutual Exclusion With Test & Set
    
- Synchronization variable
    
    ```cpp
    boolean lock = false;
    ```
    
- Process Pi
    
    ```cpp
    do {
        while (Test_and_Set(lock));
        critical section
        lock = false;
        remainder section;
    } while (1);
    ```
    
    `Test_and_Set()` 함수는 매개 변수를 읽어내고, 그 변수를 1로 바꿔 주는 역할을 한다. 위 예시에서 lock을 읽고 난 뒤 1로 바꿔준다.
    
    만약 처음에 lock의 값이 0이었다면, while문을 탈출하고 lock 값이 1이 된다. 반대로 lock의 값이 1이면 while문에서 갇히고 lock 값은 그대로 1이 된다.
    

## 출처

- [https://sangminlog.tistory.com/entry/process-synchronization?category=887652](https://sangminlog.tistory.com/entry/process-synchronization?category=887652)
- [https://www.crocus.co.kr/1371](https://www.crocus.co.kr/1371)
- [https://core.ewha.ac.kr/publicview/C0101020140404144354492628?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140404144354492628?vmode=f)
