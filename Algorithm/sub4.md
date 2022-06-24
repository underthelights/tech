---
layout: article
title: DP
tags: alg
category: alg
picture_frame: shadow
use_math: true
---

Algorithm Design & Analysis  
**[Sub4] Dynamic Programming**

<!--more-->

# [주제 4] Dynamic Programming

## 0. Algorithm Design Techniques

- Divide-and-Conquer Method
- **Dynamic Programming Method**
- Greedy Method
- Backtracking Method
- Local Search Method
- Branch-and-Bound Method
- Etc.

## 1. Dynamic Programming: Overview

- From Wikipedia

  Dynamic programming is both a 

  - mathematical optimization method and 
  - a computer programming method.

- A complicated problem is broken down into simpler sub-problems in a recursive manner.

- Overlapping subproblems: A problem is broken down into subproblems which are reused several times or a recursive algorithm for the problem solves the same subproblem over and over rather than always generating new subproblems.

- Optimal substructure: A solution to a given optimization problem can be constructed efficiently from optimal solutions of its subproblems.

- When applicable, the method takes far less time than other methods that don't take advantage of the subproblem overlap like the divide- and-conquer technique.

#### Two Approaches for Recursive Formulation

##### 1. Top Down Approach

- $T(i,j) = T(i-1,j) + T(i, j-1) + C \cdot (2i + j)$ for $i,j \geq 1$
- $T(i,0) = T(0,j) = 1$ for $i,j \geq 0$
- Easily becomes exponential!

##### 2. Bottom Up Approach

- $T(i,j) = T(i-1,j) + T(i, j-1) + C \cdot (2i + j)$ for $i,j \geq 1$
- $T(i,0) = T(0,j) = 1$ for $i,j \geq 0$
- Often much more efficient!

### [ex1] World Series Odds 

- Problem

- Dodgers and Yankees are playing the World Series in which either team needs to win *n* games first.

- Suppose that each team has a 50% chance of winning any game.

- Let $$P(i,j)$$ be the probability that if Dodgers needs *i* games to win, and

  Yankees needs *j* games, Dodgers will eventually win the Series.

- –  Ex: *P*(2, 3) = 11/16

- –  Compute $$P(i,j) $$  $$ 0 \leq i,j \leq n $$ $$\forall n$$

#### 1. [Worse] A Divide-and-Conquer Approach 

- Recursive formulation
- If we solve this recurrence relation in the divide-and-conquer way, 
- Let *T*(*n*) be the maximum time taken by a call to *P*(*i*,*j*),where *i*+*j* =*n*. Then we can prove that *T*(*n*) is exponential!
- What is the problem of this approach?

#### 2. [Better] A Dynamic Programming Approach 

- Instead of computing the same repeatedly, fill in a table as suggested below:

| 4      | 1    | 15/16 | 13/16 | 21/32 | 1/2   |
| ------ | ---- | ----- | ----- | ----- | ----- |
| 3      | 1    | 7/8   | 11/16 | 1/2   | 11/32 |
| 2      | 1    | 3/4   | 1/2   | 5/16  | 3/16  |
| 1      | 1    | 1/2   | 1/4   | 1/8   | 1/16  |
| 0      |      | 0     | 0     | 0     | 0     |
| *j  i* | 0    | 1     | 2     | 3     | 4     |

- Time Complexity
  -  For input size (m, n), computing P(m, n) takes O(mn)-time.
  -  By far better than the Divide-and-Conquer approach.

## 3. Dynamic Programming

- When the divide-and-conquer approach produces an exponential algorithm where the same sub-problems are solved iteratively,

  1) Take the recursive relation from the divide-and-conquer algorithm, and

  2) replace the recursive calls with table lookups by recording a value in a table entry instead of returning it.

  Top-down → Bottom-up

- Three elements to consider in designing a dynamic programming algorithm

  - Recursive relation
    - Optimal substructure
  - Table setup
  - Table fill order

### Application of DP

### The Manhattan Tourist Problem

- Courtesy of [Jones & Pevzner 6.3]
- Problem:
  - Given two street corners in the borough of Manhattan in New York City, find the path between them with the maximum number of attractions, that is, a path of maximum overall weight.
  - Assume that a tourist may move either to east or to south only.

- A brute force approach
  - Search among all paths in the grid for the longest path!

- A greedy approach
  - 다음 강의 주제

- A formal description of this problem
- Given a weighted graph (grid) G of size (n, m) with two distinguished vertices, a source (0, 0) and a sink (n, m), find a longest path between them in its weighted graph.

