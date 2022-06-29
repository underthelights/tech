---
layout: article
title: [SP] 10. Dynamic Memory Allocation: Basic Concepts
tags: System Programming
category: System Programming
picture_frame: shadow
use_math: true
---

# 10. Dynamic Memory Allocation: Basic Concepts

**Dynamic Memory Allocation: Basic Concepts**

# **Basic concepts**

## **Dynamic Memory Allocation**

![Untitled](10/Untitled.png)

- heap이 어떻게 관리가 되고 있는지에 대한 고민은 해 본적 없는 것 같다.
    - 영역 내 빈 공간을 관리하는게 malloc allocation이 해 주는 역할
    - space manager라 해도 과언이 아님 : space 관리를 어떻게 해 줄것이냐
    - brk ptr : 0부터 top에 해당하는 크기만큼 할당하고 이 안에서 가장 공간 효율성을 높이며, 성능을 그렇게 저하하지 않으면서 이 방식에 따라 할당함 -> 더이상 할당 할 공간이 없으면 brk pt를 올려 줌
    - → sbrk라는 system call을 통해 ‘늘려줘’ 명령하여 library의 ptr 값이 늘어남 : 공간 관리
- application에서도 malloc-free 등 지속하다가 더이상 할당할 부분이 없으면 sbrk를 통해 쭉 늘려줌
    - 처음부터 왕창 늘려놓으면?
    - 물론 그래도 되지만, application 필요한 만큼만 할당해주면 되지 미리 줄 필요가 없다.

heap 관리자를 어떻게 만들 것이냐 : 주된 목표이자 하고자 하는 내용

![Untitled](10/Untitled%201.png)

- Programmers use dynamic memory allocators (such as malloc) to acquire VM at run time.
    - For data structures whose size is only known at runtime.

Dynamic memory allocator

- process의 가상메모리 공간에 0부터 max에 해당하는 process의 가상메모리 공간
- code, data 부분 제외하고 heap부터 시작
- dynamic memmory allocator를 활용해 가상 메모리 공간 안 heap space memory를 run time시 할당하고 해제할 수 있다. Dynamic memory allocators manage an area of process virtual memory known as the heap.

- Allocator입장에서 heap은 ‘collection of variable sized blocks’ : allcoated /  free
- Types of allocators
    - Exlicit allocator: application allocates and frees space
        - 프로그래밍할 때 메모리 공간을 요청하고 free하는 행위
        - language에서 malloc, free같은 함수를 통해 직접 해 줘야 함
        - c++ new / delete를 사용해서 memroy 할당 및 해제
        - E.g., malloc and free in C
    - Implicit allocator: application allocates, but does not free space
        - explicitly하지 않고 내부적으로
        - language마다 다름
        - garbage collector : 자동적으로 사용안되는 block 중 자동으로 free해줄 수 있음
        - ex. JAVA, ML, LISP에서는 할당된것 중 사용 안되는 것들을 collect함
        - → c에서 사용하는 explicit allocator로 설명해보자

### ‘Heap’

- c language 같은 경우 standard c malloc library 할당하는 system library
- runtime 시에 필요한 vm 값을 요청하기 위함 : dynamic memory allocator
    - 일반적으로 얼만큼 data가 필요한지 모르니, static하게 잡기보다도 runtime시에만 필요로 하는 자료구조 크기가 알려진 경우 dynamic memory allocator 사용하여 malloc
- brk ptr = heap의 top
- 가상 메모리 공간 = process마다 각자 자신의 가상 메모리 공간을 가짐
    - 일부 영역 ‘heap’ - brk ptr  : heap 공간 크기를 결정할 수 있는 range를 가리킬 수 있음
- 0번지부터 brk : heap 의 크기
    - Brk ptr로부터 증가 : sbrk라는 system call
    - 내부적인 malloc같은 sbrk system call을 통해 brk ptr를 증가시킬 수 있음
    - 어떤 특정한 시점에서는 변하지 않은 값으로 저장
        - 내가 사용할 수 있는 heap이라는 공간은 그 크기만큼 사용할 수 있다
    - 이 공간을 가져다 사용 : brk ptr를 증가시켜줌으로서 heap의 공간이 더 커질 수 있음
        
        → brk ptr가 가리킴
        
- Heap : 동일한 크기의 chunk로 이루어진 array이다.
    - ex. malloc을 사용해 block 선언 / chunk 크기 3
    - → chunk 3개 가진 block을 할당하고, chunk 2, chunk 1 할당 / 나머지 공간은 free한 공간
    
    3 chunk - 2 chunk - 1 chunk block
    
- memory alloc 관점엥서 보면 Allocate되어 있고 / free한 상태이고
    
    → 이 메모리를 가져다 : 가변적 크기를 가진 이 block들의 allocate된 것들과 free된 것들의 집합.
    각각은 allocate 되어 있거나 free되어 있다.
    
- vm 공간안에서 있는, 0부터 max값이라고 하면 code, data 부분을 제외하고 heap이라고 되어 있는 부분에 top에서 Brk가 가리키는 ptr
    
    → 이 지금 말한 heap 부분이 지금 말한 부분에 해당함 
    
    - top heap brk ptr →  heap의 top에 해당하는 주소이다.

- Will discuss simple explicit memory allocation today

---

- explicit allocator
- implicit allocator

## **The malloc Package**

