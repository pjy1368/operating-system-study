## Synchronization의 고전적인 문제들

### Bounded-Buffer Problem

⇒ Producer-Consumer Problem (= 소비자-생산자 간 문제) 라고도 함

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2aa5111-f354-485d-9b3b-da8052de3e18/Untitled.png)

**생산자 프로세스**

- 비어 있는 버퍼가 있는지 확인한다. (없으면 기다린다)
- 공유데이터의 Lock을 건다.
- 빈 버퍼에 데이터 입력 및 버퍼를 조작한다.
- 공유데이터의 Lock을 푼다.
- 내용이 들어있는 버퍼를 증가시킨다.

**소비자 프로세스**

- 내용이 들어있는 버퍼가 있는지 확인한다. (없으면 기다린다)
- 공유데이터의 Lock을 건다.
- 내용이 들어있는 버퍼에서 데이터 추출 및 버퍼를 조작한다.
- 공유데이터의 Lock을 푼다.
- 빈 버퍼를 증가시킨다.

⇒ lock을 걸고 푸는 용도 및 자원의 갯수를 세는 용도로써,
**세마포어**가 필요할 것이다.

### 예제 코드

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd3179e6-2e66-4850-89be-188009dafb4b/Untitled.png)

**동기화 변수**

**semaphore**

- full = 0;
- empty = n;
- mutex = 1; [공유 데이터의 lock & unlock을 위해]

**생산자 프로세스**

- P(empty) - 빈 버퍼가 있다면 흭득하고 진행한다.
- P(mutex) - 공유 데이터에 Lock 을 건다.
- 버퍼에 데이터 입력 및 버퍼 조작
- V(mutex) - 공유 데이터의 Lock을 푼다.
- V(full) - 내용이 들어있는 버퍼의 갯수를 1 증가시킨다.

소비자 **프로세스**

- P(full) - 내용이 들어있는 버퍼가 있다면 흭득하고 진행한다.
- P(mutex) - 공유 데이터에 Lock 을 건다.
- 버퍼에서 데이터 추출 및 버퍼 조작
- V(mutex) - 공유 데이터의 Lock을 푼다.
- V(empty) - 비어있는 버퍼의 갯수를 1 증가시킨다.

### Readers-Writers Problem

⇒ 한 프로세스가 DB(공유데이터) 에 write 중일 때 다른 프로세스가 접근하면 안된다. (Read는 동시에 여럿이 해도 된다.)

### 예제 코드

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/52105cc0-e269-420c-894b-a716d27f6ca8/Untitled.png)

**공유 변수**

- int readcount = 0;
- DB 그 자체;

**동기화 변수**

- **semaphore**
    
    mutex = 1;  [readcount의 lock & unlock을 위해]
    
    db =1; [db의 lock & unlock을 위해]
    

**Writer 프로세스**

- P(db) - db(공유자원)에다가 lock을 건다.
- DB에 데이터를 쓰는 작업을 진행한다.
- V(db) - db의 lock을 푼다.

**Reader 프로세스**

- P(mutex) - readcount변수에 Lock을 건다.
- readcount를 1 증가 시킨다.
- P(db) - 만약 readcount가 1이라면 db에다가 lock을 건다.
- V(mutex) -  readcount 변수의 Lock을 푼다.
- DB를 읽는 작업 진행
- P(mutex) - readcount변수에 Lock을 건다.
- readcount를 1 감소시킨다.
- V(db) - 만약 readcount가 0이라면 db의 lock을 푼다.
(마지막에 나가는 프로세스)
- P(mutex) - readcount변수에 Lock을 건다.

***** Starvation 발생 가능!**

⇒ Reader가 계속 도착하게 되어서 Writer는 계속 대기하게 됨

큐에 우선순위를 적용하여 Writer가 오래 기다렸다면 늦게 도착한 Reader를 대기 시켜 Writer가 먼저 작업할 수 있게 하는 방법 등 사용가능

(이외에도 다른 방법이 있는지 생각해보면 좋을 것 같다.)

