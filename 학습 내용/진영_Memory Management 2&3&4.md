## 페이징 기법

### 페이징 예시

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd0decabd-9239-40dc-8a64-ec4e1cc91bcd%2FUntitled.png?table=block&id=ae9ee779-db0f-464a-84d2-5acc36f1ebb6&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 논리적 메모리는 페이지 단위로 분할이 되고, 물리적 메모리는 프레임 단위로 분할이 되어 서로 매칭된다. 이때 논리적 주소를 물리적 주소로 변환하기 위해 페이지 테이블을 활용한다.

### 주소 변환 기법

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fcb536372-8736-4869-baee-85c1e002f465%2FUntitled.png?table=block&id=594e0136-0b52-41b1-9509-52d2d0453505&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 페이징 기법에서는 CPU가 사용하는 논리적 주소를 페이지 번호(p)와 페이지 오프셋(d)으로 나누어 주소 변환에 사용한다.
- 페이지 번호는 각 페이지별 주소 변환 정보를 담고 있는 페이지 테이블 접근 시 인덱스로 사용되고, 해당 인덱스의 항목에는 그 페이지의 물리적 메모리 상의 기준 주소, 즉 시작 위치가 저장된다.
- 따라서 특정 프로세스의 p번째 페이지가 위치한 물리적 메모리의 시작 위치를 알고 싶다면 해당 프로세스의 페이지 테이블에서 p번째 항목을 찾아보면 된다.
- 페이지 오프셋은 하나의 페이지 내에서의 변위를 알려준다. 따라서 기준 주소 값에 변위를 더함으로써 요청된 논리적 주소에 대응하는 물리적 주소를 얻을 수 있다.
- 위 그림에서 f (물리적 주소의 시작 위치) + d를 취하면 처음에 CPU가 요청한 논리적 주소에 대응하는 물리적 주소가 된다.

### 페이지 테이블의 구현

- 페이지 테이블은 페이징 기법에서 주소 변환을 하기 위한 자료 구조로, 물리적 메모리인 Main Memory에 상주한다.
- 현재 CPU에서 실행 중인 프로세스의 페이지 테이블에 접근하기 위해 운영체제는 2개의 레지스터를 사용한다.
    - 페이지 테이블 기준 레지스터(page-table base register)
        - 메모리 내에서 페이지 테이블의 시작 위치를 가리킴
    - 페이지 테이블 길이 레지스터(page-table length register)
        - 페이지 테이블의 크기를 보관함
- 페이징 기법에서 모든 메모리 접근 연산은 총 2번씩 필요하다.
    - 주소 변환을 위해 페이지 테이블에 접근
    - 변환된 주소에서 실제 데이터에 접근
    - 이러한 오버헤드를 줄이고 메모리의 접근 속도를 향상하기 위해 **TLB(Translation Lock-aside Buffer)**라고 불리는 고속의 주소 변환용 하드웨어 캐시를 사용한다.

### TLB

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0a645f96-259f-4f8c-8825-eb3ebf389683%2FUntitled.png?table=block&id=787b5e69-b826-4d7b-9c1b-ad25da565fc6&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- TLB는 가격이 비싸기 때문에 빈번히 참조되는 페이지에 대한 주소 변환 정보만 담게 된다.
- 요청된 페이지 번호가 TLB에 존재한다면 곧바로 대응하는 물리적 메모리의 프레임 번호를 얻을 수 있지만, TLB에 존재하지 않는 경우에는 메인 메모리에 있는 페이지 테이블로부터 프레임 번호를 알아내야 한다.
- 페이지 테이블에는 페이지 번호가 주어지면 해당 페이지에 대응하는 프레임 번호를 얻을 수 있지만, TLB에는 페이지 번호와 프레임 번호 쌍을 가지고 있으므로 특정 페이지 번호가 있는지 TLB 전체를 찾아봐야 한다.
    - 이때 TLB 풀 스캔 시간이 오래 걸리므로 병렬적으로 탐색이 가능한 연관 레지스터를 사용한다.
- TLB는 context switch시, 이전 프로세스의 주소 변환 정보를 담고 있는 내용이 전부 지워진다.