- 실험
    - top of the heap를 setting해 놓고 그 공간 안에 더이상 빈 공간을 찾을수 없다고 하면 sbrk를 지속적으로 늘려 주면
        - → memory가 없다라고 실토함 : 현재 가용 가능한 memory를 tracking하다가 없으면 failuress
    - (until OS kernel이 최소로 필요로 하는 공간 (cspro 500MB))
        
        (실제 OS의 rsrc를 print했을 때 나오는 값보다 → process에게 그 이상은 주지 못한다)
        
    - sbrk도 요청해서 할당받고 하면 top of the heap을 가져다 brk ptr를 증가시키고 언젠가는 실패 : kernel안에서 sbrk system call handler가 threshold를 가지고 있음
    
- brk ptr : os kernel이 가짐
    - top of the heap이 넘쳐나는 경우에는 내부적으로 sbrk라는 system call을 통해 증가
    - → 그 때 heap 공간에서 free하지만 원하는 공간이 continuous하길 원한다.
    - 공간이 없어서 error를 return 하는 경우가 있을 수 있다
    = 성공하지 못해 메모리를 할당할 수 없다.
- 동적으로 결정되는 크기 : program 실행 순서에 따라 결정되는 값
- virtual memory의 시작 주소 return

- `void *malloc(size_t size)`
    - Successful: Returns a pointer to a memory block of at least size bytes aligned to an 8-byte (x86) or 16-byte (x86-64) boundary
        - If size == 0, returns NULL
        
        1. Successful : boundary에 align되는 memory block allocate한 후 ptr return
        
        - CPU 에 따라서 requested size보다 크거나 같은 사이즈
            - 8 byte의 word 크기 : 1byte만큼 활용하면 - 7byte만큼 사용 x
            - > internal fragment 등으로 활용가능
    - Unsuccessful: returns NULL (0) and sets errno
        - heap stage에서 할당할 부분이 없는 경우
- `void free(void *p)`
    - Returns the block pointed at by p to pool of available memory
        - heap space, Heap 안 다양한 메모리에서 p라는 block을 return 
        p라는 가상 메모리 ptr가 가리키는 memory
        - ex. *p가 f 부분을 가리킨다 → ‘aa’부분을 free해준다.
    - p must come from a previous call to malloc or realloc
    - 나중에 free하게 됐을 때 이 free한 부분이 모종의 status 관리하는 tag에 의해 free
    - free할 메모리의 크기는 내부적으로 알 수 있다 : meta정보를 읽고 word 2개 사이즈를 free하는구나
- Other functions
    - `calloc`: Version of malloc that initializes allocated block to zero.
        - malloc은 heap 영역에 다음과 같이 할당하면 allocate되어 있지만 해당 공간은 초기화가 되어 있지 않음
        - memory contents를 깨끗하게 지우고 싶다고 하면 해당 공간을 calloc이라는 함수를 사용하여 호출하면 malloc과 동일하게 할당하고, contents = 0으로 초기화한다 (initialize)
    - `realloc`: Changes the size of a previously allocated block.
        - a가 3개 allocate -> 4개로 늘려주는 역할
        - ptr 값을 받아 가상 메모리 주소, 크기 -> 그에 맞추어 size 조절
        - sbrk로 heap 영역을 최대로 늘리고도 부족한 경우에 발생하는가?
    - `sbrk`: Used internally by allocators to grow or shrink the heap  5
        - allocator 안에서 사용되는 system call
        - brk ptr를 올리거나 내리는, heap을 조절해주기 위한 함수
    
    malloc은 brk
    

## **malloc Example**

```c
#include <stdio.h>
#include <stdlib.h>
void foo(int n)
{
    int i, p;
    /* Allocate a block of n ints */ 
		p = (int)malloc(n * sizeof(int));
    if (p == NULL)
    {
        perror("malloc");
        exit(0);
    }
    // Initialize allocated block / 
		for (i = 0; i < n; i++) 
				p[i] = i;
    // Return allocated block to the heap * / 
		free(p);
}
```

- 굳이 그렇게 할 필요 없다 : API만 프로그래머에게 전달해주면 되는 상황이다.
    - 사용자 입장에서 api 안에서 제공되는 함수 사용
    - malloc의 class version을 만들겠다, pm hint를 주겠다
    - 인접하게 할당하는 등 mymalloc에서 그렇게 구현하면 됨

- allocator
    - word 크기 4byte 가정할 때 heap 관리자, heap 공간 중 free한 공간
    - 모종의 알고리즘을 통해 할당할 공간을 찾는다.
    - n=3 -> 3
    - 각각의 4byte만큼 떨어지며 0, 1, 2 ~~ 25’
    - 공간들은 free가 됨

## **Assumptions Made in This Lecture**

(몇가지 가정 후 설명 진행)

- memory address : 가장 최소 단위 word (8byte or 16byte)
- Heap : 연속된 가상 메모리 공간

- 실제 memory allocator 구현하는 입장에서 보면 생각할 게 많음
    - 두 칸을 가져다 free하는데, free후 p만 인자로 받음
    - word를 어떻게 free할것이냐에 대한 내용은 논의하지 않음 (나중에 배울 예정)
    - → free를 할 때 p가 가진 memory 두 개 공간이 있는데 이를 free하면 됨
