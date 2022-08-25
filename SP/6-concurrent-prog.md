---

# title: [SP] 6. Concurrent Programming
tags: System Programming
category: System Programming
picture_frame: shadow
use_math: true
---

# 6. Concurrent Prog.

Property: CSAPP 6

## **Thread**
- stack not shared, process~~ 보다 더 명확하게 state할 것



### **Process vs Thread**
- Similarity
    - has its own logical control flow               
    - can run concurrently with others (also on different core)
    - context switched?   
- Differences
    1. Process
        1. process privacy 
            - on each process, physically isolated된 상태로 OS설계시 만들어진다. 
            - → process마다 addr space가 달라서 data를 share하기 어렵다 : 
        2. via IPC Mechanism
            - (message passing shared memory) Mechanism 
            - FIFO, shared memory, semaphore
    2. Thread
        1. 1 process내 많은 execution flow가 존재한다.
        2. Heap, Data, Code가 공유된다 
            - → thread끼리 easy to share data
            - Local stack은 제외
        3. effective cost than process : process control cycle is more expensive    



## **Race, Deadlock, Livelock, Starvation, Fairness : Def & contents**
- (사고 접근) 화장실이 하나 있는데 대기자 5명 / 허니버터칩 등 대란
1. **Race**
    1. (def) outcome depends on arbitrary scheduling decisions elsewhere in the system
    2. (비유) 무한 경쟁
2. **Deadlock**
    1. (def) improper rsrc alloc prevents forward progress
    2. (비유) 교차로 무한대기 - 누구도 지나가지 못하고 state에서 횡행하며 무한대기
    3. (sol) order 설정 (OS범위)
3. **Livelock**
    1. (def) shared memory내 오직 한 Process만 접근 가능 
    → lock을 잡기 위해 매번 시도한다.
    2. (결과) 잡지도 못하는데도 불구하고 지속해서 시도하기에 inefficient CPU Usage
4. **Starvation**
    1. (def) 한 번도 lock 잡을 Chance를 갖지 못하는 Process 발생
    2. (비유) Process 하나는 잡지 못해 굶어 죽게됨
5. **Fairness**
    1. (def) 공정히 compete했음에도 한 process가 독식하며 필요한 clock cycle만큼 획득하지 못하는 현상
    2. (비유) 교차로 무한대기



## **Process based concurrency vs Event-based concurrency vs thread-base concurrency** [2021기출-12] 


### 1.  **Process-based :** server가 client 개수만큼 multiple process


- single process : if loop에 따라 file descriptor 본 후 event가 있는 경우 handling 
- **kernel automatically interleaves : multiple logical flows**
- `**Listenfd`에 각 Connection 요청 마다 parent process를 accept하고 client에 `fork()`를 띄워 `connfd`와 client를 연결시켜 각 client마다 자신만의 address space를 가질 수 있도록 한다.
- → Process Privacy : 각 client의 space는 독립적인 고립된 공간이다.**
- **Pros**
    - Handle multiple connections concurrently
    - clean sharing model : file table
    - simple, straightforward 
        - → isolated address space :execution flow 변화 고려 필요 X

- **Cons**
    - additional overhead for process control - server: client개수만큼 process fork 진행
        - 110개의 client가 들어오면 110개의 Fork 진행.
    - Nontrivial to share data between processes : Hard to share rsrcs 
        - (pros : avoid unintended sharing)과 상충되는 내용
            - 의도하지 않은 Sharing Avoidance = Process 간 Resource 공유 Nontrivial
        - Isolated 
            - → process마다 addr space가 달라서 data를 share하기 어렵다 :
            - Solution via IPC Mechanism
                - (interprocess communication message passing shared memory)
                - FIFO, shared memory, semaphore → data share가능해짐

- Issues :
    - listening server process 
        - must reap zombie child - to prevent memory leak
    - close its copy of `connfd`


