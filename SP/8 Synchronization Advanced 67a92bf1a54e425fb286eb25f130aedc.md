---
layout: article
title: [SP] 8. Synchronization: Advanced
tags: System Programming
category: System Programming
picture_frame: shadow
use_math: true
---

# 8. Synchronization: Advanced


# Review

## Semaphore

- **non-negative global integer synchronization variable. : int s; (>=0)**
- Manipulated by P and V operations. 
semaphore variable을 바꿀 수 있는 방법 p, s 를 통해 manipulate
    - manipulate : int var를 증가, 감소시킴 (+복잡)

- Binary semaphore vs Counting semaphore
    - initialize as 1 (binary) 5, 10
    - semaphore의 초기 value는 critical session에 들어갈 수 있는 thread 개수
    - critical session에는 딱 하나 thread 밖에 못 들어감
    - Counting semaphore는 그 값을 5로 초기화해두었다면 5개 cricial session에 들어갈 수있음
    
- critical session에 들어가고 나서 : P를 통해 들어갈지 안들어갈지 보고,
    - 내가 들어갔다는 이야기 = Semaphore val = 1이고 critical session에서 작업하고 나올 때 누군가가 내가 아직 critical session에서 있을 때 들어오려고 하면 P에서 blocked (semaphore val = 0) -> blocked, suspended
    - 내가 나올 때 unlock → V fn 호출한다.

### P(s)

s라는 variable이 0인지 아닌지 봄

1. $**s \neq 0$ : 0이 아니면 → 1을 빼고 나서 끝난다**
    - If s is nonzero, then decrement s by 1 and return immediately.
        - Test and decrement operations occur **atomically** (indivisibly)
2. $s=0$ : **본인이 suspend된다**
    - If s is zero, then suspend thread until s becomes nonzero 
    and the thread is restarted by a V operation.
        - 내가 이 var보고나서 이 함수 안에서 여러 thread중 누군가가 decrement했다는 것
        → 나는 그 값이 0보다 큰 값이 될 때까지 기다릴 것이다 (suspend, sleep)
            
            until 0보다 큰 값이 될 때 : var x를 0보다 크게 만든 다음 깨워줄 때까지 기다림 (signal 받을 때까지)
            
    - After restarting, the P operation decrements s and returns control to the caller.
        
        → 깨어나게 되면, s라는 variable을 decrement하고 진행됨
        

- (binary semaphore기억하고 설명) Semaphore가 1로 initialize -> 두 thread가 P/V
    - critical session을 앞 뒤로 쌓아서 lock을 가져다 lock을 얻으면 들어가고 얻지 못하면 기다림
    
    나올 땐 lock을 풀어주는 구조
    
    - 이 P라는 것은 semaphore value를 가져다 확인하고, 그 값이 0이 아니라면 decrement
    - 최초로 1로 되있을 때 들어간다고 하면 decrement하여 0으로 만들고, 그리고 critical session으로 들어감
    - 만약에 들어가려 했는데 semaphore 값을 봤는데 0이다
    
    → blocked 된 상태로 남아있고 그 semaphore에 나는 wait하고 있다며 매달려 있음
    
    - Process state transition diagram에서 보면 run 상태에서 block상태로
1. 정리 
    1. semaphore s 를 Decrement한다 
    2. $s \neq 0$ - 그 값이 0보다 크면 decrement,
    3. $s=0$이면 sleep하고 누군가가 깨워줄 때까지 suspend된다. 
    = CPU Cycle을 소모하지 않는다.
    → 즉 0보다 크거나 같은 값을 계속 체크하지 않고 suspended되어 있다가 signal오면 깨어나는 형태

### V(s):

- V의 정의 : 기다리고 있는 thread들이 재시작되는 순서를 정의하지 않는다.
    - 요구하는 유일한 사항은 V가 정확히 한 개의 waiting thread를 restart → 여러 개의 thread가 하나의 Semaphore를 가지고 있을 때 어떤 것이 V의 결과로 재시작되는지는 예측 불가능
- Increment s by 1 : s라는 variable을 increment해줌 → 그리고 나서 만일 s variable을 기다리고 있던 suspend thread가 있다면 그 중 한명을 깨워주고 → 그 thread가 decrement할수 있게 해줌
- Increment operation occurs atomically : 
atomic하게 increment시키고 누군가가 s라는 variable을 decrement하지 못해서 pending되어있는 경우 (suspended된 경우), 여러 thread중 누군가 하나를 wake시켜서 decrement할 수 있게끔 한다.

- 정리하면,
    - S를 increment하고, 이 semaphore value에 기다리고 있던 blocked 된 thread를 깨워줌
    - semaphorevalue s 를 increment
        
        → 이 semaphore value의 기다리고 있떤 blocked된 thread를 깨워 줌
        
        → 여러 thread가 다같이 깨어남 : thundering herd
        
        - block되어 기다리던 thread를 깨워 줌 
        → 깨어나 ready 상태로 가 scheduler에 의해 행사되어 critical session으로 들어감
- **‘Atomically’**
    - P,V는 atomically 수행된다 : indivisibly - 쪼개질 수 없는 함수이다.
    = p라는 함수는 실행이 되거나, 안 되거나 둘 중 하나이다.
    - variable이 뭔지 test하고 이 variable을 어떤 값으로 setting해줌 (decrement)
    - v또한 Atomically increment해주기 때문에 변수를 하나 증가해줌
    
- If there are any threads blocked in a P operation waiting for s to become non- zero, then restart exactly one of those threads, which then completes its P operation by decrementing s. 

test와 감소 연산은 semaphore s가 0이 아니면 s의 감소가 중단 없이 일어난다는 의미에서 개별적으로 일어남. V에서도 증가연산 개별적. - Semaphore를 중단 없이 load, 증가, 저장하기 때문

- **Semaphore invariant: (s >= 0)**

## Using semaphores to protect shared resources via mutual exclusion

