---
layout: article
title: [SP] 11. Dynamic Memory Allocation: Advanced Concepts
tags: System Programming
category: System Programming
picture_frame: shadow
use_math: true
---

# 11. Dynamic Memory Allocation: Advanced Concepts


Dynamic Memory Allocation: Advanced Concepts
CSE4100: System Programming
Youngjae Kim (PhD)

# Explicit Free List

## Keeping Track of Free Blocks

![Untitled](11/Untitled.png)

1. forward traverse : 모든 것을 scan하여 뒤져야 free block을 찾을 수 있음
    - adv ) spatial : 4/5만큼을 사용 가능
    - free block을 찾기 위해 모두 뒤져 정책에 찾아 free block
2. explicit free list : Size가 아니라 word를 가져다 다음의 free block을 가리키는 방법
    - adv) linear search할 때 method2 : allocated block skip 가능해서 빠름
    - disadv ) successor에 해당하는 3/5만큼을 사용 가능
3. Class 마다 linked list로 연결 - 굉장히 빠르게 가장 fit할수 있는 block을 빠르게 찾을 수 있는 기법.
4. 크기에 따라 븐류한 block

## Explicit Free Lists

- Header, footer : coleascing - 앞과 뒤
- successor- precedesor : Explicit한 방법으로 free list 관리 가능.

![Untitled](11/Untitled_1.png)

- Maintain list(s) of free blocks, not all blocks
- The “next” free block could be anywhere
    - So we need to store forward/back pointers, not just sizes
- Still need boundary tags for coalescing
- Luckily we track only free blocks, so we can use payload area
    - lucky하게도 free list만 관리 가능 → payload 영역 사용의 단점
- Logically:
    ![Untitled](11/Untitled_2.png)
- Physically: blocks can be in any order AB
    ![Untitled](11/Untitled_3.png)
    

## Allocating From Explicit Free Lists

- Doubly linked list로 연결되어있다고 하자 :
    - 회색 해당한 만큼을 allocate → free list에 있을 필요 없으므로 split하고 남은 block과 연결해줌
- ptr update를 통해 앞에서부터/뒤에서부터 연결, splitting

![Untitled](11/Untitled_4.png)

![Untitled](11/Untitled_5.png)

## Freeing With Explicit Free Lists

- Insertion policy: Where in the free list do you put a newly freed block?
- 복잡 : free list를 관리해야 하는데 가져다 return하게 되면 insertion해야 하는데 그 policy
    - → search time overhead가 큼
- LIFO (last-in-first-out) policy
    - Insert freed block at the beginning of the free list
    맨 앞에다 free block을 가져다 연결시켜줌
    - Pro: simple and constant time
    - Con: studies suggest fragmentation is worse than address ordered
- Address-ordered policy
    - Insert freed blocks so that free list blocks are always in address order:
        - `addr(prev) < addr(curr) < addr(next)`
    - Con: requires search
    - Pro: studies suggest fragmentation is lower than LIFO    
    - insert되는 block들이 작은 주소부터 높은 주소 순으로 sorting
        - → addr ordered 방법보다 fragmentation 경감
    - 실제 allocation할 때 어떤 block을 allocation하느냐를 Allocation해야 하기 때문에, address에 따라 순서대로 search하며 선택해야 하는 문제가 있음

## Freeing With a LIFO Policy (Case 1)

- 다른 free한 block을 가리킨다고 하자.
- block이 memory상 어딘가 존재할 것 : 앞 뒤에 있는 것을 header-footer 방법을 통해 조사함
    - → 앞뒤 allocated면 생각보다 간단하게
- header 다음 next에 해당하는 ptr를 다음을 가르키게 함
- 즉) 앞 뒤가 allocate되면 상대적으로 simple

![Untitled](11/Untitled_6.png)

- Insert the freed block at the root of the list
    
    ![Untitled](11/Untitled_7.png)
    

## Freeing With a LIFO Policy (Case 2)

![Untitled](11/Untitled_8.png)

- Splice out successor block, coalesce both memory blocks and insert the new block at the root of the list
    
    ![Untitled](11/Untitled_9.png)
    

## Freeing With a LIFO Policy (Case 3)

![Untitled](11/Untitled_10.png)

- Splice out predecessor block, coalesce both memory blocks, and insert the new block at the root of the list
    
    ![Untitled](11/Untitled_11.png)
    

