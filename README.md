## Python MultiThread & MultiProcessing



### CH01 : Multithreading      
- Difference between Process And Thread
- Python's GIL
- Thread(1) - Basic
- Thread(2) - Daemon, Join
- Thread(3) - ThreadPoolExecutor
- Thread(4) - Lock, Deadlock
- Thread(5) - Prod and Cons Using Queue


```
(1).프로세스
    - 운영체제 -> 할당 받는 자원 단위(실행 중인 프로그램)
    - CPU동작 시간, 주소공간(독립적)
    - Code, Data, Stack, Heap -> 독립적
    - 최소 1개의 메인스레드 보유
    - 파이프, 파일, 소켓등을 사용해서 프로세스간 통신(Cost 높음)-> Context Switching

(2).스레드
    - 프로세스 내에 실행 흐름 단위
    - 프로세스 자원 사용
    - Stack만 별도 할당 나머지는 공유(Code, Data, Heap)
    - 메모리 공유(변수 공유)
    - 한 스레드의 결과가 다른 스레드에 영향 끼침
    - 동기화 문제는 정말 주의(디버깅 어려움)

(3).멀티 스레드
    - 한 개의 단일 어플리케이션(응용프로그램) -> 여러 스레드로 구성 후 작업 처리
    - 시스템 자원 소모 감소(효율성), 처리량 증가(Cost 감소)
    - 통신 부담 감소, 디버깅 어려움, 단일 프로세스에는 효과 미약, 자원 공유 문제(교착상태), 프로세스 영향준다.

(4).멀티 프로세스
    - 한 개의 단일 어플리케이션(응용프로그램) -> 여러 프로세스로 구성 후 작업 처리
    - 한 개의 프로세스 문제 발생은 확산 없음(프로세스 Kill)
    - 캐시 체인지, Cost 비용 매우 높음(오버헤드), 복잡한 통신 방식 사용

```


```
Gil(Global Interpreter Lock)
(1).CPython -> Python(bytecode) 실행 시 여러 thread 사용 할 경우
    단일 스레드만이 Python object에 접근 하게 제한하는 mutex
(2).CPython 메모리 관리가 취약하기 때문(즉, Thread-safe)
(3).단일 스레드도 충분히 빠르다.
(4).프로세스 사용 가능(Numpy/Scipy)등 Gil 외부 영역에서 효율적인 코딩 가능
(5).병렬 처리는 Multiprocessing, asyncio 선택지 다양함.
(6).thread 동시성 완벽 처리를 위해서 -> Jython, IronPython, Stackless Python 등이 존재

```
### CH02 : Parallelism with Multiprocessing    
- Process vs Thread, Parallelism
- multiprocessing(1) - Join, is_alive
- multiprocessing(2) - Naming, Parallel processing
- multiprocessing(3) - ProcessPoolExecutor
- multiprocessing(4) - Sharing state
- multiprocessing(5) - Queue, Pipe


```
(1).Parallelism
    - 완전히 동일한 타이밍(시점)에 태스크 실행
    - 다양한 파트(부분)으로 나눠서 실행(합 나눠서 구하고 취합)
    - 멀티프로세싱에서 CPU가 1Core 인 경우 만족하지 않음
    - 딥러닝, 비트코인 채굴 등

(2).Process vs Thread(차이 비교(중요))
    - 독립된 메모리(프로세스), 공유메모리(스레드)
    - 많은 메모리 필요(프로세스), 적은 메모리(스레드)
    - 좀비(데드)프로세스 생성 가능성, 좀비(데드) 스레드 생성 쉽지 않음
    - 오버헤드 큼(프로세스), 오버헤드 작음(스레드)
    - 생성/소멸 다소 느림(프로세스), 생성/소멸 빠름(스레드)
    - 코드 작성 쉬움/디버깅 어려움(프로세스), 코드작성 어려움/디버깅 어려움(스레드)

```



