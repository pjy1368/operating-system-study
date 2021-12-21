## 데드락 문제

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2a000c29-a0fd-409f-be95-4274fdad18fd%2FUntitled.png?table=block&id=c4df7f20-ff9d-4d72-81b4-f20312e6fd60&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 데드락

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

### Resource (자원)

- 하드웨어, 소프트웨어 등을 포함하는 개념
    - ex) I/O device, CPU cycle, memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차
    - Request, Allocate, Use, Release

### 예시

- 1번째 예시
    - 시스템에 2개의 tape drive가 있다.
    - 프로세스 P1과 P2 각각 하나의 다른 tape drive를 보유한 채 다른 하나를 기다리고 있다.
- 2번째 예시
    - 이진 세마포어 A와 B가 있다.
    - 프로세스 P1이 A 자원을 얻은 상태에서 context switch가 발생하여 프로세스 P2에게 CPU 제어권이 넘어갔고, 프로세스 P2가 B 자원을 얻었다.
    - 다시 context switch가 발생하여 프로세스 P1에게 CPU 제어권이 넘어갔고, 프로세스 P1이 B 자원을 얻고 싶어하지만 이미 프로세스 P2가 B 자원을 가지고 있다.
    - 마찬가지로 프로세스 P2이 A 자원을 얻고 싶어하지만 이미 프로세스 P1이 A 자원을 가지고 있다.
    - 무한히 서로를 기다린다.
    

## 데드락 발생의 4가지 조건

### Mutual exclusion (상호 배제)

- 매 순간 하나의 프로세스만이 자원을 사용할 수 있음.

### No preemption (비선점)

- 프로세스는 자원을 내어 놓을 뿐 강제로 빼앗기지 않음.

### Hold and wait (보유 대기)

- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음.

### Circular wait (점유 대기)

- 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함.
- 프로세스 P0, P1, ... , Pn이 있을 때
    - P0은 P1이 가진 자원을 기다림.
    - P1은 P2가 가진 자원을 기다림.
    - P(n-1)은 Pn이 가진 자원을 기다림.
    - Pn은 P0이 가진 자원을 기다림.
    

## Resource-Allocation Graph (자원 할당 그래프)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffde1ce70-73ed-447c-94b9-52d843b59cdb%2FUntitled.png?table=block&id=fdba29f1-9c18-48cc-9534-4f2b0bd6139a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 정점은 프로세스 혹은 자원을 나타내며, 간선은 자원을 요청하거나 할당한다는 의미이다.

### 데드락 판단

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff544e8ea-6225-44ca-ac7b-bdd9871e293d%2FUntitled.png?table=block&id=7eb80bee-0520-4ef5-9591-9ceb56aba1cd&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 그래프에 cycle이 없으면 데드락이 아니다.
- 그래프에 cycle이 있으면
    - 자원 당 하나의 인스턴스만 있는 경우에는 데드락이다.
    - 자원 당 여러 인스턴스가 존재하는 경우에는 데드락 가능성이 있다.
- 왼쪽 그래프는 P1에게 R2 자원 1개가 할당되어 있고, P2에게 R2 자원이 할당되어 있는 상황에서 P3가 R2 자원 하나를 요구한다. P2는 R3 자원을 요청하고, R3는 P3가 가지고 있다. 이때 P1은 R1을 요청하지만, 이것은 P2가 가지고 있으므로 모든 프로세스가 자원을 반납할 수 없다. (데드락)
- 오른쪽 그래프는 P1에게 R2 자원이 할당되어 있고, P4에게 R2 자원이 할당되어 있는 상황이다. P3가 R2 자원을 요구하면 P4가 R2 자원을 반납하면 되므로 문제가 없다. (데드락 아님)

## 데드락의 처리 방법

### Deadlock Prevention (데드락 예방)

- 자원 할당 시 데드락의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

### Deadlock Avoidance (데드락 회피)

- 자원 요청에 대한 부가적인 정보를 이용해서 데드락의 가능성이 없는 경우에만 자원을 할당
- 시스템 상태가 원래 상태로 돌아올 수 있는 경우에만 자원 할당

