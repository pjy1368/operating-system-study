## Logical address vs Physical address

---

**논리적 주소 (Logical address = virtual address)**

- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 **logical address**이다.

**물리적 주소** (**Physical address)**

- 메모리에 실제 올라가는 위치

## **주소 바인딩 (=주소 변환, Address Binding**

---

- Symbolic  (사용자) → Logical  (프로그램) → Physical (하드웨어)

주소 변환 과정은 위와 같이 이루어진다.
우리가 살펴볼 내용은 Logical에서 Physical로 넘어가는 시점이다.

**방법 :**

**Compile time binding**

- 물리적 메모리 주소가 컴파일 시 결정된다.
- 메모리 위치가 변경되면 재컴파일을 해야한다.
- 컴파일러는 절대 코드를 생성한다.

⇒ 거의 사용하지 않는 방식

**Load time binding**

- 메모리 주소가 프로그램이 실행될 때 결정된다.
- Loader의 책임 하에 물리적 메모리 주소를 부여한다.
- 컴파일러가 재배치 가능한 코드를 생성한 경우 가능하다.

**Run time binding**

- 수행이 시작된 후에도 프로세스의 메모리 상 위치를 옮길 수 있음
- CPU가 주소를 참조 할 때마다 binding을 점검한다.
- 하드웨어적인 지원이 필요하다 **(MMU)**

### **MMU (Memory-Management Unit)**

- 논리적 주소를 물리적 주소로 매핑해 주는 하드웨어
- **MMU scheme**
사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해서 base register(=relocation register)의 값을 더한다.

**예시.**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a4c1b727-da44-41c3-819f-6fef4cf09d65/Untitled.png)

CPU가 논리적 주소값(346)을 요청한 상황이다.

- MMU는 limit register를 통해 주소값을 검사한다.
- CPU가 요청한 주소값을 relocation register 값과 더해서
물리적 주소 값을 만들어 낸다.
- 14000 + 346 = 14346

⇒ **limit register 구조도**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8b2462aa-363b-4b05-b4c1-3544eaf39eed/Untitled.png)

CPU가 논리 주소를 요청함 → limit register를 통해 주소 값을 검사함

YES ⇒ 기본 주소값과 더해서 물리적 주소 값을 찾아갈 수 있게 함

NO ⇒ trap에 걸려서 프로그램을 종료시키거나 할당해주지 않음

**⇒ 이처럼 사용자 프로그램은 논리 주소만을 다룬다.
    (실제 물리적 주소를 볼 수 없으며 알 필요가 없는 것이다.)**

## 용어 정리

### Dynamic loading

⇒  프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load 하는 것

- 가끔씩 사용되는 많은 양의 코드의 경우 유용하다.
- OS의 특별한 지원 없이 프로그램 자체에서 구현이 가능하다.

### Overlays

⇒ 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올리는 것

- 프로세스의 크기가 메모리보다 클 때 유용하다.
- OS의 지원없이 사용자에 의해 구현한다.
⇒ 초창기 시스템에서 수작업으로 프로그래머가 구현하는 것이다.

### Swapping

⇒ 프로세스를 일시적으로 메모리에서 **Backing store** 로 쫒아내는 것

- **Backing store (= swap area)**
⇒ ****디스크로써, 프로세스 이미지를 담을 만큼 빠르고 큰 저장공간
- **Swap in, Swap out**
⇒ 보통 중기 스케줄러에 의해 swap out 될 프로세스를 선정한다.
    - 우선순위가 낮은 프로세스를 swap out 시킴
    - 우선 순위가 높은 프로세스를 메모리에 올려놓음
    
    ⇒  Complie time binding, load time binding은 프로세스를
         원래 메모리 위치로 swap in을 해야하지만,
    ⇒ Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나
        swap in 을 할 수 있다.
    

### Dynamic Linking

⇒ Linking을 실행 시간까지 미루는 기법

- **Static linking**
    - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    - 실행파일의 크기가 커진다
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로
    메모리 낭비가 일어난다.
- **Dynamic linking**
    - 라이브러리가 실행 시에 연결됨
    - 운영체제의 도움이 필요하다
    - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고
    없으면 디스크에서 읽어오게 된다.

## Allocation of Physical Memory

메모리는 일반적으로 두 영역으로 나뉘어 사용된다.

- OS 상주 영역
    - interrupt vector와 함께 낮은 주소 영역을 사용한다
- 사용자 프로세스 영역
    - 높은 주소 영역을 사용한다
    

## 사용자 프로세스 영역의 할당 방법

### **연속 할당 (Contiguouts Allocation)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3727ca1c-eedb-4ba3-9c86-b931848b3809/Untitled.png)

- **고정 분할 방식**
    
    ⇒ 분할 되어있는 크기에 알맞게 프로그램을 할당하는 방식
    
- **가변 분할 방식**
    
    ⇒ 프로그램이 들어갈 때 프로그램 크기에 알맞게 할당을 하는 방식 
    

*용어정리

 **- 외부조각**

- 프로그램 크기보다 분할의 크기가 작은 경우
- 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할

 **- 내부조각** 

- 프로그램 크기보다 분할의 크기가 큰 경우
- 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
- 특정 프로그램에 배정되었지만 사용되지 않는 공간

 - **Hole (가용 공간)**

- 가용 메모리 공간
- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
- 프로세스가 도착하면 수용가능한 hole을 할당함

### **Dynamic Storage-Allocation Problem**

⇒ 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

- **First-fit**
    - Size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
- **Best-fit**
    - Size가 n 이상인 가장 작은 hole을 찾아서 할당
    - 최악의 경우 모든 hole 리스트를 탐색해야하며, 많은 수의 아주 작은 hole들이 생성되게 된다.
- **Worst-fit**
    - 가장 큰 hole에 할당
    - 모든 리스트를 탐색해야하며, 큰 hole들이 생성되게 된다.

**⇒ Compaction**

- 외부 조각 문제를 해결하는 한 가지 방법
- 사용 중인 메모리 영역을 한 군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 block을 만드는 것이다.
- 비용이 매우 많이 드는 방법이다.
- 프로세스의 주소가 실행 시간의 동적으로 재배치 가능한 경우에만 수행할 수 있다.

### **불연속 할당 (Noncontiguous allocation)**

- **Paging**
    - 프로세스를 구성하는 주소공간을 동일한 page단위로 나눈다.
    - 일부는 backing storage에, 일부는 물리적 주소에 저장하게 된다.
    - 물리적인 주소를 frame으로 나누고
    논리적인 주소를 page로 나눈다. (둘이 같은 크기)
    - page table을 이용해서 논리적 주소를 물리적 주소로 변환한다.
    - 외부 조각은 발생 X,  내부 조각은 발생할 수 있다.
    (마지막에는 페이지보다 작은 자투리 공간이 생길 수 있기 때문)
- **Segmentation**
- **Paged Segmentation**
