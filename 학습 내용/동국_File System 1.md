## File and File System

### File

- “관련 정보를 이름을 가지고 저장 해둔 것”
- 일반적으로는 비휘발성의 보조기억장치에 저장하게된다.
- OS는 다양한 저장장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해준다.
- **연산**
    - create, delete, read, write
    - reposition (lseek) - 파일의 시작부분, 현재 접근하고 있는 위치를 수정하는 연산
    - **open**, close - 파일의 메타데이터를 메모리로 올려두거나 내리는 연산

### File attribute (= metadata)

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들을 뜻한다.
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

### File System

- OS에서 파일을 관리하는 부분을 뜻한다.
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리한다.
- 저장 방법을 결정하며 보호하는 역할 또한 한다.

### Directory

- 파일의 메타데이터 중 일부를 보관하는 있는 일종의 특별한 파일
    
    ⇒ 메타데이터를 통해 디렉토리 아래에 있는 파일이 어떤 건지 표시함.
    
- 그 디렉토리에 속한 파일 이름 및 파일 attribute 들
- **연산**
    - 파일 탐색, 파일 생성, 파일 제거
    - 파일 이름변경, 파일 시스템 전체 탐색

### Partition(=Logical Disk)

- 하나의 물리적 디스크 안에 여러 파티션을 두는 것이 일반적이지만,
여러개를 하나의 파티션으로도 사용 할 수 있다.
- File System 용도, Swap Area 용도 등으로 사용할 수 있다.

## 추가 설명

### open() - 파일의 메타데이터를 메모리에 올리는 작업

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c20d982a-bc67-4ff2-9c3f-9aee362ed139/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082105Z&X-Amz-Expires=86400&X-Amz-Signature=bdec4bff3bb1373a4850a48eefe933fc5907e5e881b7079a55ff48bd3cec2281&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- **fd = open(”/a/b”)**
    - 기본적으로 OS는 root 디렉토리의 메타데이터를 가지고 있다.
    - 이를 메모리에 올려서 root의 내용을 탐색해 a의 메타데이터를 찾을 수 있게 된다.
    - a의 메타데이터를 메모리에 올려서 내용을 탐색해 a 아래에 있는 b의 메타데이터를 얻어올 수 있게 된다.
    - 이후 b의 메타데이터를 가리키는 포인터가 PCB 배열 안에 인덱스로 들어가게 되고,
    인덱스 값이 b 파일의 file descriptor가 되게 된다.
    - 그 값을 리턴하여 프로세스에서 open 해 사용하게 되는 것이다.
- **read(fd...)**
    - 얻어온 b의 메타데이터를 통해서 원하는 데이터를 읽어온 후,
    이를 OS의 메모리 공간에 올린 후 프로세스에게 전달해 주게 된다.
- **buffer cashing**
    - 만약 다음 작업이 똑같은 정보를 읽어오는 작업이라면 OS가 따로 개입하지 않고,
    위에서 OS가 메모리 공간에 올린 정보를 읽어와 프로세스에서 바로 사용하게 된다.
    

### 파일 보호 (File Protection)

- 각 파일에 대해 누구에게 어떤 유형의 접근(rwx)를 허락할 것인가?
1. **Access control Matrix**
    - ACL(Access Control List) - 파일별로 누구에게 어떤 접근 권한이 있는지 표시한다.
    - Capability - 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한을 표시한다.
2. **Grouping**
    - 전체 user를 owner, group, public으로 구분하여,
    각 파일에 대해 세 그룹의 접근 권한을 3비트 표시하게 된다.
    - Ex) UNIX rwx(owner) rwx(group) rwx(public) (777) 같은 느낌
    
     **⇒ 일반적으로 사용하는 방법이다.**
    
3. **Password**
    - 파일마다 비밀번호를 두는 방법이다.
    - 모든 접근 권한에 대해 하나의 비밀번호를 두는 것이다.
    - 암기하는데 어렵고 관리를 잘못하면 위험할 수도 있다는 단점이 있다.
    

## File System의 Mounting

- 서로 다른 파티션에 설치되어있는 파일 시스템에 접근해야 된다면 Mount를 이용할 수 있다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e49e65c8-d09e-4640-aa74-31923f8a4f12/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082044Z&X-Amz-Expires=86400&X-Amz-Signature=a5f7e70e3eeab067f83e0adb0f25956ca4ebf408f247d7345bb6201bfae52804&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 디스크 1의 usr 디렉토리에 Mount 하여 아래에 이어서 디스크 3에 내용에 접근할 수 있게 된다.

## Access Methods

- 시스템이 제공하는 파일 정보의 접근 방식이다.
- **순차 접근 (sequential access)**
    - 읽거나 쓰게되면 offset은 자동적으로 증가하게 된다. (순차적으로만 접근가능)
    - Ex) 카세트테이프
- **직접 접근 (direct access, random access)**
    - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있다.
    - Ex) LP 레코드 판