**(0, 0)**

- An example grid of size (4, 4)
- A possible selection determined by a greedy approach

- Basic idea
  - How can you use the solutions of smaller problems to build a solution of a problem?
  - A given optimization problem can be constructed efficiently from optimal solutions of its subproblems. →optimal substructure

- Optimal substructure 
  1. 𝒊,𝒋≥𝟏
  2. 𝒊 = 𝟎,𝒋 = 𝟏,𝟐,...,𝒏
  3. 𝒋 = 𝟎,𝒊 = 𝟏,𝟐,..,𝒎
  4. 𝒊=𝒋=𝟎
- Table setup and fill

- Given a (n, m) grid, what is the time complexity T(n, m)?
- So far, we have found the cost of the longest path from source to each vertex in the grid.
- Then, how can you print out the actual optimal path from source to sink?

### Chained Matrix Multiplication

- [Neapolitan 3.4]

- In general, to multiply an *a* x *b* matrix with a *b* x *c* matrix using the standard method, it is necessary to do *abc* elementary multiplications.

- Problem

  - Determine the minimum number of elementary multiplications, needed to multiply *n* matrices where $$ A_i \in R^{d_{i-1} \times d_i}$$

- Examples

  - A1: 20 x 2, A2: 2 x 30
  - A1(A2(A3A4)) : 30x12x8 + 2x30x8 + 20x2x8  = 3,680 multiplications 
  - (A1A2)(A3A4) : = 8,880 multiplications
  - A1((A2A3)A4) : = 1,232 multiplications 
  - ((A1A2)A3)A4 := 10,320 multiplications 
  - (A1(A2A3 ))A4 := 3,120 multiplications

  - The order of multiplication is very important!

#### Dynamic programming approach

- Definition

  - $$M(i, j)$$ : the minimum number of multiplications needed to multiply $$A_i$$ through $$A_j (i \leq j )$$

- Optimal subtructure

- Example: *M*(2, 7)

- d1 x d4 d4 x d7 

- M(2,4) + M(5,7) + d1 x d4 x d7

- M(2,2) + M(3,7) + d1 xd2 xd7 M(2,3) + M(4,7) + d1 xd3 xd7 M(2,4) + M(5,7) + d1 xd4 xd7 M(2,5) + M(6,7) + d1 xd5 xd7 M(2,6) + M(7,7) + d1 xd6 xd7 

   

| *j i* | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1     | 0    |      |      |      |      |      |      |      |
| 2     |      | 0    |      |      |      |      |      |      |
| 3     |      |      | 0    |      |      |      |      |      |
| 4     |      |      |      | 0    |      |      |      |      |
| 5     |      |      |      |      | 0    |      |      |      |
| 6     |      |      |      |      |      | 0    |      |      |
| 7     |      |      |      |      |      |      | 0    |      |
| 8     |      |      |      |      |      |      |      | 0    |



| *j i* | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1     | 0    |      |      |      |      |      |      |      |
| 2     |      | 0    |      |      |      |      |      |      |
| 3     |      |      | 0    |      |      |      |      |      |
| 4     |      |      |      | 0    |      |      |      |      |
| 5     |      |      |      |      | 0    |      |      |      |
| 6     |      |      |      |      |      | 0    |      |      |
| 7     |      |      |      |      |      |      | 0    |      |
| 8     |      |      |      |      |      |      |      | 0    |

- Table fill order

  ```c++
  for (i = 1; i <= n; i++)
      M[i][i] = 0;
  for (g = 1; g <= n - 1; g++)
      for (i = 1; i <= n - g; i++)
      {
          j = i + g;
          M[i][j] = BIG_NUM;
          for (k = i; k <= j - 1; k++)
              if ((tmp = M[i][k] + M[k + 1][j] + d[i - 1] * d[k] * d[j]) < M[i][j]){
                  M[i][j] = tmp;
                  P[i][j] = k;
              }
      }
  ```

  

- Time complexity

| *j i* | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1     | 0    |      |      |      |      |      |      |      |
| 2     |      | 0    |      |      |      |      |      |      |
| 3     |      |      | 0    |      |      |      |      |      |
| 4     |      |      |      | 0    |      |      |      |      |
| 5     |      |      |      |      | 0    |      |      |      |
| 6     |      |      |      |      |      | 0    |      |      |
| 7     |      |      |      |      |      |      | 0    |      |
| 8     |      |      |      |      |      |      |      | 0    |

- Chained matrix multiplication problem
  - $$O(n^3)$$ by Godbole (1973)
  - $$O(n^2)$$ by Yao (1972)
  - $$O(n log n)$$ by Hu and Shing (1982, 1984)

