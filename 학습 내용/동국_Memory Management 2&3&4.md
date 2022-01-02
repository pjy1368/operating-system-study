## Paging 기법

### 주소 변환

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6473b4fa-baf3-4cdb-9c20-394b458daf0e/Untitled.png)

- **page table** ⇒ 
각각의 논리적인 페이지들이 물리적인 메모리에서 어디에 올라가 있는지에 해 알려준다.
- 논리적인 페이지 번호(p)를 통해 page table에서 찾고,
물리적인 페이지 프레임 번호를 찾아갈 수 있는(f)를 가지고,
물리적 메모리에서 그 값을 찾아가게 된다.
- 내부적인 실제번호(d)는 값이 바뀌지 않는다.

### Page Table의 구현

- PTBR(Page-table base register)은 page table을 가리킨다.
- PTLR(Page-table length register)은 테이블 크기(limit)를 보관한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cf0c505a-9548-4a9d-8956-2c88d86b1d98/Untitled.png)

- 모든 메모리 접근 연산에는 2번의 memory access가 필요하다.
(page table 접근 1번, data/instruction 접근 1번)

⇒   메모리에 계속 접근하는 작업은 부담이 크기 때문에,
       TLB(Translation look-aside buffer)라는 고속의 캐시를 이용하여
       빈번히 접근하는 데이터를 캐싱하여 속도를 향상 시킨다.

- TLB(Translation look-aside buffer) ⇒
Associative register를 사용하며 page table 중 일부만 존재하게된

## Two-level page table

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b6f16038-4da6-4c49-8d72-2eabc5e1c604/Untitled.png)

- 현대의 컴퓨터에서는 메모리 주소가 매우 큰 프로그램까지 지원한다.
- Ex) 32비트 사용 시 4GB(2^32 B)의 주소 공간을 가지고 있다.
page size가 4K면 1M 개의 page table 이 필요하게 되고,
4B 라면 프로세스당 4M의 page table 이 필요하게 된다.

⇒ 대부분의 프로그램은 주소 공간의 일부분만 사용하므로
     page table의 공간이 심하게 낭비되게 된다.

- 또한 각 page마다 page table에 매칭시키고,
이것을 물리적 메모리에 일일히 매칭시키게 된다면,
엄청난 공간낭비가 이루어 지게 될 것이다.

⇒ 이를 줄이기 위해서 2단계 테이블 페이지 테이블을 사용하여 공간의 효율성을 높이는 것이다.

### 2단계 페이지 테이블의 주소변환

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ff57b7c-375e-453e-8409-86f758e07131/Untitled.png)

- P1을 통해 외부 page table에서 내부 page table로 가는 번호를 얻고
- P2를 통해 내부 page table에서 페이지 프레임으로 가는 번호를 얻고
- d를 통해서 원하는 정보를 찾아가게 된다.

### 계산 예제

페이지 하나당 4KB 이고, 페이지 테이블 하나당 크기가 4Byte 이면,
페이지 내에서 바이트 단위로 얼마나 떨어져 있는지 나타내는 offset과
페이지 번호를 나타내는 page number은 각각 몇 비트가 필요할까?

offset ⇒
4KB(2^12 바이트)를 구분하기 위해서는 **12비트가 필요하다.**

내부 ⇒ 
page table은 각 4KB/4byte = 1KB 개의 항목을 가지고 있게 되고,
1KB(2^10)를 구분하기 위해서는 **10비트가 필요하게 된다.**

바깥 ⇒
32bit - 12bit - 10bit = **10비트가 필요하게 된다.**

## 다단계 페이징 기법

- 주소공간이 더 커지면 다단계 페이지 테이블이 필요하게 된다.
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical addresss의 physical address 변환에 더 많은 메모리 접근이 필요하게 된다.
- 이 또한 TLB를 통해서 메모리 접근 시간을 줄일 수 있다.

**접근시간 계산예제**

4단계 페이지 테이블에서 TLB hit ratio(성공확률)가 98% 이고
메모리 접근시간 100ns, TLB 접근 시간이 20ns 이며,