### 연관 레지스터를 사용한 평균 메모리 접근 시간 (Effective Access Time)

- 메모리에 접근하는 시간을 1이라 하고, 연관 레지스터에 접근하는 시간을 ε이라고 하자. (이때, ε은 1보다 충분히 작은 값이다.)
- 요청된 페이지에 대한 주소 변환 정보가 연관 레지스터에 존재할 확률을 a라고 하자.
- 평균적인 메모리 접근 시간 EAT는 다음과 같다.
    - EAT = (1 + ε)a + (2 + ε)(1 - a) = 2 + ε - a
    - (1 + ε)a 항은 요청된 페이지의 주소 변환 정보가 TLB에 존재하는 경우이다.
        - TLB로부터 직접 물리적인 메모리에 얻는 데 ε시간이 소요되고, 실제 원하는 데이터에 접근하기 위해 한 번에 메모리 접근이 필요하므로 (1 + ε)이 된다. 여기에 TLB에서 존재하는 확률인 a를 곱하면, (1 + ε)a가 된다.
    - (2 + ε)(1 - a) 항은 요청된 페이지에 대한 주소 변환 정보가 TLB에 존재하지 않는 경우이다.
        - 요청된 페이지에 대한 주소 변환 정보가 TLB에 있는지 확인하는 시간 ε이 소요되고, 페이지 테이블에 접근하는 시간 ε과 실제 원하는 데이터에 접근 시간 a, 두 번의 메모리 접근이 필요하게 된다. 따라서 (2 + ε)의 시간이 소요되며, 여기에 TLB에서 찾지 못하는 비율인 (1 - a)를 곱하여 (2 + ε)(1 - a)가 된다.
    

### 계층적 페이징

- 현대의 컴퓨터는 주소 공간이 매우 큰 프로그램을 지원한다.
    - 예를 들어 32비트 주소 체계를 사용하는 컴퓨터에서는 4GB의 주소 공간을 갖는 프로그램을 지원한다.
        - 페이지 사이즈가 4K라면, 한 프로세스당 페이지 테이블을 위해 1M 크기의 페이지 테이블 메모리 공간이 필요하다.
        - 그러나 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 페이지 테이블 공간이 심하게 낭비된다.
- 위 문제로 인하여 페이지 테이블 자체를 페이지로 구성하는 2단계 페이징 기법을 사용한다.

**2단계 페이징 기법**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff30d35fe-f4cc-4d39-807d-d1f1f10a883a%2FUntitled.png?table=block&id=2ef36cbb-c15b-4141-8b39-aac51e64df2a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 주소 변환을 위해 외부 페이지 테이블과 내부 페이지 테이블의 두 단계에 걸친 페이지 테이블을 사용한다.
- 사용하지 않는 주소 공간에 대해서는 외부 페이지 테이블의 항목을 NULL로 설정하며, 여기에 대응하는 내부 페이지 테이블을 생성하지 않는다.
- 페이지 테이블을 위해 사용되는 메모리 공간을 줄이지만, 페이지 테이블의 수가 증가하므로 시간적인 손해가 뒤따른다.

**2단계 페이징 기법의 주소 변환**

- 프로세스의 논리적 주소를 두 종류의 페이지 번호(P1, P2)와 페이지 오프셋(d)으로 구분한다.
    - P1은 외부 페이지 테이블의 인덱스이고, P2는 내부 페이지 테이블의 인덱스이다.
    - 논리적 주소를 <P1, P2, d> 형태로 표시할 수 있게 된다.