## Freeing With a LIFO Policy (Case 4)

![Untitled](11/Untitled_12.png)

- Splice out predecessor and successor blocks, coalesce all 3 memory blocks and insert the new block at the root of the list
    
    ![Untitled](11/Untitled_13.png)
    

## Explicit List Summary

> 모든 block을 뒤지지 않아 좀 더 빠른 속도를 가지지만 좀 복잡함 : free state 관리 구현

- block의 크기가 최소 5word 이상이어야 함
- Comparison to implicit list:
    - Allocate is linear time in number of free blocks instead of all blocks
        - Much faster when most of the memory is full
    - Slightly more complicated allocate and free since needs to splice blocks in and out of the list
    - Some extra space for the links (2 extra words needed for each block)
        - Does this increase internal fragmentation?
- Most common use of linked lists is in conjunction with segregated free lists
Free block들을 ptr로 연결하여 ll로 관리함
    - Keep multiple linked lists of different size classes, or possibly for different types of objects
    - LIFO queue
    - 단점 : free block을 link했지만 각 block 크기 가변적
        - → fragmentation이 작은 block을 찾으려면 best fit policy에 따라 처음부터 끝까지 뒤져봐야 함.
        

# Segregated Free List

- 어떻게 segregate : n개의 size class를 만들어 n개의 free block을 안에다 집합
    - → 다른 크기의 size class에 따라서 각각 다른 free list 관리하는 기법
    

## Segregated List (Seglist) Allocators

![Untitled](11/Untitled_14.png)

- Each size class of blocks has its own free list
- Often have separate classes for each small size
- For larger sizes: One class for each two-power size
- 이전 explicit free list : 하나의 단일 lifo queue
    - → 지금 : 각각의 size을 가지는 list
- 장점 : best fit - fragmentation 최소화할 수 있는 block을 찾았다면 여기서는 class 별 로 나누었기에 내게 맞는 class를 찾을 수 있는 기법

## Seglist Allocator

`malloc(n)`
> 새롭게 요청된 메모리로부터 n개의 byte block 할당.
![Untitled](11/Untitled_15.png)
- Given an array of free lists, each one for some size class
- To allocate a block of size n:
    - Search appropriate free list for block of size m > n
    처음부터 찾는다 : m>n인 m의 크기를 가진 free list를 찾는다
        - ex. malloc(4)라고 하면 4를 수용하는 block을 가진 class를 찾음
            - → block이 있는 경우는 block할당, 나머지 frag는 집어넣음
            - malloc(6)을 요청했다면 5-8 class에서 할당받는다.
                - → 8 size block 중 나머지 2개는 1-2class로 이동한다.
            - (구현 별 문제 : 나머지 fragment를 어떻게 처리할 것인가. 앞에다 붙이는게 빠를수도 있다 (LIFO니까))
        - m payload, n 실제 class의 size
        Payload를 고려하는 것에 따라 m>=n
    - If an appropriate block is found:
        - Split block and place fragment on appropriate list (optional)
    - If no block is found, try next larger class
    만일 block size가 없다면 더 큰 class로 이동
    - Repeat until block is found
- If no block is found:
    - Request additional heap memory from OS (using sbrk())
    - Allocate block of n bytes from this new memory
    - Place remainder as a single free block in largest size class.
- To free a block:
    - Coalesce and place on appropriate list
        - block의 free : 앞, 뒤를 보고 coalesce
- Advantages of seglist allocators
    - **Higher throughput) malloc을 할 때 성능이 좋다**
        - log time for power-of-two size classes
        - size class로 나뉘어져 있기에 fragmentation을 최소화하면서 적합한 class를 빨리 찾을 수 있다.
    - **Better memory utilization**
        - First-fit search of segregated free list approximates a best-fit search of entire heap.
            - 앞에서부터 찾더라도 나보다도 큰 공간이지만 나중에 보니 fragmentation을 최소화할 수 있는 block이 있을 수도 있다.
        - Extreme case: Giving each block its own size class is equivalent to best-fit.
            - 모든 각각의 block마다 size class를 두고 나서 best fit 해당할수도 있으나 class를 너무 많이 뽑아야 함.

## More Info on Allocators

- D. Knuth, “The Art of Computer Programming”, 2nd edition, Addison Wesley, 1973
    - The classic reference on dynamic storage allocation
