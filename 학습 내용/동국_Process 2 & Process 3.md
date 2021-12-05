## Thread

> "A Thread (or lightweight process) is a basic unit of CPU utilization"
→ CPU를 수행하는 단위
> 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/624b3ede-49c6-42dd-a2ab-299ec45a9cf8/Untitled.png)

프로세스를 만들게 되면 주소공간이 만들어지게 되고,
OS에서는 PCB를 통해 이를 관리하게 된다.

만약 동일한 작업을 하는 프로세스가 여러 개 있을 때,
그 프로세스들을 모두 별도의 프로세스로 만들게 된다면 메모리 낭비가 생기게 된다.

**⇒ Thread를 통해 위와 같은 상황을 해결 할 수 있다.**

### Thread 의 구조

한 프로세스(주소공간)에 CPU의 작업단위를 여러 개 두는 것을 말한다.

Thread는 아래의 정보들을 독립적으로 가지고 있다.

- Program Counter
- register set
- stack space - 함수 호출 및 return 과 관련한 정보를 저장

또한, 아래의 정보(=task)들을 공유하여 사용한다. 

- code section
- data section
- OS resources

위처럼 여러 Thread를 가지고 있는 프로세스를 Lightweight process,

하나의 Thread를 가진 프로세르를 Heavyweight process 라고 할 수 있다.

### Thread 의 장점

1. **응답성 (Responsiveness)**
하나의 서버 스레드가 blocked(waiting) 상태가 되어있는 동안에도
다른 서버 스레드가 실행(running) 하여 빠른 처리를 할 수 있다.
    
    (ex_웹페이지에서 이미지를 로딩하는 동안 텍스트 처리를 해 보여줌)
    
2. **자원 공유 (Resource Sharing)**
    
    프로세스의 자원(=task)를 공유하여 처리 한다.
    
3. **경제성 (Resource Sharing)**
    
    여러 스레드가 협력하여 높은 처리율(Throughput)과 성능 향상을 얻을 수 있다.
    
    - 프로세스 하나를 생성하는 것 보다 스레드를 생성하는 것이 부담이 적기 때문에, 가능하면 같은 작업을 하는 프로세스인 경우에 스레드를 썼을 때 얻을 수 있는 장점이 많다.
4. **멀티 프로세서 아키텍처에서의 이용성 (Utilization of MP Architectures)**
    
    각각의 스레드는 병렬적인 작업을 통해 효율성을 높일 수 있다.
    

### 스레드의 구현방법

1. Kernel Thread
    
     - 커널의 지원을 받아 커널이 스레드의 존재를 알고 있을 때
    
    - Winodws 95/98/NT
    - Solaris
    - Digital UNIX, Mach
2. User Thread 
    
     - 라이브러리를 통해 사용자 수준의 스레드를 구현했을 때
    
    - POSIX Pthreads
    - Mach C-threads
    - Solaris threads
    
    (구현 상의 제약점이 있을 수 있음)
    
3. Real-Time Thread
