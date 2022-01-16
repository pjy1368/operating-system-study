## 디스크에 파일의 데이터를 할당하는 방식

파일은 크기가 균일하지 않다는 특징이 있다. 그래서 파일을 동일한 크기 단위로 sector로 나누어 저장을 한다. 디스크에 파일을 저장하는 방법은 크게 3가지가 존재하는데, 메모리 관리 기법 중 페이징 기법과 유사하다.

- 연속 할당
- 연결 할당
- 인덱스를 이용한 할당

### 연속 할당

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2b34c3f2-7361-490c-b8f2-38932d6b0237%2FUntitled.png?table=block&id=ebe65865-f791-4ede-a8d0-9bb35413b97c&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 하나의 파일이 디스크 상에 연속해서 저장되는 방식으로, 나누어진 각 블록들이 연속된 번호를 부여 받아 저장된다.
- 장점
    - 빠른 입출력
        - 대부분의 접근 시간은 헤더가 움직이면서 읽어 들이는 시간이다.
        - 한 번의 seek/rotation으로 많은 바이트를 한 번에 transfer할 수 있다. (모두 연속해서 붙어 있으므로 추가적인 탐색 비용이 소요되지 않음)
        - 따라서 Realtime 파일 또는 이미 run 중이던 프로세스의 swapping 용도로 사용한다.
    - 임의 접근이 가능하다.
- 단점
    - 외부 단편화가 발생한다.
    - 파일 grow가 어렵다.
        - 파일의 크기는 유동적이다. 이때 파일의 크기가 커질 때 제약 사항이 발생한다.
        - 파일 생성 시 얼마나 큰 hole을 배당할 것인가?
        - grow 정도를 크게 할수록 내부 단편화 문제가 커진다.
    

### 연결 할당

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fcee95319-f780-4e5a-a143-5bf7cd0257a4%2FUntitled.png?table=block&id=ff9876bd-170d-4415-a7e1-6f5ef6a77488&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- sector들이 각각 node가 되어 Linked List 구조를 취하면서 파일을 저장한다.
- 장점
    - 외부 단편화가 발생하지 않는다.
- 단점
    - 첫 요소부터 차례대로 읽어야 하므로 임의 접근이 불가능하다.
    - 신뢰성 문제
        - 한 sector가 고장나 pointer가 유실되면 그 이후 모든 sector로 접근할 수 없다.
    - 포인터를 위한 공간이 필요하므로 공간 효율성을 떨어뜨리게 된다.
- 변형
    - File-allocation table (FAT) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결한다.

### 인덱스를 이용한 할당

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F630677e8-7b99-4ca9-b57e-a5758a0a8701%2FUntitled.png?table=block&id=70ecbc00-1cbf-4340-bef4-830278b9e540&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 파일이 어디에 나눠져 있는지 인덱스를 적어 두는 블록 하나를 활용하고, 이를 인덱스 블록이라 부른다.
- 장점
    - 외부 단편화가 발생하지 않는다.
    - 임의 접근이 가능하다.
- 단점
    - 작은 파일의 경우 공간 낭비가 심하며, 실제로 많은 파일들이 사이즈가 작다.
    - 매우 큰 파일의 경우 하나의 인덱스 블록으로 커버할 수 없다.
        - 해결 방안: linked scheme, multi-level index
        - 전자는 index block을 여러 개 두는 것이고, 후자는 블록의 마지막에 다음 index 블록을 가리키는 값을 설정하여 서로 연결하는 것이다.

