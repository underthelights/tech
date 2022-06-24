---
layout: article
title: Divide-and-Conquer Techniques and Sorting Techniques
tags: alg
category: alg
picture_frame: shadow
use_math: true
---

Algorithm Design & Analysis  
**[Sub3] Divide-and-Conquer Techniques and Sorting Techniques**

<!--more-->


# [주제 3] 

- Divide-and-Conquer Techniques and Sorting Techniques

## 0. Algorithm Design Techniques

- Divide-and-Conquer Method
- Dynamic Programming Method
- Greedy Method
- Backtracking Method
- Local Search Method
- Branch-and-Bound Method
- Etc.

### The Divide-and-Conquer Approach

- 1. **Divide** an instance of a problem into one or more smaller instances.

     문제의 인스턴스를 하나 이상의 작은 인스턴스로 나눕니다.

- 2. **Conquer** (Solve) each of the smaller instances. Unless a smaller instance is sufficiently small, use recursion to do this.

     각 작은 인스턴스를 정복합니다. 작은 인스턴스가 충분히 작지 않으면 재귀적을 사용하여 이 작업을 수행합니다.

- 3. If necessary, **combine** the solutions to the smaller instances to obtain the solution to the original instance.

     필요한 경우 작은 인스턴스에 대한 솔루션을 결합하여 원래 인스턴스에 대한 솔루션을 확보합니다.

