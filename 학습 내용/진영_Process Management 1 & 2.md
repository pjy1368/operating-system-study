## 프로세스 생성

- 운영체제가 최초의 프로세스를 생성하면, 이미 존재하는 프로세스가 다른 프로세스를 복제 생성한다. 이때 기존 프로세스를 부모 프로세스라 하고, 새로 생긴 프로세스를 자식 프로세스라고 부른다.
- 프로세스의 트리 (계층 구조)를 형성한다.
- 프로세스는 자원을 필요로 한다.
    - 운영체제에게 받는다.
    - 부모와 공유한다.
- 자원의 공유
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부를 공유하는 모델
    - 전혀 공유하지 않는 모델
- 수행 (Execution)
    - 부모와 자식이 공존하며 수행하는 모델 (이때는 부모와 자식이 CPU를 획득하기 위해 경쟁하는 관계가 됨)
    - 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델

### 주소 공간 (Address space)

- 자식은 부모의 공간을 복사한다. (프로세스 ID를 제외한 운영체제 커널 내의 정보와 주소 공간의 정보)
- 자식은 그 공간에 새로운 프로그램을 올린다.
- 유닉스의 예
    - `fork()` 시스템 콜이 새로운 프로세스를 생성한다. 부모를 그대로 복사하고 주소 공간을 할당한다.
    - `fork()` 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램으로 주소 공간을 덮어 씌운다.

## 프로세스 종료 (Process Termination)

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려준다. (`exit()`)
    - 자식이 부모에게 out data를 보낸다.
    - 프로세스의 각종 자원이 운영체제에 반납된다.
- 부모 프로세스가 자식의 수행을 종료한다. (`abort()`)
    - 자식이 할당 자원의 한계치를 넘어선다.
    - 자식에게 할당된 task가 더 이상 필요하지 않는다.
    - 부모가 종료(exit)한다.
        - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 주지 않는다.
        - 단계적인 종료

`exit()` 는 프로그램이 끝날 때 운영체제에게 자신이 끝났음을 알리는 자발적 종료에 해당하는 시스템 콜이고, `abort()` 는 부모 프로세스가 자식 프로세스의 수행을 중단하는 비자발적 종료에 해당하는 시스템 콜이다.

## 각종 시스템 콜

### `fork()` 시스템 콜

**개념**

