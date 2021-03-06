# 컴퓨터 시스템의 구조

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1e4cc894-0f97-4338-acba-37c5a9db1e7c%2FUntitled.png?table=block&id=ebd86f5a-3c74-4c0a-bbfd-7e1cd4964257&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 큰 흐름

컴퓨터 시스템의 구조는 컴퓨터 내부 장치인 CPU, 메모리와 컴퓨터 외부 장치(입출력 장치)인 디스크, 키보드, 마우스, 모니터, 네트워크 장치 등으로 구분된다. 컴퓨터는 외부 장치에서 내부 장치로 데이터를 읽어와 (input) 각종 연산을 수행한 후, 그 결과를 외부 장치로 내보내는(output) 방식으로 업무를 처리한다.

## 각 요소에 대한 설명

### Memory

메모리는 CPU가 직접 접근할 수 있는 내부 기억 장치로서 특정 프로그램이 CPU에서 실행되려면 해당 부분이 메모리에 올라가 있어야 한다. 운영 체제는 컴퓨터가 부팅되었을 때 메모리에 올라가 있는데, 메모리에 상주하고 있는 CPU의 작업 공간을 Main Memory라고 부른다.

### Device Controller

메모리 및 입출력 장치 등의 각 하드웨어 장치에는 Device Controller라는 것이 붙어 있다. 컨트롤러는 일종의 작은 CPU로서, 각 하드웨어 장치를 제어하는 역할을 수행한다.

L**ocal Buffer**

메인 CPU의 작업 공간인 메인 메모리가 있듯이 디바이스 컨트롤러도 데이터를 임시로 저장하기 위한 작업 공간이 필요한데, 이를 Local Buffer가 그 역할을 한다.

참고로, Device Driver는 CPU가 실행하는 각 디바이스에 접근하기 위한 소프트웨어를 뜻한다.

### CPU

CPU는 클럭마다 메모리에서 명령(Instruction)을 하나씩 읽어서 실행하는 역할을 한다. IO가 일어나면 CPU가 직접 접근하지 않고 Device Controller에게 시킨다. 이렇게 디바이스 컨트롤러에게 시키는 이유는 CPU에 비해 디바이스 속도가 매우 느리기 때문이다.

**접근 범위**

CPU는 메인 메모리와 Local Buffer에 접근이 가능하다.

**인터럽트 라인 (Interrupt Line)**

CPU가 자신의 작업을 하던 중간에 인터럽트 라인에 신호가 들어오면 하던 일을 멈추고 인터럽트와 관련된 일을 먼저 처리한다.

**레지스터 (Register)**

CPU 내부에 메모리보다 더 빠르면서 정보를 저장할 수 있는 작은 공간이다. 레지스터 중에 메모리 주소를 가리키는 레지스터인 PC (Program Counter) 레지스터가 있다. CPU는 PC 레지스터가 가리키는 메모리 위치에서 인스트럭션을 읽어서 수행한다.

**Mode bit**

CPU에서 실행되는 것이 운영 체제인지 사용자 프로그램인지 구분해 준다.

- 1 (사용자 모드) - 사용자 프로그램
    - 사용자 프로그램이 CPU 가질 때는 제한된 인스트러션만 실행시킬 수 있다. 만약 Mode bit가 없다면 사용자 프로그램에서 하드웨어를 직접 접근하여 보안에 취약해 질 수 있다.
- 0 (커널 모드) - 운영 체제
    - 운영 체제가 CPU에서 실행 중일 때는 모든 인스트럭션을 실행할 수 있다. (모니터 모드 혹은 시스템 모드라고 부른다.)
    

### Timer

사용자 프로그램에서 while문으로 무한 루프를 돌게 된다면 특정 프로그램이 CPU를 독점할 수 있다. Timer는 특정 프로그램이 CPU를 독점하는 것을 막기 위한 하드웨이다.

**동작**

컴퓨터를 시작하면 처음에 운영 체제가 CPU를 가지고 있다가 사용자 프로그램에게 CPU를 넘겨준다. 하지만 그냥 넘겨 주지 않고 Timer에 값을 세팅하고 넘겨 주게 된다. 시간이 지나서 Timer의 값이 0이 되면 타이머 인터럽트가 발생하여 다른 프로그램에게 CPU를 넘겨준다.

### DMA (Direct Memory Access) Controller