여러 thread가 해당 코드를 구동한다고 생각했을 때

```c
mutex = 1 

P(mutex) 
cnt++ // Critical Session
V(mutex)
```

- Mutex 값은 초기에 1 → 0으로 바꾸어 주고
thread 1번 : block → waiting(blocked, suspended 상태)
- critical session에서 나와 mutex 에서 thread 2번이 기다리고 있고 이를 깨워준다.
- 다시한번 mutex를 잡으려고 한다 →  잡혀 critical session으로 들어간다
- Basic idea:
    - Associate a unique semaphore mutex, initially 1, with each shared variable (or related set of shared variables)
    - Surround each access to the shared variable(s) with P(mutex) and V(mutex) operations

# Using Semaphores to Coordinate Access to Shared Resources

shared resources = data structure coordinate tech : semaphore

- Basic idea: Thread uses a semaphore operation to notify another thread that some condition has become true
    - Use counting semaphores to keep track of resource state and to notify other threads
    - Use mutex to protect access to resource
- Two classic examples:
    - The Producer-Consumer Problem
    - The Readers-Writers Problem

# Producer-Consumer Problem

![Untitled](8/Untitled.png)

1) producer-consumer problem :

- 생산자와 소비자 (여러 명의 생산자) (여러 명의 소비자) - front와 rear가 만나면 empty buffer
- classical한, 상당히 잘 사용되는 concurrency example.
- 생산자 : 무언가 item을 생산하고 있음 (Insert)
    - item store @ Shared buffer (multiple slot)
    - 생산자는 slot에다가 앞에서부터 하나씩 차곡차곡 쌓는다.
    
    내가 어디까지 쌓았는지 확인하기 위해 circulared buffer : front/rear
    
    - front는 아직 소비하지 않았기 때문에 맨 처음
- 소비자. 무언가 item을 소비하고 있음 (Remove)
    - remove하게 되면 front 뒤로 이동

(상황 가정) 3명의 consumer가 있고 buffer가 empty되어 있다고 하자.

- 소비자는 살 수 있는 item이 있는가?
    
    -> empty buffer이기 때문에 없다.
    
    - 소비자는 item이 없으면 sleep하면 된다 (blocked되면 된다)
    
- 생산자 입장에서는 소비자가 소비 못하는 바람에 buffer가 없어 더 생산 불가능
    
    -> 생산자는 그 때 sleep을 한다.
    

이런 특성을 가진 구조 semaphore : 

- 내가 cs에 들어갈 수 있을 때

## Concepts

big data system에서 backend에서 개발할 수 있는 중요한 technique

- Common synchronization pattern:
    - Producer waits for empty slot, inserts item in buffer, and notifies consumer
        - producer는 empty slot을 block된 상태로 기다림 (buffer 에 item insert위해서)
        - producer는 buffer에 item을 넣고, semaphore 기다리는 consumer가 있다면 알려줌
        - empty slot이 있다 → 생산자는 생산하면 되고
    - Consumer waits for item, removes it from buffer, and notifies producer
        - consumer : item 있는지 보고 있으면 buffer에서 remove하고 producer에게 알려서 새로 생산해도 된다고 하여 produce
        - item이 buffer에 있다면 -> 소비하면 되고
        - item이 없다면 -> producer가 produce할때까지 sleep

semaphore의 또 다른 용도 : mutual exclusion 제공 + shared resource로의 접근 scheduling

- thread : semaphore 연산을 이용하여 program state의 조건이 true가 됐음을 다른 Thread에 알림
- Examples
    - Multimedia processing:
        - Producer creates MPEG video frames, consumer renders them
            - coding algorithm에 따라 나오는 data
            - Consumer thread가 렌더링
            - 분배 : mpeg frame 생성 / consumer 렌더링 (분배)
        - buffer :개별 Frame에서 data와 관련한 encoding, decoding 시간 차이로 발생한 video stream noise 제거 → producer에 slot 저장소 제공, consumer에게 encoded frame 저장소 제공
    - Event-driven graphical user interfaces
        - Producer detects mouse clicks, mouse movements, and keyboard hits and inserts corresponding events in buffer 5 Shared buffer
        - Consumer retrieves events from buffer and paints the display
        - 
        - 마우스, 키보드 클릭 등 event를 받아 event buffer에 마구 집어넣는 producer
        -> 화면에다 display해줌

 

## Producer-Consumer on an n-element Buffer

- item을 추가하고 제거하는 것이 shared variable의 renew와 관련되어 있으므로 buffer에 접근할 때 mutex 보장 + buffer로의 접근 scheduling
    - if full buffer → producer 는 slot available 할 때까지 대기
    - if empty buffer → consumer 는 item available 할 때까지 대기

- Requires a mutex and two counting semaphores (slot/item):
    - mutex: enforces mutually exclusive access to the the buffer
        - mutex : binary semaphore :0, 1
            - mutually exclusive 오로지 하나 thread만 들어가도록
    - slot, items : counting semaphore
        - 초기화 값에 따라 그만큼 들어갈 수 있음.
    - slots: counts the available slots in the buffer
        - slot : n개의 buffer라고 할 때
            - ex. n=8이라고 하면 slot semaphore는 8로 초기화되어 8개까지는 동시에 CS에 들어가서 produce할 수 있다.
    - items: counts the available items in the buffer
        - item : buffer 안 item의 개수
            
            - 처음에는 empty였으니까 0으로 initialize
            
            - sbuf라는 shared buffer
            
- Implemented using a shared buffer package called $S_{BUF}$.
    - limited buffer 조작
    - mutex semaphore : mutex buffer approach 제공
    - semaphore slot, item : counting semaphore - empty slot의 수, available item 수 확인

## `sbuf` Package

### Declarations