- Printing optimal order

```c++
void order(int i, int j)
{
    int k;
    if (i == j)
        printf(“A_ % d”, i);
    else
    {
        k = P[i][j];
        printf(“(“);
               order(i, k); order(k + 1, j); printf(“)”);
    }
}
```



→ *O*(*n*) time



9-1

2020.10.27 화요일

---



## 4. Principles of Dynamic Programming

- $$C_{ij}$$ = the cost of the shortest path from $$(0,0)$$ to $$(i,j)$$
  - Then $$C_{ij} = min \{C_{i-1,j} + w_{i-1, j} ^{s},C_{i-1,j-1} + w_{i-1, j-1} ^{se},C_{i,j-1} + w_{i, j-1} ^{e}  \}$$ 
- Recursive formulation
- Optimal substructure
- Overlapping subproblems
- Bottom-up approach

#### Optimal Substructure

- From Wikipedia
- Dynamic programming algorithms are often used for     optimization.
- A problem is said to have optimal substructure
  -  if a solution to a given optimization problem can be constructed efficiently from optimal solutions of its subproblems.
- Consequently, the first step towards devising a dynamic programming solution is to check whether the problem exhibits such optimal substructure.
  - Such optimal substructures are usually described by means of recursion.

#### Overlapping Subproblems

- From Wikipedia
- To solve a problem, we often need to solve different     parts of the problem (subproblems), then combine the solutions of the     subproblems to reach an overall solution.
- A problem is said to have overlapping subproblems if      
  - the problem can be broken down into subproblems which are reused several times or
  - a recursive algorithm for the problem solves the same subproblem over and over rather than always generating new subproblems.
- The dynamic programming approach seeks to solve each     subproblem only once, thus reducing the number of computations: 
  - (i) once the solution to a given subproblem has been computed, it is stored or "memoized": 
  - (ii) the next time the same solution is needed, it is simply looked up.

- This approach is especially useful when the number of repeating subproblems grows exponentially as a function of the size of the input.

- If a problem can be solved by combining optimal solutions to non-overlapping sub-problems, 
- the strategy is called "divide-and- conquer" instead. This is why merge sort and quick sort are not classified as dynamic programming problems.
- $$C_{ij} = min \{C_{i-1,j} + w_{i-1, j} ^{s},C_{i-1,j-1} + w_{i-1, j-1} ^{se},C_{i,j-1} + w_{i, j-1} ^{e}  \}$$ 

#### The Checkerboard Problem

- Courtesy of Wikipedia
- Restrictions
- – A checker can start at any square on the first row (*i* = 1).
- – It can move only diagonally left forward, diagonally right forward, or straight forward.
- – It must pay the cost *c*[i] when visiting the (*i*, *j*)-position.

| **i\**j\**** | **1** | **2** | **3** | **4** | **5** |
| ------------ | ----- | ----- | ----- | ----- | ----- |
| 1            | **7** | **3** | **5** | **6** | **1** |
| 2            | **2** | **6** | **7** | **0** | **2** |
| 3            | **3** | **5** | **7** | **8** | **2** |
| 4            | **7** | **6** | **1** | **1** | **4** |
| 5            | **6** | **7** | **4** | **7** | **8** |

 

| **i\**j\**** | **1** | **2** | **3** | **4** | **5** |
| ------------ | ----- | ----- | ----- | ----- | ----- |
| 1            | **7** | **3** | **5** | **6** | **1** |
| 2            | **2** | **6** | **7** | **0** | **2** |
| 3            | **3** | **5** | **7** | **8** | **2** |
| 4            | **7** | **6** | **1** | **1** | **4** |
| 5            | **6** | **7** | **4** | **7** | **8** |

Cost table c [i] [j]

- Problem
  - Given a checkerboard with *n*x*n* squares, and a cost function *c*[i] find the minimum-cost path from the first row to the last row.

- Optimal substructure

| **i\**j\**** | **1**  | **2**  | **3**  | **4**  | **5**  |
| ------------ | ------ | ------ | ------ | ------ | ------ |
| 1            | **7**  | **3**  | **5**  | **6**  | **1**  |
| 2            | **5**  | **9**  | **10** | **1**  | **3**  |
| 3            | **8**  | **10** | **8**  | **9**  | **3**  |
| 4            | **15** | **14** | **9**  | **4**  | **7**  |
| 5            | **20** | **16** | **8**  | **11** | **12** |

 

