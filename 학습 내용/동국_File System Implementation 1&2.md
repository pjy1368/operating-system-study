## 파일 정보의 저장

### 연속 할당(Contiguous Allocation)

- 파일 정보를 연속해서 할당하여 저장하는 방법이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/486292e5-bf64-4f58-a7f0-3b4165f14c9a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082220Z&X-Amz-Expires=86400&X-Amz-Signature=39ba946d0d3b7fd7317ae522d37f5d212c2abc9ab1df2b1a86422016a465eef3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- tr은 14에서 시작하여 16까지  연속으로 할당, 
mail은 19에서 시작하여 24까지 연속으로 할당되어있다.
- 단점
    - **외부 조각**
        - 위처럼 저장할 경우 17, 18은 길이가 2이하가 아닌 경우에는 할당 할 수 없게 된다.
    - **파일 확장의 어려움**
        - tr 같은 경우는 18까지 확장을 할 수는 있겠으나 그 이상으로는 할당 할 수 없게 된다.
        - 미리 크기를 더 할당해두어 확장의 가능성을 높일 수는 있겠으나,
        그렇게 되면 **내부 조각(할당은 되었지만 사용되지 않음)** 의 문제가 발생하게 된다.
- 장점
    - 빠른 I/O
        - 한번의 seek/rotation으로 많은 바이트를 변환할 수 있게 된다.
        - Deadline이 있는 Realtime 용으로 사용하거나
        이미 실행 중이던 프로세스의 swapping의 목적(빠른 쓰기/읽기)으로 사용하면 좋다.
    - 직접 접근(Direct access)가 가능

### 연결 할당(Linked Allocation)

- 파일 정보를 각각 연결해서 할당하여 저장하는 방식이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/227e614c-908d-4518-8138-70b917470069/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082232Z&X-Amz-Expires=86400&X-Amz-Signature=036e57af5a5c5c5f43070610803fbe1ed451e603eb1d592376665d2885f868b5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 디렉토리에서는 파일의 시작위치만 가지고 있고,
각각의 위치에서 다음위치를 기록되어 있는 것을 볼 수 있다.
- 단점
    - **직접접근이 불가능 함**
        - 순차접근만 가능하다.
    - **reliability 문제**
        - 하나의 섹터가 고장이 나서 다음 위치를 가리키는 포인터가 유실되면 많은 부분을 잃어버리게 된다.
        - 포인터를 위한 공간 때문에 공간 효율성이 떨어진다.
- 장점
    - **외부 조각이 발생하지 않음**

  ⇒ **변형한 방법**

- **File-allocation talbe (FAT)** 파일 시스템
- 포인터를 별도의 위치에 보관하여서 reliability 문제와 공간효율성 문제를 해결한 방법이다.

### 인덱스 할당(Indexed Allocation)

- 인덱스를 통해서 접근하여 저장하는 방식이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/87150f6c-cee1-4189-875d-2701b6cf03c1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082244Z&X-Amz-Expires=86400&X-Amz-Signature=365d47a7c7202b09171561594387408e234e4951beb104b6244789578c626843&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 디렉토리에서는 인덱스 블록의 위치를 알려주고,
그 안에는 각각의 순서에 따른 위치 정보가 기록되어있다.
- 단점
    - 작은 파일의 경우 공간 낭비가 생길 수 있다 (실제로 많은 파일들이 작은 크기이다.)
    - 너무 큰 파일의 경우 하나의 block으로 index 모두를 저장하기에는 부족하다.
        - linked scheme - 마지막에는 다른 인덱스 블록을 연결해 인덱스를 기록한다.
        - multilevel index - 인덱스 하나가 직접적으로 데이터를 가리키지 않고 또 다른 인덱스를 가리키게 함.
        - 위와 같은 방법들로 해결이 가능하다.
- 장점
    - 외부 조각이 발생하지 않음.
    - 직접 접근 가능

## UNIX의 파일시스템 구조

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/003ac186-7ce9-4b57-8ee4-2317c2e499d4/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082305Z&X-Amz-Expires=86400&X-Amz-Signature=61016f321fe4f73600dda7f7c9635c325e9f0b9cc06692095e5ad08ac5857c75&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- **Boot block**
    - 부팅에 필요한 정보를 가지고 있다. (bootstrap loader)
- **Super block**
    - 파일 시스템에 관한 총체적인 정보를 가지고 있다.
    - 어떤 블록이 사용 중이고 아닌지, 어디까지가 Inode 공간이고 Data block 공간인지 등등