```c
#include "csapp.h” 
typedef struct
{
    int *buf;
    int n;
    int front;
    int rear;
    sem_t mutex;
    sem_t slots;
    sem_t items;
} sbuf_t; /* Buffer array */ /* Maximum number of slots */ /* buf[(front+1)%n] is first item */ /* buf[rear%n] is last item */ /* Protects accesses to buf */ /* Counts available slots */ /* Counts available items */
void sbuf_init(sbuf_t *sp, int n);
void sbuf_deinit(sbuf_t *sp);
void sbuf_insert(sbuf_t *sp, int item);
int sbuf_remove(sbuf_t *sp);
```

### Implementation

```c
/* Create an empty, bounded, shared FIFO buffer with n slots */
void sbuf_init(sbuf_t *sp, int n)
{
    sp->buf = Calloc(n, sizeof(int));
    sp->n = n;/* Buffer holds max of n items */
    sp->front = sp->rear = 0;/* Empty buffer iff front == rear */ 
    Sem_init(&sp->mutex, 0, 1);/* Binary semaphore for locking */
    Sem_init(&sp->slots, 0, n);/* Initially, buf has n empty slots */ 
    Sem_init(&sp->items, 0, 0);/* Initially, buf has 0 items */                                                                                                                                                                       /* Clean up buffer sp */    
}

// Clean up buffer sup
void sbuf_deinit(sbuf_t * sp) { 
		Free(sp->buf); 
}   
```

- `sbuf_init`
    - buffer를 위한 Heap memory 할당
    - Front - rear가 empty heap을 가리키도록 설정
    - 초기값을 semaphore에 할당
    - 세 함수로 호출하기 전 한 번 호출
    - 0으로 초기화 = 0개만큼 들어간다는 이야기는 incorrect
        - Item은 있으면 thread에 넣는다.
- `sbuf_deinit`
    - 사용 마친 후 buffer space 반환
    
- ! (Semaphore 초기값) $\neq$ (critical session에 들어가는 thread의 개수)
- binary semaphore : 1개 thread만
- counting semaphore : Buffer에 있는 element 개수만큼 생산할수 있기 때문에

```c
/* Insert item onto the rear of shared buffer sp */
void sbuf_insert(sbuf_t *sp, int item)
{
    P(&sp->slots);  /* Wait for available slot */
    P(&sp->mutex);  /* Lock the buffer */
    sp->buf[(++sp->rear) % (sp->n)] = item; /* Insert the item */
    V(&sp->mutex); /* Unlock the buffer */ 
    V(&sp->items); /* Announce available item */
}
```

- Insert할 때 (thread들어갈 때) slot의 개수를 본다
- `sbuf_insert`
    - available slot을 기다리고, Mutex lock
    - Item insertion 후에 Mutex unlock한 후 new itemd이 available함을 알림
- ex. N=8
    - → decrement : n=7
        
        - 언제 block되냐 : 값이 0이 되어있을 때
        
    - 8개가 다 들어가 있다 = slot 이 없다
    
    - Insert 여러가지 thread가 떠서 insert를 하고 있기 때문에, 그 안에는 slot이 0이 될 때까지 mutex로 잡아 둠 = item을 잡아둘 때 rear ptr를 여러 thread가 움직이면 안됨
    - producer도 하나씩 순차적으로 수행해야 함,
        - 직렬화를 위해 mutex lock을 가지고 하나만 실행할 수 있도록.
    - item이 생산되었으니 increment → 1 또는 2, 3, 4
    - consumer: consume하기 전에 item에 해당하는 semaphore value를 봄
        - 0이 아니면 decrement하여 들어감

```c
/* Remove and return the first item from buffer sp */
 int sbuf_remove(sbuf_t *sp)
{
    int item;
    P(&sp->items);/* Wait for available item */
    P(&sp->mutex);/* Lock the buffer */
    item = sp->buf[(++sp->front) % (sp->n)]; /* Remove the item */
    V(&sp->mutex);/* Unlock the buffer */ 
    V(&sp->slots);/* Announce available slot */
}
return item; 
```

Writer (producer) - reader (consumer)

P(slot) / P(mutex) / CS / V(mutex) / V(item) → item을 통해 reader(consumer)중 p(item)

- `sbuf_remove`
    - item 기다린 후 mutex lock
    - item : buffer front에서 remove
    - mutex unlock후 available slot임을 알림

![Untitled](8/Untitled%201.png)

- remove
    - item을 가지고 들어가고, 나올 때는 slot을 봄
    - item이 0이 아니면 consume은 여러개가 같이 할 수 있음
    - front에서 item을 하나 씩 빼내면 ptr을 옮기고 직렬화가 됨
        
        → mutex semaphore를 잡아 한 녀석 한 번에 처리할 수 있도록
        
        → item을 뺐으니 slot이 생산
        

# Readers-Writers Problem

<Readers- Writers Problem>

- database = record 관리 : index를 tree로 관리
    - 자료구조를 update할수도 reference할수도 있음
    - database안에서는 이 문제를 해결하기 위한 solution으로 semaphore / mutex

## Concepts

- Generalization of the mutual exclusion problem
- concurrent thread의 set : shared object에 approach
    - ex. main memory data structure, disk database
- Problem statement:
    - Reader threads only read the object / Writer threads modify the object
        - Writers must have exclusive access to the object - but reader는 이 object를 무수히 다른 reader과 공유해야 할 수 있음
    - Unlimited number of readers can access the object
    - 어떤 object가 있는데 이 object가 read/write 가능함
        - 누군가 read하고 있으면 해당 object를 write하면 안 되고 기다림
            - “누군가가 읽고 있다” - 쓰려는 애들은 기다려야 하지만 읽고자 하는 obj는 읽게 해줌
            - “누군가가 쓰고 있다”
        - 누군가 write하고 있으면 read는 대기중
        - 누군가 read : 다른 read가능 (누군가가 수정하고 있는 상태가 아니기 때문에)