| **i\**j\**** | **1** | **2**  | **3**  | **4** | **5**  |
| ------------ | ----- | ------ | ------ | ----- | ------ |
| 1            | **0** | **0**  | **0**  | **0** | **0**  |
| 2            | **1** | **0**  | **-1** | **1** | **0**  |
| 3            | **0** | **-1** | **1**  | **0** | **-1** |
| 4            | **0** | **-1** | **0**  | **1** | **0**  |
| 5            | **1** | **1**  | **1**  | **0** | **-1** |

Q table q P table p[i] [j]

```c++
#include <stdio.h> #define N 5
#define INFTY 100000
int c[N + 1][N + 2] = {-1, -1, -1, -1, -1, -1, -1, -1, 7, 3, 5, 6, 1, -1, -1, 2, 6, 7, 0, 2, -1, -1, 3, 5, 7, 8, 2, -1, -1, 7, 6, 1, 1, 4, -1, -1, 6, 7, 4, 7, 8, -1};
int p[N + 1][N + 2], q[N + 1][N + 2];
int min3(int a, int b, int c)
{
    ...
}
void ComputeCBCosts(int n)
{
    int i, j, min;
    for (i = 1; i <= n; i++)
        q[1][i] = c[1][i];
    for (i = 1; i <= n; i++)
    {
        q[i][0] = INFTY;
        q[i][n + 1] = INFTY;
    }
    for (i = 2; i <= n; i++)
    {
        for (j = 1; j <= n; j++)
        {
            min = min3(q[i - 1][j - 1], q[i - 1][j],
                       q[i - 1][j + 1]);
            q[i][j] = min + c[i][j];
            if (min == q[i - 1][j - 1])
                p[i][j] = -1;
            else if (min == q[i - 1][j])
                p[i][j] = 0;
            else
                p[i][j] = 1;
        }
    }
}
void PrintShortestPath(int n, int imin)
{
    printf(" (%d, %d) <-", n, imin);
    if (n == 2)
        printf(" (%d, %d)\n", 1, imin + p[n][imin]);
    else
        PrintShortestPath(n - 1, imin + p[n][imin]);
}
void ComputeCBShortestPath(int n)
{
    int i, imin, min;
    ComputeCBCosts(n);
    imin = 1;
    min = q[n][1];
    for (i = 2; i <= n; i++)
    {
        if (q[n][i] < min)
        {
            imin = i;
            min = q[n][i];
        }
    }
    printf("*** The cost of the shortest path is %d.\n", q[n][imin]);
    PrintShortestPath(n, imin);
}
void main(void)
{
    int n;
    n = N;
    ComputeCBShortestPath(n);
}
```

## 5. [응용1] Longest Common Subsequence (LCS)

- **[T. Cormen et al.,** **Introduction to Algorithms** **(3\****rd** **ed.), The MIT Press, 2009. 16.3]** 
- Definitions
  - Given a sequence $$X = <x1, x2, ..., xm >$$ another sequence $$Z = <z1, z2, ..., zk >$$ is a subsequence of X if there exists a strictly increasing sequence$$ <i1, i2, ..., ik > $$of indices of X such that $$\forall j = 1, 2, ..., k$$, we have $$x_{ij} = z_j$$.
    - A subsequence of a given sequence is just the given sequence with some elements (possibly none) left out.
    - Ex:$$X=<A,B,C,B,D,A,B>, Z=<B,C,D,B>(<2,3,5,7>)$$
  - Given two sequences X and Y, we say that a sequence Z is a common subsequence of X and Y if Z is a subsequence of both X and Y.
    - Ex: 
    - $$X = <A, B, C, B, D, A, B>, Y = <B, D, C, A, B, A>, Z1 = <B, C, A>, Z2 = <B, C, B, A>, Z3 = <B, D, A, B>$$
  - Given a sequence $$X = <x1, x2, ..., xm >, Xi = <x1, x2, ..., xi >$$ is the ith prefix of X, for $$i = 0, 1, ..., m$$.
    - • Ex: 
    - $$X = <A, B, C, B, D, A, B>, X4 = <A, B, C, B>, X0 = null sequence$$

- Problem 
  - Given two sequences $$X = <x1, x2, ..., xm >$$ and $$Y = <y1, y2, ..., yn >$$ 
  - find a longest common subsequence of X and Y.



- Naïve approach

  - Enumerate all subsequences of X and check each subsequence to see if it is also a subsequence of Y, keeping track of the longest subsequence found.

    → Exponential algorithm!

  - The LCS problem can be solved efficiently using dynamic programming.