### 2. **Event-Based** : process 1개
- programmer에 의해 mulitple logical flow (manually)
- server daemon이 listenfd의 Connection request가 client로부터 오는지 보고 그에 맞춰 connfd 생성 → client-server channel 생성 후 응답
- **각 client들이 fd를 따로 두어, process의 조건에 따라 각자 처리한다.**
    - `**connfd` - Descriptor에 pending input의 유무를 확인한 후 : `epoll`, `select`
        - ask kernel to suspend the process, returning control to applicatoin after 1개 이상의 IO Event 발생시
        - `**listenfd` 있다면 event 처리: `accept`통해 Connection + new connfd 배열에 추가**
            - 없다면 skip한다.
    - all flows share : same address space
        - IO Multiplexing : client 1, 2,3에 fd 따로 → Process if loop에 따라 각자 처리함


- **Pros**
    - One logical control flow and address space.
    - Can single-step with a debugger.
        - total control over scheduling
        - tedious, low-lv
    - **Very Low overhead :** No process or thread control overhead.
        - Design of choice for high-performance Web servers and search engines.
        - e.g., Node.js, nginx, Tornado


- Cons
    - Significantly **more complex** to code than process- or thread-based designs.
    - Hard to provide **fine-grained concurrency** → developer 알아서
        - E.g., how to deal with partial HTTP request headers
    - Cannot take advantage of **multi-core**
        - Single thread of control + 나머지는 놀고 있다!

### 3. **Thread-Based** : process 1개 → lightweight concurrent programming
- kernel automatically interleaves : multiple logical flows
- kernel에 의해 mulitple logical flow 자동
- **each flows share : same address space**        
- process based + event based / mid overhead
- issues
    - **run ‘detached’ to avoid memory leak**
        - joinable thread - can be reaped, killed by other thread
            - → reap to free memory rsrc
        - detached thread - can’t be reaped, killed by others 
            - → rsrcs automatically reaped on termination
        - all fn called by thread must be **thread-safe**


- Pros
    - **Easy to share** data structs between threads
    - efficient than process : not so much control voer scheduling policies


- Cons
    - careful to avoid unintended sharing → subtle, hard-to-reproduce error
        - synchronization
    - 구현이 복잡하다 & debug 어렵다:
        - event orderings not repeatable + shared data로 인한 unexpected error
    - overhead많다: process creation overhead + process간 context switch overhead





## **Thread view**
### **`Thread` : stack variable에서 다른 thread가 visible한 이유**
- 개념 : Thread는 다른 stack invisible
- 실제 : trick을 통해 reference 가능
    - global variable indirection

### 1. Traditional view
- [Process] = [Process context] + [code, data, stack]
- ![Untitled](6-img/Untitled.png)
        
### 2. Alternative view
1. [Thread] + [code, data, kernel context]
    - [Thread] = [stack] + [thread context]
    - ![Untitled](6-img/Untitled_1.png)



2. each thread has
    1. has its own logical control flow  / own TID
    2. has its own stack for logical var - not protected from other thread
    3. same code, data, kernel context


- Concurrent execution
    - single-core : simulate parallelism by time slicing
    - multi-core : true parallelism




**[2021] Iterative server (6-concurrent programming)**
- [2021기출] iterative servers process one request at a time. in this iterative server, while a client exchanges messages with the server, the second client is blocking. however, the second client was still able to send a message even though the server didn't accept the connection request. where and how is the message sent by the second client managed?
    - Second client는 connection을 server에 요청했으나 First Client에 의해 blocking된 상태이다. 그러나 message를 보낼 수는 있기 때문에 해당 server process가 돌고 있는 OS의 TCP Manager내 `write()`함수를 수행할 수 있다. 이는 읽을 수 없고 buffering만 되어 있는 상태이다.
    - 실제로 수행되기 위해서는 first client가 close하여 client2와의 새로운 connection이 형성되면 해당 message를 read하고 write한다.