- Occurs frequently in real systems, e.g.,
    - Online airline reservation system
        - 고객 : 좌석 할당 시스템을 동시에 조사 가능
        - 예약 : Database에 배타적 접근
    - Multithreaded caching Web proxy
        - 무제한 Thread : 기존 page를 공유 page cache에서 가져올 수 있음
        - cache에 새 페이지를 쓰는 모든 Thread에 배타적 접근

## Variants of Readers-Writers

- First readers-writers problem (favors readers)
    - No reader should be kept waiting unless a writer has already been granted permission to use the object 
    어떠한 reader도 writer가 이미 이 객체르 이용하도록 허가하지 않았으면 계속 기다려서는 안된다
    - A reader that arrives after a waiting writer gets priority over the writer 
    reader는 단지 writer가 기다리고 있다고 해서 기다려서는 안 된다.
    
    **Favor for reader**
    
    - 어떤 object가 있다고 할 때 R1이 읽고 있다고 가정하자
        
        → W1이 오면, W1는 wait하여야 한다 : 누군가가 read하고 있기 때문에
        
        → R2가 오면 : R2는 wait없이 읽어도 된다. (Read에 favor)
        
    - w1을 bypass하고 r2가 읽을 수 있도록 하고, r3…가 와도 읽을 수 있도록 한다.
        
        → write가 먼저 왔음에도 불구하고 현재 read를 하고 있으면 동일한 object에 대한 read는 계속 가능케 해 준다.
        
        - w1 : starvation (Ri는 계속해서 read 가능하고, 그 중 절대 들어가지 못함)
    
- Second readers-writers problem (favors writers)
    - Once a writer is ready to write, it performs its write as soon as possible 
    writer를 쓸 준비가 되었다면 가능한 한 빨리 write작업 수행할 것 요구
    - A reader that arrives after a writer must wait, even if the writer is also waiting 
    비록 writer가 기다리고 있을지라도 기다려야 함.
    
    **Favor for Writer**
    
    - Write가 왔다 = write를 빠르게 처리해야 하기 때문에 뒤에 있는 녀석들을 처리하지 않는다
        
        → writer가 끝난 다음에 reader를 처리한다 (최대한 빠르게 writer를 처리한다)
        
        → write에게 favor준다 하더라도 reader가 들어가서 안나오면 starvation
        
    - Or, writer가 들어가서 나오지 않으면 reader는 starvation
    - 사실 누구에게나 starvation이 발생하고 해결할 수 있는 solution이 아님
    
- Starvation (where a thread waits indefinitely) is possible in both cases
    - 영원히 정지하고 진행하지 못하게 되는 thread

## Solution to First Readers-Writers Problem

- starvation에 대한 solution을 제공하지 않음
- reader가 어느정도 이상이 되면 막는 coding : reader가 무한대로 들어오면 안 됨
    - Starvation을 막기 위한 코드는 어떻게 구현하여야 하는가
    - → 일정 threshold 까지 들어가게 되면 writer가 한 번 정도 들어가도록 수정

- `**w semaphore` : shared obj 접근하는 critical section으로의 접근 제어**
- `**mutex semaphore` : shared readcnt var로의 접근 보호**
    - 현재 critical section에 있는 reader수 count
- `**writer` : w mutex critical section에 들어갈 때마다 lock, 떠나면 unlock**
    - → critical section에 최대 하나의 Writer만 존재하도록 보장
    
- **critical section에 처음으로 들어가는 reader만 w lock, critical section을 가장 마지막으로 떠나는 reader만이 이를 unlock하여 풀어준다 :** 한 개의 reader가 W mutex를 가지고 있는 한 무수한 Reader가 critical section에 아무 방해 없이 들어갈 수 있음을 의미
    - w가 1인 것은 : Reader, writer 둘 중 하나만 들어가야 함
    대신 reader가 들어가면 여러개가 들어갈 수 있음
    - if condition이 없으면 Reader 들어가거나 writer들어가거나
    → 약간 extension하면 reader에게 favor
    → reader가 계속 들어갈 수 있어서, 조건문을 통해 read가 한 번 들어가 있다고 하면 그제서야 semaphore가지고 들어가고 Readcnt가 1보다 크게 되면 semaphore를 하지 않음

```c
int readcnt;    /* Initially = 0 */
sem_t mutex, w; /* Initially = 1 */
void reader(void)
{
    while (1)
    {
        P(&mutex);
        readcnt++;
        if (readcnt == 1) /* First in */
            P(&w);
        V(&mutex); /* Critical section */ /* Reading happens */
        P(&mutex);
        readcnt--;
        if (readcnt == 0) /* Last out */
            V(&w);
        V(&mutex);
    }
}
```

```c
void writer(void)
{
    while (1)
    {
        P(&w); /* Critical section */ /* Writing happens */
        V(&w);
    }
}
```

- (질문) packet loss 발생 시 문제?
    
    -> 문제 안됨
    
    - socket 통신 : socket열어서 send receive information
    API 밑단 TCP IP Kernel에서 보정해줌
    - packet loss 가 생기더라도 tcp쓴다고 하면 kernel의 tcp ip stack에서 protocol이 다 보정함
    -> 잃어버리게 되면 다시 data 달라고 하여 줌

## Prethread

### Putting It All Together: Prethreaded Concurrent Server

![Untitled](8/Untitled%202.png)

- server : main thread - multiple work thread
    - main thread : 반복해서 client accept request → 연결 식별자를 buffer에 저장
    - work thread : 반복해서 remove descriptors, client service 이후 descriptor 기다림

- **Thread pooling : thread를 만들어 놓고, 필요한 thread만 wake하여 사용하는 방식**
    - Thread를 미리 만들어 놓고 안 쓰는 thread는 sleep
    - 지금까지는 connfd나오면 thread spawn한 다음 해당 thread가 connfd와 client끼리 connect하여 channel 로 echoing - 여기서는 Pool of worker thread
- Shared buffer : master thread가 accept할 때마다 connect descriptor
    - master thread가 accept할 때마다 connected descriptor를 하나 씩 채워줌 (P=S model)