- 외부 페이지 테이블로부터 P1만큼 떨어진 위치에서 내부 페이지 테이블의 주소를 얻게 되고, 내부 페이지 테이블로부터 P2만큼 떨어진 위치에서 요청된 페이지가 존재하는 프레임의 위치를 얻은 후, 해당 프레임으로부터 d만큼 떨어진 곳이 바로 찾으려는 물리적 주소이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F749488fb-7388-4312-8e5e-a3fa0f6e66c0%2FUntitled.png?table=block&id=ed19c811-735e-4937-89fd-6cf9d11d1e18&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 32비트 주소 체계를 갖는 시스템에서 페이지 하나의 크기를 4KB라 하고 페이지 테이블 항목의 크기를 4byte라고 할 때, 32비트의 논리적 주소 중 페이지 번호와 페이지 오프셋을 위해 각각 몇 비트씩 할당해야 하는가?
    - 먼저 페이지의 크기가 4KB이므로 하나의 페이지 내에서의 byte 오프셋을 결정하기 위해 12비트가 필요하다.
    - 2단계 페이징 기법에서는 내부 페이지 테이블 자체를 하나의 프레임에 보관하게 되므로 내부 페이지 테이블의 크기 역시 4KB가 된다. 페이지 테이블 항목의 크기가 4byte이므로 내부 페이지 테이블은 4KB/4byte, 즉 1K개의 항목을 가지게 된다. 1K 항목을 구분하기 위해서는 10비트가 필요하다.
    - P1은 (32 - 12- 10)을 취하여 10비트가 할당된다.

**다단계 페이징 기법**

- 프로세스의 주소 공간이 커질수록 페이지 테이블의 크기도 커지므로 주소 변환을 위한 메모리 공간 낭비가 심해진다.
- 다단계 페이지 테이블을 사용하면 페이지 테이블을 위해 사용되는 메모리 공간의 소모를 줄일 수 있지만, 메모리 접근 시간이 늘어난다.
- TLB를 통해 메모리 접근 시간을 줄일 수 있다.
- 4단계 페이지 테이블을 사용하는 경우
    - 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns
    - 요청된 페이지에 대한 주소 변환 정보가 TLB에 존재할 확률 98%
    - 평균 메모리 접근 시간 (EAT) = 0.98 x 120 + 0.02 x 520 = 128ns
        - TLB hit이 성공할 때는 TLB 접근 시간과 메모리 접근 시간의 합인 120ns가 된다.
        - TLB hit이 실패할 때는 TLB 접근 시간과 메모리 접근 시간 x 5의 합인 520ns이 된다.
    - 결과적으로 주소 변환을 위해서만 28ns가 소요된다.

### 역페이지 기법

- 페이지 테이블로 인한 메모리 공간의 낭비가 심한 이유
    - 모든 프로세스의 모든 페이지에 대한 페이지 테이블 항목을 구성해야 함.
- 이 문제를 해결하기 위해 역페이지 기법을 사용할 수 있다.
    - 물리적 메모리의 페이지 프레임 하나당 페이지 테이블에 하나씩의 항목을 두는 방식이다.
    - 논리적 주소에 대해 페이지 테이블을 만드는 것이 아니라, 물리적 주소에 대해 페이지 테이블을 만드는 것이다.
    - 시스템 전체에 페이지 테이블을 하나만 둔다.
    - 페이지 테이블의 각 항목은 어느 프로세스의 어느 페이지가 이 프레임에 저장되었는 지의 정보를 보관하고 있다.
        - 페이지 테이블의 각 항목은 프로세스 번호(pid)와 그 프로세스 내의 논리적 페이지 번호(p)를 담고 있다.
    - 페이지 전체를 탐색해야 하는 단점이 있다.
        - 따라서 일반적으로 연관 레지스터를 사용하여 병렬 탐색을 수행한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3cb872b1-8487-4765-991a-8ad07549b7c3%2FUntitled.png?table=block&id=39c8ca83-a0a1-4522-a606-56f6addbb3c7&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 공유 페이지

- 공유 코드는 메모리 공간의 효율적인 사용을 위해 여러 프로세스에 의해 공통적으로 될 수 있도록 작성된 코드를 말한다.
    - 재진입 가능 코드, 순수 코드라고도 불리며, 읽기 전용의 특성을 가지고 있다.
- 공유 페이지는 공유 코드를 담고 있는 페이지를 말한다.
    - 공유 페이지는 여러 프로세스에 의해 공유되는 페이지이므로 물리적 메모리에 하나만 적재되어 메모리를 효율적으로 사용할 수 있다.
