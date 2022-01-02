## 교착상태(deadlock)

- 일련의 프로세스들이 서로가 가진 **자원**을 기다리며 block된 상태
    
    *** **Resource(자원) - 하드웨어, 소프트웨어 등을 포함하는 개념**
    
    ⇒ Request, Allocate, Use, Release (프로세스의 자원 사용절차)
    

### 발생의 4가지 조건

1. **Mutual exclusion** (상호배제)
    
    ⇒ 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
    
2. **No preemption** (비선점)
    
    ⇒ 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
    
3. **Hold and wait** (보유대기)
    
    ⇒ 자원을 가진 프로세스사 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음
    
4. **Circular wait** (순환대기)
    
    ⇒ 자원을 기다리는 프로세스간에 사이클이 형성되어야 함
    

### Resource-Allocation Graph (자원할당그래프)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e5aa8dd-fe30-4335-9f24-a078828dd0e6/Untitled.png)

프로세스 1이 자원1을 요청하고 있고,
프로세스 2가 자원1을 가지고 있는 상태이다.

프로세스 2는 또한 자원 3을 요청하고 있고,
자원 3은 프로세스 3이 가지고 있다,

⇒  이런 상황이 데드락 일까??

- 그래프에 cycle이 없으면 deadlock이 아니다.
- 그렇다면 그래프에 cycle이 있으면 deadlock ??

아래 예제를 통해 살펴보도록 하자.

1. **첫번째 예제**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6e2ef04f-4ec6-4472-8a39-f78e98a31643/Untitled.png)

- 1번 프로세스
    - 2번 자원을 가지고 있는 상태로, 1번 자원을 요청하였다.
- 2번 프로세스
    - 1번 자원과 2번 자원을 가지고 있는 상태로, 3번 자원을 요청하였다.
- 3번 프로세스
    - 3번 자원을 가지고있는 상태로 2번자원을 요청하였다.

⇒ 각 요청에 해당하는 자원들이 모두 사용중인 상태이고, 

    해당하는 자원들에 대해서 모두 사용할 수 없는 사이클이 만들어져 있기에 deadlock 상태이다.

1. **두번째 예제**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/54846524-2b7a-414a-b1c4-7c5f206eb8b0/Untitled.png)

- 1번 프로세스
    - 2번 자원을 가진 상태로, 1번 자원을 요청하고 있다.
- 2번 프로세스
    - 1번 자원을 가지고 있다.
- 3번 프로세스
    - 1번 자원을 가진 상태로, 2번 자원을 가지고 있다.
- 4번 프로세스
    - 2번 자원을 가지고 있다.

⇒ 각 요청에 해당하는 자원들이 모두 사용중인 상태이지만, 
    해당하는 자원들에 대해서 사이클이 있지만 사이클이 없어질 수 있는 기회가 있기 
    때문에 deadlock 상태가 아니다.

(얼핏보면 deadlock 처럼 보일 수도 있겠지만, 프로세스 2와 4가 작업이 끝나게 되면 요청을 한 프로세스가 자원을 사용할 수 있게 되기 때문에 deadlock이 아닌 것이다.)

### Deadlock의 처리 방법

- **Deadlock Prevention**
    
    ⇒ 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
    
    - Mutual exclusion
        - 공유해서는 안되는 자원의 경우 반드시 성립해야 한다.
    - Hold and wait
        - 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
            
            방법 1. 프로세스 시작 시 모든 필요한 자원을 할당받게 한다.
            방법 2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청 한다.
            
    - No preemption
        - 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점된다.
        - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
        - 상태를 쉽게 저장하고 불러올 수 있는 자원에서 주로 사용한다.
    - Circular wait
        - 모든 자원 유형에 할당 순서를 정하여서 정해진 순서대로만 자원을 할당한다.
    
    ⇒ 생길지 안생길지 모르는 Deadlock을 막을려고 사전에 많은 제약을 걸어두기 때문에,
        **이용률 저하, 시스템 성능 감소, Starvation 등의 문제가 발생할 수 있다.**
    
- **Deadlock Avoidance**
    - 자원 요청에 대한 부가적인 정보를 이용해 deadlock의 가능성이 없는 경우에만 자원을 할당
    (시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원을 할당함)
    - **safe state** - 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
    - **safe sequence** - 프로세스의 sequence가 safe가 되려면,
    (Banker’s Algorithm) = “**가용자원 + 모든 프로세스의 보유자원**”에 의해 충족되어야 한다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d72728e7-5b82-49b9-8101-d6bc0e8a82b0/Untitled.png)
    
    점선 (Claim edge) → 프로세스가 자원을 미래에 요청할 수 있음을 뜻한다.
    
    실선 (Request edge) → 프로세스가 해당 자원 요청 시 실선으로 바뀐다.
    
    - **Deadlock Avoidance는 deadlock의 위험성이 있으면 자원을 할당하지 않는다.**
    
    ⇒ 위 사진에서 3번째 그림에서는 deadlock의 위험성이 있기 때문에 자원이 할당되지 않는다.
    

*** **Banker’s Algorithm**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67e16058-0e3c-485d-9aaf-ac0f974b9e19/Untitled.png)

현재 사용할 수 있는 자원과 필요로 하는 자원들을 계산하여, deadlock의 위험성이 있는지 검증한다.

→ 1번 프로세스는 B 자원 2개를 요청했을 때,
    추가 요청을 했을 때를 감안해도 자원이 충분하기 때문에, 할당이 가능하다.

→ 0번 프로세스는 B자원 1개를 요청해도,

    추가 요청을 했을 때 현재 사용 가능한 자원이 부족하기 때문에, 할당을 해주지 않는다.

⇒ 언젠가 가용자원이 생기면 할당을 해주겠지만, 위 같은 형식으로 처리하는 알고리즘 이다.

⇒ **이 또한, Deadlock을 막을려고 사전에 많은 제한을 걸게 되므로 비효율적이다.**

- **Deadlock Detection and recovery**
    
    ⇒ Deadlock이 발생할 때 까지 두었다가, 발생한 후에 조치를 취한다.
    
    - Deadlock Detection
        - 자원 당 인스턴스가 하나인 경우
        ⇒ 자원할당 그래프에서의 cycle이 곧 deadlock을 의미한다.
    - Deadlock recovery
        - Process termination
        ⇒  방법 1. deadlock에 연관된 모든 프로세스를 죽인다.
              방법 2.  deadlock의 연관된 프로세스를 하나씩 죽인다.
        - Resource Preemption
        ⇒ 비용을 최소화할 희생양을 선정하여, 그 프로세스로 부터 자원을 뺏어버린다.
    
- **Deadlock Ignorance**
    
    ⇒ Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않는다.
    
    **Deadlock이 매우 드물게 발생하기 때문에, 그에 대한 조치 자체가 부담이 될 수 있다.
    사용자가 알아서 느끼면서 해결하는 방법이며, 대부분의 범용 OS가 채택한 방법이다.**