### Prethreaded Concurrent Server Configuration

Prethreaded concurrent echo server : sbuf로 구현

```c
//echoservert_pre.c  
sbuf_t sbuf; 
/* Shared buffer of connected descriptors */

int main(int argc, char **argv)
{
    int i, listenfd, connfd;
    socklen_t clientlen;
    struct sockaddr_storage clientaddr;
    pthread_t tid;
    listenfd = Open_listenfd(argv[1]);
    sbuf_init(&sbuf, SBUFSIZE);

    for (i = 0; i < NTHREADS; i++) /* Create worker threads */
        Pthread_create(&tid, NULL, thread, NULL);

    while (1)
    {
        clientlen = sizeof(struct sockaddr_storage);
        connfd = Accept(listenfd, (SA *)&clientaddr, &clientlen);
        sbuf_insert(&sbuf, connfd); /* Insert connfd in buffer */
    }
}
//*tuning parameter
// Concurrency degree - work load, application에 따라 다름

// 너무 작게 -> 일할 thread가 없음 : memory에 있는 자료구조 소비
// Lockdb, Level db : buffering 한 다음 flush / - flush할

// worker thread를 만들어 놓고, connfd만 return하고
// sbuf package를 통해 insertion하여 buffer에 집어넣음
```

```c
void *thread(void vargp)
{
    Pthread_detach(pthread_self());
    while (1)
    {
        int connfd = sbuf_remove(&sbuf);
        / Remove connfd from buf / echo_cnt(connfd);
        / Service client * / Close(connfd);
    }
}
echoservert_pre.c  16
```

- thread란 함수는 뒤에서 main thread가 joinable할 필요 없으면 Pthread_Detach
- 자기는 joinable하고 While에 들어가 Buffer produce에서 Connfd를 하나씩 뺀다
- worker thread 하나씩 빼고 connfd를 주되, buffer에다 집어넣었으니까 하나씩 빼내어감
- sbuf remove, sbuf insert : 앞의 함수를 사용하면 됨

```c
// echo_cnt.c  

static int byte_cnt; /* Byte counter */
static sem_t mutex;  /* and the mutex that protects it */
static void init_echo_cnt(void)
{
    Sem_init(&mutex, 0, 1);
    byte_cnt = 0;
}
```

- Sem_init(&mutex, 0, 1);
    - mutex 초기화 하고 byte count - 총 echoing한 byte count 보내는 코드
    - 여러 thread가 echoing했으니까 내가 echo할 때마다 몇 byte echoing했는지 shared variable로 global하게 잡아서 byte count에 increment

```c
void echo_cnt(int connfd)
{
    int n;
    char buf[MAXLINE];
    rio_t rio;
    static pthread_once_t once = PTHREAD_ONCE_INIT; // static global
    Pthread_once(&once, init_echo_cnt); //thread 여러개 뜨더라도 초기화 1회
    Rio_readinitb(&rio, connfd); //connfd에서 buffer을 받아옴
    while ((n = Rio_readlineb(&rio, buf, MAXLINE)) != 0)
		//bytecount : 여러 개 동시 Update하면 안 되니까 Mutex lock 잡아 둠
    {
        P(&mutex);
        byte_cnt += n;
        printf("thread %d received %d (%d total) bytes on fd %d\n", (int)pthread_self(), n, byte_cnt, connfd);
        V(&mutex);
        Rio_writen(connfd, buf, n);
    }
}
```

# Crucial concept: Thread Safety

thread safe하다의 의미가 무엇인지 개념적으로 배워보자.

- 모든 함수가 safe하지도 unsafe하지도 않다.

- Functions called from a thread must be **thread-safe**
    
    **: A function is thread-safe**  thread에서 호출된 함수가 thread safe하다 
    ****$\iff$
    **it will always produce correct results when called repeatedly from multiple concurrent threads** 여러 thread가 concurrently 호출하고, 각 thread들이 excution flow에 따라 항상 correct한 결과가 나온다.
    
- correct한 것이란: 추상적 개념으로 thread safe : 
내가 expect한 결과대로 나오면 safe한데
expected 결과로 나오지 않는다면 unsafe
    - expected : thread-safe : Thread가 그 함수를 호출했을 때 thread-safe하다.
- Classes of thread-unsafe functions:
    - Class 1: Functions that do not protect shared variables 
    →lock variable
    - Class 2: Functions that keep state across multiple invocations 
    → rand var
    - Class 3: Functions that return a pointer to a static variable 
    → thread safe하게 바꾸는 기술 알려줄 것 : lock n copy
    - Class 4: Functions that call thread-unsafe functions

- 대부분 여러 system call은 thread safe하게 구현되어 있으니 thread unsafe한 함수가 이렇게 존재한다.
    - thread safe하게 바꾼 것이 rand_r, ctime_r이라던지 thread safe하게 만들었다.
- 그러나 unix programming하면은 thread-unsafe한 함수를 쓴다.
    - 옛날 multiprocessing하려고 할 때 single processor, serialized된 상태였다.
    - 옛날의 함수를 지금 사용하려고 하니까 오류
- 요즘과 같은 multi processor 시대에서는 thread unsafe하네 라는 문제들이 왕왕 생기게 되어 thread safe된 함수를 쓰라고 되어 있다.
- system call을 변환한 것들이 있는데 개발자로서 ctime과 비슷한 함수를 만들었다고 하면 thread safe한지, thread unsafe한지 확인
    - thread unsafe하면 multi threading할 때 문제가 생길 수 있음을 인지하기 시작했으므로

ex. 격투기에 쓰이는 여러 가지 기술처럼 프로그래밍도 처음부터 짜는 것이 아닌, 자신만의 기술을 가지고 있어야 함 - 간헐적으로 발생하여 debug하기가 굉장히 어렵다. 

