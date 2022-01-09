## Caching

- 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청 시 캐시로 부터 직접 서비스하는 방식
- paging system 외에도 cache memory, buffer caching, web caching 등 사용
- **캐쉬 운영의 시간 제약**
    - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 필요한 경우, 실제 시스템에서 사용할 수 없다.
    - O(1)에서 최대 O(lon n)까지 허용

### Paging System에서 LRU, LFU에 대해

- page fault인 경우에만 OS가 관여할 수 있게 되는데,
페이지가 이미 메모리에 존재하는 경우에는 참조 시의 시각 등의 정보를
OS가 알 수 없다.

⇒ 그러면 실제로는 어떤 알고리즘이 사용되는지 알아보자.

### Clock Algorithm (= Second Chance Algorithm)

- NUR(Not Used Recently), NRU(Not Recently Used) 라고도 불린다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1691159c-a4c7-426d-8ece-11238f65fffb/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095754Z&X-Amz-Expires=86400&X-Amz-Signature=d99a117f35252cce03aa7dea62221406285cd4a9a33bca29b011b62a9ea8da0a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 각 네모안에 요소들은 page frame이다.
- reference bit을 사용해서 교체 대상 페이지를 선정한다.
- reference bit이 0인 것을 찾을 때 까지 포인터를 앞으로 한칸씩 이동시키고,
이동하는 도중에 reference bit이 1인 것을 모두 0으로 바꾸게 된다.
- reference bit이 0인것을 찾게되면 그 페이지를 교체하게 되는 방식이다.

⇒ 1일 때 0으로 바꾸고, 0일 때 replace를 진행하게 된다.
     즉, 두 번의 기회가 주어지는 것 (=second chance)

- 외에도 modified bit을 두어서 변경이 된 페이지가 있다면,
 replace의 우선순위 낮추거나 backing store에 저장 후 replace하는 방식을 사용한다.

## Page Frame의 할당

- 각 프로세스에 얼마만큼의 page frame을 할당해야 할까? (=Allocation 필요)
    - 메모리에 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지를 동시에 참조하는 경우 최소 할당 수가 있어야 함
    - Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리함
- Allocation 종류
    - **Equal allocation** : 모든 프로세스에 똑같은 갯수 할당
    - **Proportional allocation** : 프로세스 크기에 비례해 할당
    - **Priority allocation** : 프로세스의 우선순위에 따라 할당
    

**이런 할당이 아니라도 replace 알고리즘들을 이용해 할당의 효과를 낼 수 있다.**

- Global replacement
    - replace 시 다른 프로세스에 할당된 frame을 빼앗아 올 수 있다.
    - 프로세스 별 할당량을 조절하는 또 다른 방법이다.
    - FIFO, LRU, LFU 같은 알고리즘이 해당된다.
    - Working set, PFF 알고리즘도 해당 됨
    (실제로 할당의 효과가 나는 알고리즘)
- Local replacement
    - 자신에게 할당된 frame 내에서만 replace를 실행한다.
    - FIFO, LRU, LFU 등의 알고리즘은 프로세스 별로 운영 시에 해당됨
    

### Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지
못한 경우에 발생한다.
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2e2ec785-8e63-438d-8573-fc62edb48510/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095814Z&X-Amz-Expires=86400&X-Amz-Signature=be6e47c172192c492f14055322e65099285b8981fc98ede5c617687af0bc13fa&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
- 프로세스가 일정량 올라가 있을 시에는 CPU 사용량을 최대로 끌어올릴 수 있지만, 한계치를 넘어가게 되면 Thrashing 현상이 일어나게 된다.
- 대부분의 프로세스의 page는 swap in / swap out 으로 매우 바쁘게 된다.
    
    ⇒ page fault 비율은 높아지고, CPU 사용량은 낮아진다.
    

***** multi programming degree를 조절하여, 이런 현상을 막아야 한다. *****

### Working-Set Model

- Locality of reference
    - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조하는데,
    해당 page들의 집합을 **locality set**이라고 한다.
- Locality에 기반해 프로세스가 일정 시간 동안 원활하게 수행되기 위해서 한꺼번에 메모리에 올라와 있어야하는 page들의 집합을 **Working Set**이라고 정의한다.
- 프로세스의 working set 전체가 메모리에 올라와 있어야 수행이 되고,
그렇지 않을 경우에는 모든 frame을 반납한 후 swap out 을 한다.
- ****multi programming degree를 조절 Thrashing을 방지 할 수 있다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/285a7046-38b3-4d56-8fd5-81533d6b22d3/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095829Z&X-Amz-Expires=86400&X-Amz-Signature=9dc32cdd38041e5256c0c0d5b82a27e89a06d41f9121980deb71f7b31999bca1&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- Window size
    - working set을 제대로 탐지하기 위해서는 widow size를 잘 결정해야 함.
    - 너무 작으면 locality set을 모두 수용하지 못할 우려가 있고,
    너무 크면 여러 개의 locality set을 수용하게 된다.
    - 전체 프로그램을 구성하는 page를 working set으로 간주할 수도 있다.
- 프로세스들의 working set size 합이 page frame 수 보다 큰 경우
    - 일부 프로세스를 swap out 시켜 남은 프로세스의 working set을 
    우선적으로 충족시켜준다.
- working set을 다 할당하고도 page frame이 남는 경우
    - swap out 되었던 프로세스에게 working set을 할당하게 된다.

### PFF(Page-Fault Frequency) Scheme

- page-fault 비율의 상한값과 하한값을 둔다.
    - 상한값을 넘으면 frame을 더 할당하고,
    하한값 이하이면 할당 frame 수를 줄이게 된다.
    - 빈 frame이 없으면 일부 프로세스를 swap out 한다.

### Page Size의 결정

- Page size를 감소시키면
    - 페이지 수 증가, 페이지 테이블 크기 증가
    - 내부조각 감소, Disk transfer(Seek/rotation vs transfer)의 효율성 감소
    
    ⇒ 필요한 정보만 메모리에 올라와 메모리 이용이 효율적이게 되지만,
         locality의 활용 측면에서는 좋지 않다.
    
    **최근에는 page size를 점점 키워주는 추세이다.**