### Deadlock Detection and recovery (데드락 탐지 및 회복)

- 데드락 발생은 허용하되, 그에 대한 탐지 루틴을 두어 데드락 발견시 회복

### Deadlock Ignorance (데드락 무시)

- 데드락을 시스템이 책임지지 않음
- UNIX를 포함한 대부분의 OS가 채택

## Deadlock Prevention (데드락 예방)

### Mutual Exclusion (상호 배제)

- 공유해서는 안되는 자원의 경우 반드시 성립해야 함.

### Hold and Wait (보유 대기)

- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
- 방법 1. 프로세스 시작 시 모든 필요한 자원을 할당 받게 하는 방법
- 방법 2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

### No Preemption (비선점)

- 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨
- 상태를 쉽게 저장하고 복구할 수 있는 자원에서 주로 사용 (CPU, memory)

### Circular Wait (순환 대기)

- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원을 할당
- ex) 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj를 할당 받기 위해서는 우선 Ri를 반납해야 함

⇒ Utilization 저하, throughput 감소, starvation 문제

## Deadlock Avoidance (데드락 회피)

- 자원 요청에 대한 부가 정보를 이용해서 자원 할당이 데드락으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임
- 시스템이 안전 상태에 있으면 데드락이 아니며, 불안전 상태에 있으면 데드락의 가능성이 있다.
- 데드락 회피는 시스템이 불안전 상태에 들어가지 않는 것을 보장한다.
    - 자원 유형 당 1개의 인스턴스만 존재
        - 자원 할당 그래프 알고리즘
    - 자원 유형 당 여러 개의 인스턴스 존재
        - 은행원 알고리즘

### safe state (안전 상태)

- 시스템 내의 프로세스들에 대한 safe sequence(안전 순서열)가 존재하는 상태

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4a662869-0ab0-4b2e-8e42-d2f1467578e2%2FUntitled.png?table=block&id=1942bc9f-0bfa-4073-ae2b-eec5663421d2&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### safe sequence (안전 순서열)

- 프로세스의 sequence <P1, P2, ... , Pn>이 안전하려면 Pi (1 ≤ i ≤ n)의 자원 요청이 **“가용 자원 + 모든 Pj (j < i)의 보유 자원”**에 의해 충족되어야 함
- 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
    - Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj (j  < i)가 종료될 때까지 기다린다.
    - P(i-1)이 종료되면 Pi의 자원 요청을 만족하여 수행한다.

### 자원 할당 알고리즘

- Claim edge (예약 간선) Pi → Rj
    - 프로세스 Pi가 자원 Rj를 미래에 요청을 할 수 있음을 뜻함 (점선으로 표시)
    - 프로세스가 해당 자원 요청 시 request edge(요청 간선)으로 바뀜 (실선으로 표시)
    - Rj가 반납되면 assignment edge(할당 간선)은 다시 예약 간선으로 바뀐다.
- 요청 간선의 할당 간선 변경 시 (점선을 포함하여) 사이클이 생기지 않는 경우에만 요청 자원을 할당한다.
- 사이클 생성 여부 조사시 프로세스의 수가 n일 때 O(n^2) 시간이 걸린다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffc207bbf-352d-4793-aaaf-a01f83358a45%2FUntitled.png?table=block&id=03a5a8ca-5e36-4a1b-8c75-d021a9ea84d7&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 은행원 알고리즘

- 가정
    - 모든 프로세스는 자원의 최대 사용량을 미리 명시
    - 프로세스가 요청 자원을 모두 할당받은 경우 유한 시간 안에 이들 자원을 다시 반납한다.
- 방법
    - 기본 개념: 자원 요청시 안전 상태를 유지할 경우에만 할당
    - 총 요청 자원의 수가 가용 자원의 수보다 적은 프로세스를 선택 (그런 프로세스가 없으면 불안전 상태)
    - 그런 프로세스가 있으면 그 프로세스에게 자원을 할당
    - 할당받은 프로세스가 종료되면 모든 자원을 반납
    - 모든 프로세스가 종료될 때까지 이러한 과정을 반복

