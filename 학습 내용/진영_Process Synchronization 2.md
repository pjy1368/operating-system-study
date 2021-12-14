## 세마포어 (Semaphore)

**Integer variable**

S를 사용함.

**아래의 두 가지 atomic 연산에 의해서만 접근이 가능**

P(S): 공유 데이터를 획득하는 과정 (lock)으로 S가 양수이어야 한다.

```cpp
// P(S)
while (S <= 0) do no-op; // ex) wait.
S--;
```

V(S): 공유 데이터를 사용하고 반납하는 과정 (unlock)

```cpp
// V(S)
S++;
```

### n개의 프로세스가 임계 구역에 들어가려는 경우

**Synchronization variable**

```cpp
semaphore mutex;
```

위 세마포어 변수 값만큼 여러 개의 프로세스가 임계 구역에 접근할 수 있다. 이번 예제에서는 mutex의 값이 1이라고 가정한다. (1개의 프로세스만 임계 구역 접근 가능)

**Process Pi**

```cpp
do {
    P(mutex); // mutex의 값이 양수면 임계 구역 접근하고, 아니면 기다린다.
    critical section
    V(mutex); // mutex의 값을 1 증가한다.
    remainder section
} while (1);
```

P(mutex) 연산 수행 시, mutex의 값이 양수가 아니면 계속 기다려야 하는데, 프로세스의 CPU 할당 시간이 끝날 때까지 무의미하게 CPU를 낭비하는데, 이러한 현상을 busy-wait 또는 spin lock 이라고 부른다. 이러한 단점을 보완하기 위해 Block & Wake-up 혹은 Sleep lock 이라고 부르는 기법이 생겨났다.

### Block / Wake-up 기법

**세마포어는 다음과 같이 정의**

```cpp
typedef struct
{
    int value; // 세마포어 변수
    struct process *L; // 프로세스 Wait Queue
} semaphore;
```

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc641df15-2324-4155-84fa-1b8bba935365%2FUntitled.png?table=block&id=4dcb89e8-0f43-4ed0-8f94-0c8661459810&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**block과 wake-up을 다음과 같이 가정**

- block
    - 커널은 block을 호출한 프로세스를 suspend하고, 이 프로세스의 PCB를 세마포어에 대한 Wait Queue에 넣음.
- wakeup(P)
    - block된 프로세스 P를 wake up하고, 이 프로세스의 PCB를 Ready Queue로 옮김.

**세마포어 연산은 다음과 같이 정의**

P(S): 세마포어 변수 S를 무조건 1 줄이고, 그 변수의 값이 음수면 해당 프로세스를 Wait Queue로 보낸 후  Block 상태로 만든다.

```cpp
// P(S)
S.value--;
if (S.value < 0)
{
    add this process to S.L;
    block();
}
```

V(S): 세마포어 변수 S를 무조건 1 늘리고, 그 변수의 값이 0보다 작거나 같으면 이미 기다리고 있는 프로세스가 있으므로 프로세스 P를 Wait Queue에서 꺼내서 Ready Queue로 보낸다. 세마포어 변수  S 값이 양수면 아무나 임계 구역에 들어 갈 수 있으므로 별다른 추가 연산을 하지 않는다.  V 연산은 특정 프로세스가 공유 데이터를 반납한 후 임계 구역에서 나가는 연산임을 기억해야 한다. 

*나 이제 임계 구역에서 나갈거니까 기다리는 친구 있으면 임계 구역 들어가~*

```cpp
// V(S)
S.value++;
if (S.value <= 0)
{
    remove a process P from S.L;
    wakeup(P);
}
```

### Busy wait vs Block Wake-up

- 일반적으로 Block Wake-up 기법이 좋음.
- 임계 구역의 길이가 긴 경우 Block/Wake-up 기법이 적합함.
- 임계 구역의 길이가 매우 짧은 경우 Block/Wake-up 기법의 오버헤드가 Busy-wait 기법의 오버헤드보다 크므로 Busy Wait 기법이 적합할 수 있음.

### 세마포어의 종류

**계수 세마포어 (Counting Semaphore)**

- 도메인이 0 이상인 임의의 정수 값
- 여러 개의 공유 자원을 상호 배제함.
- 주로 resource counting에 사용됨.

**이진 세마포어 (Binary Semaphore)**

- 0 또는 1 값만 가질 수 있음.
- 한 개의 공유 자원을 상호 배제함.
- mutex와 유사함. (완전히 같지는 않음.)

## Deadlock과 Starvation

### Deadlock

둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상

### Deadlock 예시

S와 Q가 1로 초기화된 세마포어라 하자.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff7aeaebb-d0f0-4096-b543-ee2b1cce0786%2FUntitled.png?table=block&id=a69df245-283e-4706-8c58-3a667c8b479f&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

P0가 CPU를 얻어서 P(S) 연산까지 수행하여 S 자원을 얻었다고 가정해 보자. 그런데 여기서 P0의 CPU 할당 시간이 끝나 Context Switch가 발생하여 P1에게 CPU 제어권이 넘어갔다. P1은 P(Q) 연산을 수행하여 Q 자원을 얻었으나 또 다시 CPU 할당 시간이 끝나 Context Switch가 발생하여 P0에게 CPU 제어권이 넘어갔다.

P0은 P(Q) 연산을 통해 Q 자원을 얻고 싶지만, 이미 Q 자원은 P1이 갖고 있는 상태이므로 Q 자원을 가져올 수가 없다. 마찬가지로 P1도 P(S) 연산을 통해 S 자원을 얻고 싶지만, 이미 S 자원은 P0이 갖고 있는 상태이므로 S 자원을 가져올 수 없다.

이렇게 P0와 P1은 영원히 서로의 자원을 가져올 수 없고, 이러한 상황을 Deadlock이라고 한다.

### Deadlock 해결 방안

해결 방안은 추후 자세히 배우겠지만, 자원의 획득 순서를 정해주어 해결하는 방법이 있다. S를 획득해야만 Q를 획득할 수 있게끔 순서를 정해주면 프로세스 A가 S를 획득했을 때 프로세스 B가 Q를 획득할 일이 없다.

### Starvation (기아)

- indefinite blocking
- 프로세스가 자원을 얻지 못하고 무한히 기다리는 현상이다.

### Deadlock vs Starvation

언뜻 보기에 둘 다 자원을 가져오지 못하고 무한히 기다리니까 같은 단어라고 혼동할 수 있다.

Deadlock은 P0 프로세스가 A 자원을 보유하고 있고, P1 프로세스가 B 자원을 보유하고 있을 때 서로의 자원을 요구하여 무한히 기다리는 현상이다. 반면 Starvation은 프로세스가 자원 자체를 얻지 못하고 무한히 기다리는 현상이다. Starvation은 기아라는 단어에 맞게 어떤 프로세스가 자원이 없어서 굶어 죽는다(?)고 생각하면 이해하기 편하다.

## 출처

- [https://sangminlog.tistory.com/entry/process-synchronization?category=887652](https://sangminlog.tistory.com/entry/process-synchronization?category=887652)
- [https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)