- memory 크기는 딱 두개의 word로 되어 있다
- malloc-DB
    - memory 공간 할당자
        - 어떤 요청에서 빈공간을 찾아 return해주면 된다
        - 빈 공간이 생기면 그들끼리 묶어두면 된다
    - DB : record 관리, file형태로 담아 놓고 필요한 record 찾고 수정하고 집어넣는 관점에서 볼 때 상당히 비슷한 부분이 많음.
    - 어떤 data의 영역을 관리하기 위한 system을 어떻게 build할 것이냐로 확장할 수 있음

- Memory is word addressed.
- Words are int-sized.

![Untitled](10/Untitled%202.png)

## **Allocation Example**

동적 memory allocator : gdc에서 효율적으로 잘 짜여 있음

- 두 가지의 metric.
    1. allocator이 잘 동작하느냐에 대한 correctness (malloc-free combination이 잘 동작하느냐)
    2. space effectivity에 대해서 실제 효율적이냐, 성능에서도 malloc 이 좋은가

가상 메모리 공간 continguous

- OS가 해줘야 하는 역할 / 프로그래머 입장에서는 Vm 공간 중 heap이라는 일부 공간을 contiguous하게 할당받아 그 공간들만 할당해주고 해제해주고
- block storage

- 다음과 같은 heap이라는 공간이 있다. - chunk 크기로 된 연속적인 공간

![Untitled](10/Untitled%203.png)

- `free(p2)` : p2가 가리키는 block 안 chunk 5개가 free된다.
    - Free할 때 ptr를 찾아가게 되면 정보가 있고 여기서부터 연속 5개는 하나의 block안에 포함됨을 알고 다섯개를 free시켜준다.
- `p4=malloc(2) :` 2개의 chunk를 할당하는데 어디다 할당할 것이냐?
    - → 모종의 방법을 통해 다음과 같이 할당된다.

이런 식으로, block storage 관점에서 보더라도 상당히 비슷하다. 가상 메모리 공간에서 contiguous한 공간이고, 크기 공간들 중 빈 공간들을 관리하는 관리자라고 보면 됨

## **Constraints**

- Applications
    - Can issue arbitrary sequence of malloc and free requests
    - free request must be to a malloc’d block
    - malloc, free라는 동적 memory allocator fn
        - memory alloc/free req sequence를 보되, 이 sequence는 arbitrary. Issue라고 바라봐도 됨
    - 이미 allocate된 block을 free해주는 것이 free이다.
- Allocators
    - Can’t control number or size of allocated blocks
    이미 할당된 block들 : block 크기, 개수도 임의로 바꿀 수 없음
    - Must respond immediately to malloc requests
    일단 malloc이라는 allocator같은 경우 요청에 대한 즉각 응답하여 할당
        - i.e., can’t reorder or buffer requests
    - Must allocate blocks from free memory
        - allocated된 부분에 allocate하면 안됨
        - i.e., can only place allocated blocks in free memory
        allocate하게 되면 free한 memory에 할당
    - Must align blocks so they satisfy all alignment requirements
        - 8-byte (x86) or 16-byte (x86-64) alignment on Linux boxes
        32bit machine -> 8byte, 64bit -> 16byte
        나머지는 unused 부분
    - Can manipulate and modify only free memory
    - Can’t move the allocated blocks once they are malloc’d
        - i.e., compaction is not allowed
    - buffering하지 않기 때문에 순서를 바꾸지도 않음
        - 6-5-4 순서 바꾸어 할당하겠다 하면 허용이 안됨
    

## **Performance Goal: Throughput**

- Given some sequence of malloc and free requests:
어떤 program에서 malloc. & free request
    - $R_0, R_1, ..., R_k, ... , R_{n-1 }$
- Goals: maximize throughput and peak memory utilization
    - These goals are often conflicting
- Throughput:
    - Number of completed requests per unit time
    - throughput을 높인다
    = allocate요청했을 때 immediately respond 즉각 반응
        - → allocate 5개를 빨리 찾고 해당 공간을 할당해주어야 함.
    - free하게 되면, 해당 ptr가 어디있는지 찾아 free시켜야됨
        - 상위 개념에서 보면, allocator
    - Example:
        - 5,000 malloc calls and 5,000 free calls in 10 seconds
        - Throughput is 1,000 operations/second  10
    - 어떤 allocator를 설계해서 5000개의 malloc과 free를 처리
        - → 이를 처리할 수 있는 allocator
        1000개의 operation per second보다 낮아질수도 있음
    
    → 첫 목표 : throughput 높이는 것
    
    - 나중에 설계, 구현을 보면 알 수 있음
        - throughput이 1000보다 높을수도 낮을수도 있다.

## **Performance Goal: Peak Memory Utilization**

앞과 example은 똑같고, 다음과 같은 def를 이해해야 한다.

- Given some sequence of malloc and free requests:
    - $R_0, R_1, ..., R_k, ... , R_{n-1 }$
- Def: Aggregate payload Pk
    - `malloc(p)` results in a block with a payload of p bytes
    - After request $R_k$ has completed, the aggregate payload Pk is the sum of currently allocated payloads
    - payload Pk
        - malloc Pk라고 할 때, 해당 p라는 것은 요청 memory에 block하나를 할당받는데 실제 요청한 data가 저장되는 data 크기
        - Block : 10byte -> 10byte
        - 8 byte alignment
        - k번째까지 malloc을 성공했다 → Pk 현재 allocate
    - 해당 값을 maxmize하여 해당 memory 최대한 사용할 수 있게, 빈 공간을 찾ㅇ아 꾸역꾸역 넣어야 함
