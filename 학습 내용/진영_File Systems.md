## 파일과 파일 시스템

- 파일
    - A named collection of related information
    - 일반적으로 비휘발성의 보조 기억 장치에 저장
    - 운영 체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해 줌
    - 연산자
        - create, read, write, reposition(lseek), delete, open, close 등
        - reposition: 위치를 변경 및 저장하는 연산자
        - open: 메타 데이터를 메모리에 올리는 연산
        - 연산자는 모두 시스템 콜이다.
- 파일의 속성 (혹은 메타 데이터)
    - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보
        - 파일 이름, 유형, 저장된 위치, 파일 사이즈
        - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등
- 파일 시스템
    - 운영 체제에서 파일을 관리하는 부분
    - 파일 및 파일의 메타 데이터, 디렉토리 정보 등을 관리
    - 파일의 저장 방법 결정
    - 파일 보호 등
    

## 디렉토리와 논리적 디스크

- 디렉토리
    - 파일의 메타 데이터 중 일부를 보관하고 있는 일종의 특별한 파일
    - 그 디렉토리에 속한 파일 이름 및 파일의 속성
    - 연산자
        - search for a file, create a file, delete a file
        - list a directory, rename a file, traverse the file system
- 파티션 (=논리적 디스크)
    - 하나의 물리적 디스크 안에 여러 파티션을 두는 것이 일반적임.
    - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함.
    - 물리적 디스크를 파티션으로 구성한 뒤 각각의 파티션에 파일 시스템을 깔거나 스와핑 등 다른 용도로 사용할 수 있음.

## `Open()`

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3a8437a0-414d-47aa-b35c-d6b1839af933%2FUntitled.png?table=block&id=327e18fd-dafd-4b36-b49f-503b2ba1582a&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- open(”/a/b/c”)
    - 디스크로부터 파일 c의 메타 데이터를 메모리로 가지고 옴
    - 이를 위하여 directory path를 탐색함
        - 루트 디렉토리 “/”를 open하고 그 안에서 파일 위치 “a”의 위치를 획득
        - 파일 “a”를 open한 후 read하여 그 안에서 파일 “b”의 위치를 획득
        - 파일 “b”를 open한 후 read하여 그 안에서 파일 “c”의 위치를 획득
        - 파일 “c”를 open한다.
    - Directory path의 탐색에 너무 많은 소요 시간이 걸린다.
        - 그래서 open을 read/write와 별도로 둔다.
        - 한 번 open한 파일은 read / write시 directory 탐색이 불필요하다.
    - Open file table
        - 현재 open된 파일의 메타 데이터 보관소이다. (in memory)
        - 디스크의 메타 데이터보다 몇 가지 정보가 추가된다.
            - open한 프로세스의 수
            - file offset: 파일 어느 위치를 접근 중인지 표시 (별도의 테이블 필요)
    - 파일 descriptor
        - Open file table에 대한 위치 정보 (프로세스 별)

### `Open()` 동작 과정

- open(”/a/b”)을 호출해 보자.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F621b4a32-cfd2-416f-9d99-68130cf05fa9%2FUntitled.png?table=block&id=5e7bea00-f085-422e-873c-ced93ae672b0&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

1. 디스크에 있던 root의 메타 데이터가 메모리로 올라간다.
2. root의 주소를 찾아가고, root에 존재하는 a의 메타 데이터에 접근한다.
3. a의 메타 데이터를 메모리에 올린다. (Open file table)
4. a의 주소를 찾아가고, a에 존재하는 b의 메타 데이터에 접근한다.
5. b의 메타 데이터를 메모리에 올린다. (Open file table)
6. 프로세스가 `open()` 시스템 콜의 반환 값을 전달 받는다.
    1. b의 메타 데이터가 위치하는 포인터 값 (file descriptor) ⇒ Open file table의 인덱스 값
7. `read()` 시스템 콜이 발생하면, fd 값을 이용하여 b의 컨텐츠를 읽고, 그 값을 디스크 상의 버퍼 캐시에 저장한다.
8. b의 컨텐츠를 커널 메모리 상의 버퍼 캐시에 저장한다.
9. b의 컨텐츨를 사용자 메모리 상의 버퍼 캐시에 접근한다.

file system에서는 LRU, LFU와 같은 알고리즘을 사용한 버퍼 캐싱이 가능하다. 버퍼 안에 데이터가 있든 없든, 사용자 프로세스가 open, read, write 시스템 콜을 호출하여 CPU의 제어권이 운영 체제로 넘어오기 때문이다.

## 파일 보호

- 각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?
- Access Control 방법
    - Access Control Matrix
        
        ![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fafcc00b9-5b69-4d47-a115-53ea06931e39%2FUntitled.png?table=block&id=9e008a5a-d6fe-4ca8-9b9a-393b7e8e7588&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)
        
        - Access Control List: 파일 별로 누구에게 어떤 접근 권한이 있는지 표시
        - Capability: 사용자 별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시
    - Grouping
        - 전체 user를 owner, group, public의 세 그룹으로 구분
        - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
        - UNIX에서 채택하는 방식
    - Password
        - 파일마다 패스워드를 두는 방법
        - 접근 권한 별 패스워드에 대한 암기 문제, 관리 문제가 발생함.

## 파일 시스템의 마운팅

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8d2e082b-dc57-4c08-8010-96e8044219a7%2FUntitled.png?table=block&id=a55a8850-44da-42e2-8121-a4bcdbacfc22&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 각각의 논리적인 디스크에는 파일 시스템을 설치할 수 있다.
- 루트 파일 시스템 내에 모든 파일을 접근이 가능하다. 하지만, 다른 논리적 디스크 내의 파일 시스템에 접근하고 싶다면 어떻게 할까?

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9f6e1ebd-93df-488c-bd1c-87d3b9a504eb%2FUntitled.png?table=block&id=bffb4756-9c0f-4297-aa7c-008770a75a04&spaceId=b453bd85-cb15-44b5-bf2e-580aeda8074e&width=2000&userId=80352c12-65a4-4562-9a36-2179ed0dfffb&cache=v2)

- 마운팅을 통해 서로 다른 파티션에 있는 파일 시스템에 접근할 수 있다.

## 파일 정보의 접근 방식

- 시스템이 제공하는 파일 정보의 접근 방식
    - 순차 접근
        - 카세트 테이프를 사용하는 방식처럼 접근
        - 읽거나 쓰면 offset은 자동으로 증가
    - 직접 접근
        - LP 레코드 판과 같이 접근
        - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음

## 출처

- [https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)
- [https://asfirstalways.tistory.com/142](https://asfirstalways.tistory.com/142)
