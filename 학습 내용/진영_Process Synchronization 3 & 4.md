## Bounded-Buffer-Problem (Producer-Consumer Problem)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4a38ea89-31b9-4cde-8ccc-aad33629d35b%2FUntitled.png?table=block&id=cc1b5c93-e328-4198-9788-df1c68c5f4ab&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**공유 데이터**

- buffer 자체 및 buffer 조작 변수 (empty / full buffer의 시작 위치)

**Producer (생산자)** 

- Empty 버퍼가 있는지 확인한다. (없으면 기다림)
- 공유 데이터에 lock을 건다.
- Empty 버퍼에 데이터를 입력하고 버퍼를 조작한다.
- lock을 푼다.
- Full 버퍼가 하나 증가한다.

**Consumer (소비자)** 

- Full 버퍼가 있는지 확인한다. (없으면 기다림)
- 공유 데이터에 lock을 건다.
- Full 버퍼에서 데이터를 꺼내고 버퍼를 조작한다.
- lock을 푼다.
- Empty 버퍼가 하나 증가한다.

### 발생 가능한 동기화 문제

- 공유 버퍼이기 때문에 생산자 여러 명이 동시에 한 버퍼에 데이터를 쓰거나 수정할 수 있다.
- 마찬가지로 생산자가 동시에 한 버퍼의 데이터를 읽어갈 수 있다.

### 동기화 변수

- 이진 세마포어 (공유 데이터의 상호 배제를 위해)
- 계수 세마포어 (남은 full/empty 버퍼의 수 표시)

### 예제 코드

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F796e0f4c-001d-4ce2-aa01-c1ae7d66ef23%2FUntitled.png?table=block&id=db7de881-f8b7-49ec-bff8-f398e8890b80&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**Producer**

- P 연산을 통해 empty 버퍼가 있는지 확인한다. 없으면 대기
- P 연산을 통해 empty 버퍼가 있다면 mutex를 0으로 만들고 임계 구역에 진입한다.
- 버퍼에 데이터를 입력한다.
- V 연산을 통해 mutex 값을 1로 만든다.
- V 연산을 통해 full 버퍼를 1 증가하고 임계 구역에서 나온다.

**Consumer**

- P 연산을 통해 full 버퍼가 있는지 확인한다. 없으면 대기
- P 연산을 통해 full 버퍼가 있다면 mutex를 0으로 만들고 임계 구역에 진입한다.
- 버퍼에 데이터를 빼 간다.
- V 연산을 통해 mutex 값을 1로 만든다.
- V 연산을 통해 empty 버퍼를 1 증가하고 임계 구역에서 나온다.

## Readers-Writers Problem (독자-저자 문제)

### 문제 설명

- 한 프로세스가 DB에 write 중일 때 다른 프로세스가 접근하면 안 된다.
- read는 동시에 여러 명이 해도 됨.

### Solution

- Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기 중인 Reader들을 다 DB에 접근할 수 있게 해 준다.
- Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용된다.
- 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다.
- Writer가 DB에서 빠져 나가야만 Reader의 접근이 허용된다.

### 공유 데이터

- DB 그 자체
- readCount (현재 DB에 접근 중인 Reader의 수)

### 동기화 변수

이진 세마포어 두 개를 사용한다.

- mutex (공유 변수 readCount를 접근하는 코드 (임계 구역)의 상호 배제를 보장하기 위해 사용)
- db (Reader와 Writer가 공유 DB 자체를 올바르게 접근하게 하는 역할)

### 예제 코드

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa0908b45-31c7-4376-9c30-b0b781ca1f82%2FUntitled.png?table=block&id=7d8e2be1-1e20-4e1c-b055-b95f2f1110ac&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**세마포어 설계**

상호 배제를 보장해야 하므로 각각의 세마 포어 값은 1로 지정하며, 다수의 Reader들이 readCount에 동시 접근하면 데이터 불일치의 위험이 있으므로 mutex를 정의하였다. db는 Reader와 Writer가 공통 데이터베이스에서 서로 상호 배제를 보장해야하므로 정의하였다.

**Reader 프로세스**