- 예를 들어 문서 편집기 프로그램을 공유 페이지를 사용해서 작성한 경우, 이 프로세스를 여러 개 수행하더라도 공유 코드를 담은 페이지는 메모리에 하나만 올라간다.
    - 공유 코드는 읽기 전용의 성질을 가져야 하고 모든 프로세스의 논리적 주소 공간에서 동일한 위치에 존재해야 한다.
- 사유 페이지는 프로세스들이 공유하지 않고, 프로세스 별로 독자적으로 사용하는 페이지를 뜻함.
    - 사유 페이지는 해당 프로세스의 논리적 주소 공간 중 어떠한 위치에 있어도 무방하다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F63bff394-74c1-46e0-a8d2-9a6575fe7571%2FUntitled.png?table=block&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&id=95220732-d872-4c9a-866a-c4d8837e23a2&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- ed 1, ed 2, ed 3는 공유 페이지이고, data 1, data 2, data 3는 사유 페이지이다.

### 메모리 보호

- 페이지 테이블의 각 항목에는 주소 변환 정보 외에 메모리 보호를 위한 보호 비트와 유효-무효 비트가 존재한다.
- 보호 비트는 각 페이지에 대해 읽기-쓰기/읽기 전용 등의 접근 권한을 설정하는 데 사용된다.
- 유효-무효 비트는 해당 페이지의 내용이 유효한 지에 대한 내용을 담고 있다.
    - 유효-무효 비트가 ‘유효’: 해당 메모리 프레임에 해당 페이지가 존재. 따라서 접근 허용
    - 유효-무효 비트가 ‘무효’: 해당 페이지가 물리적 메모리에 올라와 있지 않고, backing store에 존재하여 해당 메모리 프레임에 접근 불가

아래는 유효-무효 비트와 관련된 사진이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffe6972e1-1096-4702-a405-ac8a711406f7%2FUntitled.png?table=block&id=01038636-50a2-43e6-a41f-21b3c3dd4c2c&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 세그먼테이션

- 프로그램은 의미 단위인 여러 개의 세그먼테이션으로 구성될 수 있다.
    - 작게는 프로그램을 구성하는 하나 하나를 세그먼트로 정의
    - 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
    - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의됨
- 세그먼트는 다음과 같은 논리적인 유닛이라 할 수 있다.
    - main()
    - function
    - global variables
    - stack
    - symbol table
    - arrays
    - ...

### 세그먼테이션 기법

- 논리적 주소는 <세그먼트 번호(s), 오프셋(d)>으로 나뉘어 사용된다.
    - 세그먼트 번호는 해당 논리적 주소가 프로세스 주소 공간 내에서 몇 번째 세그먼트에 속하는 지를 나타낸다.
    - 오프셋은 그 세그먼트 내에서 얼마만큼 떨어져 있는 지에 대한 정보를 나타낸다.
- 세그먼테이션 테이블을 사용한다.
    - 기준점(base)과 한계점(limit)을 가지고 있다.
    - 기준점은 물리적 메모리에서 그 세그먼트의 시작 위치를 나타내고, 한계점은 그 세그먼트의 길이를 나타낸다.
- 세그먼트 테이블 기준 레지스터(STBR)와 세그먼트 테이블 길이 레지스터(STLR)을 사용한다.
    - 세그먼트 테이블 기준 레지스터는 현재 CPU에서 실행 중인 프로세스의 세그먼트 테이블이 메모리의 어느 위치에 있는지 그 시작 주소를 담고 있다.
    - 세그먼트 테이블 길이 레지스터는 그 프로세스의 주소 공간이 총 몇 개의 세그먼트로 구성되는지, 즉 세그먼트의 개수를 나타낸다.
    

### 세그먼테이션 하드웨어

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F188c9a9a-8185-4fdc-a185-3c5d364b80d0%2FUntitled.png?table=block&id=41c475d6-65ba-40ad-9537-9771a92dedad&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 논리적 주소를 물리적 주소로 변환하기 전에 두 가지 사항을 확인한다.
    - 요청된 세그먼트 번호(s)가 STLR에 저장된 값보다 작은 값인가?
        - 그렇지 않다면, 존재하지 않는 세그먼트에 대한 접근 시도이므로 예외 발생
    - 논리적 주소의 오프셋 값(d)이 그 세그먼트의 길이(limit)보다 작은 값인가?
        - 그렇지 않다면, 세그먼트 길이를 넘어서는 오프셋 위치이므로 예외 발생