- 운영체제는 자식 프로세스의 생성을 위해 `fork()` 시스템 콜을 제공한다.
- 프로세스가 해당 시스템 콜을 호출하면 CPU의 제어권이 커널로 넘어가고, 커널은 `fork()` 를 호출한 프로세스를 복제하여 자식 프로세스를 생성한다.
- `fork()` 를 수행하면 부모 프로세스의 주소 공간을 비롯해 프로그램 카운터 등 레지스터 상태, PCB 및 커널 스택 등 모든 문맥을 그대로 복제해 자식 프로세스의 문맥을 형성한다.
- 따라서 자식 프로세스는 부모 프로세스의 처음부터 수행하지 않고, 부모 프로세스가 현재 수행한 시점부터 수행하게 된다.
- 다만 자식 프로세스와 부모 프로세스의 식별자는 다르다. (운영체제가 프로세스를 관리해야 하기 때문

**소스 코드**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9d32e02b-ed27-4c0c-8c11-167a32dfd4a0%2FUntitled.png?table=block&id=18f3c4bd-3733-4408-abba-022136318566&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 부모 프로세스가 메인 함수의 첫 번째 줄부터 한 줄씩 코드를 수행하다가 `fork()` 라인에 이르면 자신과 똑같은 프로세스를 하나 생성한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F384b319d-84b6-4356-883f-39af89b29e6e%2FUntitled.png?table=block&id=f55a85b7-beee-46e8-bc01-30c2f53392c5&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 이때 `fork()` 라인까지 수행했다는 기억조차도 똑같은 자식 프로세스가 생성된다.
- 그래서 복제된 프로세스는 자기가 복제본이 아니라 원본이며, 자기를 복제해서 다른 복제본을 생성했다는 기억을 갖게 된다.
- 다만 이 자식 프로세스가 복제된 프로세스라는 사실을 알 수 있는 단서가 있는데, `fork()` 의 결과 값으로 0을 반환한다. (진짜 부모 프로세스는 0보다 큰 값을 호출한다.)

### `exec()` 시스템 콜

**개념**

- `fork()` 시스템 콜만으로는 같은 코드에 대해 조건을 분기하는 정도로 밖에 사용할 수 없다.
- 자식 프로세스에게 부모 프로세스와는 독자적인 프로그램을 수행할 수 있는 메커니즘이 바로 `exec()` 시스템 콜이다.
- 이 시스템 콜은 프로세스의 주소 공간에 새로운 프로그램을 덮어 씌운 후, 새로운 프로그램의 첫 부분부터 다시 실행하도록 한다.
- 따라서 새로운 프로그램을 수행하기 위해서는 `fork()` 시스템 콜로 복제 프로세스를 생성한 뒤, `exec()` 시스템 콜로 해당 프로세스의 주소 공간을 새롭게 수행하려는 프로세스의 주소 공간으로 덮어 씌우면 된다.

**소스 코드**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8c9c09f4-1fdf-49b2-8837-8f127dd28b9c%2FUntitled.png?table=block&id=2af3a246-6210-4f70-941d-bf777deb785e&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- `execlp()` 라인에 이르면 새로운 프로그램으로 덮어 씌운다.

### `wait()` 시스템 콜

- 자식 프로세스가 종료되기를 기다리며 부모 프로세스가 봉쇄 상태에 머무르도록 할 때 사용한다.
- 부모 프로세스가 `fork()` 후에 `wait()` 을 호출하면 자식 프로세스가 종료될 때까지 부모 프로세스를 봉쇄 상태에 머무르게 하고, 자식 프로세스가 종료되면 부모 프로세스를 준비 상태로 변경한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F36912aa1-802e-47c0-9bca-4e54803ee23e%2FUntitled.png?table=block&id=6cba9aeb-f604-4ef0-9b37-bd0c0be4fc46&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### `exit()` 시스템 콜

- 프로세스를 자발적으로 종료할 때 사용한다.
- 마지막 statement 수행 후 `exit()` 시스템 콜을 수행한다.
- 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어준다.

### `abort()` 시스템 콜

- 프로세스를 비자발적으로 종료할 때 사용한다.
- 부모 프로세스가 자식 프로세스를 강제로 종료한다.
    - 자식 프로세스가 한계를 넘어서는 자원 요청
    - 자식에게 할당된 task가 더 이상 필요하지 않음
- 키보드로 kill, break 등을 입력한 경우
- 부모가 종료하는 경우
    - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨.

## 프로세스 간의 협력

### 독립적 프로세스 (Independent process)

- 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

### 협력 프로세스 (Cooperating process)

- 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.

### 프로세스 간 협력 메커니즘 (IPC: Interporcess Communication)

- IPC는 프로세스들 간의 통신과 동기화를 이루기 위한 메커니즘을 의미한다.
- 메시지 전달 방식 (커널을 통해 메시지 전달)과 공유 메모리 방식 (일부 주소 공간을 공유)이 있다.

### 메시지 전달 방식 (Message Passing)

- 프로세스 간에 공유 데이터를 일체 사용하지 않고 메시지를 주고 받으면서 통신하는 방식이다.
- 메시지 통신을 하는 시스템은 커널에 의해 send와 receive 연산을 제공받는다.

**직접 통신 (Direct Communication)**

- 통신하려는 프로세스의 이름을 명시적으로 표시한다.
- 각 쌍의 프로세스에게는 오직 하나의 링크만 존재한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F510ce6c1-18bf-4145-a03f-74473e4ec36c%2FUntitled.png?table=block&id=4a275080-d756-4264-bd3c-d56073fd7946&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
 
**간접 통신 (Indirect Communication)**

- 메시지를 메일 박스 또는 포트로부터 전달받는다.
- 각 메일 박스에는 고유의 ID가 있으며 메일 박스를 공유하는 프로세스만 통신할 수 있다.
- 하나의 링크가 여러 프로세스에게 할당될 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9781ca48-a722-44ab-9fac-56591d8d7e54%2FUntitled.png?table=block&id=83c865b0-6d0f-4350-8ccd-8a8d20be0064&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 공유 메모리 방식 (Shared Memory)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5b5de537-a950-4e0e-8037-fa8d5b94be76%2FUntitled.png?table=block&id=2f280d7c-20ab-424b-8a5c-0c285bfdb6aa&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=1530&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 공유 메모리 방식은 프로세스들이 주소 공간의 일부를 공유한다.
- 서로의 데이터에 일관성 문제가 유발될 수 있다.
- 프로세스 간 동기화 문제를 스스로 책임져야 한다.

## 출처

운영 체제와 정보 기술의 원리 - 반효경