- P 연산을 통해 readCount 상호 배제 처리한다.
- 아무도 read 하고 있는 프로세스가 없고 read 하려는 프로세스가 나 하나라면 공통 데이터베이스에 lock을 건다. (공통 데이터베이스에 lock이 걸려 있어도 read는 여러 프로세스가 동시에 가능)
- V 연산을 통해 임계 구역에서 빠져 나온다.
- DB를 원하는 만큼 읽는다.
- P 연산을 통해 readCount 상호 배제 처리한다.
- 마지막으로 read를 그만하고 나가려는 프로세스가 나 하나라면 공통 데이터베이스에 lock을 해제한다.
- V 연산을 통해 임계 구역에서 빠져나온다.

**Wrtier 프로세스**

- P 연산을 통해 공통 데이터베이스에 lock이 걸려있는지 확인한다.
- lock이 걸려 있지 않으면 공통 데이터베이스에 lock을 걸고 임계 구역에 진입한다.
- 쓰기 작업이 끝나면 V 연산을 통해 공통 데이터베이스에 lock을 해제하고 임계 구역에서 빠져 나온다.

**위 예제의 문제점**

Writer가 쓰기 작업을 하고 싶어도 무조건 Read 하고 있는 프로세스인 Reader가 없어야 한다. 만약 Reader가 무한히 read 작업을 실행한다면 Writer는 영영 임계 구역에 진입 하지 못하는 Startvation에 빠질 수도 있다. 다만 해당 단점을 보완하는 예제는 난이도의 이유로 생략한다.

## Dining-Philosophers Problem (식사하는 철학자 문제)

### 문제 설명

철학자 다섯이서 원형 식탁에 둘러앉아 생각에 빠지다가, 배고플 땐 밥을 먹는다. 그들의 양쪽엔 각각 젓가락 한 짝씩 놓여있고, 밥을 먹으려 할 땐 다음의 과정을 따른다.

**1.** 왼쪽 젓가락부터 집어든다. 다른 철학자가 이미 왼쪽 젓가락을 쓰고 있다면 그가 내려놓을 때까지 생각하며 대기한다. 

**2.** 왼쪽을 들었으면 오른쪽 젓가락을 든다. 들 수 없다면 1번과 마찬가지로 들 수 있을 때까지 생각하며 대기한다.

**3.** 두 젓가락을 모두 들었다면 일정 시간동안 식사를 한다.

**4.** 식사를 마쳤으면 오른쪽 젓가락을 내려놓고, 그 다음 왼쪽 젓가락을 내려놓는다.

**5.** 다시 생각하다가 배고프면 1번으로 돌아간다.

### 쉽게 떠올릴 수 있는 예제 코드

```c
do {
    P(chopstick[i]);
    P(chopstick[(i + 1) % 5]);
    ...
    eat();
    ...
    V(chopstick[(i + 1) % 5]);
    V(chopstick[i]);
    ...
    think();
    ...
} while (1);
```

모두가 배고파서 동시에 왼쪽 젓가락을 드는 순간 오른쪽 젓가락을 들 수 없으므로 서로가 서로의 자원을 무한히 원하게 되는 Deadlock이 발생한다는 문제가 있다.

### 해결 방안

- 4명의 철학자만 테이블에 동시에 앉을 수 있게 한다.
- 젓가락을 두 개 모두 집을 수 있을 때만 젓가락을 집을 수 있게 한다.
- 비대칭
    - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록 방향을 정해준다.

### 세마포어 예제

