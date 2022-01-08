## 요구 페이징

- 프로그램 실행 시 프로세스를 구성하는 모든 페이지를 한꺼번에 메모리에 올리는 것이 아니라 당장 사용될 페이지만을 올리는 방식
    - I/O 양의 감소
    - Memory 사용량 감소
    - 빠른 응답 시간
    - 더 많은 사용자 수용
- 유효-무효 비트 사용
    - 무효(invalid)의 의미
        - 사용되지 않는 주소 영역인 경우
        - 페이지가 물리적 메모리에 없는 경우
    - 처음에는 모든 페이지의 유효-무효 비트가 무효 값으로 초기화된다.
        - 특정 페이지가 참조되어 메모리에 적재되는 경우 해당 페이지의 유효-무효 비트가 유효 값으로 바뀐다.
    - CPU 참조하려는 페이지가 현재 메모리에 올라와 있지 않아 유효-무효 비트가 무효로 세팅되어 있는 경우를 페이지 부재라고 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F642d8517-c320-4c59-bbc9-aa25bb46be65%2FUntitled.png?table=block&id=8a783ce6-f0eb-41f3-bf7e-572a125e51b9&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 요구 페이징의 페이지 부재 처리

- CPU가 무효 페이지에 접근하면 MMU가 trap을 발생한다. (page fault trap)
- CPU의 제어권이 커널 모드로 전환되고, 페이지 부재 처리 루틴이 호출되면서 다음과 같은 순서로 페이지 부재를 처리한다.
    - 해당 페이지에 대한 접근이 올바른가?
        - 사용되지 않는 주소 영역에 속한 페이지에 접근하려 했거나 해당 페이지에 대한 접근 권한 위반을 했을 경우 해당 프로세스를 종료한다.
    - 물리적 메모리에 비어 있는 프레임을 할당 받는다.
        - 비어 있는 프레임이 없으면 기존에 메모리에 올라와 있는 페이지 중 하나를 디스크로 쫓아낸다. (swap out)
    - 해당 페이지를 디스크에서 메모리에 읽어온다.
        - 디스크 I/O가 끝나기까지 이 프로세스는 block 상태가 되어 CPU를 선점당한다. (현재까지 저장된 CPU 레지스터 상태 및 PC 값을 PCB에 저장해 둠.)
        - 디스크 읽기가 끝나면 페이지 테이블에서 해당 페이지의 유효-무효 비트를 유효로 설정한다.
        - block 상태의 프로세스를 준비 큐로 이동시킨다.
    - 이 프로세스가 CPU를 할당받으면 실행 상태로 바뀌면서 PCB에 저장해 두었던 값을 복원하여 중단되었던 명령부터 실행을 재개한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F78761f7a-b591-427e-8215-594a3e93515a%2FUntitled.png?table=block&id=553f0e98-0936-4d48-9b61-8097463d3d6f&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### 요구 페이징의 성능

- 페이지 부재의 발생 빈도가 성능에 가장 큰 영향을 미친다.
- 유효 접근 시간(요청한 페이지를 참조하는 데 걸리는 시간)
    - (1 - P) x 메모리 접근 시간 + P x M
    - 페이지 부재 발생 비율(P) → 0 ≤ P ≤ 1
        - P = 0: 페이지 부재가 한 번도 일어나지 않은 경우
        - P = 1: 모든 참조 요청에서 페이지 부재가 발생한 경우
    - M (각종 오버헤드)
        - 페이지 부재 발생 처리 오버헤드
        - 메모리에 빈 프레임이 없는 경우 swap out 오버헤드
        - 요창된 페이지의 swap in 오버헤드
        - 프로세스의 재시작 오버헤드
        - 위 오버헤드의 총합을 뜻함.

## 페이지 교체

- 페이지 부재가 발생하여 요청된 페이지를 디스크에서 메모리로 읽어오려고 하는데, 물리적 메모리에 빈 프레임이 없을 수 있다. 이때 이미 메모리에 올라와 있는 페이지 중 하나를 디스크로 쫓아내 메모리에 빈 공간을 확보하는데, 어떠한 페이지를 교체할지 결정하는 것을 페이지 교체라고 한다.
- 아래는 페이지 교체의 예시이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F652fb268-dc4d-4bfd-915e-a53c60f52992%2FUntitled.png?table=block&id=31b08be4-2f40-4b08-bafb-3eba1f19b459&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 페이지 교체 알고리즘

- 페이지 교체를 할 때에 어떠한 프레임에 있는 페이지를 쫓아낼 것인지 결정하는 알고리즘을 페이지 교체 알고리즘이라고 한다.
    - 이 알고리즘의 목표는 페이지 부재율을 최소화하는 것이다.
    - 알고리즘의 평가
        - 주어진 페이지 참조열에 대해 부재율을 계산
        - 페이지 참조열의 예시: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