## UNIX 파일 시스템의 구조

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc74624e8-43fc-404f-a73c-c95961c862bf%2FUntitled.png?table=block&id=8a83ff6a-46bf-4b9f-a5ea-ee7a191f96db&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 유닉스 파일 시스템의 중요 개념
    - Boot block
        - 부팅에 필요한 정보를 담고 있는 블록
        - 모든 파일 시스템에 존재하는 블록
    - Super block
        - 파일 시스템에 관한 총체적인 정보를 담고 있는 블록
        - 어느 부분이 비어 있는 블록인지, 어느 부분이 사용 중인 블록인지, 어디부터가 Inode 블록인지 Data 블록인지 등을 알려 주는 정보를 가지고 있다.
    - Inode list
        - 파일 이름을 제외한 파일의 모든 메타 데이터를 따로 저장한다.
        - 파일 하나 당 Inode가 하나씩 할당이 되고, 해당 Inode는 그 파일의 메타 데이터를 갖고 있다. 이때 파일의 이름은 디렉토리가 가지고 있는데, 디렉토리는 파일의 이름과 Inode 번호를 저장하고 있다. (이전에 디렉토리가 파일의 메타 데이터를 가지고 있다고 했는데, 유닉스 파일 시스템의 디렉토리는 파일의 이름 및 파일의 대부분의 메타 데이터를 저장하는 Inode의 번호를 가지고 있다.)
        - direct blocks는 파일이 존재하는 인덱스를 저장하는 인덱스 블록이다. 파일의 크기가 크지 않다면 이 블록을 이용하여 파일을 접근할 수 있다.
        - direct blocks으로 커버할 수 있는 크기보다 저장 용량이 큰 파일은 single indirect를 통해서 하나의 level을 두어서 저장하는 방식을 취하고, 그보다 더 큰 파일은 double indirect, 더 큰 파일은 triple indirect 방식을 취한다.
    - Data block
        - 파일의 실제 내용을 보관하는 블록
        - 이 중 디렉토리 파일은 자신의 디렉토리에 속한 파일들의 이름과 Inode 번호를 가지고 있다.

## FAT 파일 시스템

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F308b27a2-2bd9-4065-a325-c8e73e07ceba%2FUntitled.png?table=block&id=4faaab53-a997-4e3b-b45d-ed6118aa9fec&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- FAT 파일 시스템은 윈도우즈 계열에서 주로 사용된다.
- 파일의 메타 데이터의 일부(위치 정보)를 FAT에 저장하고, 나머지 정보는 디렉토리가 가지고 있다. (파일 이름, 접근 권한, 소유주, 파일의 첫 번째 위치 등)
- 위 사진에서 217번이 첫 번째 블록인데, 다음 블록의 위치를 FAT에 별도로 관리한다. FAT 테이블 전체를 메모리에 올려 놓았으므로 연결 할당의 단점(임의 접근 불가, 신뢰성 문제, 공간 효율성 문제)을 전부 극복하였다. 참고로 FAT는 중요한 정보이므로 복제본을 만들어 두어야 한다.

## 빈 공간 관리

- sector가 할당되고 나서 발생하는 hole을 어떻게 관리할 것인가?
- Linked list
    - 모든 free 블록을 링크로 연결 (free list)
    - 연속적인 가용 공간을 찾기 어렵다.
    - 공간의 낭비가 없다.
- Grouping
    - Linked list의 변형
    - 첫 번째 free 블록이 n 개의 포인터를 갖는다.
        - 하나의 free 블록에 나머지 free 블록에 대한 위치 정보를 저장하는 방식
- Counting
    - 프로그램들이 종종 여러 개의 연속적인 블록을 할당하고 반납한다는 성질에 착안하였다.
- Bit map or Bit vector
    - 0이면 비어 있는 값이고, 1이면 sector 저장된 공간이다.
    - 연속된 n개의 free 블록을 찾기 효과적이지만, 0 또는 1을 저장할 부가적인 공간을 필요로 한다.

## 디렉토리 구현