- Optimal substructure of an LCS

  - Let $$X = <x1, x2, ..., xm >$$ and $$Y = <y1, y2, ..., yn > $$be sequences, and let $$Z = <z1, z2, ..., zk >$$ be any LCS of $$X$$ and $$Y$$.

    1. If $$x_m = y_n$$, 

       then $$z_k = x_m = y_n$$, 

       and $$Z_{k-1}$$ is an LCS of $$X_{m-1}$$ and $$Y_{n-1}$$. 

    2. If $$x_m \neq y_n$$, 

       then an LCS of $$X$$ and $$Y$$ is either an LCS of $$X
       _{m-1}$$ and $$Y$$ 

       or an LCS of $$X$$ and $$Y_{n-1}$$.

- Let $$c[i, j]$$ be the length of an LCS of the sequences $$X_i$$ and $$Y_j$$

- Optimal substructure for computing $$c[i, j]$$

### $$O(mn)$$ Algorithm 

- Filling the table
- Printing the LCS

### C Implementation

- Courtesy of http://www.bioalgorithms.info/downloads/code/

```c++
/** Copyright (C) 2005 Neil Jones. **/
#include <stdio.h>
char *LCS(char *a, char *b);
#define NEITHER 0
#define UP 1
#define LEFT 2
#define UP_AND_LEFT 3
int main(int argc, char *argv[])
{
    printf("%s\n", LCS(argv[1], argv[2]));
}

char *LCS(char *a, char *b)
{
    int n = strlen(a);
    int m = strlen(b);
    int **S;
    int **R;
    int ii;
    int jj;
    int pos;
    char *lcs;

    S = (int **)malloc((n + 1) * sizeof(int *));
    R = (int **)malloc((n + 1) * sizeof(int *));
    for (ii = 0; ii <= n; ++ii)
    {
        S[ii] = (int *)malloc((m + 1) * sizeof(int));
        R[ii] = (int *)malloc((m + 1) * sizeof(int));
    }
    for (ii = 0; ii <= n; ++ii)
    {
        S[ii][0] = 0;
        R[ii][0] = UP;
    }
    for (jj = 0; jj <= m; ++jj)
    {
        S[0][jj] = 0;
        R[0][jj] = LEFT;
    }
    for (ii = 1; ii <= n; ++ii)
    {
        for (jj = 1; jj <= m; ++jj)
        {
            if (a[ii - 1] == b[jj - 1])
            {
                S[ii][jj] = S[ii - 1][jj - 1] + 1;
                R[ii][jj] = UP_AND_LEFT;
            }
            else
            {

                S[ii][jj] = S[ii - 1][jj - 1] + 0;
                R[ii][jj] = NEITHER;
            }
            if (S[ii - 1][jj] >= S[ii][jj])
            {
                S[ii][jj] = S[ii - 1][jj];
                R[ii][jj] = UP;
            }

            if (S[ii][jj - 1] >= S[ii][jj])
            {
                S[ii][jj] = S[ii][jj - 1];
                R[ii][jj] = LEFT;
            }
        }
    }

    ii = n;
    jj = m;
    pos = S[ii][jj];
    lcs = (char *)malloc((pos + 1) * sizeof(char));
    lcs[pos--] = (char)NULL;

    while (ii > 0 || jj > 0)
    {
        if (R[ii][jj] == UP_AND_LEFT)
        {

            ii--;
            jj--;

            lcs[pos--] = a[ii];
        }
        else if (R[ii][jj] == UP)
        {
            ii--;
        }
        else if (R[ii][jj] == LEFT)
        {

            jj--;
        }
    }
    for (ii = 0; ii <= n; ++ii)
    {
        free(S[ii]);
        free(R[ii]);
    }

    free(S);
    free(R);
    return lcs;
}
```





## 6. [응용2] The Gapped Alignment Problem

- Problem
  - Given two sequences, find a gapped alignment that maximize the score!
  - Compare two sequences if they are similar (related). 
  - Gapped alignment
    - Example: A = **ATCGGATCT**, B = **ACGGACT** 5 * 2 +1*(-1) + 4*(-2) = 1 6 * 2 + 1*(-1) + 2*(-2) = 7
    - A possible alignment scoring scheme
      - Ex: match score = 2, mismatch penalty = -1, gap penalty = -2

- Optimal substructure

## 7. [응용3] Longest Increasing Subsequence (LIS)

- Example

- (10, 22, 9, 33, 21, 50, 41, 60, 80)→(10, 22, 33, 50, 60, 80)

