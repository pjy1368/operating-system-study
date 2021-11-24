## 동기식 입출력과 비동기식 입출력

### 동기식 입출력 (Synchronous I/O)

동기식 입출력은 입출력 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어가는 것을 말한다.

예를 들어, 프로그램이 디스크에서 어떤 정보를 읽어 오라는 요청을 했을 때 디스크 입출력이 완료되기까지는 어느 정도의 시간이 소요될 것이다. 이때 2가지 방식으로 동기식 입출력을 구현할 수 있다.

**[구현 방법 1]**

- 입출력이 끝날 때까지 인터럽트를 기다렸다가, 끝나면 사용자 프로그램에게 CPU의 제어권을 넘긴다.
- 매 시점 하나의 입출력만 일어날 수 있다.
- CPU를 낭비한다는 문제가 있다.

**[구현 방법 2]**

- 입출력이 완료될 때까지 해당 프로그램에서 CPU의 제어권을 빼앗는다.
- 입출력 처리를 기다리는 줄에 그 프로그램을 세운다.
- 다른 프로그램에게 CPU의 제어권을 넘긴다.
- 동기식 입출력은 보통 이 방법으로 구현한다.

### 비동기식 입출력 (Asynchronous I/O)

비동기식 입출력은 입출력이 시작된 후, 입출력 작업이 끝나기를 기다리지 않고 CPU 제어권을 사용자 프로그램에게 즉시 넘기는 것을 말한다.

### 동기식 입출력 vs 비동기식 입출력

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1abf057e-fe8d-4ca8-b7c5-e7e93a4f7b00%2FUntitled.png?table=block&id=c1a16f1f-5e15-4942-8dd6-42849564fc7d&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

위 그림은 동기식 입출력과 비동기식 입출력의 비교해 보여 준다. 사용자가 입출력 요청을 하면 동기식 입출력에서는 먼저 운영체제의 커널로 CPU의 제어권이 넘어와서 입출력 처리와 관련된 커널의 코드가 수행된다. 이때 입출력을 호출한 프로세스의 상태를 Blocked 상태로 바꾸어 입출력이 완료될 때까지 CPU를 할당받지 못하도록 한다. 입출력이 완료되면 I/O 컨트롤러가 CPU에게 인터럽트를 발생시켜 입출력이 완료되었음을 알려주고, Blocked 상태인 해당 프로세스에게 CPU를 할당받을 수 있는 권한을 준다. 권한을 준다는 이야기는 CPU를 할당 받을 수 있는 줄에 서게 될 수 있다는 뜻이다.

반면 비동기식 입출력에서는 CPU의 제어권이 입출력을 요청한 프로세스에게 곧바로 다시 주어지며, 입출력 연산이 완료되는 것과 무관하게 처리 가능한 작업부터 처리한다.

한편 두 방식 모두 입출력 연산이 완료되면 인터럽트를 통해 CPU에게 알려준다.

## 서로 다른 입출력 명령어

I/O는 일반적인 I/O 방식과 Memory Mapped I/O 방식이 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdef262eb-86a3-4506-9409-7dc0932cef44%2FUntitled.png?table=block&id=17511416-b0b6-4135-acdd-cfb4524c1069&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

좌측이 일반적인 I/O고, 우측이 Memory Mapped I/O에 해당한다.

### 일반적인 I/O

메모리에 접근하는 명령과 I/O 장치를 접근해야 하는 명령이 구분되어 있다.

### Memory Mapped I/O

I/O 장치도 메모리 주소에 연장 주소를 붙여 접근할 수 있다.

예를 들어 100번지에 접근하는 것은 일반 메모리에 접근하는 것이지만, 1000번지에 해당하는 메모리에 접근하는 명령은 사실 I/O를 하는 명령임을 뜻한다.

## 저장 장치 계층 구조

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fad2f7985-4674-4897-98dc-cad9ab4eb599%2FUntitled.png?table=block&id=43b9863b-c041-4c8f-8b62-7f4e74902f9f&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

위로 갈수록 속도가 빠르지만, 단위 공간 당 가격이 비싸고 용량이 적다. 반면 아래로 갈수록 단위 공간 당 가격이 싸고 용량이 많지만, 속도가 느려진다.

### Primary Storage vs Secondary Storage

CPU는 바이트 단위로 접근 가능한 매체이어야 접근이 가능하다.

**[Primary Storage]**

CPU에서 직접 접근할 수 있는 매체를 말하며, Executable (실행 가능하다)라고 부른다. 즉, 해당 저장소는 바이트 단위로 CPU 접근이 가능하므로 Primary라고 부르며 전원이 꺼지면 데이터가 사라지는 휘발성 특징을 갖는다.

**[Secondary Storage]**

CPU가 직접 접근하지 못하는 매체를 말한다. 하드 디스크의 단위는 섹터 단위이므로 CPU가 접근하지 못해서 Secondary라고 부른다. 해당 저장소는 전원이 꺼져도 데이터가 보존되는 비휘발성 특징을 갖는다.

### 캐시 메모리