### Dining-Philosophers Problem

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9010387e-9a15-47bb-a7f5-c46195dbee54/Untitled.png)

젓가락은 5개, 2개를 가져야만 밥을 먹을 수 있는 상황이다.

- 각각 왼쪽, 오른쪽 젓가락을 집을 수 있다.
- 양쪽을 집어 2개를 가지게 되면 밥을 먹을 수 있다.

**위 해결책의** **문제점**

- Deadlock의 가능성이 있다. (모두가 왼쪽 젓가락을 집었을 때)

**해결 방안**

- 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
- 젓가락을 두 개 모두 집을 수 있을 수 있을 때에만 젓가락을 집을 수 있게 한다.
- 비대칭 - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록 한다.

### 예제 코드 (해결방안)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e5337e5f-72d4-4799-84ea-973df7c7a438/Untitled.png)

**변수**

- enum {thinking, hungry, eating} state[5]; [철학자들의 상태]
- **semaphore**
    - self[5] = 0; [젓가락 2개를 다 잡을 수 있는 권한이 있는 지 확인]
    - mutex = 1; [철학자의 상태를 lock & unlock 할 수 있음]

**동작순서**

- pickup()
    - 철학자의 상태를 바꾸기 위해 공유자원에 lock을 건다.
    - 상태를 바꾸고, test를 상태, 주변 철학자가 밥을 먹고 있는 지에 대한 여부를 확인한다.
    - 공유자원의  lock을 푼다.
    - 밥을 먹기 전에 젓가락을 들도록 한다.
        
        (젓가락이 없으면 대기)
        
- eat()
- putdown()
    - 철학자의 상태를 바꾸기 위해 공유자원에 lock을 건다.
    - 상태를 바꾸고 주변 철학자의 상태를 확인하게 된다.
    (젓가락을 내려놓게 된다.)
    
    ⇒ 이때, P(self[i]) 연산에서 대기하던 철학자가 있었다면,
         밥을 먹게되는 것이다.
    
    - 공유 자원의 lock을 푼다.
- think()

**Semaphore의 문제점**

- 프로그래머가 코딩하기가 힘들다.
- 정확성(correctness)의 입증이 어렵다.
- 자발적 협력(voluntary cooperation)이 필요하다.
- 한번의 실수가 모든 시스템에 치명적 영향을 끼친다.

## Monitor

⇒ 동시 수행중인 프로세스 사이에서 추상 데이터 형식의 안전한 공유를 보장하기 위한 high-level synchronization construct

- 공유데이터에 접근하기 위해서는 내부의 프로시저를 사용해야한다.
- 모니터 내에서는 **한번에 하나의 프로세스만이 활동 가능**하다.
    
    ⇒ lock을 걸 필요가 없기에 세마포어 보다 굉장히 편리하다.
    
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요는 없음
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해
**condition variable**을 사용한다.

⇒ **condition variable**

- 조건 변수로서, wait과 signal을 통해서만 접근이 가능하다.

### 예제 코드 (생산자-소비자)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dea834ab-3551-43a7-9117-fdfa796df145/Untitled.png)

**생산자 프로세스**

empty.wait() - 빈 버퍼가 없다면 대기, 있다면 버퍼에 내용을 추가함

full.signal() - 내용이 들어있는 버퍼가 있다면, 소비자를 깨워준다.

**소비자 프로세스**

full.wait() - 내용이 들어있는 버퍼가 없다면 대기, 있다면 내용을 가져감

empty.signal() - 빈 버퍼가 있다면 생산자를 깨워준다.

### 예제 코드 (식사하는 철학자)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ea8720ca-c73b-41cd-aa5e-90bef7b13254/Untitled.png)

- 젓가락을 잡고, 내려놓고 하느 코드는 모니터 내부에 정의되어있다.

**self[i].wait()** - 젓가락 2개를 모두 들 수 있는지를 확인한다.

                  (아니라면 대기하게 함)

**self[i].signal()** - 젓가락 2개를 모두 들 수 있는 상황이기에 깨우게 된다.