- Def: Current heap size Hk
    - hk : Hk까지 요청했을 때 당시 heap의 크기를 가져다 h_k
    - heap 크기가 늘어날
    - Assume Hk is monotonically nondecreasing
    - i.e., heap only grows when allocator uses `sbrk`
- Def: Peak memory utilization after k+1 requests $U_k = \frac{\max_{i\leq k}P_i}{H_k}$
- Programmer는 vm 공간이 내가 가진 가상 memory더라도 실제 가지고 있는 memory 공간이 굉장히 크다고 판단하지만 실제 물리적으로 1GB밖에
    - 내 물리적 공간이 아닌 다른 부분에. 그런 관점에서 최대한 활용해 utilization을 높이 ->
    - 요청할 때마다 쉽게 할당해주는 공간 -> 처리를 높임 한번에 응답할 때 응답 시간을 줄임.
- utilization

## **Fragmentation**

- Poor memory utilization caused by fragmentation
    - internal fragmentation
    - external fragmentation

## **Internal Fragmentation** 내부 단편화

- For a given block, **internal fragmentation** occurs if payload is smaller than block size
    
    ![Untitled](10/Untitled%204.png)
    
- Caused by
    - Overhead of maintaining heap data structures
        
        heap을 관리하는 data structure의 overhead
        
    - Padding for alignment purposes
    - Explicit policy decisions
        - 큰 block을 return해야 하는 경우
        (e.g., to return a big block to satisfy a small request)
- Depends only on the pattern of previous requests
    
    이전에 어떤 게 왔는지에 따라 왕왕 생김
    
    - Thus, easy to measure

- 10byte + 나머지 6byte 앞과 뒤에 나뉘어 짐
    - 나머지 6byte는 사용되지 않음
- 구현에 따라 internal fragmentation을 상당히 줄일 수 있음

## **External Fragmentation**

![Untitled](10/Untitled%205.png)

- 연속적으로 이어진 공간이 5개, 2개임 (free(p2))

- Occurs when there is enough aggregate heap memory, but no single free block is large enough - external fragmentation 발생하면 할당하지 못하는 경우가 발생할 수 있음
- Depends on the pattern of future requests
미래에 어떤 요청을 하는지에 따라 생길수도 생기지 않을수도 있음
    - Thus, difficult to measure

## **Implementation Issues**

memory allocator를 어떻게 design하고 구현하느냐에 따라 여러가지 issue

- naive한 구현 시
    - 앞의 그림처럼 이런식으로 heap을 가져다 large array로 보고, 처음엔 p를 0으로 두어 구현됨
    - 오래된 p를 stack에 집어 넣고 (push(p)), oldptr = pop(p)
    
    → 둘을 가져다 allocate상태로 만들어 주고 나머지 뒤는 free하게 만들어 줌.
    
    - Ptr = malloc(2) : 이전에 있던 결국은 oldptr
    - malloc할 때마다 앞으로 가는 쪽을 보면 무조건 free
    - malloc (3)을 한다고 하면 2+3 부분을 가리키고 old ptr를 return
    - fast: 뒤로 forwarding하면서 return하면 됨

→ 그러나, 이렇게 구현하면 memory ineffective

- allocate가 이렇게 되어있는 상태에서 free한다고 가정하자.
- point ) 뒤로는 절대로 안 돌아간다.
    - 중간중간 fere하게 되면 빈 공간이 생기는데 다시 절대 reuse할 수 있는 구현이 아님. 말 그대로 naive implementation의 경우임
    - feed forward하며 alloc을 효율적으로 할 수 있으나 memory utilization
        - → throughput은 좋으나 peek는 성능이 좋지 않아짐.
    - 그냥 구동만 하면 되는거 아니냐? 하지만, memroy가 굉장히 중요한 작은 embedded환경에서는 절대 허용되지 않는다.
    - system 관점에서 보면 작은 memory를 어떻게 utilize해서 꾸겨 넣을 것인가에 좌우된다.
- 1byte, 2byte로 표현할 수 있는 것을 32byte
    
    → memory utilization이 maximize되지 않는다.
    

malloc을 직접 구현하며 ptr를 exercise, memory allocator 개념을 이해할 수 있을 것

- How do we know how much memory to free given just a pointer?
    
    1. Pointer
    
    - ptr로 가리키고 있는 memory object를 free시킨다고 할 때, 그 memory object 크기가 얼만큼 되는지 어떻게 아느냐?
    - free(ptr) : ptr이. 가리키는 object가 있는데, 이 object가 32byte라 하면
    
    free시키는 시점에 obj가 32b라는 것을 어떻게 아느냐?
    
- How do we keep track of the free blocks?
    
    2. 어떤 free한 공간이 있는지 track하기
    
    - heap이라는 공간은 앞서 이야기한대로 쭉 vm에서 allocate-free된 형태로 남아있다.
    - free한 space들을 tracking해야 하는데 이를 어떻게 해야 하느냐?
    - block 크기들은 가변적