- Thread-Safe하지 않는 function의 특징 네가지(case를 이해하면 쓰기 어렵지 않음)
    - 기억해야 하는 이유 : 정보처리기사 자격증에서 외워 쓰는 것이 아니라, 실제 프로그래밍할 때 이런 규칙들이 있다 보니 신경써야 한다. 개발자가 할 부분이고, 그럼에도 실수하기 때문이다. bug free한 프로그램

(질문) 내가 한 번 호출하면 1이고 100이고 200이고. seed값이 변하지 않으면 정해진 sequence에 대하여 random : seed값이 바뀌지 않으면 항상 같은 결과값이 나온다. rnadom한 pattern이 동일하게 나옴을 보장할 수 없다. → seed를 누군가가 바꿀수도 있는데 이를 못 바꾸게 해 달라는 것. 
동일하게 생성되어야 하는데 그렇지 않은 경우

## Thread-Unsafe Functions (Class 1)

- Failing to protect shared variables
    - Fix: Use P and V semaphore operations
    - Example: goodcnt.c
    - Issue: Synchronization operations will slow down code

- goodcnt : shared var이 있는데 count하는 var이고 초기화가 0으로 되어 있고 0으로 된 이 thread var을 increment
    - 한 번씩 increment했으니까 2가 되어야 함 (expected, thread safe)
    - count increment하는 함수가 thread safe하지 않다 (thread unsafe)
        - binary semaphore하여 mutex lock을 걸어 앞 뒤를 보호했다.
        - thread safe한 함수를 만들게 되면, 여러 thread가 increment할 때 cnt++하는 과정이
    - 세 가지 instruction으로 구성되어 있는데 중간에 interleaving되는 경우가 나오지 않아 thread safe하여 항상 2가 나온다.
        - mutex lock 사용: thread safe
        - mutex lock 비사용: thread unsafe

## Thread-Unsafe Functions (Class 2)

- Relying on persistent state across multiple function invocations
- Example: Random number generator that relies on static state
    - ex. Pseudo random integer generator
    : 매번 random함수를 호출하는데 state를 계속 keep해야 한다.
    - Global var을 적게 사용하고 Call by ref로 들어가면 다른 thread에 unavailable
        - → 없애는 방법을 서라. global variable을 최대한 피해라.
    
    ```c
    static unsigned int next = 1; 
    /* rand: return pseudo-random integer on 0..32767 */
    int rand(void)
    {
        next = next1103515245 + 12345;
        return (unsigned int)(next / 65536) % 32768;
    } /* srand: set seed for rand() */
    void srand(unsigned int seed) 
    { 
    		next = seed; 
    }
    ```
    
    - next를 static하게 공유하게 해 놓는다. → deterministic하게 시작한 처음의 seed가 1이니까 순서대로 나온다. (매 invocation마다 state를 keep하는 형태로 구현)
    - seed를 바꾸는 srand함수
        - 문제가 되는 이유: 임의의 번호 1 3 5 7 9 생산한다고 가정
        - seed가 1부터 시작, 2씩 더하여 리턴하는 함수 srand
        - 내가 state를 가지고 있으니 다음, 다음 다음, 넘어간다. thread가 중간에 들어가 next를 갑자기 3에서 3000으로 바꾸어 버리면 Rand를 다시 시행한다 하더라도 그 sequence를 다시 생성할 수 없다.
        - correct하지 않다. / thread safe하지 않다.
    - 왜 이렇게 프로그램을 짰는가? 모든 것을 thread safe하게 만들면 되는게 아닌가? 의문

## Thread-Safe Random Number Generator

Rand에서는 parameter로 void로 넘어갔지만

- nextp pointer : 내 seed와 공유되는 변수가 아닌 것
    - Rand_r caller에서 선언한 local var의 ptr를 넘겨주기 때문에 자신 seed가지고 increment
    
    → thread unsafe한 것을 safe하게 바꾸어 줄 수 있다.
    
    → arg 의 일부로 state를 넘겨라
    

- Pass state as part of argument / arg 의 일부로 state를 넘겨라
- and, thereby, eliminate global state
    
    next는 thread들이 모두 호출할 수 있다.
    
    여러 thread들이 각자 공유하고 있기 때문에 생기는 문제
    
    → global state가 발생하지 않도록 만드는 방법
    
    ```c
    /* rand_r - return pseudo-random integer on 0..32767 */
    int rand_r(int *nextp)
    {
        *nextp = *nextp * 1103515245 + 12345;
        return (unsigned int)(*nextp / 65536) % 32768;
    }
    ```
    
    - Consequence:programmer using rand_r must maintain seed

## Thread-Unsafe Functions (Class 3)

- Returning a pointer to a static variable
- Fix 1. Rewrite function so caller passes address of variable to store result
    - Requires changes in caller and callee
- Fix 2. Lock-and-copy 
lock을 잡고 copy해라
    - Requires simple changes in caller (and none in callee)
    - However, caller must free memory.

```c
/* lock-and-copy version */ 
char *ctime_ts(const time_t *timep, char *privatep)
{
    char *sharedp;
    P(&mutex);
    sharedp = ctime(timep);
    strcpy(privatep, sharedp);
    V(&mutex);
    return privatep;
}
```

lock and copy

- `ctime :` 현재의 시간을 가져다 return해주는 function (from time_t struct)
    - global variable로 되어 있는데 이를 읽어 오는 것 (by system call)
    - ctime을 수행하고 읽어오는 사이에 우연치 않게 interrupt 걸려 switch된다면
        
        → 내가 호출한 시점이 아닌 다음 시점에 ctime으로 내용이 바뀔 수 있다.
        나는 모르고 interrupted 된 시간으로 return받을 수 있다.
        
- (ctime 자체가 기지고 있는 문제) 시간이 흘르며 s1→e1 도중 s2→e2에서 time이 들어간다.
    - 함수 자체가 문제 있음 → lock : breakable하지 않게, atomic하게 잡아 중간에 누군가가 interleaving하여 들어오지 않게 함.
    - lock을 잡고 return되면 copy하여 줌 : lock을 잡았기 때문에 이를 풀어야지 들어갈 수 있음