### 메모리 보호 기법과 공유 세그먼트

**메모리 보호 기법**

- 각 세그먼트 별로 보호 비트가 있다.
    - 보호 비트는 각 세그먼트에 대해 읽기/쓰기/실행 등의 권한이 있는 지를 나타낸다.
- 각 세그먼트 별로 유효 비트가 있다.
    - 유효 비트는 각 세그먼트의 주소 변환 정보가 유효한지, 즉 해당 세그먼트가 현재 물리적 메모리에 적재되어 있는 지를 나타낸다.

**공유 세그먼트**

- 여러 프로세스가 특정 세그먼트를 공유하여 사용하는 개념이다.
- 공유 세그먼트는 이 세그먼트를 공유하는 모든 프로세스의 주소 공간에서 동일한 논리적 주소에 위치해야 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F11b0499b-945c-4b40-8abb-f850648b48d3%2FUntitled.png?table=block&id=d500acf2-573a-4239-826c-54174d0b1f3b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 세그먼테이션의 장점과 단점

**장점**

세그먼트는 의미 단위로 나누어져 있기 때문에 공유와 보안의 측면에서 페이징 기법에 비해 훨씬 효과적이다. 주소 공간의 일부를 공유하거나 특정 주소 공간에 읽기 전용 등의 접근 권한 제어를 하고자 할 경우, 이는 단순히 크기 단위가 아닌 어떤 의미 단위로 이루어질 때가 많다.

**단점**

세그먼테이션 기법에서는 프로그램을 의미 단위로 나누기 때문에 세그먼트의 길이가 균일하지 않다. 따라서 물리적 메모리 관리에서 외부 조각이 발생하게 되며, 연속 할당 메모리 관리의 가변 분할 방식에서의 동일한 문제점이 발생한다.

## 페이지드 세그먼테이션

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff0e0e307-cafb-4265-b537-0d5c9202f1a6%2FUntitled.png?table=block&id=3569cdb6-4f4c-41f1-a062-bc817e336bc8&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 페이지드 세그먼테이션 기법은 프로그램을 의미 단위의 세그먼트로 나누되, 세그먼트가 동일한 크기 페이지의 집합으로 구성한다.
    - 즉 페이지드 세그먼테이션 기법에서는 하나의 세그먼트 크기를 페이지 크기의 배수가 되도록 함으로써 세그먼테이션 기법에서 발생하는 외부 조각의 문제를 해결하며, 동시에 세그먼트 단위로 프로세스 간의 공유나 프로세스 내의 접근 권한 보호가 이루어지도록 함으로써 페이징 기법의 약점을 해소한다.
- 주소 변환을 위해 외부의 세그먼트 테이블과 내부의 페이지 테이블, 이렇게 두 단계의 테이블을 이용한다.
    - 하나의 세그먼트가 여러 개의 페이지로 구성되므로 각 세그먼트마다 페이지 테이블을 가지게 되는데, 2단계 페이지 테이블과 유사한 구조이다.
- <세그먼트 번호, 오프셋>으로 구성된 논리적 주소를 물리적 주소로 변환하는 과정은 다음과 같다.
    - 논리적 주소의 상위 비트인 세그먼트 번호(s)를 통해 세그먼트 테이블의 해당 항목에 접근한다.
        - 이 세그먼트 항목에는 세그먼트 길이와 그 세그먼트의 페이지 테이블 시작 주소가 들어 있다.
    - 오프셋 값(d)을 세그먼트 내에서의 페이지 번호(p), 페이지 내에서의 변위(d’)로 사용하도록 쪼갠다.
    - p와 d’을 이용하여 물리적 메모리에 접근한다.
    

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f)
- [https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f)
- [https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)
- 운영체제와 정보 기술의 원리 - 반효경