캐시 메모리는 CPU와 메인 메모리의 속도 차이를 완충하기 위해 존재한다. 용량이 적기 때문에 모든 것을 담아 둘 수는 없으나, 빈번히 사용되는 필요한 정보를 선별적으로 저장하여 재사용하는 캐싱 기법을 통해 시스템의 성능을 높일 수 있다.

## 프로그램의 실행 (메모리 load)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbc3e2a01-4fc8-4a19-8657-06800d2ce9e3%2FUntitled.png?table=block&id=ec542799-30dc-4121-8555-f1c2b56657ce&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

프로그램은 File System에 실행 파일 형태로 저장되어 있고, 이를 실행하면 메모리에 올라가 프로세스가 된다. 정확히 말하면, 물리적인 메모리에 프로그램이 바로 올라가는 것이 아니라 가상 메모리 단계를 추가로 거친다. 이때 독자적인 메모리 주소 공간이 형성되는데, 이 공간에는 Code, Data, Stack 영역이 있다.

**Code**

CPU에서 실행할 기계어 코드를 저장한다.

**Data**

전역 변수 등 프로그램이 사용하는 데이터를 저장한다.

**Stack**

함수가 호출될 때 호출된 함수의 수행을 마치고 복귀할 주소 및 데이터를 임시로 저장한다.

그 다음 가상 메모리에서 물리적인 메모리로 프로그램이 올라가는데, 메모리 낭비를 방지하기 위해 프로그램 중 당장 실행에 필요한 부분만 올라가고, 그렇지 않은 부분은 디스크 중 메모리의 연장 공간으로 사용되는 스왑 영역에 내려 놓는다. 즉, 주소 공간을 쪼개서 어떤 부분은 메모리에 있고, 어떤 부분은 스왑 영역에 있게 된다.

### Disk 활용

위 그림에서 스왑 영역과 File System 두 디스크가 존재하는데, 스왑 영역은 메모리의 연장 공간으로서 휘발성 특징을 갖고 있고, File System은 비휘발성 용도로 사용한다.

### 커널 주소 공간의 내용

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4d15e447-f789-462c-a6fe-fe6797ff6ae4%2FUntitled.png?table=block&id=d4a23cfe-b565-4411-a128-fcb021fd347b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**Code**

CPU, 메모리 등의 효율적으로 자원을 관리하기 위한 부분과 사용자에게 편리한 인터페이스를 제공하기 위한 부분이 주를 이루고 있다. 이 밖에도 커널의 코드는 시스템 콜 및 인터럽트를 처리하기 위한 부분을 포함한다.

**Data**

각종 자원을 관리하기 위한 자료 구조가 저장된다. CPU나 메모리와 같은 하드웨어 자원을 관리하기 위한 자료 구조뿐 아니라 프로세스를 관리하기 위한 자료 구조도 커널의 데이터 영역에 유지된다. 커널의 데이터 영역 내에는 각 프로세스의 상태, CPU 사용 정보, 메모리 사용 정보 등을 유지하기 위한 PCB를 두고 있다.

**Stack**

프로그램의 스택 영역과 마찬가지로 함수 호출 시의 복귀 주소를 저장하기 위한 용도로 사용된다. 하지만 커널의 스택은 일반 사용자 프로그램의 스택과 달리 현재 수행 중인 프로세스마다 별도의 스택을 두어 관리한다. 

이는 일반 사용자 프로그램이 자기 주소 영역 내부의 함수를 호출하면 자신의 스택에 복귀 주소를 저장하지만, 프로세스가 특권 명령을 수행하려고 커널에 정의된 시스템 콜을 호출하고 시스템 콜 내부에서 다른 함수를 호출하는 경우에는 복귀 주소가 커널 내의 주소가 되므로 커널의 스택 영역에 저장되어야 하기 때문이다.

즉, 프로그램이 자기 자신의 코드 내에서 함수 호출 및 복귀 주소를 유지하기 위해서는 자기 주소 공간 내의 스택을 사용하고, 시스템 콜이나 인터럽트 등으로 운영 체제의 코드가 실행되는 중에 함수 호출이 발생할 경우 커널 스택을 사용한다.

## 사용자 프로그램이 사용하는 함수

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc9940609-a7cc-4364-818d-466bac5e270f%2FUntitled.png?table=block&id=26c9483c-7a1c-4459-ba93-f5059c49de54&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 사용자 정의 함수

자신의 프로그램에서 정의한 함수를 말한다.

### 라이브러리 함수

자신의 프로그램에서 정의하지 않고 가져다 사용한 함수를 말하며, 자신의 프로그램 실행 파일에 포함되어 있다.

### 커널 함수

운영 체제 프로그램의 함수를 말하며, 커널 함수의 호출은 곧 시스템 콜과 일맥상통한다. 당연하지만, 주소 점프를 할 수 없으므로 인터럽트 라인을 세팅하여 CPU에게 제어권을 넘겨야 한다.

## 프로그램의 실행

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9ede1bdd-8fe0-484c-90b3-58af011d29b5%2FUntitled.png?table=block&id=d68c44bc-1c3c-48f5-ab16-d886c8d09ac6&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

위 그림은 사용자 프로그램 입장에서 본 실행 및 종료 과정이다.
