## CPU burst와 I/O burst

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6931e79b-aa89-4619-9b72-ec30cbafa705%2FUntitled.png?table=block&id=02113bf4-a8c1-448e-932a-a23d56fd01dc&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### CPU burst

CPU 버스트는 사용자 프로그램이 CPU를 직접 가지고 빠른 명령을 수행하는 단계이다. 이 단계에서 사용자 프로그램은 CPU 내에서 일어나는 명령 (ex. Add)이나 메모리(ex. Store, Load)에 접근하는 일반 명령을 사용할 수 있다.

### I/O burst

I/O 버스트는 커널에 의해 입출력 작업을 진행하는 비교적 느린 단계이다. 이 단계에서는 모든 입출력 명령을 특권 명령으로 규정하여 사용자 프로그램이 직접 수행할 수 없도록 하고, 대신 운영체제를 통해 서비스를 대행하도록 한다.

이처럼 사용자 프로그램이 수행되는 과정은 CPU 작업과 I/O 작업의 반복으로 구성된다.

## CPU bound process와 I/O bound process

각 프로그램마다 CPU 버스트와 I/O 버스트가 차지하는 비율이 균일하지 않다. 어떤 프로세스는 I/O 버스트가 빈번해 CPU 버스트가 매우 짧은 반면, 어떤 프로세스는 I/O를 거의 하지 않아 CPU 버스트가 매우 길게 나타난다. 이와 같은 기준에서 프로세스를 크게 I/O 바운드 프로세스와 CPU 바운드 프로세스로 나눌 수 있다.

### I/O 바운드 프로세스

I/O 요청이 빈번해 CPU 버스트가 짧게 나타나는 프로세스를 말한다. 주로 사용자로부터 인터랙션을 계속 받아가며 프로그램을 수행하는 대화형 프로그램이 해당된다.

### CPU 바운드 프로세스

I/O 작업을 거의 수행하지 않아 CPU 버스트가 길게 나타나는 프로세스를 말한다. 주로 프로세스 수행의 상당 시간을 입출력 작업 없이 CPU 작업에 소모하는 계산 위주의 프로그램이 해당된다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7afa61df-843f-4ec4-bbf3-c60882deabab%2FUntitled.png?table=block&id=7f83d7a1-91ac-4490-9bad-cfc20e08b92b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

프로그램이 수행되는 구조를 보면 I/O 바운드 프로세스는 짧은 CPU 버스트를 많이 가지고 있는 반면, CPU 바운드 프로세스는 소수의 긴 CPU 버스트로 구성된다는 것을 알 수 있다.

## CPU 스케줄링

컴퓨터 시스템 내에서 수행되는 프로세스의 CPU 버스트를 분석해보면 대부분의 경우 짧은 CPU 버스트를 가지며, 극히 일부분만 긴 CPU 버스트를 갖는다. 이는 다시 말해서 CPU를 한 번에 오래 사용하기보다는 잠깐 사용하고 I/O 작업을 수행하는 프로세스가 많다는 것이다. 즉 대화형 작업을 많이 수행해야 하는데, 사용자에 대한 빠른 응답을 위해서는 해당 프로세스에게 우선적으로 CPU를 할당하는 것이 바람직하다. 만약 CPU 바운드 프로세스에게 먼저 CPU를 할당한다면 그 프로세스가 CPU를 다 사용할 때까지 수많은 I/O 바운드 프로세스는 기다려야할 것이다. 이러한 이유로 CPU 스케줄링이 필요해졌다.

### CPU 스케줄러

CPU 스케줄러는 준비 상태에 있는 프로세스들 중 어떠한 프로세스에게 CPU를 할당할 지 결정하는운영체제의 코드이다.

**CPU 스케줄러가 필요한 경우**

- Running → Blocked (ex. I/O 요청하는 시스템 콜)
- Running → Running (ex. 할당 시간 만료로 인한 타이머 인터럽트)
- Blocked → Ready (ex. I/O 완료 후 인터럽트)
- Terminated

1, 4번째의 스케줄링은 강제로 빼앗지 않고 자진 반납하는 `nonpreemptive` 방식이고, 그 외의 스케줄링은 CPU를 강제로 빼앗는 `preemptive` 방식이다. 전자를 비선점형 스케줄링, 후자를 선점형 스케줄링이라고 부른다. 참고로 3번째 스케줄링은 Context Switch가 발생할 때만 해당한다.