- **private으로 받되, caller의 ptr로 받는다면 자기 것으로 받는다**
- **ptr를 받은 후 strcpy하는데 caller의 변수 stack에 있는 var의 address를 넘겨주니까 
이를 dereference하며 lock and copy를 수행함**
    - fix1 : ctime을 다 뜯어 고칠수 있다
    - fix2 : ctime을 그대로 쓰되 mutex lock : ctime_ts를 부르는 caller가 있으니가, 해당 caller는 call by reference를 그대로 받을 수 있다.

## Thread-Unsafe Functions (Class 4)

- Calling thread-unsafe functions
    - Calling one thread-unsafe function makes the entire function that calls it thread-unsafe
    - Fix: Modify the function so it calls only thread-safe functions

## Reentrant Functions

- Def: A function is **reentrant** 
$\iff$ it accesses no shared variables when called by multiple threads.

지금 봤던 것은 : function들은 unsafe하냐, safe하냐로 나누어짐

- thread safe 중 어떤 것들은 reentrant :
    - thread가 호출했을 때 shared variable을 Thread가 같이 접근하지만 safe하지 않을 때 safe하게 만들기: 피해나가는 방법.
    - 그 shared variable이 없는 것

<책에서 없는 내용>

- safe, not reentrant, safe, reentrant
- unsafe를 safe하게는 만들어주는 능력은 필요하다.
    - (unsafe, reentrant)는 큰 의미는 없다.
- Important subset of thread-safe functions
    - Require no synchronization operations
    - Only way to make a Class 2 function thread-safe is to make it reetnrant (e.g., `rand_r` )

![Untitled](8/Untitled%203.png)

## Thread-Safe Library Functions

- All functions in the Standard C Library (at the back of your K&R text) are thread-safe
    - Examples: malloc, free, printf, scanf
        - printf는 thread-safe하긴 하다 (여러 thread 동시접근시) / but not async-signal-safe
        → NOT reentrant
- Most Unix system calls are thread-safe, with a few exceptions:
    
    ![Untitled](8/Untitled%204.png)
    

# One worry: Races

- A **race** occurs when correctness of the program depends on one thread reaching point x before another thread reaches point y

thread라는 fn 실행하여 call by reference로 해서 들어와서, I는 main thread의 local variable -> 주소값을 넘김

→ 새로 생성된 thread는 그 주소값을 myid라는 local variable을 stack에 copy하고 print해줌

- If) n=2→ iteration은 2번돈다.

```c
// race.c 
/* A threaded program with a race */
int main()
{
    pthread_t tid[N];
    int i; //N threads are sharing i
    for (i = 0; i < N; i++)
        Pthread_create(&tid[i], NULL, thread, &i);
    for (i = 0; i < N; i++)
        Pthread_join(tid[i], NULL);
    exit(0);
}
/* Thread routine */ 

void *thread(void *vargp)
{
    int myid = *((int *)vargp);
    printf("Hello from thread %d\n", myid);
    return NULL;
}

```

## Race Illustration

I라는 것을 dereference하여 print하려고 프로그램을 작성했는데 myid가 1이 나올수도 있다

→ myid가 0인줄 알고 실행했는데 i=1로 증가시키면 그 주소값을 보고 있기 때문에 0이 아닌 1이 print된다

- **이런 상황이 존재하면 thread간 race가 있다.**

잘못된 상황이 발생하면

```c
for (i = 0; i < N; i++)
    Pthread_create(&tid[i], NULL, thread, &i);
```

![Untitled](8/Untitled%205.png)

- between increment of `i` in main thread - dereference of `vargp` in peer thread
    - If dereference happens while i = 0, then OK
    - Otherwise, peer thread gets wrong id value

## Could this race really occur?

- Race Test
    - If no race, then each thread would get different value of i
    - Set of saved values would consist of one copy each of 0 through 99

```c
int i;
for (i = 0; i < 100; i++){
    Pthread_create(&tid, NULL, thread, &i);
}
```

```c
void *thread(void *vargp)
{
    Pthread_detach(pthread_self());
    int i = *((int *)vargp);
    save_value(i);
    return NULL;
}
race.c
```

## Experimental Results

![Untitled](8/Untitled%206.png)

thread for iteration하여 100번 실행 → ideally, 0~99번까지 원래는 한 번씩 찍히는 게 맞다.

그러나 race가 있기 때문에

- single cpu laptop에서 구동시켰더니
    - 1, 8, 16, 42가 두 번 찍힌다 = 한 번도 찍히지 않은 것들이 생긴다
- cpu에 hyper threading까지 되어 virtual cpu 8개 까지 수행됨 (3)
    - 안찍히는 게 너무 많다.
    - single cpu에서 multi cpu server로 시켰더니 cpu 개수가 많이자게 되면,control
    - multicore cpu.arcitecture을 설계하게 되면 나중에

## Race Elimination

Avoid unintended sharing of s tate

- 원래는 main 함수에다가 heap variable을 잡아 두고 thread를 가져다 각각의 ptr를 넘겨준다
- 각각에 대해서, heap variable을 보고 print하기 때문에 항상 한 번씩만 찍히게 된다.
- i를 가지고 넘기게 되면 iteration이 빨리 돌아올 수 있기 때문에
- heap var : 각 thread가 각자 들어가기 때문에 문제 해결 가능.

```c
/* Threaded program without the race */ 
int main()
{
    pthread_t tid[N];
    int i, *ptr;
    for (i = 0; i < N; i++)
    {
        ptr = Malloc(sizeof(int));
        ptr = i;
        Pthread_create(&tid[i], NULL, thread, ptr);
    }
    for (i = 0; i < N; i++)
        Pthread_join(tid[i], NULL);
    exit(0);
}

/* Thread routine */ 
void *thread(void *vargp)
{
    int myid = *((int *)vargp);
    Free(vargp);
    printf("Hello from thread %d\n", myid);
    return NULL;
}
```