- 선형 리스트
    - <file name, file의 메타 데이터>의 리스트
    - 구현이 간단하다.
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 선형 탐색이 필요하다. (O(N))
- 해시 테이블
    - 선형 리스트 + 해싱
    - 해시 테이블은 file name을 이 파일의 선형 리스트의 위치로 바꾸어 준다.
    - 탐색 시간이 O(1)이다.
    - 해시 충돌이 발생할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa1e4bf01-7712-4a62-9ca9-a8281e576bee%2FUntitled.png?table=block&id=d0a87e0e-6294-4320-bd31-cca970c101a6&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 파일의 메타 데이터의 보관 위치
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - Inode, FAT 등
- Long file name의 지원
    - <file name, file의 메타 데이터>의 리스트에서 각 엔트리는 일반적으로 고정 크기이다.
    - 하지만 file name이 고정 크기의 엔트리 길이보다 길어지는 경우 엔트리의 마지막 부분에 file name의 뒷 부분이 위치한 곳의 포인터를 둔다.
    - file 이름의 나머지 부분은 동일한 directory file의 일부에 존재한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7dcc7d46-8356-4da2-bdcd-66fee6f1a80c%2FUntitled.png?table=block&id=dccb9466-40c7-4bde-a087-e6e67b187e83&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## VFS와 NFS

- Virtual File System (VFS)
    - 서로 다른 다양한 파일 시스템에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해 주는 OS의 레이어
- Network File System (NFS)
    - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있다.
    - NFS는 분산 환경에서 대표적인 파일 공유 방법이다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F30374ad9-5710-4826-8448-f7740bcbd819%2FUntitled.png?table=block&id=e50d5330-6010-4e5c-baee-0e5d2ae740e2&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 어떤 파일 시스템을 쓰든 상관 없이 VFS 인터페이스를 사용한다.
- 분산 시스템에서는 네트워크를 통해 파일을 공유하기 위해 NFS 클라이언트와 NFS 서버가 이용된다.

## 페이지 캐시와 버퍼 캐시

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F28056191-a440-4595-91f9-5d5db2074c02%2FUntitled.png?table=block&id=a10e50d8-70b5-49b5-b291-2e1ffe773b95&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 페이지 캐시
    - 가상 메모리의 페이징 시스템에서 사용하는 페이지 프레임을 캐싱의 관점에서 설명하는 용어
    - Memory-Mapped I/O를 쓰는 경우 파일의 I/O에서도 페이지 캐시를 사용한다.
- Memory-Mapped I/O
    - 파일의 일부를 가상 메모리에 매핑한다.
    - 매핑한 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다.
- 버퍼 캐시
    - 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 버퍼 캐시를 사용한다.
    - 파일 사용의 지역성 활용
        - 한 번 읽어 온 블록에 대한 후속 요청 시, 버퍼 캐시에서 즉시 전달
    - 모든 프로세스가 공용으로 사용
    - 교체 알고리즘 필요 (LRU, LFU 등)
- 통합 버퍼 캐시
    - 최근의 OS에서는 기존의 버퍼 캐시가 페이지 캐시에 통합됨.

### 통합 버퍼 캐시를 사용하지 않을 때와 사용할 때의 차이

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1598e970-3d0c-468c-a555-ce8122c07b46%2FUntitled.png?table=block&id=7273980f-1b59-4bb1-932e-812fc1f5d2cd&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

## 프로그램의 실행

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd81841f0-39ee-48ea-b26e-05e33c9f7517%2FUntitled.png?table=block&id=b3f8e0bc-54f5-4e7c-8f6d-5473e71380c5&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 프로그램이 실행되면 실행 파일이 프로세스가 되며, 프로세스만의 독자적인 주소 공간이 만들어 진다.
- 이 공간은 코드, 데이터, 스택으로 구분되며 당장 사용될 부분은 물리적 메모리에 올라가고, 당장 사용되지 않는 부분은 스왑 영역으로 내려간다.
- 이때 코드 부분은 이미 파일 시스템에 있기 때문에 스왑 영역에 내리지 않고, 필요 없으면 물리적 메모리에서 지운다. 나중에 필요하면 파일 시스템에서 가져오면 된다.

