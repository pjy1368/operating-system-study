## CPU and I/O Bursts in Program Execution

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eaae61af-4cb4-4570-be49-40ff32f4172c/Untitled.png)

### CPU burst

CPU만 사용하면서 연속적으로 명령을 실행하는 단계

### I/O burst

사람과 소통을 해야하는 I/O를 실행하는 단계

***** 프로그램의 실행은 CPU Burst 와 I/O Burst 의 반복으로 이루어진다.**

프로그램의 종류에 따라 각 Burst의 빈도와 길이가 다르기는 하다.

Ex_ 유전체 분석프로그램, 사람과 소통하는 프로그램 등

## CPU-burst Time의 분포

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4fd0cf46-bc2b-427b-a53a-a3a335e0f650/Untitled.png)

### I/O bound job (= I/O bound process)

CPU를 가지고 계산하는 시간보다, I/O에 많은 시간이 필요한 작업
 (= 시간이 오래걸림)

### CPU bound job (= CPU bound process)

CPU를 오래 가지고 계산을 하는 작업

효율성을 위해서는 CPU 스케줄링을 통해서 사용자와 직접 소통하는 작업(= I/O bound job)과 CPU bound job을 적절하게 섞어서 사용할 수 있게 해야한다. 

## CPU Scheduler & Dispatcher

---

### CPU Scheduler

 - Ready 상태의 프로세스 중세서 이번에 CPU를 줄 프로세스를 고른다.

- **CPU 스케줄링이 필요한 경우, 프로세스의 상태변화**
    1. Running → Blocked (ex_ I/O를 요청하는 시스템 콜)
    2. Running → Ready (ex_ 할당시간 만료로 인한 timer interrupt)
    3. Blocked → Ready (ex_ I/O 완료 후 인터럽트)
    4. Terminate

**1, 4에서의 스케줄링은 강제로 빼앗지 않고 자진반납하는
nonpreemptive (비선점형) 스케줄링이라고 한다.**

(오래걸리는 작업을 하는 1번, 프로그램이 종료된 4번)

**다른 모든 스케줄링은 강제로 빼앗는** **preemptive (선점형) 스케줄링이다.**

### Dispatcher

 - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.

 ⇒ 이 과정을 문맥교환이라고 한다.)

## Scheduling Criteria (스케줄링의 성능척도)

---

### **시스템 입장**

- CPU utilization (이용률)
    - 전체 시간 중에서 CPU가 일을 진행한 비율
    
    (ex_ 주방장이 전체 업무 시간동안 일을 한 비율)
    
- Throughput (처리량)
    - 주어진 시간동안 CPU가 몇개의 일을 진행했는지
    
    (ex_ 시간당 몇명의 손님이 밥을 먹고 나갔는가)
    

### **프로세스(=고객) 입장**

- Turnaround time (소요 시간, 반환 시간)
    - 프로세스가 CPU를 가지고 다 쓸 때 까지 소요한 시간
    - 프로세스가 Ready 큐에 있다가 CPU를 쓰러 들어온 후 CPU를 다 쓰고 I/O를 하러 나갈 때 까지의 시간
    
    (ex_ 손님이 중국집에 들어와서 주문을 하고 밥을 먹고 나갈 때 까지 걸린 시간)
    
- Waiting time (대기 시간)
    - 프로세스가 CPU가 Ready 큐에 줄서서 기다린 시간
    
    (ex_ 손님이 기다린 시간 [음식 주문 등])
    
- Response time (응답 시간)
    - 프로세스가 CPU를 가지기까지 걸린 시간
    
    (ex_ 음식이 나오기 까지 걸린 시간)
    

## 스케줄링 알고리즘

---

### FCFS (First-come First-Served, 비선점형 스케줄링)

먼저 도착한 순서대로 처리하는 알고리즘이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adabb80c-bf14-402a-8f22-e440aedb7c58/Untitled.png)

처리하는데 걸리는 시간

- P1 = 24, P2 = 3, P3 = 3