### CH03 : Concurrency, CPU Bound vs I/O Bound
- What Is Concurrency
- Blocking vs Non-Blocking I/O
- Multiprocessing vs Threading vs AsyncIO
- I/O Bound(1) - Synchronous
- I/O Bound(2) - threading vs asyncio vs multiprocessing
- CPU Bound(1) - Synchronous
- CPU Bound(2) - Multiprocessing


```
Concurrency(동시성)

- CPU 가용성 극대화 위해 Parallelism 의 단점 및 어려움을 소프트웨어(구현)레벨에서 해결하기 위한 방법
- 싱글코어에서 멀티스레드 패턴으로 작업을 처리
- 동시 작업에 있어서 일정양 처리 후 다음 작업으로 넘기는 방식
- 즉, 제어권 주고 받으며 작업 처리 패턴, 병렬적은 아니나, 유사한 처리 방식

Concurrency(동시성) vs Parallelism(병렬성) 비교

동시성 : 논리적, 동시 실행 패턴(논리적), 싱글코어, 멀티 코어에서 실행 가능, 한 개의 작업 공유 처리, 디버깅 매우 어려움, Mutex, Deadlock
병렬성 : 물리적, 물리적 동시 실행, 멀티 코어에서 구현 가능, 주로 별개의 작업 처리, 디버깅 어려움, OpenMP, MPI, CUDA

```

```
blocking IO vs Non-blocking

    blocking IO
    - 시스템 콜 요청시 -> 커널 IO 작업 완료 시 까지 응답 대기
    - 제어권(IO작업) ->  커널 소유 -> 응답(Response)전 까지 대기(Block) -> 다른 작업 수행 불가(대기)

    Non-blocking
    - 시스템 콜 요청시 -> 커널 IO 작업 완료 여부 상관없이 즉시 응답
    - 제어권(IO작업) ->  유저 프로세스 전달 -> 다른 작업 수행 가능(지속) -> 주기적 시스템 콜 통해서 IO 작업 완료 여부 확인

Async vs Sync

    Async : IO 작업 완료 여부에 대한 Noty는 커널(호출되는 함수) -> 유저프로세스(호출하는 함수)
    Sync : IO 작업 완료 여부에 대한 Noty는 유저프로세스(호출하는 함수) -> 커널(호출되는 함수)
```


```
CPU Bound vs I/O Bound

    CPU Bound
    - 프로세스 진행 -> CPU 속도에 의해 제한(결정) -> 행렬 곱, 고속 연산, 압축 파일, 집합 연산 등
    - CPU 연산 위주 작업

    I/O Bound
    - 파일 쓰기, 디스크 작업, 네트워크 통신, 시리얼 포트 송수신, 디스크 작업 -> 작업에 의해서 병목(수행시간)이 결정
    - CPU 성능 지표가 수행시간 단축으로 크게 영향을 끼치지 않음.
    
메모리 바인딩, 캐시 바운딩

작업 목적에 따라서 적절한 동시성 라이브러리 선택이 중요

최종 비교

    - Multiprocessing : Multiple processes, 고가용성(CPU) Utilization -> CPU-Bound -> 10개 부엌, 10명 요리사, 10개 요리
    - Threading : Single(Multi) process, Multiple threads, OS decides task switching. -> Fast I/O-Bound -> 1개 부엌, 10명 요리사, 10개 요리
    - AsyncIO : Single process, single thread, cooperative multitasking, tasks cooperatively decide switching -> Slow I/O-Bound -> 1개 부엌, 1명 요리사, 10개 요리
```











#### Note
 
Stack, Queue, Computer CS, 운영체제     


1. 파이썬 코드 정리 및 검사      
-> Pycodesytel, Autopep8, Pylint      

2. 코드 적게 쓰기    
-> 간결한 논리적 표현, 라인은 돈이 든다.   


3. 오픈소스 참여(Level3) 코드 줄여 개선 -> 문제점 토론 -> 내 실력을 공개    


4. 디버깅(테스트 위주로 개발) -> 예상치 못한 예외 or 오류 or 에러     

5. 술력도가 쌓이면 급격하게 성장 -> 트렌디하게 기술 흐름에 대한 관심   
Github -> Trand Ranking