- Wilson et al, “Dynamic Storage Allocation: A Survey and Critical Review”, Proc. 1995 Int’l Workshop on Memory Management, Kinross, Scotland, Sept, 1995.
    - Comprehensive survey

# Garbage collection

## Implicit Memory Management: Garbage Collection

> integer pointer
- int type 128 byte (32 * 4byte) (heap에 할당)
- stack에 할당 (local variable)
- address space를 보면
    ![Untitled](11/Untitled_16.png)
> 실제 사용하지 않는 공간임에도 불구하고 자리를 차지함
- garbage collection : 자동으로 heap에 할당된 storage들에 대한 reclamation
- Garbage collection: automatic reclamation of heap-allocated storage—application never has to free
    
    ```c
    void foo() {
    		int *p = malloc(128);
    		return; /** p block is now garbage */
    }
    ```
- Common in many dynamic languages:
    - Python, Ruby, Java, Perl, ML, Lisp, Mathematica
- Variants (“conservative” garbage collectors) exist for C and C++
    - However, cannot necessarily collect all garbage

## Garbage Collection

> c에서의 garbage collection 구현의 어려움
- How does the memory manager know when memory can be freed?
내가 할당한 memory allocation이 runtime시에 언제 free될 것이냐
    - In general we cannot know what is going to be used in the future since it depends on conditionals
    - But we can tell that certain blocks cannot be used if there are no pointers to them
    - = program이 어떻게 실행될지 execution order를 정확히 모르면 모른다.
    그러나 적어도, heap에 할당된 memory object를 point하는 ptr가 없다면 해당 allocated block은 사용되지 않는다.
    - (point하는 ptr가 없으니까 나중에 garbage collect해도 되는구나)
- Must make certain assumptions about pointers    
    1. memory int type에 있는 변수 안의 값이 ptr 일수도 아닐수도 있다.    
        - Memory manager can distinguish pointers from non-pointers
        - ptr 는 다른 memory obj를 가리키는 address일수도, int일수도 있으나 이를 구분할 방법은 c에서는 없음.
        - memory manager가 할 수 있다고 가정
    2. 어떤 block이든간에 시작을 point함
        - All pointers point to the start of a block
    3. type casting같이 ptr 숨김을 못한다.
        - Cannot hide pointers
        - (e.g., by coercing them to an int, and then back again)

## Classical GC Algorithms

- Mark-and-sweep collection (McCarthy, 1960)
    - Does not move blocks (unless you also “compact”)
- Reference counting (Collins, 1960)
    - Does not move blocks (not discussed)
- Copying collection (Minsky, 1963)
    - Moves blocks (not discussed)
- Generational Collectors (Lieberman and Hewitt, 1983)
    - Collection based on lifetimes
        - Most allocations become garbage very soon
        - So focus reclamation work on zones of memory recently allocated
- For more information:
    - Jones and Lin, “Garbage Collection: Algorithms for Automatic Dynamic Memory”, John Wiley & Sons, 1996. Bryant and O’Hallaron, Computer Systems: A Programmer’s Perspective, Third Edition

## Memory as a Graph
> grapy로 표현 : heap memory object로 만든 direct graph
- 어떤 ds를 만들었는데 heap 안에 있는 object를 stack밖에서 point하는 경우
    - root node는 heap 안에 있는 것을 가리키는 형상
- direct graph :
    - 모종의 자료구조로 인해 binary tree를 heap 안에서 만들었다고 하면 Root로부터 시작하여 heap 안의 object를 point하는 direct graph
- 어느 순간에 가게 되면 외부에서 가리키는 ptr이 없어지는 경우도 존재.
- ex. heap에는 이미 할당되어 있지만 함수가 return되며 point하는 local var이 사라짐
    - → not-reachable garbage 생성
    - heap node는 이런식으로 tree로 표현되지만 실제로는 1dim array : 어떤 부분은 free하고 어떤 부분은 allocated, 어떤 부분은 reachable-not reachable
- 식별할 수 있다면 reachable하지 않는 놈들을 free하면 됨 : mark and sweep algorithm
- We view memory as a directed graph
    - Each block is a node in the graph /  Each pointer is an edge in the graph
    - Locations not in the heap that contain pointers into the heap are called root nodes (e.g. registers, locations on the stack, global variables)
    ![Untitled](11/Untitled_17.png)
    - A node (block) is reachable if there is a path from any root to that node.
    - Non-reachable nodes are garbage (cannot be needed by the application)

