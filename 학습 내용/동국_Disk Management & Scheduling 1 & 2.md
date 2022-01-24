## Disk Structure

- **Logical Block**
    - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간을 뜻한다.
    - 주소를 가진 1차원 배열처럼 취급
    - 정보를 전송하는 최소 단위
- **Sector**
    - Logical block이 물리적인 디스크에 매핑된 위치를 뜻한다.
    - Sector 0 은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다.

## Disk Management

- **Physical formatting**
    - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 작업이다.
    - 각 섹터는 header + 실제 data + trailer 로 구성이 되어있다.
    - 데이터를 읽고 쓰는데 부가적인 정보가 되는 header, trailer 는 sector number,
    ECC(Error-Correcting Code) 등의 정보가 저장되며 controller 가 직접 접근 및 운영한다.
- **Partitioning**
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 작업이다.
    - OS는 이것을 독립적인 disk로 취급한다. (logical disk)
- **Logical formatting**
    - 파일 시스템을 만드는 작업이다.
    - FAT, inode, free space 등의 구조에 사용된다.
- **Booting**
    - ROM에 있는 “small bootstrap loader” 실행
    - sector 0 (boot block)을 메모리에 올려 실행하라고 지시
    - sector 0에 있는 “full bootstrap loader program” 실행
    - OS를 디스크에서 메모리에 올려 실행한다.

## Disk Scheduling

### Access time

- **Seek time**
    - head를 해당 실린더로 움직이는데 걸리는 시간
- **Rotational latency**
    - head가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
- **Transfer time**
    - 실제 데이터의 전송 시간

### Disk bandwidth

- 단위 시간 당 전송된 바이트의 수

⇒ **Disk에 접근하는 시간은 거의 Seek time에 좌우되기 때문에, 
     Disk 의 Seek time을 줄여서 bandwidth를 높혀야 된다.**

## Disk Scheduling Algorithm

- 큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서
시작한 각 알고리즘의 수행결과는? (실린더 위치는 0 ~ 199)
    - **98, 183, 37, 122, 14, 124, 65, 67**

### FCFS (First Come First Service)

- 먼저 들어온 순서대로 처리하는 알고리즘이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/28cb27ba-7c18-438c-b4f2-dac6385d0d4f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220124%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220124T145042Z&X-Amz-Expires=86400&X-Amz-Signature=d0965f518ec5541146f20f9de40467435ab3fd6030ac335e41cb4107b7a089e9&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67
- 총 이동거리 640 으로 굉장히 비효율적인 것을 볼 수 있다.

### SSTF (Shortest Seek Time First)

- 현재 head 위치에서 가장 가까운 위치부터 처리하는 알고리즘이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7d6fcdbc-4d84-42fd-8ff4-b94562769fa6/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220124%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220124T145102Z&X-Amz-Expires=86400&X-Amz-Signature=e40920962e9f6096f9d2d8baf86561de1d525b27776c0e61dfe7e46390a63259&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 53 → 65 → 67 → 37 → 14 → 98 → 122 → 124 → 183
- 총 이동거리 236으로 head의 이동거리가 많이 줄어들게 된다.
- 계속해서 가까운 위치가 큐에 들어오게 되면 먼 위치에 있는 경우는 처리할 수 없게된다. 
⇒ Starvation 문제가 발생할 수 있다.

### SCAN

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/97e2680e-9594-4506-9a8f-4caddaec3979/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220124%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220124T145121Z&X-Amz-Expires=86400&X-Amz-Signature=426faa7b0e583df9af049e12c7e05562c834b8627f00705ea929f6794219ea5d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른쪽 끝에 도달하면 역방향으로 이동하며 오른 길목에 있는 모든 요청을 처리하면서 끝으로 이동한다.
- 문제점
⇒ 실린더 위치에 따라서 대기 시간이 다르다.
(가장자리에 있는 경우와 가운데 있는 경우)

### C-SCAN

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/eace6ccc-c407-4672-8f8d-4505fff9803f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220124%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220124T145207Z&X-Amz-Expires=86400&X-Amz-Signature=99d56c822f04aecf108a00e90796d15cb504bcc2a078453dd16291a40bbb2cc5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동한다.
- SCAN 보다 균일한 대기 시간을 제공한다

### N-SCAN

- SCAN의 변형 알고리즘
- 일단 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 처리한다

  ⇒ 대기시간의 편차를 줄일 수 있음

### LOOK and C-LOOK

- SCAN 과 C-SCAN의 변형 방법이다
- 한쪽 끝에서 다른쪽 끝으로 이동하면 가되, 이동하는 방향 앞에 더 이상 처리할 요소가 없다면
바로 돌아오게 된다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f542a2df-72d9-4ae4-aa11-d3425bee7bb6/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220124%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220124T145145Z&X-Amz-Expires=86400&X-Amz-Signature=8179af266c1f60bea505bc60dbe43685a7fcc2e827c458462a57e732ab8964ab&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 53 → 65 → 67 → 98 → 122 → 124 → 183 → 14 → 37
- 끝에 도착하기 전에 돌아오는 것을 볼 수 있다.

### Disk-Scheduling 알고리즘의 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져있다.
- FIle의 할당 방법에 따라 디스크 요청이 영향을 받는다.
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록
OS와 별도의 모듈로 작성되는 것이 바람직하다.

## Swap-Space Management

- Disk를 사용하는 두 가지 이유로는 다음과 같다.
    - memory의 휘발성 특징 **⇒ File System 으로 해결**
    - 프로그램 실행을 위한 memory 공간 부족 **⇒ Swap-Space(Swap area)로 해결**
- Swap-Space
    - Virtual memory System에서는 디스크를 memory의 연장 공간으로 사용하게 된다.
    - 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적이다.
        - 속도 효율성이 우선이기 때문에, 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
        - 따라서, block의 크기 및 저장 방식이 일반 파일시스템과 다르다

## RAID (Redundant Array of Independent Disks)

- 여러 개의 디스크를 묶어서 사용
- 디스크 처리 속도 향상
    - 여러 디스크에 block의 내용을 분산 저장
    - 병렬적으로 읽어 옴 (Interleaving, striping)
- 신뢰성 향상
    - 동일 정보를 여러 디스크에 중복 저장
    - 하나의 디스크가 고장 시 다른 디스크에서 읽어옴 (Mirroring, shadowing)
    - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다.
