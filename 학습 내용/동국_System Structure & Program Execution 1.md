## 컴퓨터 시스템의 구조

![구조.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2c6613b5-3c3f-4971-add7-4726d30e43cc/구조.png)

### CPU

  -  메모리에 있는 프로그램을 처리

**PC (Program Counter)**

- ****다음 작업을 정해두고 순서에 맞게 일을 처리함

**Mode bit**

- 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치
- (사용자 프로그램 수행 = 1 [한정된 작업만 수행할 수 있음] , OS 코드 수행[커널모드, 시스템 모드] = 0 [모든 작업 수행 가능])

### Memory

  - 프로그램들을 적재해두다가 처리해야할 작업을 CPU에 넘김

**Memory Controller** 

- DMA, CPU가 같은 값을 처리하지 않게 조정해줌 (일관성을 지킴)

 *** 운영체제는 각 프로그램들이 필요한 작업들을 CPU나 각 컨트롤러에게 전달해 진행하게함

### DMA Controller

   - I/O device가 CPU에게 일일히 Interrupt를 거는 것은 매우 비효율적이기 때문에 DMA가 중간에서 local buffer에 담긴 데이터를 메모리에 담아주는 역할을 함

### Timer

  - CPU를 독점하는 것을 막아주는 요소

    Timer Interrtupt를 통해 정해진 시간이 흐른 뒤 OS에게 제어권이 넘어가도록 함

### 각 I/O device (디스크, 키보드, 마우스, 프린터, 모니터 등)

  - 각 컨트롤러를 통해 동작을 수행함

local buffer에 작업이 끝난 데이터를 저장해둠

I/O가 끝나면 Device controller는 CPU에게 intterupt를 검

### Interrupt

인터럽트 벡터 (인터럽트의 처리 루틴 주소를 가지고 있음)

인터럽트 처리 루틴 = 인터럽트 핸들러 (인터럽트를 처리하는 커널 함수)

### 사용자 프로그램이 OS의 서비스를 이용하기 위한 과정

1. System Call을 걸어 요청함 (Software Intterupt)
2. 작업이 다 끝나면 CPU에게 알려줌 (Hardware Intterupt)
- **System Call**
사용자 프로그램이 운영체제의 서비스를 받기 위해서 커널 함수를 요청하는 것
(Intterupt를 통해 Mode bit을 0으로 변경하고 해당 작업을 진행한다)

## **현대의 운영체제는 인터럽트에 의해 구동된다!**