### 디스패처 (Dispatcher)

CPU를 누구한테 누구한테 줄 지 결정했으면 해당 프로세스에게 넘겨야 하는데, 디스패처가 이 역할을 수행한다. 이 과정이 Context Switch에 해당한다.

### 스케줄링의 성능 평가 (Scheduling Criteria)

**시스템 입장**

- CPU utilization (이용률)
    - 전체 시간 중에서 CPU가 일을 한 시간의 비율
    - keep the CPU as busy as possible
- Throughput (처리량)
    - 주어진 시간동안 준비 큐에서 기다리고 있는 프로세스 중 몇 개를 끝마쳤는지 나타낸다.
    - 즉 CPU의 서비스를 원하는 프로세스 중 몇 개가 원하는 만큼의 CPU를 사용하고 이번 CPU 버스트를 끝내어 준비 큐를 떠났지 측정한 것이다.
    - 더 많은 프로세스들이 CPU 작업을 완료하기 위해서는 CPU 버스트가 짧은 프로세스에게 우선적으로 CPU를 할당하는 것이 유리하다.
    - number of processes that complete their execution per time until

**프로세스 입장**

- Turnaround time (소요 시간, 반환 시간)
    - 프로세스가 CPU를 요청한 시점부터 자신이 원하는 만큼 CPU를 다 쓰고 CPU 버스트가 끝날 때까지 걸린 시간을 뜻한다.
    - (준비 큐에서 기다린 시간) + (실제로 CPU를 사용한 시간)
    - amount of time to execute a particular process
- Waiting time (대기 시간)
    - CPU 버스트 기간 중 프로세스가 준비 큐에서 CPU를 얻기 위해 기다린 시간의 합을 뜻한다.
    - 시분할 시스템의 경우, 한 번의 CPU 버스트 중에도 준비 큐에서 기다린 시간이 여러 번 발생할 수 있다.
    - 이때 대기 시간은 CPU 버스트가 끝나기까지 준비 큐에서 기다린 시간의 합을 뜻하게 된다.
    - amount of time a process has been waiting in the ready queue
- Response time (응답 시간)
    - 프로세스가 준비 큐에 들어온 후 첫 번째 CPU를 획득하기까지 기다린 시간을 뜻한다.
    - 응답 시간은 대화형 시스템에 적합한 성능 척도로서, 사용자 입장에서 가장 중요한 성능 척도라고 할 수 있다.
    

**생활 속에서의 비유를 통한 스케줄링의 성능 평가 이해**

- 중국집에 주방장과 손님이 있다.
- 이용률, 처리률 → 중국집 입장에서의 척도
    - 이용률: 전체 시간 중 주방장이 일한 시간의 비율
    - 처리률: 주방장이 정해진 시간 동안 요리를 만들어 준 손님의 수
- 소요 시간, 대기 시간, 응답 시간 → 손님 입장에서의 척도
    - 소요 시간: 손님이 중국집에 들어와서 주문한 음식을 다 먹고 나가기까지 소요된 총 시간
    - 대기 시간: 음식을 먹은 시간을 제외한 순수하게 기다린 시간 (음식이 조금씩 여러 번 나왔더라도 중간 중간 기다린 시간을 다 합쳐야 함)
    - 응답 시간: 최초의 음식이 나오기까지 기다린 시간
    

## 스케줄링 알고리즘 (Scheduling Algorithms)

### 선입 선출 스케줄링 (FCFS, First-Come First-Served)

- 먼저 온 순서대로 처리하는 방식 (`nonpreemptive`) = (비선점)
- CPU를 오래 쓰는 프로세스가 먼저 와서 CPU를 할당 받으면, 나머지 프로세스들은 전부 기다려야하므로 효율적이지 않음

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3ce679c7-e144-4abc-bf98-f05d2fb75c6b%2FUntitled.png?table=block&id=81efd224-c8c9-498c-8301-0cef8c7a66d3&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 위 사진처럼 어떤 프로세스가 먼저 실행되느냐에 따라 전체 대기 시간에 상당한 영향을 미침
    - 1번
        - 대기 시간: P1 = 0, P2 = 24, P3 = 27
        - 평균 대기 시간: (0 + 24 + 27) / 3 = 17
    - 2번
        - 대기 시간: P1 = 6, P2 = 0, P3 = 3
        - 평균 대기 시간: (6 + 0 + 3) / 3 = 3