- What do we do with the extra space when allocating a structure that is smaller than the free block it is placed in?
    
    3. free block에 allocate하려고 하는데 자료구조가 내가 받은 free block보다 작아서 그 block을 다 사용하지 못한다고 하면 extra space는 어떻게 할 것이냐?
    
    Ex. Free block하나를 할당 받았고 그사이즈는 64byte이다.
    
    내가 쓰려고 하는 data 공간은 48byte이다. 그럼 나머지 부분은 어떻게 할 것이냐?
    
    3. 얼마만큼 free하면 되는지도 알고 있어야 함
    
    free한 공간보다 작을 때 padding을 붙이거나 다른 공간을 사용하거나
    
    - 어떤 것을 가져다 선택할 것인가
    - allocate된 것을 pool로 어떻게 return할 것인가.
- How do we pick a block to use for allocation -- many might fit?
    
    4. allocation하려 하는데 free block이 여러개 있다. 그럼 어떤 block을 선택할 것이냐?
    
    - 모두 free block인데 어떤 block을 선택할 것이냐?
- How do we reinsert freed block?
    
    5. free block 어떻게 return할 것이냐?
    

## **Knowing How Much to Free**

For issue 1

- block 맨 앞에다가 word size를 4byte라고 하자.
4byte로 되어 있는 크기에 무엇을 집어넣을 것인지 : header field, header
- block의 크기를 가져다 적을 것이다.
- Standard method
    - Keep the length of a block in the word preceding the block.
        - This word is often called the header field or header
    - Requires an extra word for every allocated block p0 p0 = malloc(4) 5 free(p0)  16 block size payload

![Untitled](10/Untitled%206.png)

block의 크기 만큼을 free시켜주면 됨

p0가 가리키는 block의 크기가 5였음을

P0가 가리키는 word를 읽어 봄으로써 사이즈를 읽어내 deallocate할 수 있다.

## **Keeping Track of Free Blocks**

For 2nd issue

- free block을 어떻게 track할 것인가.
- **Method 1: Implicit list using length—links all blocks**
    
    ![Untitled](10/Untitled%207.png)
    
    - block의 크기를 가지고 free block을 track하는 방법
    - 모든 block들 : 5 free block / 4 alloc block / 6 free block / 2 alloc block
    - 맨 앞 word size 앞에 적혀진 size
        - Ptr를 heap의 맨 시작부터 읽기 시작하면 첫 번째 block의 크기이기 때문에 p에서 5를 더해주면 그 다음 block의 첫 번째 앞을 보게 되고, 4를 더해주면 다음 block 맨앞, 2 더해주면 다음 block맨 앞
        
        → logically linking 가능 : ptr값이 있으면 다음 다음 block을 계속하여 traverse 가능
        
    - 모든 block을 traverse해보면서 free var tracking 가능
    - 단점) method1 : 모든 block을 linking하기 때문에 실제 우리가 필요한 건 allocated block보다는 free bock이다. 심지어 allocated block까지 traverse해야 하는 단점.
        - 직접 free한 block으로 가고 싶지만 못한다 (모든 block 앞의 크기 정보를 가지고 모든 block을 traverse하다보니 linking할수는 있지만 free block만을 traverse는 불가능)
- **Method 2: Explicit list among the free blocks using pointers 5462**
    
    ![Untitled](10/Untitled%208.png)
    
    - 앞의 것과는 다름 : free block을 track하기 위해 link로 만든 것을 활용해서 모든 block을 traverse하는 앞의 것 / 앞의 size 정보를 사용해 다음 block으로 갔다
    - 그 다음의 free block을 가리키는 word를 할당함. :주소 정보 다음 word에 그 다음 free word의 주소값을 가지고 있다.
    - 앞의 모든 block들을 free
    
    단점) extra로 그 다음 free block을 가리키는 만큼의 extra memory 비용을 지불해야 함
    
    - > 공간 효율이 떨어짐
    
- **Method 3: Segregated free list**
    - Different free lists for different size classes
    - suffistigated than 1, 2 method
    - free block size가 다양한 block들
        - → 그러면 free block들을 관리할 때, 같은 크기의 group으로 묶어 class로 만들자.
        - 내가 요청하는 block의 크기가 작다고 하면, class1에 해당하는 block 중 하나를 찾고 크기가 크다고 하면 3에 해당하는 block 중 탐색
- **Method 4: Blocks sorted by size**
    - Can use a balanced tree (e.g. Red-Black tree) with pointers within each free block, and the length used as a key  17
    - 모든 block size로 balanced tree를 만들되 각각을 free block을 맏늘도록 하고 요구되는 크기보다 크기에 딱 맞는, sorted order로 되어 있는 balanced tree를 찾아낸다
    - → complicated, intelligent
    

# **Implicit free lists**

- 크기가 가변적인 block : 어떤 block은 가변적이더라도 어떤 chunk에 alignment 되어 있음 : 8byte-16byte-24byte 등 multiples of 8byte
    - → 5byte malloc(payload) + 3byte padding = 8byte mallc
- header : payload의 크기
- LSB : allocated or deallocated bit (1 : allocated / 0 : free)

For example, if a block is imposed a double word alignemnt, the block size is a multiple of 8 and the 3 low order bits of the block size are always zero.

- 31 … 4 3 2 1 0 / 29bit를 가지고 size를 표현함
- 맨 끝 2 - 1 - 0 : 0th bit에서는 allocate or deallocate indicator, 실제로 2-1th bit는 unused형태
- Heap이라는 것은 결국 block의 집합이다 : free block 3 / block 3