## Mark and Sweep Collecting

- Can build on top of malloc/free package
    - Allocate using malloc until you “run out of space”
    - malloc을 지속한다 : 공간이 없을 때까지
        - 공간이 없다 = sbrk를 써서 계속 확장하더라도 malloc이 잘 안되는 경우 memory 공간이 없음
- When out of space:
    - Use extra mark bit in the head of each block
        - Header에다가 extra bit를 가져가 mark한다
        - mark : 앞서 root에 해당하는 녀석을 인자로 돌려 reachable한 녀석들을 dfs하여 mark bit set
    - Mark: Start at roots and set mark bit on each reachable block
        - Recursively Mark를 수행하며 각 block에 reachable한 경우에 한하여 mark해줌
            
            → not reachable은 allocated되어 있지만 mark bit이 set되어있지 않음
            
    - Sweep: Scan all blocks and free blocks that are not marked root
        - 1dim 평면의 array의 처음부터 끝 brk까지 scan하며 mark되지 않은 block에 대하여 free
    
    ![Untitled](11/Untitled_18.png)
    
    - Before mark - free list를 pt하는게 아닌 application 관점에서 앞서의 tree를 구현
    - after mark - rechable한 경우 mark bit을 setting해줌
    - after sweep - not rechable 들은 free를 해줌 (allocated but not marked)

## Assumptions For a Simple Implementation

- Application
    - `new(n)`: returns pointer to new block with all locations cleared
        - New(n) : heap memory obj를 할당하고 ptr를 return
    - `read(b,i)`: read location i of block b into register
        - Read(), block b에서 i번째에 해당하는 값을 읽어 register에 집어넣음
    - `write(b,i,v)`: write v into location i of block b
        - write(b,i,v) : block b의 i번째에 v를 쓴다.
- Each block will have a header word
앞에는 header word가 있다고 가정하고 collector를 구현하자.
    - addressed as b[-1], for a block b
    - Used for different purposes in different collectors
- functions/Instructions used by the Garbage Collector
    - `is_ptr(p)`: determines whether p is a pointer
    p가 ptr인지 int인지 구분할 수 없지만 구분된다고 가정하자.
    - `length(b)`: returns the length of block b, not including the header 
    block b에서의 header를 제외한 word개수
    - `get_roots()`: returns all the roots

## Mark and Sweep (cont.)

- Mark using depth-first traversal of the memory graph
    - depth-first traversal : Heap node들은 mark bit가 setting되어있지 않기에 ptr인지 아닌지 체크
        - → ptr 아니었다면 return, ptr라면 밑으로
        - markbit set
    - P의 크기만큼을 보면서 p[i]를 recursive하게 돌아감 → 초록tordls reachable obj에 대해 mark해줌

```c
ptr mark(ptr p)
{
    if (!is_ptr(p))
        return; // do nothing if not pointer
    if (markBitSet(p))
        return;                     // check if already marked
    setMarkBit(p);                  // set the mark bit
    for (i = 0; i < length(p); i++) // call mark on all words
}
mark(p[i]); // in the block return;
```

- Sweep using lengths to find next block
    - P (시작) end (breakpoint)
    ```c
    ptr sweep(ptr p, ptr end)
    {
        while (p < end)
        {
            if markBitSet (p)
                clearMarkBit();
            else if (allocateBitSet(p))
                free(p);
            p += length(p);
        }
    }
    ```

## Conservative Mark & Sweep in C
> c 언어 상 p가 가리키는 것이 memory block 중 ptr block, int block일 수 있음
- memory value type를 저장하지 않기에 우리는 구분 불가능했던 것
- 구현 방법 : block에서 left/right해서 있는 값은 무조건 ptr라고 하고
    - balenced bt를 만들어 point하여 allocate block을 따라가 mark and sweep한다.
    - → conservative하게 정확하게 구분할 수는 없지만 어느정도 determine 한다라고 가정
> 실제 가리키는게 not allocated이지만 allocated라고 가정해놓고 point
- A “conservative garbage collector” for C programs
    - `is_ptr()` determines if a word is a pointer by checking if it points to an allocated block of memory
    - But, in C pointers can point to the middle of a block
        ![Untitled](11/Untitled_19.png)
