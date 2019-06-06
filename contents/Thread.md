# Thread
**:book: Contents**

* [Thread](#Thread)

---

### Thread

- **Thread란**

  - "A thread(or lightweight process) is a basic unit of CPU utilization"

  - 스레드란 cpu의 실행 단위 입니다.

  - cpu의 구성은

    <독립적으로 스레드마다 가지는것>

    - program counter
    - register set
    - stack space

    <스레드끼리 공유하는것>

    - code section

    - data section(초기화된 데이터)

    - bss section(초기화 되지 않은 데이터)

    - OS resources

```
cf)process와 thread의 차이
<Process>
-process란 OS로부터 실행에 필요한 자원(메모리)을 할당받아 process가 된다.
-process는 데이터,메모리등의 자원, thread로 구성

<thread>
-process에게 할당된 자원을 이용해서 실제로 작업을 수행하는 것
-모든 process에는 최소한 하나 이상의 thread가 존재하며, 둘 이상의 thread를 가진 process를 
 multi-threaded process라 한다.
 
```



​      

- **Thread를 왜 등장하게 되었는가?**

  
<div>
  <img src="https://t1.daumcdn.net/cfile/tistory/99CD323A5A5644C123" width="70%">
</div>

```
process를 만들어지면 운영체제의 메모리 공간 data section에 PCB(Process Control Block) 자료구조가 만들어진다. 만약, 동일한 기능을 하는 process를 여러개 실행하면 운영체제의 data section에 process 수 만큼 PCB가 만들어지기 때문에 이는 결국 메모리 낭비를 가져온다. 이를 효율적으로 개선한것이 스레드이다.
같은 기능을 하는 프로세스를 여러개를 띄어놓을 때 다음과 같은 문제가 발생한다.
=>각 Process마다 os의 data section 공간을 차지

그렇다면 같은 기능을 하는 여러 프로세스를 하나의 process로 실행되게 하려면 어떠한 부분이 해결되어야 하는가(그러면서 메모리 효율은 좋게)?
1.PCB를 하나의 Register(pc,registers)공간에 각각 thread정보를 저장
2.각각 pcoess마다 실행부분이 다르기 때문에 stack 영역은 스레드별로 나눈다.
3.data,code,os의 자원 영역은 스레드간 공유한다.

이처럼 스레드를 사용하면 메모리 효율성 문제를 해결할 수 있다.

```



- **스레드의 장점**

  <div>
    <img src="https://t1.daumcdn.net/cfile/tistory/993B51475A56452433" width="70%">
  </div>

  - Responsiveness(응답성)

    ```
    Thread를 사용하게 되면 하나의  thread흐름이 I/O작업때문에 blocked(waiting) 상태가 되었을 때 동일한 task내의 다른 Thread가 running되어 클라이언트에게 빠른 응답성을 제공한다.
    
    웹서버에서 html문서(다수의 이미지 포함)를 받아서 랜더링을 하는 상황을 가정해보자!!
    <single thread인 경우>
    1. 하나의 스레드가 웹서버로 부터 html을 요청한다.
    2. html안에 이미지 파일이 여러 개 있어서 이를 또 다시 요청하고 받아온다.
    2. 데이터를 받는 과정도 I/O이므로 다 받아올때까지 Process가 Blocked 된다.
    3. 사용자 입장에서는 관련 파일을 다 받아올때까지 기다리게 됨으로 응답성이 떨어진다.
    
    <multi thread인 경우>
    1. 하나의 스레드가 웹 서버로 부터 html을 요청한다.
    2. html안에 이미지 파일이 여러 개 있어서 이를 또 다시 요청하고 받아온다.
    3. 이 때 여러 이미지 파일을 각각 스레드가 요청하는 방식을 사용해 서버에서 받아온다.
       (요청 스레드는 blocked 상태가 됨)
    4. 비동기적으로 받아온 순서대로 웹 브라우저가 랜더링을 하기 때문에 먼저 받아온것은 바로 사용자
       에게 보여줄 수 있기 때문에 사용자 입장에서는 응답성이 높아진다.
          
    ```

  - Resource Sharing(자원공유)

    ```
    스레드는 data,code,os의 자원 영역은 스레드간 공유한기 때문에 메모리 측면에서 효율적인다.
    ```

  - Economy(경제성)

    ```
    <crating/cpu switching 측면에서의 경제성>
    1.process를 하나 만드는것은 오버헤드가 크다. 그러나 process 하나에 스레드를 추가하는 것은 오버헤드가 작다. 
    
    2.process를 context switch할 때 오버헤드가 크나 스레드의 경우는 동일한 주소공간을 쓰기 때문에 대부분의 문맥을 그대로 사용하기 때문에 경제성 측면에서 효율적이다.
    ```

    

  - Utilization of MP Architectures(멀티 프로세스 아키텍쳐인 경우)

    ```
    멀티 프로세스는 cpu가 여러개 존재하는 컴퓨터이다. 멀티 프로세스 아키텍쳐에서 스레드를 사용하면 각각 스레드가 서로 다른 cpu에서 일을 할 수 있어서 결과가 더 빨리 얻을 수 있음
    
    예를 들어 1000*1000 행렬 두 개를 곱셈을 한다고 했을때,
    각각 연산에 대해서 다중 스레드로 처리하고 각 cpu로 할당합니다. 그러게 되면 병렬적으로 일이 처리되어 결과를 더욱 빨리 얻을 수 있다.
    ```

    



- **Threads의 구현**

  - User Threads

    ```
    1.커널 스레드는 스레드가 여러개있다는 사실을 운영체제가 알고 있다.
    ```

    

  - Kernel Threads

    ```
    1.유저 스레드는 하나의 process안에 여러개의 thread가 있다는것을 os가 모르고 있습니다. user program이 이러한 스레드를 관리합니다.
    ```

    

- **Single thread와 Multi Thread**

  ```
  "single core"인 경우에 "같은 작업(출력,네트워크 등)"을 처리할 때 오히려 Single thread가 더 빠른 경우가 있다.
  
  상황,하나의 process에서
  	A('호정'출력하는 소스)//하나의 process안이다.
  	B('예린'출력하는 소스)//하나의 process안이다.
  signle thread로 돌렸을때,
  A작업이 모두 끝난후 B작업이 실행
  
  Multi thread로 돌렸을때,
  A작업과 B작업이 번갈아 가면서 실행
  
  결과는, Single thread 작업이 더 빠르다. 그 이유는, 같은 작업을 수행하였을때 멀티 스레드는 스레드간의 context switching이 빈번히 일어나는 점과 A작업이 출력될때 B작업이 병행적으로 출력을 못하기 때문에 왜냐하면 이미 A작업이 출력이기 때문에 기다려야 한다.때문에 무조건 멀티 스레드를 사용한다고 해서 빠른것은 아니다.
  
  서로 다른 자원을 사용하는 작업(데이터 입력, 네트워크로 파일 주고받는 작업, 프린터로 파일 출력하는 작업등)인 경우 멀티스레드 프로세스가 더 효율적이다. 예를 들어 입력받는 작업(A), 화면에 보여주는 작업(B)가 있을때 하나의 스레드로 처리하면 입력이 끝날때까지 화면에 보여줄 수 없지만(CPU낭비) 멀티 스레드를 사용하면 입력을 기다리는 동안 출력이 동시에 이루어져서 효율적인 CPU사용을 이끌어낼 수 있다.
  ```

 

- **병행(concurrent)과 병렬(parallel)**

  병행이란, 번갈아가면서 실행되는것을 의미한다.

  병렬이란, 번갈아가면서 실행되는데 겹치는 부분이 존재할 때 병렬이라 한다.

  ![concurrent vs parallel](https://nikgrozev.com/images/blog/Overview%20of%20Modern%20Concurrency%20and%20Parallelism%20Concepts/concurrent_vs_parallel.png)

  



> :arrow_double_up:[Top](#Thread)    :leftwards_arrow_with_hook:[Back](https://github.com/HanHoJung/OS) 

---

## Reference

https://jun-itworld.tistory.com/17























