## **Method 1: Implicit List**

Implicit list

- 두 가지 정보를 가지고 있어야 함.
    - (1) 각각의 block은 status 정보를 가지고 있어야 함 : free or allocated
    - (2) traverse하기 위해서는 ptr를 가지고 ptr size만큼을 더해주어야 하기에 각각의 size 정보를 가지고 있어야 함 : size
- (가정) 주어진 block에 대해 앞서 이야기한 대로, size 정보가 앞에 존재한다.
status 정보도 필요해서 각각을 가져다가 8byte씩 할당했다고 가정하자. (Word size : 8byte)
    - 두 가지를 저장하기 위해 16byte spatial cost 지불
- size와 allocation status를 16byte가 아니라 8byte만큼 저장할 수 있지 않을까?

→ 각각 8byte word로 저장한다고 하면,

- 1 million block * 2 * 8 = 16MB / 1 million block * 8 = 8MB
    - 8MB만큼의 비용을 더 지불해야 함. : 16byte로 저장해야 하는걸 8byte로 지불하자
    - Block들은 alignment되어 있는데, 어떤 system architecture이느냐에 따라서 8byte or 16byte alignment되어 있을 것이다.
- 8 byte alignment되어 있다면 : block size가 8-16-24-32-48-56-64-…의 형식
    - 8 byte를 2진법으로 표현한다고 하면, 8byte = 64bit 00..001000
        - 16byte = 0…010000
        - 32byte = 0…011000
    - → 최하단 세 bit들은 항상 0이다.
    - → 그러면 이 안에서, 맨 마지막 bit를 allocated. Free status를 구분하기 위해 flag로 사용해도 되지 않을까? 어차피 alignment되어 있기 때문에 사용하지 않으니까, 저 장소에 embedding하자
- 1 : allocated / 0 : free / 나머지 bit는 size로 쓰면 됨
- 8byte가 alignment되는 unit이고 malloc(5)라고 하면
1byte : size / allocation status
나머지 5byte : payload
그리고 2byte : padding
(Spatial cost를 다음과 같이 줄일 수 있다)

- For each block we need both **size** and **allocation status**
    - Could store this information in two words: wasteful!
- Standard trick
    - If blocks are aligned, some low-order address bits are always 0
    - Instead of storing an always-0 bit, use it as a allocated/free flag
    - When reading size word, must mask out this bit

![Format of allocated and free blocks](10/Untitled%209.png)

Format of allocated and free blocks

## **Detailed Implicit Free List Example**

![Untitled](10/Untitled%2010.png)

- word size : 4byte인 경우에 대해 설명해보자.
    - block 각각이 8/16/32/16byte, word size information / LSB : 0 free 1 allocated
    - dotted :double word에 대해 align된 point
    - payload는 2word니까 allocate되어 있지만 사용되지 않는 부분

- advantage : simplicity
- Disadvantage :
    - any operation requires a search of the free list, such a placing allocated blocks
    - Free block을 찾기 위해 traverse를 계속 해야 함. -> free list를 찾아야 하는 time overhead

여러 free block이 있을 때 어떤걸 선택할 것이냐?

## **Implicit List: Finding a Free Block**

- First fit:
    - Search list from beginning, choose first free block that fits:
        - block 처음부터 scanning을 쭉 함
        
        ```c
        p = start;
        while ((p < end) && // not passed end
        			((*p & 1) || // already 
        			allocated(*p <= len)))  // too small 
        			p = p + (*p & -2); // goto next block(word addressed)
        ```
        
        - allocate되어 있다고 하면 무조건 skip하는 세번째 condition
            - word 크기가 4byte였다고 하면, 32bit 중 31bit (size) + 1bit(1 or 0)
            - 0을 가져다 만드려면 and operation을 할 때
            - 11…110으로 된 operation : 2’s complement하면 -2를 가지고 and를 취하게 되면, 결국 *p ptr 값이 무슨 값이든간에 lsb는 무조건 0이 됨
                - → 그 만큼의 크기를 더해준다는 의미로 jump해줌
            
            만일) 1이라는 값과 non allocate였으면 *p<=len 확인
            
    - Can take linear time in total number of blocks (allocated and free)
    항상 앞에서부터 시작해야 하기에 느림
    - In practice it can cause “splinters” at beginning of list
    - free block을 찾기 위해 쭉 들어가 있는데 들어와서 찾고 들어와서 찾고 …
        - 찾아서 내가 필요로 하는 크기보다 작은것을 요구했음에도 처음부터 또 들어가 찾아내고
    - → 항상 내가 한 번 찾으면 처음부터 search하고, 처음에 검색하고 그다음부터 쭉
    - allocation하기 전 free 한 부분을 찾는 것이 시작
- **Next fit:**
    - Like first fit, but search list starting where previous search finished
        - 처음부터 찾는게 아니라, 다음 부터 찾음
    - Should often be faster than first fit: avoids re-scanning unhelpful blocks
        - next fit은 앞에서부터 찾을 필요가 없어 일반적으로 first fit보다 빠름 : rescanning할 필요는 없으니까 반드시 그렇진 않음 : 이전에 할당한 것으로부터 free space가 생기면 앞에서 찾는게 더 빠를 수도 있음
    - Some research suggests that fragmentation is worse