- So how to find the beginning of the block?
    - Can use a balanced binary tree to keep track of all allocated blocks (key is start-of-block)
    - Balanced-tree pointers can be stored in header (use two additional words)
- Left: smaller addresses  / Right: larger addresses
    
    (Left / right)를 통해 balanced bt를 구축하게 됏을 때 left가 alloc 안되어 있지만 되어 있다는 block이라고 하면 garbage collection할 때 memory leak이 발생하기는 하나 구현할 수는 있다.
    
    ![Untitled](11/Untitled_20.png)
    

# Memory-related perils and pitfalls

## Memory-Related Perils and Pitfalls

- Dereferencing bad pointers
- Reading uninitialized memory
- Overwriting memory
- Referencing nonexistent variables
- Freeing blocks multiple times
- Referencing freed blocks
- Failing to free blocks

## Dereferencing Bad Pointers

- The classic scanf bug
    ```c
    int val;
    ...
    scanf(“%d”, val);
    ```

## Reading Uninitialized Memory

- Assuming that heap data is initialized to zero
    ```c
    /* return y = Ax */
    int *matvec(int **A, int *x)
    {
        int y = malloc(Nsizeof(int));
        int i, j;
        for (i = 0; i < N; i++)
            for (j = 0; j < N; j++)
                y[i] += A[i][j] * x[j];
        return y;
    }
    ```

## Overwriting Memory

- Allocating the (possibly) wrong sized object
    ```c
    int **p;
    p = malloc(Nsizeof(int));
    for (i = 0; i < N; i++)
    {
        p[i] = malloc(Msizeof(int));
    }
    ```

- Off-by-one error
    ```c
    int **p;
    p = malloc(N * sizeof(int));
    for (i = 0; i <= N; i++)
    {
        p[i] = malloc(Msizeof(int));
    }
    ```

- Not checking the max string size
    ```c
    char s[8];
    int i;
    gets(s); /* reads “123456789” from stdin */
    ```

- Basis for classic buffer overflow attacks
- Misunderstanding pointer arithmetic
    ```c
    int *search(int *p, int val)
    {
        while (*p && *p != val)
            p += sizeof(int);
        return p;
    }
    ```

- Referencing a pointer instead of the object it points to
    ```c
    int *BinheapDelete(int **binheap, int *size)
    {
        int *packet;
        packet = binheap[0];
        binheap[0] = binheap[*size - 1];
        *size--;
        Heapify(binheap, *size, 0);
        return (packet);
    }
    ``` 

## Referencing Nonexistent Variables
- Forgetting that local variables disappear when a function returns
    ```c
    int *foo () 
    {
            int val;
            return &val;
    }
    ```

## Freeing Blocks Multiple Times

- Nasty!
    ```c
    x = malloc(N*sizeof(int));
    // <manipulate x>
    free(x);

    y = malloc(M*sizeof(int));
    // <manipulate y>
    free(x);
    ```

## Referencing Freed Blocks

- Evil!
    ```c
    x = malloc(Nsizeof(int));
    // <manipulate x> 
    free(x);
    ... 

    y = malloc(Msizeof(int));
    for (i = 0; i < M; i++)
        y[i] = x[i]++;
    ```

## Failing to Free Blocks (Memory Leaks)

- Slow, long-term killer!
    ```c
    foo()
    {
        int x = malloc(Nsizeof(int));
        ... return;
    }
    ```
- Freeing only part of a data structure
    ```c
    struct list
    {
        int val;
        struct list *next;
    };

    foo()
    {
        struct list *head = malloc(sizeof(struct list));
        head->val = 0;
        head->next = NULL;
    // <create and manipulate the rest of the list>
    ... free(head);
        return;
    }
    ```

## Dealing With Memory Bugs

- Debugger : gdb
    - Good for finding bad pointer dereferences  Hard to detect the other memory bugs
- Data structure consistency checker
    - Runs silently, prints message only on error  Use as a probe to zero in on error
- Binary translator: valgrind
    - Powerful debugging and analysis technique
    - Rewrites text section of executable object file
    - Checks each individual reference at runtime
    - Bad pointers, overwrites, refs outside of allocated block
- glibc malloc contains checking code
    - `setenv MALLOC_CHECK_ 3`