## 디스크의 구조

- 디스크는 컴퓨터 시스템의 대표적인 2차 저장 장치이다. 수행한 작업의 결과를 영구히 보관할 수 있다는 특징이 있다.
- 디스크 외부에서는 디스크를 일정한 크기의 저장 공간으로 이루어진 1차원 배열처럼 취급한다.
    - 이 일정한 크기의 저장 공간을 **논리 블록**이라고 하며, 디스크에 데이터가 저장될 때 논리 블록 단위로 저장되고, 디스크 외부로 입출력이 일어날 때에도 논리 블록 단위로 전송된다.
    - 논리 블록에 저장된 데이터를 접근하기 위해서 해당 블록의 인덱스 번호를 디스크에 전달해야 한다. 그러면 디스크 컨트롤러는 해당 인덱스에 맞는 논리 블록의 데이터에 대해 입출력을 한다.
    - 이때 각 논리 블록이 저장되는 디스크 내의 데이터를 **섹터**라고 부른다. `(논리 블록 : 섹터 = 1 : 1)`
- 디스크의 물리적인 구조는 마그네틱의 원판으로 구성되는데, 이 원판은 하나일 수도 있고 여러 개일 수도 있다.
- 각각의 원판은 **트랙**으로 구성되고 각 트랙은 섹터로 나뉘며, 섹터에 최소한의 단위 정보가 저장된다.
- 여러 개의 원판에서 상대적 위치한 동일한 트랙의 집합을 **실린더**라고 한다.
- 섹터 0은 최외곽 실린더의 첫 번째 트랙에 있는 첫 번째 섹터이다.
    - 디스크에 데이터를 읽고 쓰기 위해서는 arm이 해당 섹터가 위치한 실린더로 이동한 후 원판이 회전하여 디스크 헤드가 저장된 섹터 위치에 도달해야 한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1496c3a0-22eb-41dd-9757-11d7552f8f91%2FUntitled.png?table=block&id=1f33187b-2cf1-4714-93fa-94baeb72bf3b&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 디스크 관리

- Physical Formatting (Low-Level Formatting)
    - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    - 각 섹터는 header + 실제 데이터(보통 512 bytes) + trailer로 구성
    - header와 trailer는 sector number, ECC(Error-Correcting Code) 등의 정보가 저장되며 컨트롤러가 직접 접근 및 운영함.
- Partitioning
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 가정
    - OS는 이것을 독립적 디스크로 취급한다. (logical disk)
- Logical Formatting
    - 파일 시스템을 만드는 것
    - FAT, inode, free space 등의 구조가 포함됨.
- Booting
    - ROM에 있는 “small bootstrap loader”의 실행
    - sector 0(boot block)을 load하여 실행
    - sector 0은 “full bootstrap loader program”
    - OS를 디스크에서 load하여 실행

## 디스크 스케줄링

- 디스크에 대한 접근 시간(access time)은 아래 세 가지 시간의 합이다.
    - 탐색 시간(seek time)
        - 디스크 헤드를 해당 실린더 위치로 이동하는 데 걸리는 시간
    - 회전 지연 시간(rotational latency)
        - 디스크가 회전해서 읽고 쓰려는 섹터가 헤드 위치에 도달하기까지 걸리는 시간
    - 전송 시간(transfer time)
        - 해당 섹터가 헤드 위치에 도달한 후 데이터를 실제로 섹터에 읽고 쓰는 데 소요되는 시간
- 회전 지연 시간과 전송 시간은 상대적으로 수치가 작고 운영체제 입장에서 통제하기 힘든 부분이라서 탐색 시간을 줄이기 위해 헤드의 움직임을 최소화하는 스케줄링 작업을 한다.
- 즉 디스크 스케줄링의 가장 중요한 목표는 디스크 헤드의 이동 거리를 줄이는 것이다.

## 디스크 스케줄링 알고리즘

### FCFS (First Come First Service) 스케줄링

- FCFS 스케줄링은 디스크에 먼저 들어온 요청을 먼저 처리하는 방식이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F31be4562-259d-4a0a-96d6-01f36fe66374%2FUntitled.png?table=block&id=8e5201fc-f4cc-45fb-b4ac-27eb2c8f9a94&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 현재 디스크 헤드가 53번 실린더에 있을 경우 헤드는 53에서 출발해 98, 183, ...과 같이 요청이 들어온 순서대로 이동하게 되므로 총 헤드가 이동한 거리를 640이 된다.
- 이 스케줄링은 합리적인 것처럼 보이지만 효율성은 매우 떨어진다.
- 최악의 경우 디스크의 한쪽 끝과 반대쪽 끝을 왔다갔다하면 탐색 시간이 매우 길어지므로 접근 시간이 기하급수적으로 커진다.

### SSTF (Shortest Seek Time First) 스케줄링

- SSTF 스케줄링은 헤드의 현재 위치로부터 가장 가까운 위치에 있는 요청을 제일 먼저 처리하는 알고리즘이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2008bdc2-de01-41f1-969b-c0f9880ef1f0%2FUntitled.png?table=block&id=d9f72368-c50e-4f3d-8191-cb4431493641&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 53번 실린더에서 출발하여 가장 가까운 65번, 그 후 65번에서 가장 가까운 67번, ...을 반복하면 총 이동 거리는 236이 된다. FCFS보다 약 3배나 성능 개선이 이루어진 것을 알 수 있다.
- 그러나 SSTF 스케줄링은 starvation 문제를 초래할 수 있다.
    - 현재의 헤드 위치 근방으로 무한히 요청이 들어오면, 헤드 위치에서 멀리 있는 요청은 영원히 기다려야 할 수도 있다.

### SCAN 알고리즘