**예제**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F52f3048b-76a6-4ba6-bf4a-61c24c0891d9%2FUntitled.png?table=block&id=0be6ba22-6744-4791-ae21-35cf99e4df78&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- A 자원은 10개의 인스턴스, B 자원은 5개의 인스턴스, C 자원은 7개의 인스턴스가 존재한다.
- Allocation은 현재 각 프로세스에 할당된 자원의 수를 뜻한다.
- Max는 각 프로세스마다 최대로 할당 받고 싶은 자원의 수를 뜻한다.
- Available은 각 자원이 프로세스에게 추가로 할당 할 수 있는 가용 자원의 수를 뜻한다.
- Need는 각 프로세스가 현재 최대로 필요로 하는 자원의 수를 뜻하며, Max에서 Allocation을 뺀 값이다.
- 자원은 현재 가용 자원을 보고, Need만큼 자원을 줄 수 있는 프로세스를 하나도 찾지 못하면 불안전한 상태가 된다. 있다면 해당 프로세스에게 자원을 주고, 프로세스가 끝날 때 모든 자원을 가져온다. 이 과정을 반복하면 <P1, P3, P4, P2, P0>라는 안전 순서열을 만들 수 있다.

## Deadlock Detection and Recovery (데드락 탐지 및 회복)

### 데드락 탐지

- 데드락 탐지
- 자원 유형 당 하나의 인스턴스만 존재
    - Wait-for 그래프 알고리즘
        - 자원 유형 당 하나의 인스턴스만 존재
        - Wait-for 그래프
            - 자원 할당 그래프의 변형
            - 프로세스만으로 node 구성
            - Pj가 가지고 있는 자원을 Pk가 기다리고 있는 경우 Pk → Pj
        - 알고리즘
            - Wait-for 그래프에 cycle이 존재하는지 주기적으로 조사
            - 시간 복잡도는 O(N^2)
- 자원 유형 당 여러 개의 인스턴스가 존재
    - 은행원 알고리즘과 유사한 방법 활용

**Wait-for 그래프 (자원 당 하나의 인스턴스)**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc30375b8-1751-4116-9d98-ffdc91c4c65c%2FUntitled.png?table=block&id=51428024-11d5-4e99-9f38-131e0f45ea20&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 자원을 빼 버리고 실선을 이으면 됨

**은행원 알고리즘 변형 (자원 당 여러 개의 인스턴스)**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1a5a0333-cfe4-471c-822a-5bfb8bf7bef9%2FUntitled.png?table=block&id=e1aaa666-b456-47a5-82e8-c03f7c9f4b02&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- Request는 추가 요청 가능량이 아니고 현재 실제로 요청한 자원량을 나타낸다.
- <P0, P2, P3, P1, P4> 라는 안전 순서열이 존재하므로 데드락이 아니다.
- 기존 은행원 알고리즘에 비해 현재를 중시하고 낙관적인 알고리즘이라 볼 수 있다.

### 데드락 회복

- 프로세스 종료
    - 모든 데드락 상태인 프로세스를 강제 종료
    - 데드락 사이클이 없어질 때까지 하나씩 프로세스를 강제 종료
- 자원 선점
    - 비용을 최소화할 희생 프로세스를 선정
    - 안전 상태로 롤백하여 프로세스를 재시작
    - Starvation 문제
        - 동일한 프로세스가 계속 희생 프로세스로 선정되는 경우
        - 비용 요소에 롤백 횟수도 같이 고려해야 함 (단순히 비용만 최소화하면 안 된다는 뜻)
        

## Deadlock Ignorance (데드락 무시)

- 데드락이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음
    - 데드락이 매우 드물게 발생하므로 데드락에 대한 조치 자체가 더 큰 오버헤드일 수 있음
    - 만약, 시스템에 데드락이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 프로세스를 죽이는 등의 방법으로 대처함
    - UNIX, Windows 등 대부분의 OS에서 채택하였음

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f)
- [https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)
