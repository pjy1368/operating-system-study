## Semaphores (세마포어)

- 앞의 방식들을 추상화 시킨 것
- 앞서 보았던 lock을 걸고, lock을 푸는 작업을 대신 해준다.
(공유 자원을 획득하고, 다시 반납하는 작업 등)

P (S) 연산 - lock을 거는 연산

```c
while (S<=0) do no-op;
S--;
```

V (S) 연산 - lock을 푸는 연산

```c
S++;
```

 

**** 위 작업들이 atomic 하게 이루어져야 한다.**

세마포어를 사용한 예제

```c
Synchronization variable:
semaphore mutex; // 1로 정의

do{
	P(mutex);
	/* 임계구역 */
	V(mutex); 
	/* 나머지구역 */
} whlile (1);
```

프로그래머는 위처럼 세마포어를 이용해서 더 간단하게 프로그램을
작성할 수 있다.

**Block & Wakeup (=sleep lock)** 방식의 구현을 이용한다.

- 임계구역의 길이가 긴 경우 적당하다.

**Busy-wait (=spin lock)**

- 임계구역의 길이가 매우 짧은 경우 오버헤드가 비교적 더 작다.

⇒ 그렇기 때문에 일반적으로 Block & Wakeup 방식이 더 효율적으로 사용할 수 있기 때문에 좋다.

### Block & Wakeup Implementation

```c
typedef struct
{
	int value; // 세마포어 변수
	struct process *L; // 세마포어를 사용하기 위한 대기 큐
}semaphore;;
```

세마포어는 위와 같이 정의 할 수있다.

자세히 살펴보자면 아래와 같이 구현이 된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4841d797-125e-4031-a3b7-b1a13673c135/Untitled.png)

세마포어 변수 값이 음수라면, 가져갈 프로세스가 없으니 block()

세마포어 변수 값이 양수라면, 프로세스를 하나 빼주고 wakeup()

- **block()**
    - 커널은 block을 호출한 프로세스를 대기 시킨다.
    - 이 프로세스의 PCB를 세마포어를 위한 대기 큐에 넣는다.
- **wakeup(P)**
    - block 된 프로세스 P를 wakeup 시킨다.
    - 이 프로세스의 PCB를 준비 큐로 옮긴다.

### 세마포어의 종류

**Counting semaphore**

- 도메인이  0 이상이 임의의 정수값
- 주로 resource counting에 사용

**Binary semaphore (=mutex)**

- 0 또는 1 값만 가질 수 있는 세마포어
- 주로 mutual exclusion (lock/unlock)에 사용

### 세마포어를 사용할 때 주의할 점

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9010387e-9a15-47bb-a7f5-c46195dbee54/Untitled.png)

**Deadlock**

- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
- Ex_ 모두가 젓가락이 필요로 되어 모두 굶게 됨

**Starvation**

- 프로세스가 대기 된 이유에 해당하는 세마포어 큐에서 빠져나갈 수없는 현상
- Ex_ 1, 3 번이 젓가락을 계속 사용하여 2번이 사용할 수 없게 됨
