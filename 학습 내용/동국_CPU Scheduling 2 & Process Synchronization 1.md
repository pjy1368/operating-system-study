## Multilevel Queue

---

- Ready Queue를 우선순위에 따라서 여러개로 분할한다.
- 각 큐는 독립적인 알고리즘을 사용한다.
    - foreground (interactive - 사람과의 소통) ⇒ RR
    - background (batch - 사람과의 소통 없음) ⇒ FCFS

**큐에 대한 스케줄링을 한다.**

- Fixed priority scheduling - 우선순위가 높은 프로세스가 CPU를 많이 사용해야 한다.

       ⇒ 기아현상 발생 할 수 있음

- Time slice - 각 큐별로 CPU 시간을 적절한 비율로 할당한다.

## Multilevel Feedback Queue

---

- 프로세스가 다른 큐로 이동이 가능하다. (우선순위 변경 가능)
- aging을 이와 같은 방식으로 구현할 수 있다.

**Multilevel-feedback-queue scheduler를 정의하는 파라미터**

- queue의 수
- 각 큐의 scheduling 알고리즘
- 프로세스를 상위 큐로 보내는 기준
- 프로세스를 하위 큐로 보내는 기준
- 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/142d32a2-b40d-40ef-8580-4665d9d6ea96/Untitled.png)

빠르게 처리를 완료할 수 있는 프로세스는 높은 우선순위로 처리를 완료하고 빠져나갈 수 있다.

오래 처리를 하였음에도 불구하고 처리가 안된 프로세스는 우선순위가 낮아지게 된다.

## Multiple-Processor Scheduling

---

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해지게 된다.

**Homogeneous processor인 경우**

- Queue에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 한다.
- 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에서는 문제가 더 복잡해진다.

**Load sharing (부하 공유)**

- 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요하다.
- 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법

**Symmetric Multiprocessing (대칭형)** [모든 프로세스가 대등함]

- 각 프로세서가 각자 알아서 스케줄링을 결정한다.

**Asymmetric Multiprocessing (비대칭형)**

- 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따르게된다.

## Multiple-Processor Scheduling

---

**Hard real-time system**

- 정해진 시간 안에 반드시 끝내도록 스케줄링 해야한다.

**Soft real-time system**

- 일반 프로세스에 비해 높은 priority를 갖도록만 한다.
    
    ⇒ 데드라인을 무조건 지키지는 못함
    

## Thread Scheduling

---

**Local Scheduling**

- User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄 할지 결정한다.

**Global Scheduling**

- Kenel level thread의 경우 일반 프로세스와 마찬 가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정한다.

## 알고리즘 평가 방법(Algorithm Evaluation)

### Queueing models

- 확률 분포로 주어지는 도착률과 처리율 등을 통해 각종 성능 척도 결과 값을 계산한다.

### Implementation(구현) & Measurement(성능 측정)

- 실제 시스템에 알고리즘을 구현해 실제 작업에 대해서 성능을 측정해 비교한다.

### Simulation(모의 실험)

- 알고리즘을 모의 프로그램으로 작성 후 trace를 넣어 결과를 비교한다.

(trace는 입력 값, 실제 뽑아낸 값일 수도 있고 임의로 만든 값으로 할 수도 있다.)

# Process Synchronization

## 데이터의 접근

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9821faa-71c3-413e-a445-e496ecf27eba/Untitled.png)

- Execution Box (CPU, process) - 데이터가 실행되는 장소
- Stroage Box (Memory, address Space) - 데이터가 저장되있는 장소

**컴퓨터 시스템 안에서 데이터 접근하게 되는 순서**

1. 데이터가 저장되어 있다.
2. 연산할 데이터를 가져온다.
3. 연산 수행
4. 연산결과를 저장한다.

### Race Condition (경쟁 상태)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e4a11744-e4cc-4929-b7a8-43129d1a79eb/Untitled.png)

저장공간을 공유하는 실행공간이 여러개가 있는 경우 Race Condition의 가능성이 있다.

- 여러 프로세스 들이 동시에 공유 데이터를 접근해야하는 상황
    
    **⇒ 데이터의 불일치 문제로 이어질 수 있음**
    
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라진다.
    
     **⇒ 프로세스간의 실행 순서를 정해주는 매커니즘이 필요**
    

(ex_ Multiprocessor system, 공유메모리를 사용하는 프로세스들)

⇒ **race condition을 막기 위해서는 동시에 실행되는 프로세스들 간에는 동기화(synchronize)가 잘 되어야한다.**

## OS에서 Race Condition의 발생 경우

### Interrupt handler vs Kernel

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e2265d6e-33ad-48cc-91f3-32d1f1f31a91/Untitled.png)

Kernel이 작업을 수행하는 도중에 Interrupt가 발생하여 인터럽트 처리루틴이 실행되야하는 상황이다.

- 양쪽 다 커널 코드이므로 kernel 주소 공간을 공유하고 있다.
- 작업을 진행하던 도중 인터럽트가 걸리고 처리루틴을 실행하게되면 루틴 내에서 값을 변경했다고 한들 이전에 읽어온 값으로 데이터를 처리하게 되기 때문에 문제가 발생하게 된다.

**해결법**

⇒ 인터럽트가 발생해도 진행하던 작업을 끝내고 수행될 수 있게 처리를 하여 해결한다.

### Preempt CPU while in kernel mode

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a456a99-71b4-4a6e-8c44-4ee97e7a35b1/Untitled.png)

CPU가 커널모드에 있을 때 할당시간이 끝나게 되어 CPU를 빼앗기게 되는 상황이다.

- 프로그램이 실행되는 중에 커널을 사용하기 위해 시스템 콜을 부르게 된다.
- 커널모드에서 값을 변경하려는데, CPU 할당시간이 끝나 CPU를 빼앗기게 되고 문맥교환이 이루어진다.
- 다른 프로세스에서 값을 변경한 후, CPU를 다시 가져오게 되고 문맥교환이 일어나게된다.
- 하지만 이전에 값을 증가하기 이전에 상태를 가지고 있기 때문에 값의 변경은 한번 밖에 이루어지지 않게된다.

**해결법**

 ⇒ 커널 모드에서 수행 중일 때에는 CPU를 preempt를 하지 않는다.
     커널 모드에서 사용자 모드로 돌아갈 때 선점을 진행한다.

### **OS에서의 race condition**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8f1bf48-c557-4ef6-8d91-ef68dc3d283c/Untitled.png)

CPU가 여러 개가 있는 상황에서 발생하는 상황이다.

**해결법**

1. 한번에 하나의 CPU만이 커널에 들어갈 수 있게하는 방법 
    
    (커널 전체에 lock을 걸기 때문에 비효율적이다)
    
2. CPU하나가 데이터를 사용하고 있는 상태라면 그 데이터에 Lock을 걸어 사용하지 못하게 하는 방법

## 임계 구역 문제(The Critical-Section Problem)

 - n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우

- 각 프로세스 code segment에는 공유데이터를 접근하는 코드인 **임계구역**이 존재한다.

**문제점**

 ⇒ 하나의 프로세스가 임계구역에 있을 때 다른 모든 프로세스는 
임계구역에 들어갈 수 없어야한다.
