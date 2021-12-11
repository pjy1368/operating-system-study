### 멀티 레벨 큐 (Multi-Level Queue)

- Ready Queue를 우선 순위에 따라 여러 개로 분할한다.
    - 빠른 응답을 필요로 하는 대화형 작업은 전위 큐에 넣는다.
        - 전위 큐에서는 주로 라운드 로빈 스케줄링 기법을 사용한다.
    - 계산 위주의 작업은 후위 큐에 넣는다.
        - 후위 큐에서는 주로 FCFS 스케줄링 기법을 사용한다.
- 멀티 레벨 큐 자체에 대한 스케줄링이 필요하다.
    - 고정 우선 순위 방식 (Fixed Priority Scheduling)
        - 전위 큐에 있는 프로세스에게 우선적으로 CPU가 할당되고, 전위 큐가 비어 있는 경우에만 후위 큐에 있는 프로세스에게 CPU가 할당된다.
        - Starvation 가능성이 존재한다.
    - 타임 슬라이스 방식 (Time Slice)
        - 각 큐에 CPU time을 적절한 비율로 할당한다.
        - ex) 80%는 전위 큐, 20%는 후위 큐
        

### 멀티 레벨 피드백 큐 (Multi-Level Feedback Queue)

- 프로세스가 여러 개로 분할된 Ready Queue 내에서 다른 큐로 이동이 가능하다.
- 멀티 레벨 피드백 큐를 이용하여 aging 기법으로 구현할 수 있다.
    - aging 기법은 우선 순위가 낮은 큐에서 오래 기다렸으면 우선 순위가 높은 큐로 승격하는 방식이다.
- 멀티 레벨 피드백 큐를 정의하는 요소
    - 큐의 수
    - 각 큐의 스케줄링 알고리즘
    - 프로세스를 상위 큐로 승격하는 기준
    - 프로세스를 하위 큐로 강등하는 기준
    - 프로세스가 도착했을 때 들어갈 큐를 결정하는 기준
        - 보통 처음 들어오는 프로세스는 우선 순위가 가장 높은 큐에 CPU 할당 시간을 짧게 하여 배치한다.
        - 만약 주어진 할당 시간 안에 작업을 완료하지 못하면 CPU 할당 시간을 조금 더 주되, 우선 순위가 한 단계 낮은 큐로 강등한다.
        - 이 과정을 반복하다가 최하위 큐에 배치가 된다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd77349e4-ba3c-44a0-9d73-fa72a0c59c34%2FUntitled.png?table=block&id=53da3b15-c6b8-4ec0-bd07-66565ec5875d&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 다중 처리기 스케줄링 (Multi-Processor Scheduling)

- CPU가 여러 개인 시스템 환경에서 사용하는 기법이다.
- 프로세스를 Ready Queue에 한 줄로 세워서 각 CPU가 알아서 다음 프로세스를 꺼내어 가도록 한다.
- 일부 CPU에 작업이 편중되는 현상을 방지하기 위해 로드 밸런싱 메커니즘을 사용한다.
    - 대칭형 다중 처리
        - 각 CPU가 각자 알아서 스케줄링을 결정
    - 비대칭형 다중 처리
        - 하나의 CPU가 다른 모든 CPU의 스케줄링 및 데이터 접근을 책임지고 나머지 CPU는 거기에 따라 움직이는 방식

### 실시간 스케줄링 (Real-time Scheduling)

- 정해진 시간 안에 반드시 실행이 되어야 하는, 즉 deadline이 있는 프로세스일 때 사용하는 기법이다.
- 경성 실시간 시스템 (hard real-time system)
    - 정해진 시간 안에 반드시 작업이 끝나도록 스케줄링해야 한다.
- 연성 실시간 시스템 (soft real-time system)
    - 데드라인이 존재하기는 하지만 지키지 못했다고 해서 위험한 상황이 생기지는 않는다.
    - 일반 프로세스에 비해 높은 우선 순위를 갖도록 구현한다.

### 스레드 스케줄링 (Thread Scheduling)

- Local Scheduling
    - 유저 레벨 스레드의 경우 운영체제가 해당 스레드의 존재를 모른다.
    - OS가 아닌 사용자 프로세스가 직접 어느 스레드한테 CPU를 줄 것인지 결정한다.