- **Inode list**
    - 파일들의 메타데이터를 따로 기록해둔 곳이다.
    - 파일의 이름을 제외한 파일의 모든 메타데이터(파일의 소유주, 접근 시간, 크기 등)을 가지고 있다.
    - 파일의 위치정보 또한 Inode에 기록이 되어있는데,
    크기에 따라서 single indirect, double indirect, triple indirect 까지 사용을 하며,
    매핑 되어있는 인덱스를 따라 가야지 정보에 접근이 가능하다.
- **Data block**
    - 파일의 실제 내용을 보관하고 있다.
    - 파일의 이름과 Inode의 번호를 가지고 있다.

## FAT File System

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f5d05a8e-dad3-4b73-ac1a-b948c1e34269/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082318Z&X-Amz-Expires=86400&X-Amz-Signature=dce6d3fc267a605c26409dffede784f38e87b35cc9ff820f1663ac539aa14e0d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- **Boot block**
- **FAT**
    - 다음 위치를 가리키는 포인터를 저장하고 있다.
    - 다음 위치가 존재하지 않는다면 EOF(End Of File)를 통해서 알려주게 된다.
- **Root directory**
- **Data block**
    - directory 에서 파일의 모든 메타데이터를 가지고 있다.
    - 다음 위치를 가리키는 포인터를 FAT 공간에 따로 저장하여서 보관하게된다.

## 빈 공간 관리 (Free-Space Management)

### Bit map or Bit vector

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/53612d83-61de-415b-9b35-d49964895b30/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082329Z&X-Amz-Expires=86400&X-Amz-Signature=0893c1f0d636a9cb24a788ef9931b22a07babcf2499281c888107ccd973af61f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

bit가 0이라면, 비어있는 공간

bit가 1이라면, 이미 파일에 할당된 공간

- Bit map은 부가적인 공간을 필요로 한다.
- 연속적인 n개의 free block을 찾는데 효과적이다.

### Linked List

- 비어있는 공간들을 링크로 연결해둔다. (= 공간의 낭비가 없다)
- 연속적인 가용공간을 찾는 것은 쉽지가 않기 때문에 쓰기 어렵다.

### Grouping

- linked list 방법의 변형된 방법이다.
- 첫번째 비어있는 공간이 n개의 포인터를 가지고,
n-1 개의 포인터는 빈 공간, 1개는 다시 비어있는 공간을 가리키는 n개의 포인터를 가진다.

### Counting

- 몇개의 빈공간이 있는지 개수를 가지고있어서 해당하는 빈공간을 찾고있는 곳에 할당해준다.

## 디렉토리 구현방법 (Directory Implementation)

### Linear list

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d690dc08-cb4b-4e5a-ac0f-56776cde0882/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082348Z&X-Amz-Expires=86400&X-Amz-Signature=b1c46d7c2daca3f5e24d163b7c5a6f92912263e353d7685fd1c19ed99d192825&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 파일 이름, 파일의 메타데이터에 대한 리스트
- 구현이 간단하지만, 일일히 검색을 해야하기 때문에 비효율적이다.

### Hash Table

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e0108911-56aa-48f4-88c9-09573af5d45a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082418Z&X-Amz-Expires=86400&X-Amz-Signature=a39da18a6823656426998186e8121175ade3c7aa75788fbcd29ce441e77cdbf2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- linear list + hashing 한 방법이다.
- 해쉬 테이블은 파일 이름을 이 파일의 linear list의 위치로 바꾸어준다.
- search time을 없앤다.
- Collision(충돌)이 발생 가능하다.

### File의 metadat의 보관 위치

⇒ 두가지 방법이 존재

- 디렉토리 내에 직접 보관한다.
- 디렉토리에는 포인터를 두고 다른 곳에 보관한다.

### Long file name의 지원

- 파일이름, 파일의 메타데이터의 리스트에서는 각 요소는 일반적으로 고정 크기이다.
- 파일 이름이 고정 크기의 요소 길이보다 길어지는 경우 요소의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두어서 저장하게 된다.
- 이름의 나머지 부분은 동일한 디렉토리 파일의 일부에 존재하게 된다.

## VFS and NFS

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/09767edc-09a4-4de6-b63b-7f019cfbb54c/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082430Z&X-Amz-Expires=86400&X-Amz-Signature=95bf61f780ff533c7f34e2c06dba7e488e9ee94853d287e8dcf7f0f89ba901a0&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

### VFS (Virtual File System)

