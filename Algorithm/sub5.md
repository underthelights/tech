---
layout: article
title: Greedy Method
tags: alg
category: alg
picture_frame: shadow
use_math: true
---

Algorithm Design & Analysis  
**[Sub5] Greedy Method**

<!--more-->

# [주제 5] Greedy Methods

## 0. Algorithm Design Techniques

- Divide-and-Conquer Method
- Dynamic Programming Method
- Greedy Method
- Backtracking Method
- Local Search Method
- Branch-and-Bound Method
- Etc.

## 1. The Greedy Method

- A technique to follow the problem-solving heuristic of making the locally optimal choice at each stage.

  각 단계에서 휴리스틱적으로 최적의 선택을 하는 PS 기술

- Strategy

  - Make the choice that appears best at each moment!
  - It is hoped to arrive at a globally optimal solution by making a locally optimal choice.

- Pros and cons

  - [ref](https://en.wikipedia.org/wiki/Greedy_algorithm)
  - Simple and straightforward to design an algorithm.
  - Does not guarantee the optimal solution to all problems 
    - Local maximum versus global maximum

### 1) Huffman Coding

- Data compression
  - Data compression can save storage space for files.
  - Huffman coding is just one of many data compression techniques.
- Problem
  - Given a file, find a binary character code for the characters in the file, which represents the file in the least number of bits.
- Example
  - Original text file: ababcbbbc
  - Huffman codes:a=10,b=0,c=11 → Compressed file: 1001001100011
  - Is it possible to have a code set where a = 01, b = 0, and c = 11?

### 2) Prefix Codes

- No codeword can be a prefix of any other code.
  - Otherwise, decoding is impossible! 
  - Uniquely decodable!
    - ☺ Example 1
    - a = 00, b = 1110, c = 110, d = 01, e = 1111, f = 10
    - Example 2
    - a = 00, b = 1100, c = 110, d = 01, e = 1111, f = 10

- Binary trees corresponding to prefix codes
  - The code of a character c is the label of the path from the root to c.
  - Decoding of an encoded file is trivial.

- Problem
  - Given a file *F* to be encoded with a character set $$V = \{v_1, v_2, ..., v_n \}$$  , find an optimal prefix binary code with a corresponding binary tree *T* that minimizes the cost function where *freq*(*v**i*) is the number of times *v**i* occurs in *F*, and *depth*(*v**i*) is the depth *v**i* of in *T*.
  - A Greedy approach successfully finds an optimal code.

### 3) Huffman’s Algorithm 

#### 1. Contents

- Idea
  - Put the rarest characters at the bottom of the tree.

- A greedy approach
  - Repeat the following until only one tree is left:
    1. Start from a set of single node trees.
    2. Pick up two trees u and v with the lowest frequencies.
    3. Merge them by adding a root node w where the frequency of the new node is the sum of those of u and v.
    4. Replace u and v by w.

#### 2. Implementation and Time Complexity 

- Implementation issues
  - How can you manage a dynamic set to which the following operations occur frequently:
    - Delete the elements with the highest priority from the list. 
    - Insert an element with some priority into the list.
    - The answer is to use Priority Queue.
  - The priority queue can be implemented in many ways. Which one would you use?

| Representation        | Insertion    | Deletion     |
| --------------------- | ------------ | ------------ |
| Unordered array       | *O*(1)       | *O*(*n*)     |
| Unordered linked list | *O*(1)       | *O*(*n*)     |
| Sorted array          | *O*(*n*)     | *O*(1)       |
| Sorted linked list    | *O*(*n*)     | *O*(1)       |
| Heap                  | *O*(log *n*) | *O*(log *n*) |

- ✓ The answer is to use the priority queue based on (min) heap.

  ```c++
  typedef struct _node
  {
      char symbol;
      int freq;
      struct _node *left;
      struct _node *right;
  } NODE;
  NODE *u, *v, *w;
  
  for (i = 1; i <= n; i++)
  {
  /* insert the n single-node trees */ }
  for (i = 1; i <= n - 1; i++)
  {
      u = PQ_delete();
      v = PQ_delete();
      w = make_a_new_node();
      w->left = u;
      w->right = v;
      w->freq = u->freq + v->freq;
      PQ_insert(w);
  }
  w = PQ_delete();
  /* w points to the optimal tree. */
  ```

  → $$O (nlogn)$$ time

#### 3. Correctness of the Huffman’s Algorithm

- siblings, branch
- **(Proof by mathematical induction)** 
  - If the set of trees obtained in the *i-*th step are branches in a binary tree corresponding to an optimal code, then the set of trees obtained in the (*i*+1)st step are also branches in a binary tree corresponding to an optimal code.
  - Optimal binary tree
  - i-th step
  - (i+1)-th step
  - – **(Base step)** When *k* = 0, each tree is trivially a branch of an optimal tree.
  - – **(Induction step)** Suppose that the proposition is true when *k* = *i*, that *S* is the set of trees that exist after the *i*th step, and that *T* is the corresponding optimal tree. Let *u* and *v* be the root of the trees combined in the (*i*+1)st step. 
  - **Case 1:** If *u* and *v* are siblings in *T*, we are done.
  - **Case 2:** Otherwise, assume that *u* is at a level in *T* at least as low as *v*, and that *w* is the *u*’s sibling in *T*.
    - The branch in *T* with root *w* is one of the trees in *S* or contains one of those trees as a subtree.why?
    - Therefore, *freq*(*w*) ≥ *freq*(*v*) and *depth*(*w*) ≥ *depth*(*v*) in *T*
    - If we create a new tree *T’* by swapping the two branches with root *v* and *w,* then *bits*(*T’*) *= bits(T) +* (*depth*(*w*) *–* *depth*(*v*))***(*freq*(*v*) *–* *freq*(*w*)) ≤ *bits*(*T*)*.*
    - Since *bits*(*T*) ≤ *bits*(*T’*)*, T’* is also optimal. Hence, the proposition also holds when *k* = *i*+1.  What happens if all the steps are done?

### 4) Maximum Non-overlapping Intervals

- Problem
- Example

- Possible strategies for choosing activities 

  - Longest one first
  - Shortest one first
  - Earliest start first
  - Earliest finish first

- Correctness of “Earliest-finish-first”-based algorithm

  - 귀류법 (Proof by contradiction)
  - Selecting *a*1 reduces the problem to finding an optimal solution for activities not overlapping with *a*1.

- Greedy algorithm

  → $$O(n log n + n) = O(n log n)$$ time

### 5) Scheduling: Minimizing Total Time in the System 

- Problem
  - Consider a system in which a server is about to serve n clients. Let *T* = {*t*1, *t*2, ..., *t**n*} be a set of positive numbers, where **t****i** is the estimated time-to-completion for the *i*th client. What is the optimal order of service where the total (wait+service) time in the system is minimized?
  - Hair stylist with waiting clients, pending operations on a shared hard disk, etc.
- Example
  - *T* = {*t*1, *t*2, *t**3*} = {5, 10, 4}

| Schedule  | Total Time in the System          |
| --------- | --------------------------------- |
| [1, 2, 3] | 5 + (5 + 10) + (5 + 10 + 4) = 39  |
| [1, 3, 2] | 33                                |
| [2, 1, 3] | 10 + (10 + 5) + (10 + 5 + 4) = 44 |
| [2, 3, 1] | 43                                |
| [3, 1, 2] | ☞ 4 + (4 + 5) + (4 + 5 + 10) = 32 |
| [3, 2, 1] | 37                                |

- A naïve approach

  - Enumerate all possible schedules of service, and select the optimal one.

    → *O*(*n*!)

- A greedy approach

  - Algorithm: Sort *T* in nondecreasing order to get the optimal schedule. → *O*(*n* log *n*)
  - Correctness: Does the greedy approach always find a schedule that minimizes the total time in the system?
    - 귀류법 (Proof by contradiction)
    - Let *S* = [*s*1, *s*2, ..., *s**n*] be an optimal schedule, and *C*(*S*) be the total time for *S*. 
    - If they are not scheduled in nondecreasing order, then, for at least one *i*(1≤*i*≤*n*-1),*s**i* >*s**i*+1.
    - Now consider the schedule *S’* = [*s*1, *s*2, ..., *s**i*+1, *s**i*, ..., *s**n*] that is obtained by interchanging *s**i* and *s**i*+1.
    - Then,𝐶𝑆 −𝐶𝑆′ =(𝑖∙𝑠𝑖+1+(𝑖+1)∙𝑠𝑖)−(𝑖∙𝑠𝑖+(𝑖+1)∙𝑠𝑖+1)=𝑠𝑖− 𝑠𝑖+1 > 0. Therefore, ...

### 6) Scheduling: Minimizing Lateness

- Problem

  - Let *J* = {1, 2, ..., *n*} be a set of jobs to be served by a single processor.

  - The *i*th job takes **t****i** units of processing time, and is due at time **d****i** .

  - When the *i*th job starts at time *s**i*, its lateness *l**i* = max{0, *s**i* + *t**i* - *d**i* }.

  - Goal: Find a schedule *S* so as to minimize the maximum lateness

    *L* = max{*l**i*}. 

- Example

  - *S* = {3, 2, 6, 1, 5, 4} → maximum lateness = 6

| Job  | *t**i* | *d**i* |
| ---- | ------ | ------ |
| 1    | 3      | 6      |
| 2    | 2      | 8      |
| 3    | 1      | 9      |
| 4    | 4      | 9      |
| 5    | 3      | 14     |
| 6    | 2      | 15     |

- Possible greedy approaches
  - Sort jobs in nondecreasing order of processing time ti 
  - **Shortest Jobs First** **(?)
  - Sort jobs in nondecreasing order of slack di - ti :
  - **Smallest Slack-Time First** **(?)**
  - ** ➢ Sort jobs in nondecreasing order of deadline di :
  - **Earliest Deadline First** **(O)**
  - An optimal schedule *S* = {1, 2, 3, 4, 5, 6} → maximum lateness = 1

| Job  | *t**i* | *d**i* |
| ---- | ------ | ------ |
| 1    | 3      | 6      |
| 2    | 2      | 8      |
| 3    | 1      | 9      |
| 4    | 4      | 9      |
| 5    | 3      | 14     |
| 6    | 2      | 15     |

- Correctness of “Earliest-deadline-first”-based algorithm

  - 사실

    1. 만약 주어진 schedule에 inversion이 있을 경우, 최소한 연달아 schedule된 두 개의 inversion된 job이 있음.
       - Inversion이란 deadline 관점에서 봤을 때 서로 순서가 뒤 바뀐 두 개의 job의 쌍 을 말함.

    2. 연달아 있는 inversion 상태의 두 개의 job의 순서를 서로 바꿀 경우, maximum lateness를 증가시키지 않음.

  - 증명

    1. *S*를 최소 개수의 inversion을 가지는 최적의 schedule이라 가정. 
    2. 만약 *S*에 inversion이 없다면, 위의 방법으로 구한 schedule과 동일.
    3. 만약 *S*에 inversion이 있다면, 이 경우 연달아 있는 inversion된 두 job의 순서를 서로 바꾸면, 결과로 발생하는 schedule *S’*는 maximum lateness를 증가시키지 않음으로 역시 또 다른 최적의 schedule임.
    4. 그러나 *S’*는 *S* 보다 inversion의 개수가 적음. 이는 *S*에 대한 가정에 대한 모순. 따라 서 *S*에는 inversion이 없고 따라서 이는 위의 방법으로 구한 schedule과 동일함.

### 7) Scheduling with Deadlines

- Problem
  - Let *J* = {1, 2, ..., *n*} be a set of jobs to be served. 
  - Each job takes one unit of time to finish.
  - Each job has a deadline and a profit.
    - If the job starts before or at its deadline, the profit is obtained.
  - Schedule the jobs so as to maximize the total profit (not all jobs have to be scheduled).

- Example:

| Schedule | Total Profit |
| -------- | ------------ |
| [1, 3]   | 30 + 25 = 55 |
| [2, 1]   | 35 + 30 = 65 |
| [2, 3]   | 35 + 25 = 60 |
| [3, 1]   | 25 + 30 = 55 |
| [4, 1]   | ☞ 40+30=70   |
| [4, 3]   | 40 + 25 = 65 |

| Job  | Deadline | Profit |
| ---- | -------- | ------ |
| 1    | 2        | 30     |
| 2    | 1        | 35     |
| 3    | 2        | 25     |
| 4    | 1        | 40     |

- A greedy approach

  - Sort the jobs in non-increasing order by profit.
  - Scan each job in the sorted list, adding it to the schedule if possible.

- Example

  S = EMPTY

  Is S = {1} OK? • Yes: Sß{1} ([1])

  Is S = {1, 2} OK? • Yes: Sß{1, 2} ([2, 1])

  Is S = {1, 2, 3} OK? • No.

  Is S = {1, 2, 4} OK?

  Yes: Sß{1, 2, 4} ([2, 1, 4] or [2, 4, 1])

  Is S = {1, 2, 4, 5} OK? • No.

  Is S = {1, 2, 4, 6} OK? • No.

  Is S = {1, 2, 4, 7} OK? • No.

   

  <After sorting by profit>

| Job  | Deadline | Profit |
| ---- | -------- | ------ |
| 1    | 3        | 40     |
| 2    | 1        | 35     |
| 3    | 1        | 30     |
| 4    | 3        | 25     |
| 5    | 1        | 20     |
| 6    | 3        | 15     |
| 7    | 2        | 10     |



| Job   | Deadline | Profit |
| ----- | -------- | ------ |
| **1** | **1**    | 100    |
| **2** | **6**    | 80     |
| **3** | **3**    | 90     |
| **4** | **3**    | 120    |
| **5** | **5**    | 40     |
| **6** | **4**    | 105    |
| **7** | **1**    | 115    |
| **8** | **2**    | 85     |
| **9** | **4**    | 50     |

• Example **1**

#### Implementation Issues

- A key operation in the greedy approach

  - Determine if a set of jobs *S* is feasible.
  - Fact
    - *S* is feasible if and only if the sequence obtained by ordering the jobs in *S* according to nondecreasing deadlines is feasible.

  - Example
    - Is *S* = {1, 2, 4} OK?→[2(1), 1(3), 4(3)]→Yes!
    - Is *S* = {1, 2, 4, 7} OK?→[2(1), 7(2), 1(3), 4(3)]→No

- An $O(n^2)$implementation

  - Sort the jobs in non-increasing order by profit. 
  - For each job in the sorted order,
    - See if the current job can be scheduled together with the previously selected jobs, using a linked list data structure.
      - If yes, add it to the list of feasible sequence.
      - Otherwise, reject it.

  - Time complexity
    - When there are *i*-1 jobs in the sequence,
      - at most *i*-1 comparisons are needed to add a new job in the sequence, and
      - at most *i* comparisons are needed to check if the new sequence is feasible.

- Is the time complexity always **O****(n2)**? **
  - **What if **n** **>>** **d****max**?**
    - **• *O*(*n*log*n*+*nd**max*)
  - What if **n** **>>** **d****max** and **n** **>>** **k****scanned**?**
    - **• *O*(*n* + *k**scanned* log *n* + *k**scanned* *d**max*) = *O*(*n*)  
    - Is this complexity achievable when a max heap data structure is employed?

| Job   | Deadline | Profit |
| ----- | -------- | ------ |
| **1** | **1**    | 100    |
| **2** | **6**    | 80     |
| **3** | **3**    | 90     |
| **4** | **3**    | 120    |
| **5** | **5**    | 40     |
| **6** | **4**    | 105    |
| **7** | **1**    | 115    |
| **8** | **2**    | 85     |
| **9** | **4**    | 50     |

#### Correctness of the Greedy Method

- Left as an exercise.

## 2. Data Structures for Disjoint Sets 

- [ref](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)

- Partition

  - A partition of a set X is a set of non-empty subsets of X such that every element x in X is in exactly one of these subsets.
  - Example: X = {1, 2, 3, 4, 5, 6}→{ {1, 3, 5}, {2}, {4, 6} }

- Disjoint-set data structure (union-find data structure)

  - Used to effectively manage a collection of subsets that partition a given set of elements.

- Basic operations on disjoint sets

  - Makeset(x)
    - Make a set containing only the given element x.
  - S = Find(x)
    - Determine which set the particular element x is in.
      - Typically return an element that serves as the subset’s representative.
      - May be used to determine if two elements are in the same subset.
  - Union(x, y) (or Merge(*x*, *y*))
    - Merge two subsets into a single subset.

- Applications 

  - Tracking the connected components of an undirected graph
    - Decide if two vertices belong to the same component, or if adding an edge between them would result in a cycle.
    - Useful for implementing the Kruskal’s algorithm for finding minimum spanning tree

- Scheduling with deadlines 

- Computing shorelines of a terrain

- Classifying a set of atoms into molecules or fragments. 

- Connected component labeling in image analysis

- Example
  $$U=\{a,b,c,d,e\}$$

  -  For (each *x* in U) 
     - Makeset(*x*); → {*a*}, {*b*}, {*c*}, {*d*}, {*e*} 
     - Union(*a*, *c*); → {*a*, *c*}, {*b*}, {*d*}, {*e*}
     - {a, c} = Find(a);
     - Union(*c*, *e*); → {*a*, *c*, *e*}, {*b*}, {*d*} 

- Implementation of disjoint sets using reversed trees 

  - Makeset(x)

    ```c++
    Makeset(x){
    	parent(x) := x
    	rank(x) := 0
    }
    ```

    - **Time complexity:** *O*(1)

- Two ways of implementing the Union Operation

  - **Time complexity:** *O*(*n*) 1
  - **Time complexity:** *O*(log *n*)

- **Union by rank**

  - Always attach the smaller tree to the root of the larger tree.
  - The **rank** increases by one only if two trees of the same rank are merged.
    - The rank of a one-element tree is zero.
  - The Union and Find operations can be done in O(log n) in the worst case.
    - The number of elements in a tree of rank r is at least 2 . (Proof by induction)
    - The maximum possible rank of a tree with n elements is O(log n).

- S = Find(x)

  - Time complexity: O(depth of x in the tree)

    ```c++
    Find(x) {
    if (x == parent(x))
    return x else
    return Find(parent(x)) }
    Find(x) {
    while (x != parent(x))
        x := parent(x)
      return x
    }
    ```

- Union(x, y)

  - **Time complexity:** 2 Find op’s *+ O* (1)

    ```c++
    Union(x, y) { x0 := Find(x) y0 := Find(y) if (x0 == y0)
    return
    if (rank(x0) > rank(y0))
      parent(y0) := x0
      else
    parent(x0) := y0
    if (rank(x0) == rank(y0))
    }
    rank(y0) := rank(y0)+1
    ```

    

- Disjoint set의 path compression 연산에 대해서도 알아볼 것.

- Two ways of implementing the Union Operation

  - **Time complexity:** *O*(*n*) 1
  - **Time complexity:** *O*(log *n*)

- **Union by rank**

  - Always attach the smaller tree to the root of the larger tree.
  - The **rank** increases by one only if two trees of the same rank are merged.
    - The rank of a one-element tree is zero.
  - The Union and Find operations can be done in O(log n) in the worst case.
    - The number of elements in a tree of rank r is at least 2 . (Proof by induction)
    - The maximum possible rank of a tree with n elements is O(log n).

- S = Find(x)

  - Time complexity: O(depth of x in the tree)

    ```c++
    Find(x) {
    if (x == parent(x))
    return x else
    return Find(parent(x)) }
    Find(x) {
    while (x != parent(x))
        x := parent(x)
      return x
    }
    ```

- Union(x, y)

  - **Time complexity:** 2 Find op’s *+ O* (1)

    ```c++
    Union(x, y) { x0 := Find(x) y0 := Find(y) if (x0 == y0)
    return
    if (rank(x0) > rank(y0))
      parent(y0) := x0
      else
    parent(x0) := y0
    if (rank(x0) == rank(y0))
    }
    rank(y0) := rank(y0)+1
    ```

    

- Disjoint set의 path compression 연산에 대해서도 알아볼 것.

#### Another Algorithm Based on Disjoint Sets

- Method

  - $$d_{max}$$ : the maximum of the deadlines for *n* jobs.

  - Add a job as late as possible to the schedule being built, but no later than its deadline.

    

  - Sort the jobs in non-increasing order by profit.

  - Initialize $$d_{max}+1$$ disjoint sets, containing the integers $$0, 1, 2, ..., d_{max}$$

  - For each job in the sorted order,

    - Find the set *S* containing the minimum of its deadline and *n*.
      - If small(*S*) = 0, reject the job.
      - Otherwise, schedule it at time small(*S*), and merge *S* with the set containing small(*S*)-1.

- Time complexity

  - $$O(n log m)$$ for the disjoint set manipulation, where *m* is the minimum of $$n$$ and $$d_{max}$$
  - $$O(nlogn$$) for sorting the profits.

- Example

| Job   | Deadline | Profit |
| ----- | -------- | ------ |
| **1** | **1**    | 100    |
| **2** | **6**    | 80     |
| **3** | **3**    | 90     |
| **4** | **3**    | 120    |
| **5** | **5**    | 40     |
| **6** | **4**    | 105    |
| **7** | **1**    | 115    |
| **8** | **2**    | 85     |
| **9** | **4**    | 50     |