### 최적 페이지 교체 (Optimal Algorithm)

- 물리적 메모리에 존재하는 페이지 중 가장 먼 미래에 참조될 페이지를 쫓아내는 알고리즘이다.
- 미래의 참조를 알아야 하므로 오프라인에서만 사용된다.
- 다른 알고리즘의 성능에 대한 상한선(upper bound)을 제공한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe9b65d78-1dee-44e1-9281-78b6425d6c19%2FUntitled.png?table=block&id=aa754114-bd12-46c3-835e-1c8357afd57d&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 초기 4회는 물리적 메모리가 비어있으므로 불가피하게 페이지 부재가 발생한다.
- 이후 1, 2는 물리적 메모리에 1, 2페이지가 있으므로 페이지 부재가 발생하지 않는다.
- 페이지 5는 물리적 메모리에 없으므로 가장 먼 미래에 참조되는 페이지 4를 디스크로 쫓아낸다.
- 위와 같은 방식을 반복한다.

### 선입 선출 알고리즘 (FIFO Algorithm)

- 물리적 메모리에 가장 먼저 올라온 페이지를 우선적으로 내쫓는 알고리즘이다.
- 메모리를 증가하였음에도 페이지 부재가 오히려 늘어나는 현상(FIFO의 이상 현상)이 발생할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5c665e61-6286-44d9-878d-801ac54901c5%2FUntitled.png?table=block&id=cc9cf0cc-4908-4ab9-a67d-c8e6d2aa6086&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 위 그림은 메모리 프레임 개수를 늘렸음에도 페이지 부재가 늘어난 현상의 예제이다.

### LRU (Least Recently Used) 알고리즘

- 가장 오래 전에 참조가 이루어진 페이지를 쫓아내는 알고리즘이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fcc6a3b12-604f-4587-9f19-96e33b9ab976%2FUntitled.png?table=block&id=792aae38-161b-48a1-8533-50f5cdc4d61c&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 초기 4회는 물리적 메모리가 비어있으므로 불가피하게 페이지 부재가 발생한다.
- 이후 1, 2는 물리적 메모리에 1, 2페이지가 있으므로 페이지 부재가 발생하지 않는다.
- 페이지 5는 물리적 메모리에 없으므로 가장 오래 전에 참조된 페이지 3을 디스크로 내쫓는다.
- 위와 같은 방식을 반복한다.

### LFU (Least Frequently Used) 알고리즘

- 페이지의 참조 횟수가 가장 적은 페이지를 교체하는 알고리즘이다.
- 최저 참조 횟수인 페이지가 여러 개 있는 경우
    - LFU 알고리즘 자체에서는 여러 페이지 중 임의로 선정한다.
    - 성능 향상을 위해 가장 오래 전에 참조된 페이지를 지우게 구현할 수 있다.
- 장점
    - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 페이지의 인기도를 좀 더 정확히 반영할 수 있다.
- 단점
    - 참조 시점의 최근성을 반영하지 못한다.
    - LRU보다 구현이 복잡하다.
    

**LRU vs LFU**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff28b038d-fa78-49b2-b148-b7bd018eb660%2FUntitled.png?table=block&id=aa821eb8-7380-4c35-b3c1-c64a49a9d0ff&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

**LRU와 LFU 알고리즘의 구현**

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6e71b254-9576-43cd-b9a2-dab58bfbe918%2FUntitled.png?table=block&id=38531026-bb73-4944-83f8-dda43b179fc5&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- LRU는 정렬된 LinkedList 방식으로 페이지를 교체할 때 가장 위에 있는 LRU 페이지를 교체하면 되고, 특정 페이지가 참조되었을 때는 List의 맨 아래로 바로 보내면 되므로 구현이 간단하고 시간 복잡도가 O(1)이다.
- LFU는 페이지를 교체할 때는 가장 위에 있는 LFU 페이지를 교체하면 되지만, 특정 페이지가 참조되었을 때는 자기 자신보다 아래에 있는 노드와 참조 횟수를 모두 비교해야 하므로 일반적인 List를 사용하면 시간 복잡도가 O(N)이 된다. 그래서 인접 노드 간의 참조 횟수를 빠르게 비교하기 위하여 Heap 자료 구조를 사용하며, 이것의 시간 복잡도는 O(log N)이 된다.

## 출처

[https://core.ewha.ac.kr/publicview/C0101020140509151648408460?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140509151648408460?vmode=f)

운영체제와 정보 기술의 원리 - 반효경