![image](https://user-images.githubusercontent.com/46957634/122663646-2c84b300-d1d7-11eb-8794-6b356dce4a7b.png)

### Recursion

- Tower of Hanoi
  - [Geeksforgeeks](https://www.geeksforgeeks.org/c-program-for-tower-of-hanoi/) 
  - ![image](https://user-images.githubusercontent.com/46957634/122663711-81c0c480-d1d7-11eb-99c8-76837c5120cf.png)
- - $T(n) = 2T(n-1) +1, n>1$, $T(1) = 1$
- Recursive thinking!
  - [princeton](https://introcs.cs.princeton.edu/java/23recursion/)
  - ![image](https://user-images.githubusercontent.com/46957634/122663720-88e7d280-d1d7-11eb-8b35-1590e32b0406.png)

## 1. Sorting

>  A sorting algorithm is said to be stable if two items with equal keys appear in the same order in sorted output as they appear in the input array to be sorted.
>
>  Sorting Algorithm의 Stability : 정렬되지 않은 상태에서 같은 key 값을 가진 원소의 순서가 정렬 후에도 유지하느냐
>
>  일부 정렬 알고리즘은 삽입 정렬, 병합 정렬, 버블 정렬 등과 같이 본질적으로 안정적입니다. (정렬 후에도 원래의 순서가 유지됨)

- Problem: 

  - Given a list of n items, arrange them **in a certain order.**
    - Ex: non-increasing, non-decreasing, or etc.

- Some criteria for choosing a sorting algorithm 

  > - How many items will you be sorting? 
  >   얼마나 많은 원소를 정렬할 것인가?
  >
  > - Will there be duplicate items in the data?
  >   데이터에 중복 항목이 있습니까?
  >
  > - What do you know about the data?
  >   데이터에 대해 알고 계십니까?
  >
  > - Is the data already partially sorted? 
  >
  >   데이터는 이미 부분적으로 정렬되어 있는가?
  >
  > - Do you know the distribution of the items?
  >
  >   품목의 분포를 알고 있습니까?    
  >
  > - Are the keys of items very long or hard to compare? 
  >
  >   항목 키가 매우 길거나 비교하기 어렵습니까?
  >
  > - Is the range of possible keys very small?
  >   가능한 키의 범위가 매우 작습니까?
  >
  > - Do you have to worry about disk accesses?
  >   디스크 액세스에 대해 염려해야 합니까?
  >
  > - Do you need a stable sorting algorithm?
  >   안정적인 정렬 알고리즘이 필요한가?
  >
  > - How much time do you have to write and debug your routine?
  >   루틴을 작성하고 디버깅하는 데 얼마나 많은 시간이 필요합니까?

- ref. [Skiena, Steven S. The Algorithm Design Manual: The CD-ROM. 2 June 1997. 7 Dec. 2005](https://drive.google.com/drive/u/0/folders/1IyehEF9hz96cFu5js6ZWyfR4_Mvk1xb8), 

  - 원래 [링크](http://www2.toki.or.id/book/AlgDesignManual/BOOK/BOOK4/NODE148.HTM) 깨짐

### A Formal Definition of Sorting

- A **partial order** on a set S is a relation R such that for each a, b, and c in S:
  - $$aRa$$ is true (R is reflexive).
  - $$aRb$$ and $$bRc$$ imply $$aRc$$ (R is transitive)
  - $$aRb$$ and $$bRa$$ imply $$a = b$$ (R is antisymmetric)
- A Linear Order or Total Older on a set $$S$$ is a partial order R on S such that for every pair of elements a, b, either aRb or bRa.
- The sorting problem
  - Given a sequence of $$n$$ elements $$a_1, a_2, ..., a_n$$ drawn from a set having a linear order $$\preceq $$
  - find a permutation $$\Pi = (\pi_1, \pi_2, ..., \pi_n)$$ of $$(1,2,...,n)$$ that will map the sequence into a nondecreasing sequence $a_{\pi_1}, a_{\pi_2},...,a_{\pi_n}$ such that $a_{\pi_1} \preceq a_{\pi_i+1}$ for $1 \leq i < n $
- Ex: $ \leq$ on $\mathbb{Z}$$, $ $\subseteq $ on sets 
- Sorting on data with partial order?
- ref. [이산수학 내용](https://blog.naver.com/1net1/220735043638)

## 1) Merge Sort

- **Problem**: Sort *n* keys in nondecreasing sequence.
- **Inputs**: positive integer *n,* array of keys *S* indexed from 1 to *n*.
- **Outputs**: the array *S* containing the keys in nondecreasing order. 배열 S는 감소하지 않는 순서로 정렬된 key를 갖는다
  1. **Divide** the array into two subarrays each with ~n/2 items.
  2. **Conquer** each subarray by sorting it recursively.
  3. **Combine** the solutions to the subarrays by merging them into a single sorted array.

![image](https://user-images.githubusercontent.com/46957634/122665593-12e96880-d1e3-11eb-81e0-a0d1a78f5680.png)

- A simple implementation

```c++
// Sort a list from A[left] to A[right].
// Should be optimized for higher efficiency!!!

void merge_sort(item_type *A, int left, int right){
    int middle;
    if (left < right){
      	//divide : O(1)
        middle = (left + right) / 2;            					
      	//conquer : 2T(n/2)
        merge_sort(A, left, middle);
        merge_sort(A, middle + 1, right);
				//combine : O(n)
      	merge(A, left, middle, right);
    }
}

item_type *buffer; 
// extra space for merge sort, allocated beforehand

void merge(item_type *A, int left, int middle, int right){
    int i, i_left, i_right;
    memcpy(buffer + left, A + left, sizeof(item_type) * (right - left + 1));
		//O(r-l+1), O(n)
  	i_left = left;
    i_right = middle + 1;
    i = left;

    while ((i_left <= middle) && (i_right <= right)){
        if (buffer[i_left] < buffer[i_right])
            A[i++] = buffer[i_left++];
        else
            A[i++] = buffer[i_right++];
    }
    while (i_left <= middle)
        A[i++] = buffer[i_left++];
    while (i_right <= right)
        A[i++] = buffer[i_right++];
}
```

- An example of merging two arrays

| k    | left        | right       | merged                  |
| ---- | ----------- | ----------- | ----------------------- |
| 1    | 10 12 20 27 | 13 15 22 25 | 10                      |
| 2    | 10 12 20 27 | 13 15 22 25 | 10 12                   |
| 3    | 10 12 20 27 | 13 15 22 25 | 10 12 13                |
| 4    | 10 12 20 27 | 13 15 22 25 | 10 12 13 15             |
| 5    | 10 12 20 27 | 13 15 22 25 | 10 12 13 15 20          |
| 6    | 10 12 20 27 | 13 15 22 25 | 10 12 13 15 20 22       |
| 7    | 10 12 20 27 | 13 15 22 25 | 10 12 13 15 20 22 25    |
| -    |             |             | 10 12 13 15 20 22 25 27 |



### Worst-case time complexity

- 편의상 $n=2m$이라 할 경우 ( $m \in Z^+ \cup \{0\}$)

  - $T(n) = 2T(\frac {n} {2}) + cn, n \geq 2$

    - 2 : Number of subproblems
    - $\frac n 2$ : Subproblem size

  - $T(1) =1 $

    $\rightarrow T(n) = O(nlogn)$

| Merge Sort |                 |         |
| ---------- | --------------- | ------- |
| Divide     | Conquer         | Combine |
| $O(1)$     | $2T(\frac n 2)$ | $O(n)$  |

- *n*개의 원소를 *k*개와 *l*개로 나누어 진행한다고 가정하면 ($$n=k+l$$),
  - $$T(n) = T(k) + T(l) + cn (k \approx l)$$
  - $$n = 2^m$$ 이 아닌 일반적인 경우에도 같은 시간 복잡도를 가짐을 증명할 수 있음.

### Solving Recurrence Equations

- \- Solve the following recurrences $T(n)$ for given  $T(1)=1$

  \- 1. $T(n) = aT(n-1) + bn$

  \- 2. $T(n) = T(n/2) + bnlog n $

  \- 3. $T(n) = aT(n-1) + bn^2$

  \- 4. $T(n) = aT(n/2) + bn^2$

    \5. $T(n) = T(n/2) + clog n $

    \6. $T(n) = T(n/2) + cn $

    \7. $T(n) = 2T(n/2) + cn $

    \8. $T(n) = 2T(n/2) + cnlogn $

    \9. $T(n) = T(n-1) + T(n-2)$, for $T(1) = T(2) = 1$

### Some Derivations

1. $T(n) = 2 T(n/2) + cn$, $T(1) = 1$

   - assume $n=2^m$, i.e., $m = log_2 m$ for some $m \geq 0, m \in \mathbb{Z}$

   - $T(2^m) = 2T(2^{m-1})+c \cdot 2^m$

     $= 2 \{2T(2^{m-2})+c \cdot 2^{m-1} \}+c \cdot 2^m$

     $=2^2 \cdot T(2^{m-2})+2 \cdot c \cdot 2^m$

     $= 2^2 \{2 \cdot T(2^{m-3})+c \cdot 2^{m-2} \}+2 \cdot c \cdot 2^m$

     $ ... = 2^m \cdot T(2^0) + m \cdot c \cdot 2^m $

     $ = n \cdot 1 + (log_2 n) \cdot c \cdot n = O(nlog n )$

2. $T(n) = T(n-1) + cn$, $T(1) = 1$

3. $T(n) = 2 T(n/2) + cn^2$, $T(1) = 1$

   - Assume $n=2^m$ for some $m \in \mathbb{Z} - \mathbb {Z^-}$

   - $2 \cdot T (2^{m-1}) + c \cdot 2 ^2m$

     $ = 2 \{ 2 \cdot T(2^{m-2}) + c \cdot 2 ^{2(m-1)} \} + c \cdot 2 ^2m $

     $= 2^2 \cdot T(2^{m-2}) + c \{ 2^{2m-1} + 2 ^{2m}\} $

     $ = 2 \{ 2 \cdot T(2^{m-3}) + c \cdot 2 ^{2(m-2)} \} + c \{ 2^{2m-1} + 2^{2m} \} $

     $= 2^3 \cdot T(2^{m-3})+ c \{ 2^{2m-2} + 2^{2m-1} + 2 ^{2m}\} $

     $ … = 2^m + 2 \cdot c \cdot 2^{2m} - 2 \cdot c \cdot 2^m$

     $ =2 \cdot c \cdot n^2 - (2 \cdot c -1) n = O(n^2)$

   

### Another Implementation of Merge Sort

- ref. [Horowitz 7.6.3]

```c++
int rmerge(element list[], int lower, int upper){
/*sort the list, list[lower], ..., list[upper]. the link field in each record is initially set to -1*/
// list[lower], …, list[upper]까지 오름차순으로 정렬.
// 각 레코드의 link filed는 초기에 -1로 설정
	int middle;
  if (lower >= upper) return lower;
  else{
    middle = (lower + upper)/2;
    return listmerge(list, rmerge(list,lower,middle), rmerge(list, middle+1, upper));
  }
}
```

- $rmerge$ returns an integer that points to the start of the sorted list. start = rmerge(list, 0, n-1);

```
typedef struct {
   int key;
   int link;
} element;
```

```c++
int listmerge (element list[ ], int first, int second){
// first와 second가 가리키는 서브리스트들을 합병
  int start = n;
  while (first != -1 && second != -1) {
    if (list[first].key <= list[second].key) {
      list[start].link = first; 
      start = first;
      first = list[first].link;
    }
    else {
      list[start].link = second; 
      start = second;
      second = list[second].link;
    } 
  }if (first == -1)
    list[start].link = second;
  else list[start].link = first;
  return list[n].link; 
  // 합병된 리스트의 시작 인덱스를 return
}
```



- $listmerge$ takes two sorted chains, first and second, and returns an integer that points to the start of a new sorted chain that includes the first and second chains.

- listmerge 함수 수행 예 start

  ![image](https://user-images.githubusercontent.com/46957634/122666037-f6026480-d1e5-11eb-8a0a-74b195d732fa.png)

## 2) Quick Sort

- Pivot strategy

- 1 **Divide**

  - Select a **pivot element**, and then divide the array into two subarrays such that ....

- 2 **Conquer**

  - sort each subarray recursively. 

- 3 **Combine**

  - do nothing.

  ![image](https://user-images.githubusercontent.com/46957634/122666063-0d415200-d1e6-11eb-9b54-9cb6c1a6f4f0.png)

- A simple implementation

```c++
// Sort a list from A[left] to A[right].
// Should be optimized for higher efficiency!!!

void **quick_sort**(item_type *A, int left, int right) { 
  int pivot;
  if (right - left > 0) {
    //divide
    pivot = partition(A, left, right);
    //conquer
    quick_sort(A, left, pivot - 1);
    quick_sort(A, pivot + 1, right); 
  }
}


```

```c++
#define SWAP(a, b) { 
	item_type tmp; 
	tmp = a; 
	a = b; 
	b = tmp; 
}

int partition(item_type *A, int left, int right) { 
  int i, pivot;
  pivot = left;
  for (i = left; i < right; i++) {
    if (A[i] < A[right]) {
      SWAP(A[i], A[pivot]);
      pivot++;
    //How is the pivot element chosen in this function?  
    } 
  }
  SWAP(A[right], A[pivot]);
  return(pivot);
}
```

### Cost Analysis

- Cost
  - $T(n) = T(m_1) + T(m_2) + cn (m_1 + m_2 = n-1)$ if $n>1$
  - $T(1) = 1$

| Quick Sort |       |     |
| ————— | —————— | ——— |
| Divide   | Conquer   | Combine |
| O(n)    | T(m1) +T(m2) | O(1)  |

- Worst-case time complexity

  - 매 단계에서 선택한 pivot element가 가장 크거나 가장 작을 경우,

  - $T(n) = T(0) + T(n-1) + cn$, $T(1) = 1$

    then $T(n) = O(n^2)$

  - Skewed vs well-balanced trees

- Average-case time complexity

  - $T(n) = \sum_{p=1}^n {T(p-1) + T(n-p)} + cn$, $T(0) = 1 \rightarrow$ Then $T(n) = O(n log n)$

### 직관적인 시간 복잡도 추정

![image](https://user-images.githubusercontent.com/46957634/122666279-52b24f00-d1e7-11eb-8e4e-f6b66b313437.png)

	- $T(n) = T(m_1) + T(m_2) + cn (m_1 + m_2 = n-1)$ if $n>1$
	- $T(1) = 1$

### Average Case Time Complexity

- 첫 번째 사실

  - $n \leq 0, \forall n \in \mathbb{Z}$, $T_{ave}(n)$ 을 $n$ 개의 원소를 가지는 배열을 퀵 정렬 방법을 사용하여 정렬하는데 걸리는 평균 수행시간이라고 하자. 그러먼 어떤 양의 정수 b와 c에 대해 다음과 같은 재귀 관계 존재

  - $T_{ave} (n) \geq cn + \frac {1}{n} \sum_{p=1}^{n} \{ T_{ave} (p-1) + T_{ave} (n-p) \}$

    $ = cn + \frac{2}{n} {\sum_{p=0}^{n-1} {T_{ave}(p)}}$ $\forall n \geq 2$

    $T_{ave} (1) \leq b$

    $T_{ave} (0) \leq b$

    $Cost_{ave} = \sum_{p=1}^n {P_r (p) \cdot Cost(p)} = \frac {1}{n} \sum_{p=1}^n {... + ...}$

- 두 번째 사실

  - $ k=2(b+c)$ 라 할 때, 2보다 같거나 큰 모든 정수 $n$에 대하여 $T_{ave} (n) \leq kn log_e n$ 과 같은 관계 존재

  - 증명: 위의 부등식을 수학적 귀납법을 사용하여 증명하자.

    1. $n=2$    

       - 첫 번째 사실로부터 다음과 같은 관계 성립

         $T_{ave}(2) \leq 2c + T_{ave} (0) + T_{ave} (1) \leq 2(b+c) \leq k \cdot 2 \ log_e 2$

         따라서 두 번째 사실 성립

    2. 3보다 같거나 큰 임의의 $n$ 이 given

       Assume that : $m<n$ 인 모든 $m$ 에 대하여 두 번째 사실 성립한다고 가정하자.

       - 그러면 첫 번째 사실과 이 과정을 사용하여 다음과 같은 관계 유도 가능

         $T_{ave} (n) \leq cn + \frac {2} {n} \sum_{m=0}^{n-1} {T_{ave} (m)}$

         $= cn + \frac 2 m \{ T_{ave} (0)+T_{ave} (1) \} + \frac {2} {n} \sum_{m=2}^{n-1} {T_{ave} (m)}$

         $\leq cn + \frac {4b} n + \frac {2k} n \sum_{m=2}^{n-1} {m log_e m}$

         그러므로 $T_{ave} (n) \leq cn + \frac {2} {n} \sum_{p=0}^{n-1} {T_{ve} (p)}$ $\forall n \geq 2$

       함수 $x log_e x$가 $x$에 대하여 아래로 볼록한 함수이어서 $m log_e m \leq \int_m^{m+1} x log_e x dx$ 라는 사실을 이용하면 다음과 같은 관계식을 얻는다.

       	$T_{ave} (n)= cn + \frac {4b}n + \frac {2k}n \int_2^n x log_e x dx$
       	
       	$\leq cn + \frac {4b}{n} + \frac{2k}{n} \{ \frac{n^2 log_e n}{2} - \frac {n^2} 4 \}$
       	
       	$= knlog_e n + \{ cn + \frac{4b} n - \frac {kn} 2\}$		

       
       ​	
       
       > $\int_2^n x log_e x dx =[\frac 1 2 x^2 log_e x - \frac {x^2} 4]_2^n $
       >
       > $= (\frac {n^2} 2) log_e n - \frac {n^2} 4 - (2log_e 2 - 1) \leq \frac {n^2} {2} {log_e n} - \frac {n^2} {4}$
       
       이 때, $ cn + \frac{4b} n - \frac {kn} 2 = (c-\frac k 2 )n + \frac {4b} n = b(\frac 4 n -n)$ 과 같고 이 값은 2보다 같거나 큰 n에 대해 항상 0보다 같거나 작으므로 $T_{ave} (n) \leq kn log_e n$ 이 되어 
       
       3보다 같거나 큰 임의의 n에 대해서도 두 번째 사실이 성립한다. 따라서 2보다 같거나 큰 모든 정수 n에 대해 다음과 같은 두 번째 사실이 성립한다.

### Anther Implementation

```c++
void quicksort (element list[ ], int left, int right){
// list[left], …, list[right]까지 오름차순으로 정렬.
// list[left].key를 중추 키(pivot key)로 선정
// list[left].key ≤ list[right + 1].key 라고 가정
    int pivot, i, j;
    element temp;
    if (left < right) {
        i = left; j = right + 1;
        pivot = list[left].key;
        do {
        // pivot을 중심으로 왼쪽과 오른쪽 리스트 생성
        // 왼쪽 리스트: pivot보다 적은 키들을 저장, 오른쪽은 반대
            do // 왼쪽부터 pivot보다 큰 키를 검색
                i++;
            while (list[i].key < pivot);
            do // 오른쪽부터 pivot보다 작은 키를 검색
                j--;
            while (list[j].key > pivot);
            if ( i < j ) // 각 리스트의 속성을 만족하도록 데이터 교환
                SWAP( list[i], list[j], temp );
        } while ( i < j );
        SWAP( list[left], list[j], temp );
        quicksort( list, left, j – 1 ); // 왼쪽 리스트를 다시 정렬
        quicksort( list, j + 1, right ); // 오른쪽 리스트를 다시 정렬
    }
}
```



### Improving the Performance of Quick Sort

- How can you select a “good” pivot element?

  - Choose a random element in the list.
  - Choose **the median of the first, middle, and final** elements in the list.
  - Choose **the median of the entire elements** in the list. (bad idea)
  - Etc.

- Program 7.4. improved quicksort

  - Choosing the median of the first, middle, and final elements as the partitioning element and cutting off the recursion for small subfiles can significantly improve the performance of quicksort. This implementation partitions on the median of the first, middle, and final elements in the array (otherwise leaving these elements out of the partitioning process). Files of size 11 or smaller are ignored during partitioning; then, insertion from is used to finish the sort.

    ```c++
    #define M 10void quicksort(ITEM[] a, int l, int r){    if (r - l <= M)        return;    exch(a, (l + r) / 2, r - 1);    compExch(a, l, r - 1);    compExch(a, l, r);    compExch(a, r - 1, r);    int i = partition(a, l + 1, r - 1);    quicksort(a, l, i - 1);    quicksort(a, i + 1, r);}void sort(ITEM a[], int l, int r){    quicksort(a, l, r);    insertion(a, l, r);}
    ```

- How can you minimize the bookkeeping cost involved in the recursive calls?

  - Much of the pushing and popping of the frame stack is unnecessary.

  - Lists of size smaller than M are ignored during quick sort, then do a single sorting pass at the end.

  - Avoid making the recursive call on the larger subrange.  The depth of recursion <= O(log n)

    ```
    quicksortTRO(E, first, last)    int first1, last1, first2, last2;    first2=first;last2=last;    while(last2-first2 >1)        pivotElement = E[first];        pivot = pivotElement.key;        int splitPoint = partition(E, pivot, first2, last2) ;        E[splitPoint] = pivotElement;        if(splitPoint< (first2+last2)/2)            first1 = first2;            last1 = splitPoint-1;            first2=splitPoint+1;            last2 = last2;        else                first1 = splitPoint+1;first2;            last1 = last2;            first2 = first2;            last2 = splitPoint-1;        quicksortTro(E,first1, last1);        //continue loop for fist2, last2.    return;
    ```

### Example: Quick Sort

- By courtesy of David R. Musser

- Average-case:$ O(n log n) $

- Worst-case: $O(n^2)$

  ![image](https://user-images.githubusercontent.com/46957634/122667318-2ef20780-d1ed-11eb-914a-40d49ccae67a.png)

#### Quicksort: Implementation 2 [K. Loudon]

```c++
#include <stdlib.h>
#include <string.h>
#include "sort.h"

static int compare_int(const void *int1, const void *int2){
    // Compare two integers (used during median-of-
    // three partitioning
    if (*(const int *)int1 > *(const int *)int2)
        return 1;

    else if (*(const int *)int1 < *(const int *)int2)
        return -1;

    else
        return 0;
}

static int partition(void *data, int esize, int i, int k,int (*compare)(const void *key1, const void *key2)){
    char *a = data;
    void *pval, *temp;
    int r[3];
    /*  Allocate storage for the partition value
      and swapping. */
    if ((pval = malloc(esize)) == NULL)
        return -1;
    if ((temp = malloc(esize)) == NULL)
    {
        free(pval);
        return -1;
    }
    /* Use the median-of-three method to find the partition value.  */
    r[0] = (rand() % (k - i + 1)) + i;
    r[1] = (rand() % (k - i + 1)) + i;
    r[2] = (rand() % (k - i + 1)) + i;
    issort(r, 3, sizeof(int), compare_int);
    memcpy(pval, &a[r[1] * esize], esize);
    /* Create two partitions around the partition   value.  */
    i--;
    k++;
    while (1)    {
        /* Move left until an element is found in the wrong partition. */
        *do { k--; }
        while (compare(&a[k * esize], pval) > 0);
        /* Move right until an element is found in the wrong partition. */
        do{
            i++;
        }while (compare(&a[i * esize], pval) < 0);

        if (i >= k){
            break;
        }
        /* Stop partitioning when the left and right counters cross. */
        else{
            /* Swap the elements now under the left and   right counters.  */
            memcpy(temp, &a[i * esize], esize);
            memcpy(&a[i * esize], &a[k * esize], esize);
            memcpy(&a[k * esize], temp, esize);
        }
    }

    /* Free the storage allocated for
     partitioning. */

    free(pval);
    free(temp);
    /* Return the position dividing the two

 partitions. */

    return k;
}

int qksort(void *data, int size, int esize, int i, int k, int (*compare)(const void *key1, const void *key2)){
    int j;
    /* Stop the recursion when it is not possible
     to partition further. */
    if (i < k)
    {
        // Determine where to partition the elements
        if ((j = partition(data, esize, i, k,
                           compare)) < 0)
            return -1;
        // Recursively sort the left partition
        if (qksort(data, size, esize, i, j, compare)< 0)
          return -1;

        // Recursively sort the right partition
        if (qksort(data, size, esize, j + 1, k, compare) < 0)
            return -1;
    }

    return 0;
}
```

#### 

## 3. Insertion Sort

- Insertion Sort: Example 1
  - ![image](https://user-images.githubusercontent.com/46957634/122666532-c30da000-d1e8-11eb-8a5a-0a01596b077b.png)
- Insertion Sort: Example 2
  - Insertion : $O(n+d)$ in the worst case over sequences that have d inversions
  - ![image](https://user-images.githubusercontent.com/46957634/122666537-cc970800-d1e8-11eb-81d1-24d76bd77728.png)
  - When does the insertion sort run fast?
  - 이러한 insertion sort의 성질을 quick sort의 성능 향상에 활용하자.

### Insertion Sort: Implementation

```c++
void insertion_sort(int *A, int n) { 
  int i, j, tmp;
  for (i = 1; i < n; i++) {
    tmp = A[i];
    j = i;
    while ((j > 0) && (tmp < A[j - 1])) {
      A[j] = A[j - 1];
      j--; 
    }A[j] = tmp; 
  }
}
```

- Sort a list of elements by iteratively inserting a next element in a progressively growing sorted array.

  ```c++
  #include <stdlib.h>
  #include <string.h>
  #include "sort.h"
  int issort(void *data, int size, int esize, int (*compare)(const void *key1, const void *key2))
  {
      char *a = data;
      void *key;
      int i, j;// Allocate storage for the key element.
  if ((key = (char *)malloc(esize)) == NULL)
      return -1;
  // Repeatedly insert a key element among the sorted elements.
  for (j = 1; j < size; j++)
  {
      memcpy(key, &a[j * esize], esize);
      i = j - 1;
      // Allocate storage for the key element.
      if ((key = (char *)malloc(esize)) == NULL)
          return -1;
      // Repeatedly insert a key element among the sorted elements.
      for (j = 1; j < size; j++)
      {
          memcpy(key, &a[j * esize], esize);
          i = j - 1;
          /* Determine the position at which to insert the key element. */ while (i >= 0 && compare(&a[i * esize], key) > 0)
          {
  
              memcpy(&a[(i + 1) * esize], &a[i * esize], esize);
              i--;
          }
          memcpy(&a[(i + 1) * esize], &a[i * esize], esize);
          i--;
      }
      memcpy(&a[(i + 1) * esize], key, esize);
  }
  // Free the storage allocated for sorting.
  free(key);
  return 0;
  ```

  }


#### Insertion Sort: Run-Time Analysis 

- Worst case

  - No. of comparisons:

    $1+2+ ...+n-1 = O(\frac {n^2}{2})$

  - No. of record assignments:

    $1+2+ ...+n-1 = O(\frac {n^2}{2})$

- Average case

  - No. of comparisons

    $\sum_{i=1}^{n-1} {\frac{1+2+...+i+i}{i+1} } =\sum_{i=1}^{n-1} {(\frac{i}{2}+1-\frac{1}{i+1}) } $

    $\approx \frac{(n-1)(n+4)}{4} - ln n = O(\frac{n^2} 4)$

  - No. of record assignments

    $\sum_{i=1}^{n-1} {\frac{0+1+2+...+i}{i+1} +2} = \frac{n(n-1)}{4}+2(n-1) = O(\frac{n^2}4) $

    ![image](https://user-images.githubusercontent.com/46957634/122666961-46c88c00-d1eb-11eb-9843-c32b1b40299b.png)

#### Selection Sort: Example

![image](https://user-images.githubusercontent.com/46957634/122666974-57790200-d1eb-11eb-97d3-6b3280bca477.png)

## 4. Selection Sort

### Selection Sort: Implementation

- $T(n) = O(n^2)$

```c++
#define SWAP(a, b)         {                  
        item_type tmp; 
        tmp = a;       
        a = b;         
        b = tmp;       
    }

void selection_sort (item_type *A, int n){
    int i, j, cur;

    for (i = 0; i < n - 1; i++)    {
        cur = i;
        for (j = i + 1; j < n; j++) 
            if (A[j] < A[cur])
                cur = j;
        SWAP(A[i], A[cur]); // what if i == cur? }
    }
}
```

### Selection Sort: Run-Time Analysis

- Worst case

  - No. of comparisons

    $\sum_{i=0}^{n-2} (n-i-1) = \frac {n(n-1)} 2 = O (\frac {n^2} {2})$

  - No. of record assignments

    $3(n-1) = O(3n)$

- Average case

  - No. of comparisons

    $\sum_{i=0}^{n-2} (n-i-1) = \frac {n(n-1)} 2 = O (\frac {n^2} {2})$

  - No. of record assignments

    $3(n-1) = O(3n)$

- [생각해보기] If we code like “if (i != cur) SWAP(A[i], A[cur]);”, what is the average cost?

## 5) Bubble Sort

### Bubble Sort: Example

![image](https://user-images.githubusercontent.com/46957634/122667028-a161e800-d1eb-11eb-8d5c-9399fdbae93d.png)

### Bubble Sort: Implementation

```c++
#define SWAP(a, b){
    item_type tmp;
    tmp = a;
    a = b;
    b = tmp;
}

void bubble_sort(item_type *A, int n){
    int i, j;

    for (i = 0; i < n - 1; i++){
        for (j = n - 1; j > i; j--){
            if (A[j] < A[j - 1])
                SWAP(A[j], A[j - 1]);
        }
    }
}
```

### Bubble Sort: Run-Time Analysis

- Refer to The Art of Computer Programming (Vol. 3)

- Worst Case

  - No. of comparisons

    $\sum_{i=1}^{n-1} (n-1-i) = \frac {n(n-1)} 2 = O (\frac {n^2} {2})$

  - No. of record assignments

    $\sum_{i=1}^{n-1} 3i = \frac 3 2 n(n-1)= O (\frac {3} {2} n^2) $

- Average case

  - No. of comparisons

    $\sum_{i=1}^{n-1} (n-1-i) = \frac {n(n-1)} 2 = O (\frac {n^2} {2})$

  - No. of record assignments

    $\frac 1 2  \sum_{i=1}^{n-1} 3i = \frac 3 2 n(n-1)= O (\frac {3} {4} n^2) $

## 6) Cost Comparison

|                         | Selection     | Insertion                  | Bubble          |
| ----------------------- | ------------- | -------------------------- | --------------- |
| # of comparisons        | $\frac{n^2}2$ | $\frac {n^2} 4 $ (average) | n2/2 (average)  |
|                         |               | n2/2 (worst)               | n2/2 (worst)    |
| # of record assignments | 3n            | n2/4 (average)             | 3n2/4 (average) |
|                         |               | n2/2 (worst)               | 3n2/2 (worst)   |

### Comparison Sorts

![image](https://user-images.githubusercontent.com/46957634/122667080-f9005380-d1eb-11eb-8e13-b6d1753611ad.png)

https://en.wikipedia.org/wiki/Sorting_algorithm#Comparison_of_algorithms

- 

### Performance Comparisons

- By courtesy of David R. Musser
- ![image](https://user-images.githubusercontent.com/46957634/122667328-3ca78d00-d1ed-11eb-8f27-71a4578f1589.png)

## 2. Selection Algorithm

### 1. Selection of Both Maximum and Minimum Elements

- **Problem**

  - Find both the maximum and the minimum elements of a set containing n elements (assume $$n = 2m$$ for some integer $$m$$).

- [Aho 2.6]

- ```
  begin
  	MAX <- any element in S;
  	for all other elements x in S do
  		if x>MAX then MAX<- x
  end
  ```

  - $T(n) = (n-1) + (n-2) = 2n-3$ comparisons

- ```
  procedure MAXMIN(S):
  if |S| = 2 then
  	begin
  		let S = {a, b};
  		return (MAX(a,b), MIN(a,b))
  	end
  else
  	begin
  		divide S into two subset S1,S2, each with the half of elements
  		(max1, min1) <- MAXMIN(S1);
  		(max2, min2) <- MAXMIN(S2);
  		return(MAX(max1, max2), MIN(min1, min2))
  	end
  ```

  $T(n) = 2T(n/2) + 2$ for $n > 2$, $T(n) = 1$ for $n = 2$

  → $T(n) = (3/2)n - 2$ comparisons

- This is the minimum!

### 2. Multiplication of Two n-bit Numbers

- The traditional method requires $$O(n^2)$$ bit operations.

- A divide-and-conquer approach

- ![image](https://user-images.githubusercontent.com/46957634/122671162-4f778d00-d200-11eb-83b9-5f6cce938ced.png)

- $xy = (a2^{\frac n 2} + b)(c2^{\frac n 2} + d) = ac2^n + (ad+bc)2^\frac n 2 + bd$

  ```
  u = (a+b)*(c+d);
  v = a*c, w = b*d;
  z = v * pow(2,n) + (u-v-w) * pow(2, n/2) + w;
  ```

- [Aho 2.6]

- $T(n) = 1$for $n = 1$

- $T(n) = 3T(n/2) + cn$ for $n > 1$

  → $T(n) = O(nlog3)$

- $O(n^2) → O(n^{1.59})$

- Read [Neapolitan 2.6].

### 3. Selection of the k-th Smallest Element

- [A. Aho, J. Hopcroft, and J. Ullman, Design and Analysis of Algorithms, Addison-Wesley, 1974. 3.6] 

- Problem

  - Given a sequence of $S$ of $n$ elements and an integer $k (1 <= k <= n)$, find the $k^{th}$ smallest element of $S$.

- **Solution 1:** 

  - Choose the smallest element repeatedly k times.

    $C = c(n-1)+c(n-2)+c(n-3)+...+c(n-k) = c \cdot k \cdot n - c \cdot \frac {k(k+1)} 2$

    if $ k= \frac n 2 $ then $C = c \cdot \frac {n^2} 2 - c \cdot \frac {n^2 + 2n} 8 = O(n^2)$

- **Solution 2:** 

  - Build a min-heap, and then extract the smallest element repeatedly $$k$$ times.

    $C = c \cdot n + d \cdot k \cdot log n$

    if $ k= \frac n 2 $ then $C = c \cdot n + d \cdot \frac n 2 \cdot log n = O(n log n)$

- **Can we design an $O(n)$-time algorithm?**

#### 3.1. Observation

- At least $O(n)$ time is necessary.
- If we use a divide-and-conquer scheme like the merge sort, 
- What about $T(n) = 3T(n/3) + cn$?
- ![image](https://user-images.githubusercontent.com/46957634/122671326-fe1bcd80-d200-11eb-8be5-42c57ba72ed1.png)

- Can we design an O(n)-time algorithm for this selection problem? 
- ![image](https://user-images.githubusercontent.com/46957634/122671392-4affa400-d201-11eb-965a-7b2d2cf5a918.png)
- What about $T(n) = T(an) + T(bn) + cn$ with $a + b < 1$?
- cost : $cn\{1+(a+b)+(a+b)^2+...\} \leq cn \frac 1 {1-a+b}$ 
  - so $O(n)$

#### 3.2. Algorithm

- Step 1: Divide S into floor(|S|/5) sequence of 5 elements each with up to four leftover elements.
  - ![image](https://user-images.githubusercontent.com/46957634/122671443-8d28e580-d201-11eb-9814-afbdb12c426e.png)
- Step 2: Sort each 5-element sequence.
  - ![image](https://user-images.githubusercontent.com/46957634/122671447-91550300-d201-11eb-9cd1-d959760a5607.png)
- Step 3: Let M be the sequence of medians of the 5-element sets. Then, let m be the median of the elements in M.
- Step 4: Let S1, S2, and S3 be the sequences of elements in S less than, equal to, and greater than m, respectively.
  - ![image](https://user-images.githubusercontent.com/46957634/122671458-a2057900-d201-11eb-85c5-9339d85fe7be.png)
  - If $|S1| >= k$, then find the k-th smallest element of S1.
  - else if $(|S1| + |S2| >= k)$, then m is the $k^{th}$ smallest element of S. 
  - else find the $(k – |S1| - |S2|)^{th}$ smallest element of $S3$.

```
procedure SELECT(k,s):
if |S|<50 then	
	begin
		sort S;
		return kth smallest element in S
	end
else
	begin
		divide S into |S|/5 sequences of 5 elements each with up to four leftover elements;
		sort each 5-element sequence;
		let M be the sequence of medians of the 5-element sets;
		m <- SELECt (M/2, M);
		let s1, s2 and s3 be the sequences of elements in S less than, equal to, and greater than m, respectively;
		if |s1|>= k 
			then return m
		else 
			return SELECT (k-|s1|-|s2|, s3)
	end
```

 			A divide-and-conquer strategy

- Facts
  - At least one-fourth of the elements of S are less than or equal to m.
  - At least one-fourth of the elements of S are greater than or equal to m.
  - |S1| <= 3n/4 and |S3| <= 3n/4

> - S1: the set of all elements less than m 
> - S2: the set of all elements equal to m
> - S3: the set of all elements greater than m

![image](https://user-images.githubusercontent.com/46957634/122671544-2952ec80-d202-11eb-8a59-efcc8ed6779f.png)

#### 3.3. Time Complexity

> - Input size n = |S|
> - |M| <= ceil(n/5)
> - |S1| <= 3n/4
> - |S3| <= 3n/4

```
procedure SELECT(k,s):
if |S|<50 then	
	begin
		sort S;
		return kth smallest element in S
	end
else
	begin
		divide S into |S|/5 sequences of 5 elements each with up to four leftover elements;
		sort each 5-element sequence;
		let M be the sequence of medians of the 5-element sets;
		m <- SELECt (M/2, M);
		let s1, s2 and s3 be the sequences of elements in S less than, equal to, and greater than m, respectively;
		if |s1|>= k 
			then return m
		else 
			return SELECT (k-|s1|-|s2|, s3)
	end
```

### 4. Selection Algorithm: Complexity Analysis

- Theorem

  - $\forall c, d \in \mathbb{R^+}$, if the following recurrence relation holds:
  - $T(n) \leq d $ for $n \leq 49$
  - $T(n) \leq T(\frac n 5) + T (\frac {3n} 4) + cn $ for $n \geq 50$
  - then $T(n) = O(n)$

- Proof

  - We want to prove that $T(n) \leq kn$ for some constant $k, \forall n \geq 1$

    1. Base case

       $T(n) \leq d \leq dn$ $\forall n \geq1$

       Therefore, $T(n) \leq kn $ $\forall 1 \leq n \leq 49$ if we select k such that $k \geq d$

    2. Inductive step

       assume that $n \geq 5$ and $T(m) \leq km$ $\forall m < n$

       Then, $T(n) \leq T(\frac n 5) + T (\frac {3n} 4) + cn  $

       $\leq k \frac n 5 + k \frac {3n} 4+ cn = \frac {19}{20}kn +cn$ 

       $= kn + (c-\frac k {20})n \leq kn$ if $k \geq 20c$

    - So if we choose k s.t. $k = max(d, 20c)$, $T(n) \leq kn$ for all $n \geq 50$.

## 3. Master Theorem 

### Master Theorem 1

- [Neapolitan 2.8]
- Let a, b, and c be nonnegative constants. 
- The solution to the recurrence $T (1)=1$, and $T(n)=aT(n/c)+bn$, for $n>1$for $n$ a power of $ c$ is
  - 1. $T(n)=O(n)$, if $a<c$ 
    2. $T(n)=O(nlogn)$, if $a=c$ 
    3. $T(n) = O(nlogca)$, if $a > c$
- Prove this by induction!
- Avoid divided-and-conquer if, for example–
  - An instance of size n is divided into two or more instances each almost of size n.
  - An instance of size n is divided into almost n instance of size n/c, where c is a constant.
- The divide-and-conquer strategy often leads to efficient algorithms, although not always!

### Master Theorem 2

212p

## Miscellaneous

### Finding the Closest Pair of 2D Points

##### 1. 내용

**[J. Kleinberg and E. Tardos,** **Algorithm Design****, Addison Wesley, 2005. 5.4]** 

• Problem
 – Given *n* points in the plane, find the pair that is closest together.

• Notation

• Naïve algorithm
 – Compute the distance between each pair of points

and take the minimum → *O*(*n*2) - time

##### 2. Applying the Divide-and-Conquer Strategy 

- [Shamos and Hoey]

- Simple assumption for an easy explanation

  - No two points in P have the same *x*-coordinate or the same *y*- coordinate.

- General idea

  [Preprocessing]

  - Build a list $P_x$ in which all the points in $P$ have been sorted by increasing *x*- coordinate→$O(n log n)$

  - Build another list $P_y$ in which all the points in $P$  have been sorted by increasing *y*-coordinate→$ O(n log *n*)$ 

  [Recursion for $ P$  with $ |P| = n$ ]

  - [Divide] Partition $P$ into two subsets $Q$ and $R$ → $O(n)$
  - [Conquer] Find the closest pairs in $Q $and $R$, respectively→$2T(n/2)$ 
  - [Combine] Use this information to get the closest pair in P→O(n) 
  - Time-complexity $O(n log n) + T(n)$ where $T(n) = cn +2T(n/2) → O(n log n)$

  

- The stage [Divide]: Partition P into two subsets Q and R. 

  - Create Q and R, where • 
    - Q: the set of points in the first ceil(n/2) positions of the list Px (the “left half”), and 
    - R: the set of points in the final floor(n/2) positions of the list Px (the “right half”). 
  - Furthermore, create Qx, Qy, Rx, and Ry, where
    - Qx consisting of the points in Q sorted by increasing x-coordinate, • 
    - Qy consisting of the points in Q sorted by increasing y-coordinate, •
    - Rx consisting of the points in R sorted by increasing x-coordinate, and • 
    - Ry consisting of the points in R sorted by increasing y-coordinate. ✓ Can be done in O(n). 

- The stage [Conquer]: Find the closest pairs in Q and R, respectively.

  - Recursively determine a closest pair (q0*, q1*) of points in Q.

- Recursively determine a closest pair (r0*, r1*) of points in R. 

  - Can be done in 2T(n/2).

- The stage [Combine]: Use the obtained info. to get the closest pair in P. 

  - Question : are there points $q \in Q, r \in R$ for which $d(q,r)<\delta$?

    - How can we answer this question in linear time? 

  - [Fact 1] (Why?)  

    - if there $\exists q \in Q , r \in R$ for which $d(q,r)<\delta$ 
    - then each of $q,r$ lies within a distance $\delta $of$ L$

  - [Fact 2] 

    - $\exists q \in Q, r\in R$ for which $d(q,r)<\delta$ $\iff$ $\exists s, s^{'} \in S$ for which $d(s,s^{'})<\delta$
    - [photo]

  - $x^*$: the x-coordinate of the rightmost point in $Q$

  - $\delta=min(d(q_0^*,q_1^*),d(r_0^*,r_1^*))$

    - [photo]

  - [Fact 3] 

    - if $s, s^{' } \in S$ have the property that $d(s, s^{'})<\delta$, then $s, s^{'}$ re within 15 positions of each other in the sorted list $S_y$ 
      - $S_y$ : the list consisting of the points in $S$ sorted by increasing $y$-coordinate. 
      - Each box contains at most one point of $S$. (Why?) 
      - If two points in $S$ are at least 16 positions apart in $S_y$ , ...
      - 

  - [merge] : $O(n)$

    1. For each $s \in S_y $ , compute its distance to each of the next 15 pts in $S_y$
    2. Let $s, s^{'}$ be the pair achieving the minimum of these distances
    3. Compare $d(s, s^{'})$ with $\delta$

    - [code]