성공 했을 때 = 100 + 20  / 실패 했을 때 100 + (100*4단계) + 20 = 520

⇒ **0.98 x 120 + 0.02 x 520 = 128 ns / 그렇게 크게 오버헤드 되진 않는다.**

### Inverted Page Table 기법

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a4361022-bd24-4dd1-97e3-b1ed3dc83cdb/Untitled.png)

- 페이지 프레임 하나 당 페이지 테이블에 하나의 항목을 둔 것이다.
- 각 페이지 테이블 항목들은 각각의 물리적 메모리의 페이지 프레임이 담고있는 내용들을 표시한다.

⇒ 테이블 전체를 탐색해야하므로 효율성을 떨어짐(공간 효율 위한 방법)

⇒ associative register 를 이용해야 효율성을 높일 수 있다.

### Shared pages 기법

- Shared code (Re-entrant code = Pure code)
    - read-only로 하여 프로세스 간에 하나의 코드만을 메모리에 올림
    - 모든 프로세스의 **논리 주소공간에서 동일한 위치에 있어야 한다**.
- Private code and data
    - 각 프로세스들은 독자적으로 메모리에 올림
    - 논리 주소공간의 아무 곳에 와도 무방하다.

### 메모리 보호

 - Page table의 각 entry 마다 아래의 bit을 둔다.

- Protection bit
    - 페이지에 대한 접근 권한 (read/write/read-only)
- Valid-invalid bit
    - Valid
        
        ⇒ 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻한다. (접근 허용)
        
    - Invalid
        
        ⇒ 해당 주소의 frame에 유효한 내용이 없음을 뜻함. (접근 불허)
        

## Segmentation

프로그램은 의미 단위인 여러 개의 segment로 구성되어 있다.

- 작게는 프로그램을 구성하는 함수 하나하나,
크게는 프로그램 전체를 하나의 세그먼트로 정의가 가능하다.
- 일반적으로 code, data, stack 부분 하나씩을 세그먼트로 정의한다.

⇒ 구조는 Page 기법이랑 비슷한 측면이 있다.

논리 주소는 <segment-number, offset> 두가지로 구성되어있다.

- Segment table - base, limit 과 같은 segment 정보 존재
- Segment-table base register (STBR) - segment table의 시작 위치
- Segment-table length register (STLR) - segment 의 entry의 수

### 세그먼트 주소변환

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f320861-1b84-46e7-af3d-e21c692b90a5/Untitled.png)

- segment의 수를 체크하여 이보다 더 크거나,
segment의 길이보다 offset이 더 길지 않은지 체크한다.

### Sharing of Segments

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf82fffd-27a0-4ebe-aaee-5e2a6dcfeb7b/Untitled.png)

- 다른 프로세스에서 같은 세그먼트를 공유하고 있는 상황이다.
- 이는 모든 프로세스의 주소 공간에서 동일한 논리적 주소에 위치해야 한다

### 장단점

- Protection
    - 각 세그먼트 별로 protection bit가 있다.
    - read/write/execute 실행 권한이 있는지를 확인한다.
    - Valid bit를 통해 정보의 유효성을 확인한다.
- Sharing
    - shread segment
    - same segment number
    
    ⇒ segment는 의미 단위이기 때문에 공유와 보안에 있어서 paging보다 훨씬 효과적이다.
    
- Allocation
    - 크기가 균일하지 않기에 외부 조각 문제가 발생할 수 있다..
    - First fit/Best fit 같은 방법을 사용해야 한다.
    

### Segmentation with Paging

⇒ 세그먼트 하나가 여러 개의 페이지로 구성되어 있는 방법이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d26d66e3-43b8-41c1-88f7-bf96cecbd6a2/Untitled.png)

- segment 당 페이지 테이블이 존재하고 있다.
- 이번에도 또한, 세그먼트의 길이와 요청한 offset을 비교하여 길이의 이내가 아니라면 trap에 걸리게 한다.
- offset을 쪼개서 앞부분은 페이지 번호, 뒷 부분은 다시 페이지에서의 offset으로 사용하게 된다.
- 이를 이용하여 물리적 메모리에 접근하게 된다.