```c
// Variable
enum {thinking, hungry, earting} state[5]; // 철학자들이 가질 수 있는 상태
semaphore self[5] = 0; // 철학자들이 젓가락 2개를 모두 들 수 있는 지 판단 (1이면 가능)
semaphore mutex = 1; // state[i]를 동시에 접근하지 못하도록 상호 배제 기능

do {
    pickup(i);
    eat();
    putdown(i);
    think();
} while (1);

void pickup(int i) {
    P(mutex); // state 상호 배제
    state[i] = hungry;
    test(i); // 내가 지금 젓가락 2개를 들 수 있는지 확인하고 가능하면 식사를 함.
    V(mutex); // 임계 구역 탈출
    P(self[i]); // 젓가락 2개를 들 수 없는 상태로 변경
}

void putdown(int i) {
    P(mutex); // state 상호 배제
    state[i] = thinking;
    // 양 옆 철학자가 식사할 수 있는 상태인지 확인하고
    // 가능하면 식사하게 해 줌.
    test((i + 4) % 5);
    test((i + 1) % 5);
    V(mutex); // 임계 구역 탈출
}

void test(int i) {
    if (state[(i + 4) % 5] != eating && state[i] == hungry && state[(i + 1) % 5] != eating) {
        state[i] = eating;
        V(self[i]); // 젓가락 2개를 들 수 있는 상태로 변경
    }
}
```

기존 세마포어 코드와는 다르게, self[i] 세마포어는 초기 값을 0으로 설정하는 것을 알 수 있다.

## Monitor

### 세마포어의 문제점

- 코딩하기 힘들다.
- 정확성의 입증이 어렵다.
- 자발적 협력이 필요하다.
- 한 번의 실수가 모든 시스템에 치명적인 영향을 끼친다.
    
