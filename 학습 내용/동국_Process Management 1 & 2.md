## 프로세스 생성 (Process Creation)

### 부모 프로세스가 자식 프로세스를 생성한다.

  방법 :  **Copy-on-write(COW)**

자식은 부모의 주소공간을 복사함 (binary and OS data)

자식은 그 공간에 새로운 프로그램을 올림

- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요하기 때문에 아래의 방법들을 사용한다.
    - 운영체제로부터 받는다. (일반적)
    - 부모와 공유한다. (모두 공유, 일부 공유 등 있음)
- 수행 모델들은 아래의 방법들이 있다. (Execution)
    - 부모와 자식이 공존하며 수행되는 모델
    - 자식이 종료(terminate)될 때 까지 부모가 기다리는(wait) 모델

### **생성 단계의 시스템 콜**

**fork()** - 새로운 프로세스를 생성

부모를 그대로 복사하여 주소공간을 할당한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a304f4f-40ee-4778-ac74-b26ccd3160cc/Untitled.png)

- 메인 함수의 코드를 한줄 씩 실행하다가, 
fork() 를 수행하게되면 부모를 복사하여 프로세스를 새로 만든다.
- fork() 의 결과값을 통해서 부모 프로세스와 자식 프로세스를 구분하여 다른 작업을 수행할 수 있게 한다.

*** **부모 프로세스의 문맥을 그대로 복사한다! (정확히는 PC)**

⇒ ****때문에 다음에 수행해야할 곳을 fork()가 아니란 걸 알고 있기에fork() 가 무한정으로 이루어지지는 않는다.

**exec()** - 새로운 프로그램을 메모리에 올림

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf726beb-82e2-4417-8be3-378c03f347aa/Untitled.png)

- fork()로 복사가 된 후, 자식 프로세스는 exec() 을 수행하게 된다.
- exec() 을 실행하게 되면 새로운 프로그램으로 완전히 바뀌게 된다.
    
    ⇒ exec() 이후에 실행될 코드가 있었다면, 이는 실행 될 수 없게 됨.
    
- **execlp("프로그램 이름", "프로그램 이름", "전달할 인자", (char*) 0); 과 같은 형식으로 작성되어 사용된다.**

### **종료 단계의 시스템 콜**

**wait() -** 프로세스가 대기상태로 들어감

- 자식 프로세스가 작업을 완료할 때 까지 부모 프로세스가 기다리게 되는 상태 (Block상태)
- 이후 작업이 완료되면 커널이 부모 프로세스를 깨운다. (Ready 상태)

**exit()** - 프로세스를 스스로 종료하도록 함

- 자발적 종료
    - 프로세스가 마지막 명령을 수행한 후 OS에게 이를 알려준다.
- 비자발적 종료
    - 키보드로 Kill, break 등을 입력한 경우
    - abort 된 경우

**abort()** - 부모 프로세스가 자식의 수행을 종료시킴(강제 종료)

⇒ 할당 자원의 한계치를 넘겼을 때, 할당된 태스크가 필요없을 때 등

## 프로세스 간 협력

**독립적 프로세스 (Independent Process)**

 - 프로세스는 각자의 주소 공간을 가지고 실행되기에 원칙적으로는 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

**협력 프로세스 (Cooperating process)**

 - IPC를 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.

*** **프로세스 간 협력 메커니즘(IPC : Interprocess Communication)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/784ea432-0add-465c-b46f-5a9ac98c937e/Untitled.png)

- **message passing (커널을 통해 메시지 전달)**
    - Direct Communication (통신하려는 프로세스의 이름을 명시)
    - Indirect Communication (mailbox, port를 통해 메시지 간접전달)
- **shared memory (주소 공간 공유)**
    - 커널에게 요청하여 shared memory 공간을 따로 마련한다.
    - shared memory 공간을 공유하여 작업을 진행한다.

(공유를 하는 만큼 결과가 이상하게 나올 수도 있기 때문에,
그만큼 서로 신뢰할 수 있는 관계여야만 좋은 결과가 나올 수 있다.)

⇒ Thread - 프로세스 간의 협력은 아니지만 동일 프로세스내의
                    thread 간에서는 주소공간을 공유하므로 협력이 가능하다.