- 서로 다른 다양한 파일 시스템에 대해서 동일한 시스템 콜 인터페이스 (API)를 통해 접근할 수 있게 해주는 OS의 layer를 뜻한다.

### NFS (Network File System)

- 분산 시스템에서는 네트워크를 통해서 파일이 공유될 수 있다.
- NFS는 분산 환경에서의 대표적인 파일 공유 방법이다.

⇒ **서버에 있는 데이터에 접근하기 위해서는 NFS를 통해 서버의 VFS로 접근해 파일 시스템에 접근하게 되는 것이다.**

## Page Cache and Buffer Cache

### Page Cache

- 메모리 영역의 페이징 시스템에서 사용하는 page frame을 Page Cache 라고 한다.

### Buffer Cache

- 파일 시스템을 통한 I/O 연산은 메모리의 특정 공간인 buffer cache에 저장해두고,
이 파일을 다른 프로세스가 요청하게 되면 디스크로 가지않고 데이터를 주게 된다.

### Unified Buffer Cache

- 최근의 OS에서는 기존의 buffer cache가 page cache에 통합되어 사용된다.
- buffer cache 또한 page 단위로 데이터를 관리하게된다.

### Memory-Mapped I/O

- 파일의 일부를 메모리영역에 매핑 시켜놓고 사용하는 방법이다.
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다.

### 예제

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8fd128b6-e120-4f14-8a54-49b2a97e2d33/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082458Z&X-Amz-Expires=86400&X-Amz-Signature=efec9e1ff5efea3234633a32e997dc6b884aeba15434aac60ce97b0ce69b1d45&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- Unified Buffer Cache를 사용하지 않는 경우
    - read(), write()를 이용하면 시스템콜을 통해 이미 메모리에 올라와 있는 내용은 사용자 프로그램의 주소영역에 Copy를 해주고, 아닌 경우에는 디스크의 file System에서 읽어와 사용자 프로그램에 Copy를 통해 전달해주게 된다.
    - m-mapped를 이용하면 page cache에 올라와 있는 내용은 커널의 도움을 받지 않고 자신의 메모리를 접근하듯이 데이터를 읽고 쓸 수 있게 된다.
- Unified Buffer Cache 의 경우
    - m-mapped를 이용할 시 buffer cache와 Page cache가 따로 존재하는게 아니기에 Page cache에 바로 읽고 쓰고 할 수 있게 된다.

## 프로그램의 실행

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9f18cd17-45d4-4a13-8325-7af01ce27447/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082516Z&X-Amz-Expires=86400&X-Amz-Signature=74373b653bb8d8d1a0f8f732e3fba0b586fcf8505dfd8e8ff1465fb3356e0c3d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 파일 시스템에서 실행 파일을 실행 시키면 프로세스가 된다.
- 프로세스는 독자적인 주소공간 (stack, data, code)을 가지게 된다. (= virtual memory)
- 주소변환을 통해서 당장 필요한 부분은 물리적 메모리에 올라가게 된다.
- 물리적 메모리에는 한계가 있기 때문에 당장 사용하지 않는 부분은 swap area에 올라가게 된다.
    - 여기서 code 부분은 swap area로 쫒겨날 때에 파일 시스템에 file 형태로 존재하기 때문에,
    따로 swap area에 저장되지 않는다.

### Memory Mapped I/O

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/76b605e2-aa79-46b6-8f3f-9a874be704fb/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220116%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220116T082532Z&X-Amz-Expires=86400&X-Amz-Signature=ce232f49b684cc254ade78b3d3303940ea21d0283e508be9eeef7108c9be8c5f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 데이터 파일의 일부를 주소공간의 일부에 매핑해두게 된다.
- 프로그램이 실행 된 후 메모리 위치에 접근 하였을 때,
찾는 page가 안 올라와 있다면 page fault가 발생하게된다.
- CPU가 OS에 넘어가고 page를 물리적인 메모리에 올려 놓게 된다.
- 이후에는 가상 메모리에 매핑되어있는 페이지에 접근할 시에는 OS의 도움을 받지 않고 바로 물리적인 메모리에 읽고 쓸 수 있게 되는 것이다.

  ⇒ Swap area로 쫒겨나게 될 경우

Memory Mapped 된 page이기 때문에 파일에 수정된 내용을 써주고 쫒겨나게 될 것 이다.

 

*** **다른 프로세스가 해당 page를 사용하게 될 경우**

- 물리적 메모리에 올라가 있는 page를 프로세스에 Copy하여 사용할 수 있게된다.