- **Best fit:**
    - Search the list, choose the **best** free block: fits, with fewest bytes left over
    - Keeps fragments small—usually improves memory utilization
        - 공간적 utilization의 최대화 : 내가 요구하는 memory를 가장 근접하게, allocate했을 때 free block이 사용되지 않는 internal fragmentation을 찾는 것 : best fit
        - left over 되는 block이 작은 것들
    - Will typically run slower than first fit
        - 최선의 선택을 위해 처음부터 끝까지 다 뒤져봄 -> 느리다.

## **Implicit List: Allocating in Free Block**

- free block을 찾아 splitting
- Addblock(p,4) 6개가 빈 공간이기 때문에 4만큼을 그 공간에 할당
    - allocate : 4 / freed 2 (나누어 주어야 함)
- Allocating in a free block: splitting
    - Since allocated space might be smaller than free space, we might want to split the block
    
    ```c
    void addblock(ptr p, int len)
    {
        intnewsize = ((len + 1) >> 1) << 1;// round up to even 
        int oldsize = *p & -2;// mask out low bit
        *p = newsize | 1;// set new length
        if (newsize < oldsize)
    				*(p+newsize) = oldsize - newsize;  22   // set length in remaining 
    } // part of block 
    ```
    
    - 예전에 있던 oldsize = 6
    - 맨 밑 low bit를 mask out해주고 
    새로운 size에는 일단 allocate되어 있으니가 allocate 되어 있음을 표시해줌
    - len = 4. Len =5를 집어 넣어보고 round up to even을 확인해봐라.
        - double word alignment되어 4 byte의 multiple인데 4, 8, 12  -> even number를 곱해줘야 함
        - Masking이라고 해서 맨 끝에 alignment를 맞추어 줌 (mask out low bit)

## **Implicit List: Freeing a Block**

allocate : 비교적 simple

- 내가 만족하는 free한 block을 찾을 때 까지, 앞에서 끝까지 다 scan해서 최적의 여분을 가장 적은 frag을 남기는 free를 찾던, allocation 한 후 split하면 됨.
- Simplest implementation:
    - Need only clear the “allocated” flag
    
    ```c
    void free_block(ptr p) { 
    		*p = *p & -2 
    }
    ```
    
- But can lead to “false fragmentation”
    - free는 복잡합 : false fragmentation
        - 4를 free시키고 싶으면 4 word를 return해야 함
        - memory allocator 입장에서 free를 볼 때, 최대한 합쳐서 큰 chunk를 많이 만들어 놓는게 좋다
    - malloc안에서 sbrk를 호출하여 brk라는 top of the heap를 늘려 줌
        - → heap
        - Contiguous한 공간이 없어 false fragmentation :
            - 공간이 있음에도 없다고 보고받아 sbrk를 통해 늘려주게 됨.
        - 그렇지 말고 fragment들끼리 합칠 수 있으면 합쳐야 함 : correlasing
        - : 메모리 공간 효율적으로 사고하기

![Untitled](10/Untitled%2011.png)

free(p)

![Untitled](10/Untitled%2012.png)

malloc(p) → OOPS!

- P부분을 free 시켰다고 하자 : allocate bit를 free로
    - → 지금과 같이 4개 / 2개로 분리됨.
    - → 5개짜리를 allocate할 수 없음 : 연속 5 free block이 없기 때문에 (false fragmentation)
    - 연속적으로 이 2개가 쪼개져있다 보니 allocate할 수 없음
- Oops! There is enough free space, but the allocator won’t be able to find it

## **Implicit List: Coalescing**

- coalesce
    - free할 때 그 다음에 있는 녀석을 합쳐주는 것 Or 내가 free할 때 걔랑도 합쳐 줌
    - pt가 가진 obj가 앞과 뒤를 보고, free인지 확인한 후 합칠지 안 합칠지 결정.
- join한다
    - 내가 free하고자 하는 block의 앞과 뒤를 보고 -> free하다면 합쳐주는 것
- Join **(coalesce)** with next/previous blocks, if they are free
- Coalescing with next block
    
    ![Untitled](10/Untitled%2013.png)
    
    free(p)
    
    ![Untitled](10/Untitled%2014.png)
    
    - free(p) : 가상 주소를 가지고 빈 공간으로 free list를 return하는 상황으로 고려

```c
void free_block(ptr p)
{
    *p = *p & -2; // clear allocated flag 
    next = p + *p; // find next block 
    if ((*next & 1) == 0) // add to this block if 
        *p = *p + *next;    // not allocated 

    }
```

- 코드 설명 :
    - Word size 4인 block : free할 때
    - 4 & -2 연산 : 00000010, 11111101 +1 ->11111110
        - Even한 double word로 alignment하여 값을 뽑아오고
        - 그 다음 block이 현재 pointer p에서 값을 더한 것이 다음 block
        - 그 block에 and operation : 다음 block이 free라고 하면
    - → 그 값은 4에다가 next에 해당하는 2를 더해 값을 update해줌
    - → 다음 block에 있는 현재 free하단 block이랑 합침.
- But how do we coalesce with previous block?
    - 이전 block은 어떻게 합칠 것인가? (이 슬라이드에서는 forward)
    - 이전 block이 free인지를 봐야하는데, 이전 block을 reference할 수 있는 방법이 없다 : 문제 발생

## **Implicit List: Bidirectional Coalescing**

간단하지만 유명하다

![Untitled](10/Untitled%2015.png)