- 긴 프로세스 하나 때문에 짧은 프로세스 여러 개가 기다리는 현상을 `Convoy effect` 라 부름

### 최단 작업 우선 스케줄링 (SJF, Shortest-Job-First)

- SJF는 CPU 버스트가 가장 짧은 프로세스에게 제일 먼저 CPU를 할당하는 방식
- 평균 대기 시간을 가장 짧게 하는 최적 알고리즘
- 두 가지 방식
    - nonpreemptive (비선점형)
        - 일단 CPU를 잡으면 더 짧은 프로세스가 들어와도 CPU 버스트가 완료될 때까지 CPU를 선점당하지 않음.
    - preemptive (선점형)
        - SRTF (Shortest-Remaining-Time-First)
        - CPU를 잡았다 하더라도 더 짧은 프로세스가 들어오면 CPU를 뺴앗김.
- 문제점
    - Starvation (기아): 짧은 프로세스로 인해 긴 프로세스가 영원히 CPU를 잡지 못할 수 있음
    - CPU 버스트 시간을 미리 알 수 없음: 과거 CPU 사용 시간을 통해 추정만 가능
    

**비선점형 방식 예시**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb2e8b44a-8ae7-4208-906a-7c0fe0940f6a%2FUntitled.png?table=block&id=e1b58b78-5783-4b35-9514-00386fa1dc03&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**선점형 방식 예시**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F57a006cb-39ce-4913-adfa-ec036015fa07%2FUntitled.png?table=block&id=11e49da6-2cd9-484d-9c1c-a2deb4de4f9e&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 우선 순위 스케줄링 (Priority Scheduling)

- 우선 순위가 제일 높은 프로세스에게 CPU를 할당
- 일반적으로 우선 순위 값 (priority number)가 작을 수록 높은 우선 순위를 갖는다.
- 두 가지 방식
    - nonpreemptive (비선점형)
        - 일단 CPU를 잡으면 더 높은 우선 순위를 가진 프로세스가 들어와도 CPU 버스트가 완료될 떄까지 CPU를 선점당하지 않음.
    - preemptive (선점형)
        - CPU를 잡았다 하더라도 더 높은 우선 순위를 가진 프로세스가 들어오면 CPU를 빼앗김
- SJF는 일종의 우선 순위 스케줄링이라고 볼 수 있음 (우선 순위 = 예상되는 다음 CPU 버스트 시간)
- 문제점
    - Starvation (기아): 우선 순위가 낮은 프로세스는 영원히 CPU를 잡지 못할 수 있음
- 해결 방안
    - Aging (노화): 아무리 우선 순위가 낮은 프로세스라 하더라도 시간이 오래 지나면 우선 순위를 높여주는 기법.

### 라운드 로빈 스케줄링 (RR, Round Robin)

- 각 프로세스는 동일한 크기의 할당 시간인 `time quantum` 을 가짐
- 할당 시간이 지나면 프로세스는 CPU를 빼앗기고 Ready Queue 맨 뒤에 가서 줄을 서게 됨
- 짧은 응답 시간을 보장함
    - 조금씩 CPU를 줬다 뺏었다를 반복하기 때문에 CPU를 최초로 얻기까지 걸리는 시간이 짧음
    - n개의 프로세스가 Ready Queue에 있고, 할당 시간이 q time unit인 경우 어떤 프로세스도 `(n - 1) * q time unit` 이상 기다리지 않음
    - CPU를 할당받기 위해 기다리는 시간이 CPU 버스트에 비례
- 성능
    - q가 커질 수록 FCFS에 가까워짐
    - q가 작을 수록 context switch 오버헤드가 증가함
- 일반적으로 SJF보다 평균 turnaround time (소요 시간)이 길지만, 응답 시간은 짧음
- 시간이 오래 걸리는 job과 짧게 걸리는 job이 섞여 있을 때는 효율적이지만, 모든 시간이 동일한 job만 있을 때는 비효율적이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2ef65149-2145-4990-84c2-5d84630bb7ac%2FUntitled.png?table=block&id=b8e516ac-4e24-4355-b4c8-98999ede3e63&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

위와 같이 라운드 로빈은 일정 할당 시간(20)을 공평하게 할당 받아서 CPU 사용이 가능하다.

## 출처

- [https://sangminlog.tistory.com/entry/cpu-scheduling](https://sangminlog.tistory.com/entry/cpu-scheduling)
- 운영 체제와 정보 기술의 원리 - 반효경
