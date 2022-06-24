## layout: article
title: Heap-based Priority Queues and Heap Sort 
tags: alg
category: alg
picture_frame: shadow
use_math: true


Algorithm Design & Analysis  
**[Sub2] Heap-based Priority Queues and Heap Sort**

<!--more-->

# [주제 2] 

- Heap-based Priority Queues and Heap Sort 

## A Variety of Priority Queue Implementations

- [Priority Queue 1: Max(Min) Heap]
- [Priority Queue 2: Min-Max Heap]
- [Priority Queue 3: Heap and Hashing]
- [Priority Queue 4: Deap]
- [Priority Queue 5: Leftist Tree]
- [Priority Queue 6: Binomial Heap]
- [Priority Queue 7: Fibonacci Heap]
- ![image](https://user-images.githubusercontent.com/46957634/122647374-ee4bad00-d15e-11eb-89e4-54a40fe2c7af.png)

### [Job Scheduling Example: Priority Queue]

- Consider the following sequence of requests in an operating system:
- ![image](https://user-images.githubusercontent.com/46957634/122647405-0fac9900-d15f-11eb-93f9-64b1de13a5ee.png)

### Requirement 1

- CPU executes the process with the highest priority first.
- Use a heap structure – a simple max heap. 
- ![image](https://user-images.githubusercontent.com/46957634/122647442-34087580-d15f-11eb-9f5c-13429a351669.png)

```c++
typedef struct _process {
	int proc_id; 
  char *owner; 
  int priority;
} Process;

static Process *_proc_heap; 
static int _proc_heap_size = 0; 
static int _proc_heap_ptr = 0;

int PH_create(int n);        
int PH_full();
int PH_empty();
int PH_insert(Process item);
int PH_delete(Process *item);
```

### Requirement 2

- The priority of processes can be modified after they are placed in the priority queue.

  ```c++
  int H_change_priority(int proc_id, int new_priority);
  ```

  - This function requires locating a particular process in the heap, but the basic heap operations provide no efficient way to do it.

  - Employ an auxiliary data structure such as a hash table that keeps track of the location of each process in the heap structure.

- Once the two requirements are satisfied, the operating system can process the following basic commands efficiently:

  ```assembly
  INSERT  <proc_id>  <owner>  <priority>
  DELETE
  CHANGEPR <proc_id> <new_priority> PRINTHEAP
  END
  ```

## Priority Queue 1: Max(Min) Heap

- ref. [Horowitz 5.6.2] [Neapolitan 7.6]
- Problem
  - The following operations must be performed as mixed in data processing:
    - Store a record with a key in an arbitrary order. 
    - Fetch the record with the current largest key.
- A solution
  - Design a data structure that offers an efficient implementation of the following operations:
- Insert an element with an arbitrary key. 
- Delete an element with the largest key.

### An Array Implementation

- Ref. [Sedgewick 9.2]

```c++
void PQinit();
int PQempty();
void PQinsert(int);
int PQdelmin();
void PQdec(int);

#include<stdlib.h> 
static int *pq;
static int N;
#define MAX_N 10000;

void PQinit(){
    pq = malloc(MAX_N * sizeof(int));
    N = 0;
}
int PQempty(){
    return N == 0;
}
void PQinsert(int v){
    pq[N++] = v;
}

int PQdelmin(){
    int j, min = 0;
    for (j = 1; j < N; j++)
        if (less(pq[min], pq[j]))
            min = j;
    exch(pq[min], pq[N - 1]);
    return pq[--N];
}

int less(int i, int j){
    return **... * *;
}

void exch(int i, int j){
    ...
}

void PQdec(int k){
    ...
}
```

- What will be the worst-case time complexity of each operation?

### Max(Min) Heap: Definitions 

#### Definition 1

- [Horowitz 5.6.2] [Neapolitan 7.6]
- A max(min) heap is a complete binary tree where the key value in each internal node is no smaller(larger) than the key values in its children.

#### Definition 2

- A binary tree has the max(min) heap property if and only if
  - The number of nodes of the tree is either 0 or 1, or
  - 2 For the tree that has at least two nodes, the key in the root is no smaller(larger) than that in each child and the subtree rooted at the child has the max(min) heap property.
- A max(min) heap is a complete binary tree that has the max(min) heap property.

![image](https://user-images.githubusercontent.com/46957634/122647729-9b72f500-d160-11eb-97d7-cef5538c7012.png)

### Brainstorming on Max Heap Operations

- Max Heap Example 
- Deletion Example 1
- ![image](https://user-images.githubusercontent.com/46957634/122647775-d07f4780-d160-11eb-8249-1b1534e8a838.png)
- Deletion Example 2 
- Insertion Example
- ![image](https://user-images.githubusercontent.com/46957634/122647794-ea208f00-d160-11eb-86ac-a6332a4c56a3.png)
- Deletion from a Max Heap

```c++
#define MAX_ELEMENTS 200
#define HEAP_FULL(n) (n == MAX_ELEMENTS-1) #define HEAP_EMPTY(n) (!n)
 typedef struct {
   int key;
   /* other fields */
 } element;
 element heap[MAX_ELEMENTS]; 
int n = 0;
```

- $$ref.$$ [Horowitz 5.6.2]

- $C = log_2 n$

  ```c++
  //최대 히프에 삽입
  void insert_max_heap(element item, int* n){
   int i;
   if(HEAP_FULL(*n)){
    fprintf(stderr,"the heap is FULL\n");
    exit(1);
   }
   i=++(*n);
   while((i != 1) && (item.key > heap[i/2].key)){
    heap[i] = heap[i/2];
    i/=2;
   }
   heap[i] =item;
  }
  
  element delete_max_heap(int* n){
   int parent,child;
   element item,temp;
   if(HEAP_EMPTY(*n)){
    fprintf(stderr,"the heap is EMPTY\n");
    exit(1);
   }
  
   item=heap[1];
   temp=heap[(*n)--];
   parent = 1;
   child = 2;
   while(child <= *n){
    if(child< *n) && (heap[child].key) < heap[child+1].key
     child++;
    if(temp.key >= heap[child.key) break;
  
    heap[parent] =heap[child];
    parent=child;
    child* = 2;
   }
   heap[parent] = temp;
   return item;
  }
  ```

- ![image](https://user-images.githubusercontent.com/46957634/122647830-1cca8780-d161-11eb-9408-a66c9c381603.png)

- 

- ![image](https://user-images.githubusercontent.com/46957634/122647835-22c06880-d161-11eb-9514-ac6a220cb436.png)

- $O(log n)$

### Another Heap Implementation (Min Heap)

- $$ref.$$ [Sedgewick 9.3]

```c++
void PQinit(int);
int PQempty();
void PQinsert(int);
int PQdelmin();
static int *pq;
static int N;
void PQinit(int maxN){
    pq = malloc(maxN * sizeof(int));
    N = 0;
}

int PQempty() { 
    return N == 0; 
}

void PQinsert(int v){
    pq[++N] = v;
    fixUp(pq, N);
}

Item PQdelmin(){
    exch(pq[1], pq[N]);
    fixDown(pq, 1, N - 1);
    return pq[N--];
}

fixUp(int a[], int k){
    while (k > 1 && a[k / 2] > a[k]){
        exch(a[k], a[k / 2]);
        k = k / 2;
    }
}

fixDown(int a[], int k, int N){
    int j;
    while (2 * k <= N){
        j = 2 * k;
        if (j < N && a[j] > a[j + 1])
            j++;
        if (a[k] <= a[j])
            break;
        exch(a[k], a[j]);
        k = j;
    }
}
```

- What will be the worst-case time complexity of each operation?

### Comparisons of Priority Queue Implementations

| Representation        | Insertion    | Deletion     |
| --------------------- | ------------ | ------------ |
| Unordered array       | *O*(1)       | *O*(*n*)     |
| Unordered linked list | *O*(1)       | *O*(*n*)     |
| Sorted array          | *O*(*n*)     | *O*(1)       |
| Sorted linked list    | *O*(*n*)     | *O*(1)       |
| Max heap              | *O*(log *n*) | *O*(log *n*) |

### Heap Sort in C Implementation

- $$ref.$$ [Horowitz 7.7] [Neapolitan 7.6]

- | -         | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
  | --------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
  |           |      |      |      |      |      |      |      |      |      |      |
  | unordered | 26   | 5    | 77   | 1    | 61   | 11   | 26   | 15   | 48   | 19   |
  | max heap  | 77   | 61   | 59   | 48   | 19   | 11   | 26   | 15   | 1    | 5    |
  | ordered   | 1    | 5    | 11   | 115  | 19   | 26   | 48   | 59   | 61   | 77   |
  |           |      |      |      |      |      |      |      |      |      |      |
  |           |      |      |      |      |      |      |      |      |      |      |
  |           |      |      |      |      |      |      |      |      |      |      |
  |           |      |      |      |      |      |      |      |      |      |      |
  |           |      |      |      |      |      |      |      |      |      |      |
  |           |      |      |      |      |      |      |      |      |      |      |

- Method

  1. Convert an input array of n unordered items into a max heap.
  2. Extract the items from the heap one at a time to build an ordered array.

- 주어진 정수들을 비감소 순서(non-decreasing order)대로 정렬하라.

~~~c++
typedef struct{
 int key;
 /* other fields */

```
} element;
Element list[MAX_SIZE];
```
} element;
Element list[MAX_SIZE];
~~~

```c++
void heapsort(element list[], int n){ 
    /*perform heapsort on the array*/
    int i, j;
    element temp; 
    
    // Make a (max) heap  
    for(i = (n)/2; i > 0; i--)     
        adjust(list, i, n);  
		// Extract items one by one 
    for(i = n - 1; i > 0; i--) {
        SWAP(list[1], list[i+1], temp);
        adjust(list, 1, i);  
    }
}
        
```

1. $O(n) $
2. $O(nlog n) \rightarrow O(nlogn)$

- Make a (max) heap.
- ![image](https://user-images.githubusercontent.com/46957634/122662909-9a2de080-d1d1-11eb-8fe8-2eb072282a82.png)
  ![image](https://user-images.githubusercontent.com/46957634/122662936-cd706f80-d1d1-11eb-9198-6026c24d1561.png)

### The adjust() function

- Input
  - a binary tree T whose left and right subtrees satisfy the heap property but whose root may not

- Output
  - a binary tree T adjusted so that the entire binary tree satisfies the heap property

```c++
void adjust(element list[], int root, int n){
    int child, rootkey;
    element temp;
    temp = list[root];
    rootkey = list[root].key;
    child = 2 * root;
    while (child <= n){
        if ((child < n) && (list[child].key < list[child + 1].key))
            child++;
        if (rootkey >= list[child].key)
            break;
        else{
            list[child / 2] = list[child];
            child *= 2;
        }
    }list[child / 2] = temp;
}
```

- Executed *d* times, where *d* is the depth of the tree with root *i*
  - So $$O(d)$$ time

### Cost of Make-Heap

![image](https://user-images.githubusercontent.com/46957634/122662979-19231900-d1d2-11eb-89b5-4984c1e54f42.png)

- $C_{MH} \leq (k-1)2^0 + (k-2)2^1 + (k-3)2^2 + ...+1 \cdot 2^{k-2}$
- $ I=  (k-1)2^0 + (k-2)2^1 + (k-3)2^2 + ...+1 \cdot 2^{k-2}$
- $ 2I=  (k-1)2^1 + (k-2)2^2 + (k-3)2^3 + ...+1 \cdot 2^{k-1}$
- $ 2I-1I=  -(k-1) + 2^1+ 2^2 + ... + 2^{k-2}$
- $I = -k+ \frac {1 \cdot (2^k-1)}{2-1} = 2^k -k -1$
- so $C_{MH} \leq 2^k -k -1$

- Time Complexity Analysis
  - $2^k \leq 2n$, $-k < -log_2 n$ 
  - then $2^k -k -1 < 2n - log_2n -1$
  - so $C_{MH} = O(n)$

#### Extract items one by one.

![image](https://user-images.githubusercontent.com/46957634/122663098-0ceb8b80-d1d3-11eb-8a7d-57667950b627.png)

```c++
for(int i=n/2; i>0;i--) adjust(list,i,n);
for(int i=n-1;i>0;i--){
  SWAP(list[1], list[i+1], temp);
  adjust(list,1,i);
}
```

### Complexity of Item Extractions

![image](https://user-images.githubusercontent.com/46957634/122663149-54721780-d1d3-11eb-8294-493b8b047ea5.png)

$2^c \leq n < 2^{c+1} \rightarrow c \leq log_2 n < c+1$ 

- for a given $n$, the cost (depth) is $c = ⌊log_2n⌋$
- C_{IE}=⌊logn-1⌋+⌊logn-2⌋+⌊logn-3⌋...+⌊log2⌋+⌊log1⌋$_
- $\leq log2 + log3 + ...+log {n-1} < \sum_{i=2}^n log_2 n= O(n log n)$

- Heap Sort : $C_{MH} +C_{IE} = O(n)+ O(nlogn) = O(nlogn)$

## Priority Queue 2: Min-Max Heap

- Problem
  - The following operations must be performed as mixed in data processing:
    - Store a record with a key in an arbitrary order. 
    - Fetch the record with the current largest key. 
    - Fetch the record with the current smallest key.

- A solution
  - Design a data structure that offers the efficient implementation of the following operations (Double-Ended Priority Queue):
    - Insert an element with an arbitrary key. 
    - Delete an element with the largest key. 
    - Delete an element with the smallest key.

- $$ref.$$ [Horowitz 9.1]

---

{교육과정 외}

## Priority Queue 3: Heap and Hashing



## Priority Queue 4: Deap



## Priority Queue 5: Leftist Tree



## Priority Queue 6: Binomial Heap



## Priority Queue 7: Fibonacci Heap