![Untitled](10/Untitled%2016.png)

- **Boundary tags [Knuth73] - bidirectional coalescing : 앞, 뒤 모두를 합치는 방법**
    - Replicate size/allocated word at “bottom” (end) of free blocks
    - Allows us to traverse the “list” backwards, but requires extra space
    - Important and general technique!
    
- 원래 : 그 다음으로 forward하며 traverse하는 방법은 현재 block의 크기 header를 알면 그 다음 block의 시작 주소를 알 수 있었음
    - double word alignment를 해서 그 다음이 누군지 addressing할 수 있었음
- header라는 block에 이 block의 크기라는 hint를 심어둔 것.
    - 이전 block size를 알 수 있는 방법이 있다면 이전 block을 찾아볼 수 있음
    - → header가 아니라 footer를 가져다 boundary tag 삽입
    
    : 각 block의 bottom에다 size에 해당하는 word만큼을 복제해보자
    
- *p : p를 free한다고 하자
    - forward :
    - backward.: 이전의 word의 ptr 값을 보면, 이전 block의 크기를 알 수 있기 때문에 전의 시작 주소를 알 수 있음 -> LSB 값을 보고 이전 block이 free인지 allocate인지 알 수 있음
- 단점 : extra space 필요
    - 사실 backward로 갈 수 있는 size에 대한 정보를 word 크기만큼 추가적으로 사용한 것 : 실제 data를 저장할 공간 payload를 backward traverse 가능한 meta info로 활용한 것이기에 실제 extra space heap space는 줄게 됨.

## **Constant Time Coalescing Case**

coalescing 하는 방법

- heap안에서 이전과 이후,

각각으 ㅣ상황을 네가지로 분리

1. 이전 block과 다음 block이 allocate되어 있는 block

2. Alloc - free

3. Free - alloc

4. Free - free

![Untitled](10/Untitled%2017.png)

### Case 1

Alloc-alloc
-> coalescing자체가 발생하지 않음

![Untitled](10/Untitled%2018.png)

### Case 2

Allocate - free
-> n(1), m2(0)이므로 n+m2 (0) merge

![Untitled](10/Untitled%2019.png)

### Case 3

case 2와 동일
-> Size 정보 update

![Untitled](10/Untitled%2020.png)

### Case 4

앞, 뒤 둘다 free 인 경우에 각각의 block size를 더해 n + m1 + m2 (0)

- 이미 allocate 되어 있었다 하면 앞 뒤를 합쳐 first fragmentation 최고화, contiguous 하게 최대한 크게 만들어서 free list를 구축해 free block 발견

![Untitled](10/Untitled%2021.png)

## **Disadvantages of Boundary Tags**

- Internal fragmentation
    
    이전에는 bounday point가 없었다.
    
    extra로 padding을 payload로 사용할 수 있었는데 동일한 정보임에도 duplicate를 해야 함
    
    → internal fragmentation 발생 확률 커짐
    
    만일 그래프같은 연산을 한다면 node, edge에서 node가 작은 크기의 memory alloc해준다고 하자
    
    → 수 byte를 alloc하지만 앞 뒤 header footer를 둠으로서 상대적으로 payload 크기가 작아 extra overhead를 지불해야 함
    
- Can it be optimized?
    - Which blocks need the footer tag?
    - What does that mean?

- footer를 사용하 back traverse
- free할 때 관심있는 것은 : 이전 block이 free일 때만 corelasing
    - 앞 block이 allocate인지 구분 :
- Free(p) : boundary tag를 보고 나서 allocate/free 구분
    - block 자체가 free였으면 바로 이전까지는 쉽게 갈 수 있음
    - 만일 allocate되어있다면 합칠 이유가 없음 
    (allocate되어 있으면 footer 필요 없음 = 앞으로 갈 일이 없음)

→ trick : 이전에 있는게 free 아니라면 footer가 굳이 필요없다.

- 현재 이전의 block이 free인지 아닌지 식별할 bit를 보고, 만일 이전의 block이 allocate되어 있다면 안에서 질책
- free block -> footer / allocated -> footer 굳이 필요 없음.
- free인지는 그 이전으로 돌아가야 함.

# **Summary of Key Allocator Policies**

- Placement policy:
    - First-fit, next-fit, best-fit, etc.
    - Trades off lower throughput for less fragmentation
    - Interesting observation: segregated free lists (next lecture) approximate a best fit placement policy without having to search entire free list

- Splitting policy:
    - When do we go ahead and split free blocks?
    - How much internal fragmentation are we willing to tolerate?
- Coalescing policy:
    - Immediate coalescing: coalesce each time free is called
    - Deferred coalescing: try to improve performance of free by deferring coalescing until needed. Examples:
        - Coalesce as you scan the free list for malloc
        - Coalesce when the amount of external fragmentation reaches some 32 threshold

여러개가 동시에 free한다고 하면 새로 써 주고 free

필요하면 처믐부터 scan해가며 할 수도 있을 것

## **Implicit Lists: Summary**

- Implementation: very simple
- Allocate cost:
    - linear time worst case
- Free cost:
    - constant time worst case
- even with coalescing
- Memory usage:
    - will depend on placement policy
    - First-fit, next-fit or best-fit
- Not used in practice for malloc/free because of linear-time allocation
    - used in many special purpose applications
- However, the concepts of splitting and boundary tag coalescing 33 are general to all allocators