- (0, 8, 4, 12, 2, 10, 6, 14, 1, 9, 5, 13, 3, 11, 7, 15)→(0, 2, 6, 9, 11, 15)

  (0, 4, 6, 9, 11, 15) ...

- Algorithm

- Optimal substructure

```c++
int LIS(int *a, int N)
{

    int *best, *prev, i, j, max = 0;

    best = (int *)malloc(sizeof(int) * N);
    prev = (int *)malloc(sizeof(int) * N);
    for (i = 0; i < N; i++)
        best[i] = 1, prev[i] = i;

    for (i = 1; i < N; i++)
        for (j = 0; j < i; j++)
            if (a[i] > a[j] && best[i] < best[j] + 1)
                best[i] = best[j] + 1, prev[i] = j;
    for (i = 0; i < N; i++)
        if (max < best[i])
            max = best[i];

    // Print the LIS using prev[] here. free( best ); free( prev );
    return max;
}

```

[CSE3081(2반)] 알고리즘 설계와 분석

2020학년도 2학기 강의자료

(2020.11.03 화요일)

### Minimal Triangulation

- **[A. Aho, J. Hopcroft, and J. Ullman,** **Data Structures and Algorithms****, Addison-Wesley, 1983. 10.2]**
- Problem
  - Given a set of n vertices for convex polygon, find a triangulation such that no two chords cross each other, and the total length of the chords selected is a minimum.

- Counting all possible selections of chords in an inefficient way results in an exponential algorithm.

## 8. [응용4] The 0-1 Knapsack Problem 

- Problem
- Example
- An intuitive interpretation
  - There are n items in a store.
  - The i th item weighs wi kilograms and is worth pi wons, where wi and pi are positive integers.
  - A thief has a knapsack that can carry at most W kilograms, where W is a positive integer.
  - What items should the thief take to maximize his “profit”?

### 0. A 0-1 Knapsack Problem in Real Life

