## Demand Paging

- 실제로 필요할 때 page를 메모리에 올리기에 다음과 같은 장점이 있다.
    
    ⇒ I/O 양의 감소, 메모리 사용량 감소, 빠른 응답 시간, 더 많은 사용자 수용
    
- Valid/Invalid bit의 사용
    - Invalild의 의미
        - 사용되지 않는 주소 영역인 경우
        - 페이지가 물리적 메모리에 없는 경우
    
    ⇒ 처음에는 모든 page entry가 Invalid로 초기화 된다.
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3bac946d-c1ef-4646-b8f5-01c97630e22f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095301Z&X-Amz-Expires=86400&X-Amz-Signature=9047c3b85111751bcbff0dababe8f8545091703d2c99733195e64d9b94d2989b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
- 0, 2, 5 (A, C, F) 같은 경우에는 당장 필요한 부분이기 때문에,
물리적 메모리에 올라가 있고 Valid로 표시가 되어있다.
- 1, 3, 4 (B, D, E) 같은 경우에는 주소공간에는 올라가 있지만 사용되지 않고 있는 부분이기 때문에 Invalid로 표시가 되어있다.
- 6, 7 (G, H) 같은 경우에는 페이지가 물리적 메모리가 없는 상태이기 때문에,
Invalid로 표시가 되어있다.

 *** 주소변환 시에 Invalid bit이 설정되어 있으면, **“Page Fault”** 라고한다. ***

### Page Fault

- Invalid page를 접근하면 MMU가 trap을 발생시킨다. (page fault trap)
- 커널 모드로 들어가서 page fault handler가 불러지게 된다.
- page fault 처리 순서
    1. Invalid 를 참조한다.
    2. 빈 page frame을 가져온다. (없으면 뺏어온다  = Page Replace)
    3. 해당 페이지를 디스크에서 메모리로 읽어온다.
        1. 디스크 읽기/쓰기 작업이 끝날 때 까지
        이 프로세스는 CPU를 선점 당한다. (block)
        2. 디스크 읽기가 끝나면 page table entry를 기록하고,
        valid/invalid bit을 valid로 바꾼다.
        3. 준비 큐에 프로세스를 넣는다
    4. 이 프로세스가 CPU를 잡고 다시 실행된다.
    5. 아까 중단되었던 명령을 다시 실행한다.
- **Performance of Demand Paging**
    - Page Fault의 비율 0 ≤ p ≤ 1
    (p가 0이면 page fault가 아니고, 1이면 모든 참조가 fault인 경우다.)
    - (1-p) x 메모리 접근시간 x p (OS & HW page fault Overhead 등)
    

### Free frame이 없는 경우

- Page replacement
    - 어떤 frame을 빼앗아올지 결정해야한다.
    - 곧바로 사용되지 않을 page를 쫒아내는 것이 좋다.
    - 동일한 페이지가 여러 번 메모리에서 쫒겨났다가 다시 들어올 수 있다.
- Replace Algorithm
    1. **Optimal Algorithm (=  Belady’s Algorithm)**
        
        ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0e8aed52-1573-4d2c-a10e-884fc64aa412/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095333Z&X-Amz-Expires=86400&X-Amz-Signature=9dbb39ce6c416e20b36f751860643f9d368abac21c87a592adc0d0be88285959&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
        
        - MIN (OPT) - 가장 먼 미래에 참조되는 page를 replace 한다.
        - 미래를 전부 안다고 가정하는 알고리즘이기 때문에,
        실제로는 사용이 가능한 알고리즘은 아니다.
        - 하지만, 다른 알고리즘에 대한 upper bound 즉, 성능의 한계를 알 수있도록 참고 할 수 있는 자료가 된다.
        
    2. **FIFO (First In First Out) Algorithm**
        
        ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/36bea657-2220-4379-88b8-6bceaf20639d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095432Z&X-Amz-Expires=86400&X-Amz-Signature=f7e95665f6331a3e002dcb951d17c670d602fa69fc917069602f0811102891e5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
        
        - 먼저 들어오는 것을 먼저 replace하는 알고리즘이다
        - 이 알고리즘은 page frame을 늘리면 더 성능이 나빠지기도 하는
        문제점이 있다. ( FIFO Anomaly = Belady’s Anomaly )
    3. **LRU (Least Recently Used) Algorithm**
        
        ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0036468c-bb18-433c-b7f8-cdf82aeecbdb/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095442Z&X-Amz-Expires=86400&X-Amz-Signature=06c0c0471db3ffbb5b5f05cd7639613beb1b932bf1f21a90ffd04ba37aafd1ce&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
        
        - 가장 오래 전에 참조된 것을 지우는 알고리즘이다.
    4. **LFU (Least Frequently Used) Algorithm**
        - 참조 횟수가 가장 적은 페이지를 지우는 알고리즘이다.
    
    - **장단점**
        - LRU는 직전 참조 시점만 보는 게 아니라 장기적인 시간 규모를 보기 때문에 LFU보다 더 page의 인기도를 좀 더 정확히 반영할 수 있다.
        - LFU는 LRU와는 달리 참조 시점의 최근성을 반영하지 못한다.
        - LFU는 LRU보다 구현이 복잡하다.
        
    - **구현**
        - **LRU**
            - 가장 최근에 참조된 요소를 아래에 둔다. (최고 우선순위)
                
                ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/61c0b2f4-e110-41ca-b347-e0c688c84dff/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095608Z&X-Amz-Expires=86400&X-Amz-Signature=6d6696211f7514d6f0d1ba949a63de95342a52cffc77f4b673b3d9a24524a457&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
                
            - O(1)인 시간 복잡도가 나오게 된다.
        - **LFU**
            - 맨 위에 참조 횟수가 가장 적은 요소를 두고,
            밑에 자식 요소와만 비교하여 아래로 내려가는 방식을 반복함.
                
                ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/80f4821a-e140-44b8-bf76-025855cecf07/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220109%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220109T095623Z&X-Amz-Expires=86400&X-Amz-Signature=2c77b380040130d032883393a14ca25e71d492c4a2a51f604345ecd2f4184efb&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
                
            - O(log n)인 시간 복잡도가 나오게 된다.
