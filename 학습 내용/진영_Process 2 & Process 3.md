## Thread란

### 정의

프로세스 내에서 실행 되는 여러 흐름의 단위 혹은 프로세스가 할당 받은 자원을 이용하는 실행 흐름의 단위를 뜻한다.

### 구성

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3ab2c737-1fce-43d2-9203-0a61d9917e95%2FUntitled.png?table=block&id=d86f46c1-724d-4a0f-bc70-7b906b8d26a0&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- program counter
- register set
- stack space

쓰레드마다 CPU 관련 정보들을 각자 가지고 있어야 한다.

### Thread가 다른 Thread와 공유하는 부분

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9469d96e-1e8d-4104-86ce-89a97ac1de2c%2FUntitled.png?table=block&id=4d7c3b39-bef6-4a09-b234-2a953bf1eaff&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- code section
- data section
- OS resources

Thread끼리 Address space의 data, code 영역은 공유하되 stack은 별도로 할당 받는다. PCB에서는 program counter와 register set을 제외한 프로세스 관련 정보 및 자원을 모두 공유한다. 여기서 thread들이 공유하는 부분인 code section, data section, OS resources를 task라고 한다. 그래서 전통적인 개념의 heavyweight process는 하나의 쓰레드를 가지고 있는 task로 볼 수 있다.

### 장점

**응답성 (Responsiveness)**

다중 쓰레드로 구성된 Task 구조에서 하나의 서버 쓰레드가 blocked(watiting) 상태인 동안에도 동일한 Task 내의 다른 쓰레드가 실행되어 빠른 처리가 가능하다. 예를 들어 웹 브라우저가 쓰레드를 여러 개 가지고 있을 때, 하나의 쓰레드는 이미지를 비롯한 추가 데이터를 받기 위해 서버에 요청을 걸어서 blocked 상태가 된 후, 다른 쓰레드가 이미 받아 놓은 HTML 텍스트를 화면에 출력할 수 있다. 이러한 비동기식 입출력을 통해 응답성을 높일 수 있다.

**자원 공유 (Resource Sharing)**
하나의 프로세스 안에 CPU 수행 단위인 쓰레드를 두게 되면 code, data, resource 자원을 공유하여 효율적으로 자원 활용이 가능하다.

**경제성 (Economy)**

- 동일한 일을 수행하는 다중 쓰레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
- 새로운 프로세스 하나를 만드는 것 보다 기존의 프로세스에 쓰레드를 추가하는 것이 오버헤드가 훨씬 적다.
    - 프로세스의 Context Switching과 달리 캐시 메모리를 초기화할 필요가 없고 Code, Data, Resource 영역을 공유하므로 Stack 영역만 처리하면 되기 때문이다.

**멀티 프로세서 아키텍처에서의 이용성 (Utilization of MP Architectures)**

각각의 쓰레드가 서로 다른 CPU를 가지고 병렬적으로 작업을 진행해서 훨씬 효율적으로 작업을 수행할 수 있다.

### 단점

- 자원의 공유로 인한 동기화 문제가 발생할 수 있다.
- 디버깅이 까다롭다.

### 구현 방식

**Kernel Threads**

여러 개의 쓰레드가 있다는 사실을 운영체제가 알고 있어서 쓰레드 간의 CPU 교환을 커널이 CPU 스케줄링하듯이 관리한다.

- Windows 95, 98, NT
- Solaris
- Digital UNIX, Mack

**User Threads**

여러 개의 쓰레드가 있다는 사실을 운영체제가 모르는 상태에서 사용자 프로그램이 라이브러리의 지원을 받아 스스로 여러 개의 쓰레드를 관리한다. 커널이 쓰레드의 존재를 모르기 때문에 구현에 제약이 있을 수 있다.

- POSIX Pthreads
- Mach C-threads
- Solaris threads

**Real-Time Threads**

## 출처

[http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)

[https://seungwoolog.tistory.com/37](https://seungwoolog.tistory.com/37)

[https://reese-dev.netlify.app/OS/ban-05/](https://reese-dev.netlify.app/OS/ban-05/)

운영 체제와 정보 기술의 원리 - 반효경