# Another worry: Deadlock

- Def: A process is **deadlocked** $\iff$
    
     it is waiting for a condition that will never be true 
    
    - 서로서로 기다리는 상태에서 전진하지 못하고 hanging하는 상태
- Typical Scenario
    - Processes 1 and 2 needs two resources (A and B) to proceed / thread 1, 2번이 있는데
    Process 1 acquires A, waits for B  / t1은 a라는 자원을 가진 후 b라는 자원을 가져야 진행할 수 있다.
    Process 2 acquires B, waits for A  / t2은 b라는 자원을 가진 후 a라는 자원을 가져야 진행할 수 있다. 공교롭게도 a,b를 각각 잡은 상태에서 t1은 b를 기다리고 t2는 a를 기다린다.
    - Both will wait forever!
        - 둘다 rsrc를 획득하려고 하는데 서로의 rsrc를 기다리고 있는 이런 상태라고 보면 된다.
        - 두 thread가 hanging하여 진행하지 못함.

## Deadlocking With Semaphores

<concurrent programming 기술 중 하나>

- mutex한거 같은데 왜 hanging하고 있지 할 때 solution
- Deadlock 해결 → 같은 ordering을 주게 되면 deadlock을 해결할 수 있음
    - t1 : a -> b / t2 : b -> a 에서 a->b
    - 같은 순서로 획득할 수 있게 하면 deadlock을 회피할 수 있다

```c
int main()
{
    pthread_t tid[2];
    Sem_init(&mutex[0], 0, 1); /* mutex[0] = 1 */
    Sem_init(&mutex[1], 0, 1); /* mutex[1] = 1 */
    Pthread_create(&tid[0], NULL, count, (void)0);
    Pthread_create(&tid[1], NULL, count, (void *)1);
    Pthread_join(tid[0], NULL);
    Pthread_join(tid[1], NULL);
    printf("cnt=%d\n", cnt);
    exit(0);
}

void *count(void *vargp)
{
    int i;
    int id = (int)vargp;
    for (i = 0; i < NITERS; i++)
    {
        P(&mutex[id]);
        P(&mutex[1 - id]);
        cnt++;
        V(&mutex[id]);
        V(&mutex[1 - id]);
    }
    return NULL;
}
```

```c
Tid[0] : 
P(s0);
P(s1);
cnt++;
V(s0);
V(s1);
```

```c
Tid[1] :
P(s1);
P(s0);
cnt++;
V(s1);
V(s0);
```

## Deadlock Visualized in Progress Graph

![Untitled](8/Untitled%207.png)

- Locking introduces the potential for **deadlock:** waiting for a condition that will never be true
- Any trajectory that enters the **deadlock region** will eventually reach the **deadlock state**, waiting for either s0 or s1 to become nonzero
    - deadlock region : overlap되는 구간을 반드시 지나야 함
- Other trajectories luck out and skirt the deadlock region
- Unfortunate fact: deadlock is often nondeterministic (race)

## Avoiding Deadlock Acquire shared resources in same order

```c
int main()
{
    pthread_t tid[2];
    Sem_init(&mutex[0], 0, 1); /* mutex[0] = 1 */
    Sem_init(&mutex[1], 0, 1); /* mutex[1] = 1 */
    Pthread_create(&tid[0], NULL, count, (void *)0);
    Pthread_create(&tid[1], NULL, count, (void *)1);
    Pthread_join(tid[0], NULL);
    Pthread_join(tid[1], NULL);
    printf("cnt=%d\n", cnt);
    exit(0);
}
void *count(void *vargp)
{
    int i;
    int id = (int)vargp;
    for (i = 0; i < NITERS; i++)
    {
        P(&mutex[0]);
        P(&mutex[1]);
        cnt++;
        V(&mutex[id]);
        V(&mutex[1 - id]);
    }
    return NULL;
}
```

```c
Tid[0] : 
P(s0);
P(s1);
cnt++;
V(s0);
V(s1);
```

```c
Tid[1] :
P(s0);
P(s1);
cnt++;
V(s1);
V(s0);
```

## Avoided Deadlock in Progress Graph

![Untitled](8/Untitled%208.png)

- No way for trajectory to get stuck
- Processes acquire locks in same order
- Order in which locks released immaterial

- 순서를 바꾸어 수행하게 되면 → forbid region이 변화함
- overlap되는 부분을 들어가지 않으면 됨.

- (여담) 김영재 교수님석사 시절,
    - thread가 여러개 돌아가는데 서로 msg 주고 받는 프로그램 → 정말 간헐적으로 생김
    - 내가 짠 sw위에 benchmark를 돌리는데, 어떤 complexity로 돌리느냐에 따라서 정말 잘 돌아가거나 천번에 한번 안 돌아가거나 등 코드가 복잡해지다 보니 나도 모르게 실수하기도 한다 (deadlock 등)
        - 하다가 그냥 멈춘다 : 내가 benchmark 1시간 돌리다가
        - 죽지도 않고 그대로 멈춰 있어서 debug하기가 너무 힘들었다.
    - 마구 달려들기 보단 thread safe-unsafe
        - Race가 발생할 수 있으니까 조심해야 하고
        - deadlock은 항상 회피해야 하는데 발생할 수 있으므로 차량용 급발진 등 example이 나온다.
            - 요즘 자동차들도 sw로 구현 :sw bug가 언제든지 나타날수 있다
            - critical software들은 정말 조심해서 test도 많이 하고 bug fix도 잘 해야한다.
    - project 2 : Thread pooling을 해 보았다는 것 자체로도 자부심을 가져도 괜찮다
        
        그 개념을 알고있다는 것 자체로도 cs core에 도달해 있다.
        
        어렵긴 하다.