- Global Scheduling
    - 커널 레벨 스레드의 경우 일반 프로세스처럼 커널의 단기 스케줄러가 어떤 스레드를 스케줄할 지 결정한다.

## 스케줄링 알고리즘의 평가

### 큐잉 모델

- 주로 이론가들이 수행하는 방식이다.
- 확률 분포를 통해 프로세스들의 도착률과 CPU의 처리율을 입력값으로 주면, 복잡한 수학적 계산을 통해 각종 성능 지표인 CPU의 처리량, 프로세스의 평균 대기 시간 등을 구하게 된다.

### 구현 및 실축

- 주로 구현가들이 수행하는 방식이다.
- 운영 체제 커널의 소스 코드 중 CPU 스케줄링을 수행하는 코드를 수정해서 커널을 컴파일한 후 시스템에 설치한다. 그런 다음 동일한 프로그램을 원래 커널과 CPU 스케줄러를 수정한 커널에서 수행해 보고 실행 시간을 측정한다.

### 시뮬레이션

- 가상으로 CPU 스케줄링 프로그램을 작성한 후 프로그램의 CPU 요청을 입력값으로 넣어 어떠한 결과가 나오는지 확인한다.
- 입력값은 가상으로 생성할 수도 있고 실제 시스템에서의 CPU 요청 내역을 추출해 사용할 수도 있다.
    - 실제 시스템에서 추출한 입력값을 트레이스 (trace)라고 부른다.
    - 트레이스는 몇 초에 어떤 프로세스가 도착하고, 각각 CPU  버스트 시간을 얼마로 하는지에 대한 정보를 시간 순서대로 적어 놓은 파일을 말한다.

## 데이터 접근

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4457309e-3421-4058-90d0-a7a071630732%2FUntitled.png?table=block&id=b94b3ae3-3045-4230-8450-a038b4c90a7a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

컴퓨터 시스템에서 데이터 연산은 저장 공간과 실행 공간이 아래와 같은 흐름으로 동작하면서 이루어 진다.

1. 저장 공간에 데이터가 있다.
2. 연산할 데이터를 실행 공간으로 가져온다.
3. 실행 공간에서 연산한다.
4. 연산 결과를 저장 공간에 반영한다.

저장 공간은 메모리나 해당 프로세스의 주소 공간, 디스크 등이 있고, 실행 공간은 CPU나 프로세스, 컴퓨터 내부 등이 있다.

## Race Condition

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe638dedb-9994-4ddd-9b8d-ec97f2b89799%2FUntitled.png?table=block&id=a8e30354-fe5c-47de-a94e-38dd3be2c964&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

저장 공간을 공유하는 실행 공간이 여러 개 있는 경우 Race Condition의 가능성이 있다. 예를 들어 메모리에 count 변수가 있고 서로 다른 CPU가 각각 증가 연산, 감소 연산을 한다고 가정해 보자.

정상적인 동작을 한다면 CPU A가 count 변수를 가져와 1 증가하여 메모리에 반영하고, CPU B가 count 변수를 가져와 1 감소하고 다시 메모리에 반영하여 count 변수 값의 변화가 없을 것이다. 하지만 CPU A에서 증가 연산을 하는 동안 CPU B가 메모리에 있는 count 변수를 가져가서 연산한다면 결과는 `(count - 1)`이 저장될 것이다. 이처럼 공유하는 하나의 주체에 여러 주체가 마치 경쟁하듯 접근하려 하는 것을 경쟁 상태, 즉 race condition이라고 한다.

- 운영 체제에서 race condition이 발생하는 상황
    - 커널 수행 중 인터럽트 발생 시
    - 프로세스가 시스템 콜을 호출하여 커널 모드로 수행 중인 가운데 context switch가 일어나는 경우
    - 멀티 프로세서에서 공유 메모리 내의 커널 데이터

## 운영 체제에서의 race condition

### 인터럽트 핸들러 vs 커널

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5e3adc74-6156-41a1-aaf4-0f0ca306ce8a%2FUntitled.png?table=block&id=a2496ca7-c98d-4542-bb53-b831a4763f08&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