- [ref](http://standardwisdom.com/softwarejournal/2010/03/bang-for-the-buck-knapsacks-in-real-life/)
- Problem
  - You have a marketing budget of 5 million dollars.
  - You have the following marketing options and their paybacks in new potential customers:

| Option                               | Cost (dollars) | Expected reach (people) |
| ------------------------------------ | -------------- | ----------------------- |
| Super bowl                           | 3M             | 80M                     |
| Radio ad campaign for 40 metro areas | 800K           | 20M                     |
| TV non peak hour campaign            | 500K           | 22M                     |
| City top paper network               | 2M             | 75M                     |
| Viral marketing campaign             | 50K            | 4M                      |
| Web advertising                      | 600K           | 10M                     |

- Which marketing campaigns would you choose to maximize the total expected reach under the condition that, for each of these marketing campaigns, you either select it or you don’t?

### 1. How to Solve the 0-1 Knapsack Problem

- Naïve approach
  - There are 2n subsets of {1, 2, ..., n}! 
- Dynamic programming approach
  -  Let $$P(i,w)$$ be the maximized profit obtained when choosing items only from the first **i** items under the restriction that the total weight cannot exceed **w**.
  -  If we let A* be an optimal subset of {1, 2, ..., n},
  -  Optimal substructure

- Example

w

|      | 0    | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 0    | 0    | 0    | 3    | 3    | 3    |
| 2    | 0    | 0    | 0    | 2    | 3    | 3    | 3    |
| 3    | 0    | 0    | 4    | 4    | 4    | 6    | 7    |
| 4    | 0    | 0    | 4    | 4    | 4    | 8    | 8    |



### 3. How to Reconstruct the Solution

|      | 0    | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 0    | 0    | 0    | 3    | 3    | 3    |
| 2    | 0    | 0    | 0    | 2    | 3    | 3    | 3    |
| 3    | 0    | 0    | 4    | 4    | 4    | 6    | 7    |
| 4    | 0    | 0    | 4    | 4    | 4    | 8    | 8    |

### 4. Implementation and Time Complexity

```c++
int zero_one_knapsack(int *p, int *w, int n, int W)
{
    int i, ww, tmp;
    for (ww = 0; ww <= W; ww++) P[0][ww] = 0;
    for (i = 1; i <= n; i++)
    {
        P[i][0] = 0;
        for (ww = 1; ww <= W; ww++)
        {
            if (w[i] <= ww)
            {
                if ((tmp = p[i] + P[i - 1][ww - w[i]]) > P[i - 1][ww])
                    P[i][ww] = tmp;
                else
                    P[i][ww] = P[i - 1][ww];
            }
            else P[i][ww] = P[i - 1][ww];
        }
    }
    return P[n][W];
}
```

$$O(nW)$$ Time

### 5. 0-1 Knapsack Example 1: n = 6, W = 10

|      | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| pi   | 6    | 4    | 5    | 3    | 9    | 7    |
| wi   | 4    | 2    | 3    | 1    | 6    | 4    |

| P    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |
| 2    | 0    | 0    | 4    | 4    | 6    | 6    | 10   | 10   | 10   | 10   | 10   |
| 3    | 0    | 0    | 4    | 5    | 6    | 9    | 10   | 11   | 11   | 15   | 15   |
| 4    | 0    | 3    | 4    | 7    | 8    | 9    | 12   | 13   | 14   | 15   | 18   |
| 5    | 0    | 3    | 4    | 7    | 8    | 9    | 12   | 13   | 14   | 16   | 18   |
| 6    | 0    | 3    | 4    | 7    | 8    | 10   | 12   | 14   | 15   | 16   | 19   |

| Q    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 0    | 0    | 0    | 1    | 1    | 1    | 1    | 1    | 1    | 1    |
| 2    | 0    | 0    | 1    | 1    | 0    | 0    | 1    | 1    | 1    | 1    | 1    |
| 3    | 0    | 0    | 0    | 1    | 0    | 1    | 0    | 1    | 1    | 1    | 1    |
| 4    | 0    | 1    | 0    | 1    | 1    | 0    | 1    | 1    | 1    | 0    | 1    |
| 5    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 1    | 0    |
| 6    | 0    | 0    | 0    | 0    | 0    | 1    | 0    | 1    | 1    | 0    | 1    |
|      |      |      |      |      |      |      |      |      |      |      |      |
| ?    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| 0    |      |      |      |      |      |      |      |      |      |      |      |
| 1    | 0    |      |      |      |      |      |      |      |      |      |      |
| 2    |      |      | 2    |      |      |      |      |      |      |      |      |
| 3    |      |      |      |      |      | 5    |      |      |      |      |      |
| 4    |      |      |      |      |      |      | 6    |      |      |      |      |
| 5    |      |      |      |      |      |      | 6    |      |      |      |      |
| 6    |      |      |      |      |      |      |      |      |      |      | 10   |

Selected items: i = 2, 3, 4, 6 Obtained profit: 19

- Is the time-complexity $$O(nW)$$ an efficient one? 
  - This is not a linear-time algorithm!
    - A problem is that W is not bounded with respect to n.
    - What if n = 20 and W = 20!?→O(n*n!)
    - When W is extremely large in comparison with n, this algorithm is worse than the brute-force algorithm that simply considers all subsets.
  - This algorithm can be improved so that the worst-case number of entries computed is O(2n).
  - No one has ever found an algorithm for the 0-1 Knapsack problem whose worst-case time complexity is better than exponential, yet no one has proven that such an algorithm is not possible!

### 6. A Variation of the 0-1 Knapsack Problem

- Problem

  - Decision Problem

- Example
  ${ 1, 2, 7, 14, 49, 98, 343, 686, 2409, 2793, 16808, 17206, 117705, 117993 }, L = 138457$

  → ${1, 2, 7, 98, 343, 686, 2409, 17206, 117705}$

- fill (i, j)

#### A Divide-and-Conquer Approach

- Let fill(i,j) return TRUE $\iff$ $\exists$ subset of the first *i* items that has total length *j*.

- When *fill*(*i*, *j*) returns TRUE,
  1 If the *i*th item is used, *fill*(*i* - 1, *j* - *l**i*) must return TRUE. 2 If the *i*th item is not used, *fill*(*i* - 1, *j*) must return TRUE.

  \- To solve fill(int n, int L),

```c++
int fill(int i, int j) { // l[i]: global variable if (i == 0) 
  {
    if(j == 0) return TRUE;
    else return FALSE;
  }

if (fill(i-1, j)) 
  return TRUE;
else if (l[i] <= j)
  return fill(i-1, j-l[i]);
}
```

#### A Dynamic Programming Approach 

- The optimal substructure : $O(nL)$ time implementation

```c++
 F[0][0] = TRUE;
 for (ll = 1; ll <= L; ll++) 
   F[0][ll] =FALSE; 
for (i = 1; i <= n; i++) {
  for (ll = 0; ll <= L; ll++) { 
    F[i][ll] = F[i-1][ll];
    if (ll – l[i] >= 0)
      F[i][ll] = F[i][ll] || F[i-1][ll-l[i]]; 
  }
}
return (F[n][L]);
```

- Example
  - *L* = 15, (*l*1,*l*2,*l*3,*l*4,*l*5,*l*6,*l*7)= (1, 2, 2, 4, 5, 2, 4)

|      | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   | 13   | 14   | 15   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | T    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    |
| 1    | T    | T    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    |
| 2    | T    | T    | T    | T    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    |
| 3    | T    | T    | T    | T    | T    | T    | F    | F    | F    | F    | F    | F    | F    | F    | F    | F    |
| 4    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | F    | F    | F    | F    | F    | F    |
| 5    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | F    |
| 6    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    |
| 7    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    | T    |

##### Subset Sum 

- Problem
- Example
- Application
  - There are 𝑛 jobs, each of which takes 𝑤𝑖 time. Now we have a CPU with 𝑊 free cycles, and want to choose the set of jobs that minimizes the number of idle cycles.

#### Relation to the 0-1 Knapsack problem

- 참고

- If it is possible to solve the 0-1 knapsack problem in polynomial time, the subset sum problem can be solved in polynomial time too.

- Somebody has already proven that the subset sum problem is very hard.
- In other words, the subset sum problem is **NP**-complete. Hence, the 0-1 knapsack problem is also a very hard problem. In other words, the 0-1 knapsack problem is also **NP**-complete.

[주제 5] Greedy Methods

Algorithm Design Techniques

- Divide-and-Conquer Method
- Dynamic Programming Method
- Greedy Method
- Backtracking Method
- Local Search Method
- Branch-and-Bound Method
- Etc.

#### The Fractional Knapsack Problem 

- Problem
- A greedy approach
  1. Sort the items in nonincreasing order by profits per unit weight  						.
  2. Choose the items, possibly partially, one by one until the knapsack is full.
- Example: {*w*1, *w*2, *w*3} = {5, 10, 20}, {*p*1, *p*2, *p*3} = {50, 60, 140}, *W* = 30
  - Choose all of the 1st item: (5, 50)
  - Choose all of the 3rd item: (20, 140)
  - Choose half of the 2nd item: (10/2, 60/2)
- Implementation 1
  - Sort the items → *O*(*n* log *n*) 
  - Repeat the choice → *O*(*n*)
- Implementation 2
  - Put the items in a heap→*O*(*n*)
  - Repeat the choice → *O*(*k* log *n*)
- *O*(*n* + *n* log *n*) = *O*(*n* log *n*) *O*(*n* + *k* log *n*) = ?

$\rightarrow$ Could be faster if only a small number of items are necessary to fill the knapsack.

- The greedy method always find an optimal solution to the fractional Knapsack problem!Correctness
- Does the greedy approach always find an optimal solution to the 0- 1 Knapsack problem?

##### 0-1 Knapsack Example 2: n = 6, W = 10

- 0-1 knapsack (dynamic programming)
- Fractional knapsack (greedy)
- Selected items: i = 3, 4 Obtained profit: 15
- Time Complexity: O(nW)

|      | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| pi   | 4    | 5    | 12   | 3    | 4    | 3    |
| wi   | 4    | 2    | 9    | 1    | 6    | 2    |



|       | 4     | 2     | 6     | 3     | 1     | 5     |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| pi    | 3     | 5     | 3     | 12    | 4     | 4     |
| wi    | 1     | 2     | 2     | 9     | 4     | 6     |
| pi/wi | 3.000 | 2.500 | 1.500 | 1.333 | 1.000 | 0.667 |

- Selected items: i = 4, 2, 6, 3(5) 
- Obtained profit: 17.67
- Time Complexity: O(n log n)

##### 0-1 knapsack (greedy 1)

|       | 4     | 2     | 6     | 3     | 1     | 5     |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| pi    | 3     | 5     | 3     | 12    | 4     | 4     |
| wi    | 1     | 2     | 2     | 9     | 4     | 6     |
| pi/wi | 3.000 | 2.500 | 1.500 | 1.333 | 1.000 | 0.667 |

- Selected items: i = 4, 2, 6 
- Obtained profit: 11
- Time Complexity: O(n log n)

##### 0-1 knapsack (greedy 2)

|       | 4     | 2     | 6     | 3     | 1     | 5     |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| pi    | 3     | 5     | 3     | 12    | 4     | 4     |
| wi    | 1     | 2     | 2     | 9     | 4     | 6     |
| pi/wi | 3.000 | 2.500 | 1.500 | 1.333 | 1.000 | 0.667 |

- Selected items: i = 3 
- Obtained profit: 12
- Time Complexity: O(n log n)