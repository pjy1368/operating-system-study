## 프로세스의 개념

> " Process is a program in execution" = 실행 중인 프로그램
> 

### *** **프로세스의 문맥 (context)**

⇒ **이 프로세스가 어디까지 실행이 되었는지 알 수 있는 요소**

1. CPU의 수행 상태를 나타내는 하드웨어의 문맥
    - Program Counter, 각종 register 가 어떤 값을 가지고 있는지
2. 프로세스의 주소 공간 
    - code, data, stack (메모리)에 어떤 값이 들어있는가
3. 프로세스 관련 커널 자료구조
    - OS가 해당 프로세스에 PCB, Kernel stack 값이 어떤지

**위와 같은 내용들을 통해 프로세스가 현재 어떤 상태에 있는지 알아 낼 수 있다.**

## 프로세스의 상태 (Process State)

프로세는 상태가 변경되며 수행된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/22f7297c-0cf4-4f60-868e-8ce7b08cd773/Untitled.png)

- New - 프로세스가 생성중인 상태
- Ready - CPU를 기다리는 상태
    
    (메모리 등 다른 준비 작업조건을 모두 만족한 상태)
    
- Running - CPU를 잡고 Intruction을 수행 중인 상태
- Blocked(wait, sleep) - CPU를 줘도 Instruction을 수행할 수 없는 상태
    
    (Process 자신이 요청한 event(ex_I/O)가 만족되지 않아 이를 기다리는 상태)
    
- Terminated - 프로세스의 수행이 끝난 상태 (다 끝내고 정리 작업)

**Kenel 에서는 Queue 구조를 이용해 작업 내용을 구성한다.**

 - 프로세스가 CPU를 사용하다가도 Timer Inturrupt 같은 요소 때문에 다시 Queue로 줄을 서게 되거나 I/O 작업을 위해 대기를 하기도 하며 우선순위를 통해 결정 되기도 하기 때문에 프로세스 상태도와 같이 생각해보면 좋을 것 같다.

추가 - 밑에서의 중기스케줄러에 의해 등장하게 됨

- Suspended (Stopped)
    
    외부적인 이유로 인해 프로세스의 수행이 정지된 상태
    
    프로세스는 통째로 디스크에 swap out 되게된다. (**메모리를 잃게 됨**)
    
    (ex_ 사용자가 직접 프로세스를 정지 시킨 경우,
    메모리에 너무 많은 프로세스가 올라와있을 때 등등...)
    

**Blocked는 자신이 요청한 event가 완료되면 Ready가
Suspended는 외부에서 resume을 해주어야만 Active가 됨**

## PCB (Process Control Block)

 - 운영체제가  각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7a8288b7-1c68-454f-87eb-4e4639dd98b4/Untitled.png)

1. OS가 관리 상 사용하는 정보
    1. Process state, Process ID, priority, scheduling information
2. CPU 수행 관련 하드웨어 값
    1. PC, registers
3. 메모리 관련
    1. Code, data, stack  정보
4. 파일 관련
    1. Open file descriptors

## 문맥 교환 (Context Switch)

 - CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

CPU가 다른 프로세스에게 넘어갈 때 (Inturrpt 등 때문에)
다음 CPU를 얻었을 때 작업을 이어나가기 위해서 다음 같은 작업을 수행

- CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
- CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

***** System call 이나 Inturrupt가 발생 시 반드시 context switch가
 일어나는 것 은 아니다.**

- System call 이나 Inturrupt가 발생하고 처리를 완료한 후 다시 원래 프로세스로 돌아오게 된다면 그럴 때는 context switch가 일어난 것이 아니다.

(물론 문맥의 저장은 있겠지만, 프로세스가 바뀌지 않기에 부담이 적음)

- Timer Inturrupt와 같이 프로세스를 바꾸기 위한 Inturrupt를 통한다면 context switch가 일어나게 된다.

## 프로세스를 스케줄링하기 위한 큐

- **Job Queue -** 현재 시스템 내에 있는 모든 프로세스 집합이다.
    - **Ready Queue -** 현재 메모리에 있으면서 CPU를 잡아서 실행되기를 기다리는 준비 상태의 프로세스 집합
    - **Device Queues -** I/O 디바이스의 처리를 기다리는 봉쇄 상태의 프로세스 집합

***** 프로세스들은 각 큐들을 오가면 수행된다.**

## 스케줄러 (Scheduler)

- **Short-term Scheduler (단기 스케줄러 or CPU scheduler)**
    
    어떤 프로세스를 다음번에 running 시킬지를 결정함
    
    프로세스에 CPU를 주는 스케줄러
    
- **Long-term Scheduler (장기 스케줄러 or Job scheduler)**
    
    시작 프로세스 중 어떤 것들을 ready queue로 보낼지를 결정함
    
    프로세스에 memory를 주는 스케줄러
    
    degree of Multiprogramming을 제어함
    
    (하지만 time sharing 방식에는 Long-term 스케줄러가 없음)
    
- **Medium-term Scheduler (중기 스케줄러 or Swapper)**
    
    여유공간 마련을 위해 프로세스를 메모리에서 디스크로 쫒아냄
    
    프로세스에게서 memory 뺏는 스케줄러
    
    degree of Multiprogramming을 제어함
    
    (그렇기에 Medium-term 스케줄러를 이용함)
    

 ***** degree of Multiprogramming - 수행 중인 프로세스의 수**