원칙적으로 메모리는 CPU에 의해서만 접근할 수 있는 장치이다. CPU 외의 장치가 메모리의 데이터에 접근하기 위해서는 CPU에게 인터럽트 발생시켜 CPU가 대신 컨트롤러의 로컬 버퍼와 메모리 사이에서 데이터를 옮겨 준다. 하지만, 작업 처리 속도가 매우 빠른 CPU가 인터럽트를 많이 당하면 비효율적이다. 그래서 CPU 이외에 메모리 접근이 가능한 DMA 컨트롤러를 둔다.

DMA를 사용하게 되면 로컬 버퍼에서 메모리로 읽어오는 작업을 CPU 대신 수행해 줄 수 있다. 이때, DMA는 바이트 단위가 아니라 블록이라는 큰 단위로 정보를 메모리로 읽어온 후에 인터럽트를 발생시켜서 작업 완료 신호를 CPU에게 보낸다. 이러한 방식으로 CPU에 발생하는 인터럽트의 빈도를 줄여 CPU를 효울적으로 이용할 수 있다.

다만, CPU와 DMA가 동시에 메인 메모리에 접근할 수 있다는 문제점이 있어서 누가 메모리에 먼저 접근하게 만들지 Memory Controller가 교통 정리하는 역할을 한다.  

## 입출력 I/O의 수행

모든 입출력 명령은 운영 체제만 사용할 수 있는 특권 명령으로만 가능하다. 그렇다면 사용자 프로그램은 어떻게 I/O를 할 수 있을까? 바로, 시스템 콜을 활용한다. 

시스템 콜은 운영 체제에게 I/O를 요청하는 것을 말한다. 운영 체제의 서비스를 받기 위해 커널 함수를 호출하는 것이라고 생각하면 된다.

### 동작

mode bit가 1인 상태로 사용자 프로그램이 실행되고 있다가 I/O를 해야 하는 상황이 오면 바로 OS의 주소로 점프를 할 수가 없다. 그래서 프로그램이 직접 인터럽트 라인을 세팅 한 후, CPU에게 인터럽트를 보내면, CPU는 다음 인스트럭션을 수행하는 대신 mode bit를 0으로 바꾸고 CPU 제어권을 OS로 넘기게 된다. 이러한 방식으로 사용자 프로그램이 OS에게 I/O 요청을 보낼 수 있다.

## 인터럽트

CPU가 프로그램을 실행하고 있을 때, 입출력 하드웨어 등의 장치나 예외 상황이 발생하여 처리가 필요한 경우에 CPU에게 알리는 일종의 이벤트를 뜻한다. 좁은 의미의 인터럽트는 하드웨어 인터럽트를 의미하지만, 넓은 의미의 인터럽트는 소프트웨어 인터럽트까지 포함한다.

소프트웨어 인터럽트는 Trap이라고도 부르며, 프로그램이 오류를 발생하여 Exception이 발생하거나, 시스템 콜을 통해 커널 함수를 호출할 때 등이 있다.

### 하드웨어 인터럽트 vs 소프트웨어 인터럽트

CPU 인터럽트 라인에 신호를 보내서 인터럽트를 알려주는 방식은 똑같다. 다만 하드웨어 인터럽트는 컨트롤러 등 하드웨어 장치가 CPU의 인터럽트 라인을 세팅하는 반면, 소프트웨어 인터럽트는 소프트웨어가 CPU의 인터럽트 라인을 세팅한다.

### 인터럽트 벡터

운영 체제는 할 일을 쉽게 찾아가기 위해 인터럽트 벡터를 가지고 있는데, 인터럽트 벡터란 인터럽트 종류마다 번호를 정해서 번호에 따라 처리해야 할 코드가 위치한 부분을 가리키고 있는 자료 구조를 말한다.

### 인터럽트 처리 루틴 (인터럽트 핸들러)

인터럽트 벡터에서 실제 처리해야 할 코드를 인터럽트 처리 루틴 혹은 인터럽트 핸들러라고 부른다. 

인터럽트 처리 루틴을 통해 해당하는 인터럽트 처리를 완료하고 나면 원래 수행하던 작업으로 돌아갈 위치를 알아야 하므로 인터럽트 처리 전에 수행 중이던 작업이 무엇이었는지 반드시 저장해야 하는데, 이러한 정보를 저장히기 위해 운영 체제는 PCB라는 공간을 별도로 가지고 있다.

## 출처

[http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)

[https://asfirstalways.tistory.com/115?category=664092](https://asfirstalways.tistory.com/115?category=664092)

[https://hyojaedev.tistory.com/28?category=1153831](https://hyojaedev.tistory.com/28?category=1153831)

운영 체제와 정보 기술의 원리 - 반효경