대기 시간 (평균 대기시간 [ (0 + 24 + 27) / 3 = 17 ])

- P1 = 0, P2 = 24, P3 = 27

뒤에 있는 프로그램이 시간이 짧게 걸리게 되더라도 앞에있는 프로그램의 처리시간이 길면 대기 시간이 길어지게 되는 현상

⇒ **Convoy effect, 호위 효과**

### SJF (Shortest-Job-First)

CPU 처리시간이 가장 짧은 것 부터 처리하는 알고리즘이다.

두 가지 방식을 이용할 수 있다.

### 비선점형 방식

- CPU를 잡게되면 이번 CPU burst가 완료 될 때 까지 CPU를 뺏기지 않는다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5676ca3d-2437-4cad-95c6-8c4f5e80dba8/Untitled.png)

처리하는데 걸리는 시간

- P1 = 7, P2 = 4, P3 = 1, P4 = 4

대기 시간 (평균 대기시간 [ (0 + 6 + 3 + 7) / 4 = 4 ])

- P1 = 0, P2 = 6, P3 = 3, P4 = 7

### 선점형 방식

- SRTF (Shortest-Remaining-Time-First)
- CPU를 잡게 되더라도 더 짧은 처리 시간을 가진 작업이 들어오면 CPU를 빼앗기게 된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1bfb20e1-5f73-4cff-b3a7-4ef6b30b07be/Untitled.png)

처리하는데 걸리는 시간

- P1 = 7, P2 = 4, P3 = 1, P4 = 4

대기 시간 (평균 대기시간 [ (9 + 1 + 0 + 2) / 4 = 3 ])

- P1 = 9, P2 = 1, P3 = 0, P4 = 2

**문제점**

- Starvation (기아 현상): 짧은 처리시간을 가진 프로세스가 계속해서 도착해서 긴 처리시간을 가진 프로세스가 CPU를 잡지 못할 수 있다.
- CPU 사용시간을 미리 알 수 없다. (이전 사용시간을 통해 추정 가능)

    예상 대기시간 = 실제 CPU 사용시간 + 예측했던 CPU 사용시간

⇒  **SJF 방식을 사용하기에는 어려움이 있음**

### Priority Scheduling (우선순위 스케줄링)

 - 우선순위가 가장 높은 작업을 먼저 처리하는 알고리즘이다.

이 또한 두 가지 방식을 이용할 수 있다.

### 비선점 방식

- CPU를 잡게 되면 더 높은 우선순위를 가진 프로세스가 와도 CPU를 뺏기지 않는다.

### 선점 방식

- CPU를 잡게 되더라도 더 높은 우선순위를 가진 프로세스가 오면 CPU를 빼앗기게 된다.

**문제점**

- Starvation (기아 현상): 짧은 처리시간을 가진 프로세스가 계속해서 도착해서 긴 처리시간을 가진 프로세스가 CPU를 잡지 못할 수 있다.

**해결법**

- Aging (노화 현상): 우선순위가 낮은 프로세스이더라도 너무 오래기다리게되면 우선순위를 조금 올려주어 처리할 수 있게하는 방법

### Round Robin(RR)

 - 각 프로세스는 동일한 크기의 할당 시간을 가짐 (time quantum)

할당 시간이 지나면 프로세스는 선점 당하게 되고 Ready 큐의 제일 뒤에 가서 다시 줄을 서게 된다.

⇒ 짧은 응답시간을 보장한다.

**할당시간에 따른 성능**

- 할당 시간을 너무 크게 주게 되면, FCFS 와 같은 느낌이 된다.
- 할당 시간을 너무 적게 주게 되면, context switch가 너무 빈번하게 일어나게 되어 시스템 전체의 성능이 안좋아지게 될 수도 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0fd6cbd9-e8d4-48eb-b93b-fd6f0df24afa/Untitled.png)

***** 일반적으로 SJF 보다 평균 처리속도는 길지만, 응답속도는 더 짧다.**