![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F00947b68-068f-44ff-b52b-bc45551e7d2b%2FUntitled.png?table=block&id=55a044cc-e443-4664-8213-3d42ca9fc428&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
    

### 개념

모니터는 프로그래밍 언어 차원에서 동기화 문제를 해결할 수 있는 고수준 동기화 도구라고 할 수 있다. 

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7e38105d-fe5f-4f42-b8cd-75de18c13547%2FUntitled.png?table=block&id=c381f9fa-d3e1-47da-a468-0059c5223d2c&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

프로세스가 공유 데이터에 접근하기 위해서는 위와 같이 모니터 내부의 프로시저를 통해서만 공유 데이터를 접근할 수 있도록 설계한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F13504d56-cb84-4e66-8442-995246d28a7e%2FUntitled.png?table=block&id=a35139a3-1de0-4762-aa27-334475307948&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

예를 들어 공유 데이터들이 있으면 밖에서 아무나 접근할 수 있는 것이 아니라 모니터 내부에 공유 데이터에 접근하는 프로시저를 정의해 놓고 이 프로시저를 통해서만 접근하게 제어한다. 

모니터가 내부에 있는 프로시저는 동시에 여러 개가 실행되지 않도록 통제하는 권한을 준다. 즉 모니터 내에서는 한 번에 하나의 프로세스만이 활동이 가능하도록 제어하므로 프로그래머 입장에서 lock을 임의로 걸 필요가 없다는 장점이 생긴다.

모니터 안에 active한 프로세스가 없을 때, 외부 entry queue에서 기다리고 있는 프로세스가 들어와서 코드를 실행할 수 있게 된다.

**Condition Variable**

세마포어에서 자원의 개수를 세는 로직이 필요하듯이, 모니터도 자원의 개수를 세는 로직은 필요하다. (다만 모니터는 lock을 프로그래머가 걸 필요는 없다는 점이 세마포어와의 큰 차이점)

모니터는 조건 변수를 사용하고, 해당 변수는 wait 또는 signal 연산에 의해서만 접근 가능하도록 제한한다. 이때 조건 변수는 값을 카운트하거나 의미있는 값을 갖는 변수는 아니지만 프로세스를 잠들게 하거나 줄을 서게 하는 역할을 수행한다.

- wait
    - 자원이 없으면 프로세스가 줄을 서서 기다리게 한다.
    - `x.wati()` 을 호출한 프로세스는 다른 프로세스가 `x.signal()` 을 호출하기 전까지 잠들게 된다.
- signal
    - 모니터에 접근하고 빠져나갈 때 signal 연산을 호출해서 기다리는 프로세스가 모니터에 접근할 수 있도록 한다.
    - `x.signal()` 은 정확하게 하나의 잠이 든 프로세스를 깨운다.
    - 잠이 든 프로세스가 없으면 아무 일도 일어나지 않는다.
    

### 생산자 - 소비자 문제 코드를 모니터로 구현

```c
monitor bounded_buffer
{
    int buffer[N];
    // 조건 변수는 값을 가지지 않고, 자신의 큐에 프로세스를 매달아서
    // sleep 시키거나 큐에서 프로세스를 깨우는 역할만 수행함.
    condition full, empty;

    void produce(int x) {
        if (empty 버퍼가 없다.) {
            empty.wait();
        }
        empty 버퍼에 x를 추가한다.
        full.signal();
    }

    void consume (int *x) {
        if (full 버퍼가 없다.) {
            full.wait();
        }
        full 버퍼에서 데이터를 꺼내 간 뒤 *x에 값을 저장한다.
        empty.signal();
    }
```

- 생성하거나 소비하기 위해서는 반드시 모니터 내부 코드를 실행해야 한다.
- lock을 걸거나 푸는 일이 없다.

**생산자**

- empty 버퍼가 없으면 empty 버퍼를 기다리는 큐에서 대기한다. (wait 연산)
- empty 버퍼가 있다면 empty 버퍼에 데이터를 집어넣는다.
- 작업이 끝나면 `full.signal()` 를 통해 full 버퍼를 기다리는 큐에게 프로세스 하나를 깨우라고 알린다.

**소비자**

- full 버퍼가 없으면 full 버퍼를 기다리는 큐에서 대기한다. (wait 연산)
- full 버퍼가 있다면 full 버퍼의 데이터를 꺼내서 *x에 값을 저장한다.
- 작업이 끝나면 `empty.signal()` 를 통해 empty 버퍼를 기다리는 큐에게 프로세스 하나를 깨우라고 알린다.

### 식사하는 철학자 문제 코드를 모니터로 구현

```c
monitor dining_philosopher
{
    enum {thinking, hungry, eating} state[5];
    condition self[5];

    void pickup(int i) {
        state[i] = hungry;
        test(i);
        if (state[i] != eating) {
            self[i].wait();
        }
    }

    void putdown(int i) {
        state[i] = thinking;
        test[(i + 4) % 5];
        test[(i + 1) % 5];
    }

    void test(int i) {
        if ((state[(i + 4) % 5] != eating) && (state[i] == hungry) && (state[(i + 1) % 5] != eating) {
            state[i] = eating;
            self[i].signal();
        }
    }
}

// 각 철학자의 동작
Each Philosopher:
{
    pickup(i);
    eat();
    putdown(i);
    think();
} while (1)
```

**pickup**

- 자신의 상태를 배고픈 상태로 변경한다.
- 자신이 왼쪽 젓가락과 오른쪽 젓가락을 모두 들 수 있는지 확인한다.
    - 모두 들 수 있다면 자신의 상태를 먹는 상태로 변경하고, wait 큐에서 자신을 깨운다.
    - 모두 들 수 없다면 wait 큐에 대기한다.

**putdown**

- 식사를 마쳤으므로 자신의 상태를 생각 상태로 변경한다.
- 자신의 왼쪽과 오른쪽 철학자에게 식사할 수 있는 기회를 준다.
    - 여기서 기회라는 것은 무조건 식사할 수 있다는 의미는 아니고, `test()` 함수를 통해 식사가 가능한지 판단할 수 있다는 의미이다.

**test**

- 자신이 왼쪽 젓가락과 오른쪽 젓가락을 모두 들 수 있는지 확인하고, 가능하면 자신의 상태를 먹는 상태로 변경하고, wait 큐에서 자신을 깨운다.

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)
- [https://velog.io/@yuhyerin/kocw-운영체제-반효경-14.-프로세스-동기화3-고전적인-동기화-문제-3가지](https://velog.io/@yuhyerin/kocw-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EB%B0%98%ED%9A%A8%EA%B2%BD-14.-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%8F%99%EA%B8%B0%ED%99%943-%EA%B3%A0%EC%A0%84%EC%A0%81%EC%9D%B8-%EB%8F%99%EA%B8%B0%ED%99%94-%EB%AC%B8%EC%A0%9C-3%EA%B0%80%EC%A7%80)