### Memory Mapped I/O 수행

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F672038d7-c741-482f-8855-9d0d11c2cdfe%2FUntitled.png?table=block&id=66072938-79fc-4652-ae69-b7adedbde664&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 프로세스가 일반 데이터 파일을 I/O하고 싶을 수 있다.
- 이때 `mmap()` 을 호출하면 Memory Mapped I/O 방식으로 파일을 I/O할 수 있고, `mmap()` 은 시스템 콜이므로 운영 체제에 CPU의 제어권이 넘어간다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3811817d-cb5e-4ae3-9b01-85d98ab2830a%2FUntitled.png?table=block&id=937a03e7-6927-4326-bce7-2b1022e49d3f&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 운영 체제는 데이터 파일의 일부를 프로세스 주소 공간에 매핑을 한다.
- 만약 데이터 파일이 매핑된 영역을 접근했을 때, 실제 물리적인 메모리에 페이지가 올라와 있지 않다면 페이지 부재가 발생한다. 그렇지 않으면 가상 메모리의 매핑된 영역은 물리적 메모리의 페이지 프레임과 일치가 되므로 프로세스가 데이터 파일에 대해 I/O를 하고 싶을 때 운영 체제의 도움 없이 독자적으로 I/O를 수행할 수 있다.
- 물리적 메모리에 올라간 데이터 파일과 매핑된 페이지 프레임을 쫓아내야 할 때는 스왑 영역으로 내리는 것이 아니라 수정된 사항을 파일 시스템에 적용하고 물리적 메모리에서 지운다.
- 현재 프로세스 B가 데이터 파일에 대해 Memory Mapped I/O를 수행하여 물리적 메모리에 페이지 프레임을 올려 두었는데, 프로세스 A도 이 페이지 프레임을 공유하여 사용할 수 있다.

### `read()` 수행

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F53a11257-e564-444f-8113-6d9463c6c75b%2FUntitled.png?table=block&id=3da271bd-c373-4409-8576-b71ce4dd6d04&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 프로세스가 일반 데이터 파일을 I/O를 하는 방법으로 `read()` 시스템 콜을 호출할 수도 있다.
- `read()` 시스템 콜을 호출하면 메모리의 버퍼 캐시를 확인해야 하는데, 통합 버퍼 캐시 환경에서는 페이지 캐시가 버퍼 캐시 역할을 동시에 수행한다. 그래서 Memory Mapped I/O로 올라 간 페이지 캐시를 버퍼 캐시로 사용할 수 있다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe2f83030-54b7-4f04-8276-3d7cdc49bc01%2FUntitled.png?table=block&id=0ed4b982-8052-4723-8b27-fd6073239b93&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 운영 체제는 버퍼 캐시에 있던 내용을 복사하여 프로세스의 주소 공간에 할당한다.

### Memory Mapped I/O vs `read()`

- Memory Mapped I/O
    - 가상 메모리에 올라 온 영역이 곧 파일이므로 시스템 콜 없이 I/O 작업을 할 수 있다.
    - 페이지 캐시에 있는 내용을 복사할 필요가 없다.
    - 여러 프로세스가 `mmap()` 을 사용하여 같은 영역을 공유하여 사용하면 일관성 문제가 발생할 수 있다.
- `read()`
    - 매번 운영 체제의 중재를 받는다.
    - 페이지 캐시에 있는 내용을 복사해야 한다.
    - 여러 프로세스가 `read()` 를 사용해도 일관성 문제가 발생하지 않는다.

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)
- [https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)
- [https://asfirstalways.tistory.com/143?category=664092](https://asfirstalways.tistory.com/143?category=664092)
- [https://asfirstalways.tistory.com/145?category=664092](https://asfirstalways.tistory.com/145?category=664092)
- [https://asfirstalways.tistory.com/147?category=664092](https://asfirstalways.tistory.com/147?category=664092)