- SCAN 알고리즘은 헤드가 디스크 원판의 안쪽 끝과 바깥쪽 끝을 오가며, 그 경로에 존재하는 모든 요청을 처리한다.
- 즉 디스크의 어떠한 위치에 요청이 들어오는 가와 상관 없이 헤드는 정해진 방향으로 이동하면서 길목에 있는 요청을 처리하며 지나간다.
- 문제점: 실린더 위치에 따라 대기 시간이 다르다.
    - 제일 안쪽이나 제일 바깥쪽 위치보다는 가운데 위치가 기다리는 평균 시간이 짧다.
    - 예를 들어 0번에서 199번까지 200개의 실린더를 가진 디스크의 경우 0번 실린더나 199번 실린더를 지금 막 지나가고 나서 해당 지점에 요청이 들어왔다면 반대쪽 끝까지 헤드가 갔다 와야 하므로 이동 거리 400만큼 대기해야 하지만,100번 실린더를 지금 막 지나가고 나서 해당 지점에 요청이 들어왔다면 대기해야 하는 이동 거리가 200에 불과하다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F030f8095-2007-43b0-9ffd-c47e8630d123%2FUntitled.png?table=block&id=1cb22914-66eb-413e-a92e-d97ee66b5651&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### SCAN 스케줄링

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd6756e23-301a-4be2-a432-7c152cfa0305%2FUntitled.png?table=block&id=d3df08ea-bfab-49fd-bb93-c656a4d4c073&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 헤드가 53번 실린더에서 출발하여 0번 실린더로 갔다가 199번 실린더 방향으로 가면서 중간에 존재하는 요청을 처리한다. 총 이동 거리는 208이 된다.

### C-SCAN 알고리즘

- C-SCAN 알고리즘은 SCAN처럼 헤드가 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다. 그러나 SCAN과 달리 헤드가 다른 족 끝에 도달해 방향을 바꾼 후에는 요청을 처리하지 않고 곧바로 출발점으로 다시 이동만 한다.
- SCAN 알고리즘보다 균일한 대기 시간을 제공한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1f46a947-e32a-45db-b4e2-ae84c1936498%2FUntitled.png?table=block&id=df8b555f-c310-43a0-befc-7393cfc4f7d4&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

### C-SCAN 스케줄링

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fae15de82-60bd-447a-9124-73431094e11a%2FUntitled.png?table=block&id=8b5aa9c5-580c-4b08-8a0c-fa03fe328895&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 헤드가 53번 실린더에서 출발하여 199번 실린더까지 이동하면서 중간에 있는 요청을 처리한다.
- 헤드가 199번 실린더에 도달하면 0번 실린더로 이동하는데, 이때 중간에 있는 요청을 처리하지 않는다. 이후 0번 실린더에서 다시 199번 실린더로 이동하면서 중간에 있는 요청을 처리한다.
- 총 이동 거리는 383이 된다.

## N-SCAN, LOOK, C-LOOK 알고리즘

- N-SCAN 알고리즘
    - SCAN의 변형 알고리즘
    - 일단 헤드가 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 요청은 되돌아올 때 처리함
- LOOK, C-LOOK 알고리즘
    - SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동
    - LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동 방향을 즉시 반대로 이동함

### C-LOOK 스케줄링

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2b4b2f4c-aa26-41f4-9039-9b67ef7f8792%2FUntitled.png?table=block&id=3843c7db-2267-4ebe-b5c6-835cbcdc4bf6&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- C-SCAN과 유사한데, 0번이나 199번 실린더의 요청이 있지 않은 이상 그 위치와 제일 가까운 요청을 처리하고 곧바로 위치를 반대 방향으로 전환한다.
- LOOK 스케줄링은 SCAN과 유사한데, 마찬가지로 요청이 없으면 0번이나 199번 실린더 끝까지 이동하지 않는다.

### 디스크 스케줄링 알고리즘의 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있다.
- File의 할당 방법에 따라 디스크 요청이 영향을 받는다.
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다.

## 스왑 영역 관리

- 디스크를 사용하는 이유
    - 메모리의 휘발적인 특성 → File System
    - 프로그램 실행을 위한 메모리 공간의 부족 → Swap Space (Swap Area)
- 스왑 영역
    - 가상 메모리 시스템에서 디스크를 메모리의 연장 공간으로 사용
    - 파일 시스템 내부에 둘 수도 있으나 별도 파티션을 사용하는 것이 일반적임
        - 공간 효율성보다는 속도 효율성이 우선
        - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
        - 따라서 block의 크기 및 저장 방식이 일반 파일 시스템과 다름

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff31f6d63-b8b6-4be4-87a1-461dd6e49c1f%2FUntitled.png?table=block&id=e3032b86-184f-4433-a7d2-a01b9f942b93&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## RAID

- RAID는 여러 개의 디스크를 묶어서 사용하는 것을 말한다.
- RAID의 사용 목적
    - 디스크 처리 속도 향상
        - 여러 디스크에 block의 내용을 분산 저장
        - 병렬적으로 읽어올 수 있음(interleaving, striping)
    - 신뢰성 향상
        - 동일 정보를 여러 디스크에 중복 저장
        - 하나의 디스크가 고장나도 다른 디스크에서 읽어올 수 있음(mirroring, shadowing)
        - 단순한 중복 저장이 아니라 일부 디스크에 parity(오류 검출 코드)를 저장하여 공간의 효율성을 높일 수 있음

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff84b205d-325b-4895-92d3-a7b8376f62d1%2FUntitled.png?table=block&id=36f4e4af-6805-4ecf-a041-56569da91d86&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140523151255773807?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140523151255773807?vmode=f)
- [https://core.ewha.ac.kr/publicview/C0101020140527124647396004?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140527124647396004?vmode=f)
- 운영체제와 정보 기술의 원리 - 반효경