커널 모드가 수행 중인 상태에서 인터럽트가 발생하여 인터럽트 처리 루틴이 실행되는 상황이다. 커널이 증감 연산을 할 때 메모리에서 count 변수를 load하고, count 변수를 증감하고 count 변수를 메모리에 store하는 3가지 연산이 발생한다. 

만약 load 연산을 수행한 후 증감 연산을 수행하기 전에 인터럽트가 들어왔다고 가정하자. 그러면 인터럽트 핸들러를 통해 인터럽트를 수행할 것이다. 이때 하필 인터럽트가 공유 변수인 count를 1 감소하는 연산이었고, 인터럽트가 끝난 이후 커널은 이전 연산 과정인 load 이후부터 수행하므로 count는 감소하지 않은 상태이다. 결과적으로 count는 기존 값에서 1이 증가된 수치가 된다.

이와 같이 중요한 변수의 값을 건드리는 동안에는 인터럽트가 발생해도 연산이 끝나고 수행될 수 있게끔 disable 처리를 해 준다.

### 커널 내에서 실행 중인 프로세스를 선점하는 경우

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F008849e3-23e8-41d5-a8d9-1aa3ee4c9af6%2FUntitled.png?table=block&id=78f88c21-996e-4e03-87ca-0314cc9fbc4c&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

프로세스 A가 count 변수를 load하고, 1 증가하기 위해 시스템 콜을 호출하여 커널 모드에서 작업을 처리하는 도중에 CPU 할당 시간이 끝나 context switch가 발생하였다. 이후 프로세스 B가 CPU를 할당 받아 A와 동일하게 시스템 콜을 호출했고, count 변수를 1 증가하였다. 그리고 다시 context switch가 발생하여 프로세스가 A가 CPU를 잡아 count 변수를 1 증가하였다. 

count 변수 값이 2 증가해야 하는데 결과는 그렇지 않다. 프로세스 A는 프로세스 B가 증가 연산을 하기 전 count 값을 갖고 있었고, CPU를 다시 할당받았을 때 그 시점의 context를 가지고 값을 증가하였기 때문에 결과적으로 1만 증가하였다.

이를 해결하기 위해 커널 모드에서 수행 중일 때는 CPU 할당 시간이 끝나도 선점하지 않고, 사용자 모드로 돌아갔을 때 선점한다.

### 멀티 프로세서

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8a2a9043-b56b-4092-924b-26352f8aba2c%2FUntitled.png?table=block&id=87aa6590-a910-43a7-b337-aa3a4630c0f7&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

CPU가 메모리에서 데이터를 가져 오기 전에 lock을 걸어 다른 CPU가 같은 데이터에 접근하는 것을 막아 준다. 연산이 끝난 후 데이터를 다시 메모리에 저장할 때 lock을 풀어 줌으로써 다른 CPU가 접근할 수 있게 해 준다.

- 방법 1
    - 한 번에 하나의 CPU만 커널에 들어갈 수 있게 하는 방법
    - 커널 전체에 lock을 걸기 때문에 비효율적이다.
- 방법 2
    - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 해당 데이터에 lock을 거는 방법

## 프로세스 동기화 문제

- 공유 데이터의 동시 접근은 데이터의 불일치 문제를 유발할 수 있다.
- 일관성 유지를 위해서는 협력 프로세스 간의 실행 순서를 정해 주는 메커니즘이 필요하다.
- Race condition
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- race condition을 막기 위해서는 병행 (concurrent) process는 동기화되어야 한다.

## 임계 구역 문제 (The Critical-Section Problem)

- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 임계 구역 (critical section)이 존재한다.
- 하나의 프로세스가 임계 구역에 있을 때 다른 모든 프로세스는 임계 구역에 들어갈 수 없어야 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2cb3651d-b280-45a2-a052-d3a4c6fbb0c4%2FUntitled.png?table=block&id=e87ab55a-c6f2-42ef-93e0-2ae7e703aae3&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 출처

- [https://sangminlog.tistory.com/entry/process-synchronization?category=887652](https://sangminlog.tistory.com/entry/process-synchronization?category=887652)
- 운영 체제와 정보 기술의 원리 - 반효경
