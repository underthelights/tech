---
layout: article
title: Intro & Complexity
tags: alg
category: alg
picture_frame: shadow
use_math: true

---

Algorithm Design & Analysis  
**[Sub1] Introduction to Algorithms and Complexity**

<!--more-->


# [주제 1] Introduction to Algorithms and Complexity

## 1. How to think and solve problems with computer

#### Data Structure→Algorithm→Theory of Computation

- 어떻게 하면 주어진 복잡한 문제를 이진수 형태의 낮은 수준의 명령어만 이해하는 ‘단순한’ 컴퓨터 상에서 효율적으로 해결할 수 있을까?
  1. [Data Structure] 주어진 추상적인 문제를 **어떠한 자료 구조**를 사용하여 컴 퓨터의 구조에 최적화된 형태로 표현할 수 있을까?
  2. [Algorithm] 주어진 추상적인 문제를 어떠한 **알고리즘을** 사용하여 컴퓨터를 사용하여 가장 효율적으로 해결할 수 있을까
  3. [Complexity] 과연 컴퓨터가 주어진 문제를 **효율적으로** 해결할 수 있을까 ?
  4. [Computability] 과연 컴퓨터가 세상의 **모든 문제를 해결**할 수 있을까?
     - Data Structure & Algorithm $\rightarrow$ 1, 2, 3
     - Automata Theory $\rightarrow$ 4

## 2. Def. of Algorithm

### Definition of Algorithm

- from [Horowitz 1.2]

- An **algorithm** is a **finite set of instructions that**, if followed, accomplishes a particular task. In addition, all algorithms must satisfy the following criteria:

  1)  **Input**. Zero or more quantities from the outside. 외부로부터 0개 이상의 수량이 입력으로서 들어온다.

  2)  **Output**. At least one quantity is produced. 하나 이상의 결과값이 수행된다.

  3)  **Definiteness**. Each instruction is clear and unambiguous. 각 지침은 모두 명확하며, 애매하게 쓰여 있지 않다.

  4)  **Finiteness**. If we trace out the instructions of an algorithm, then for all cases, the algorithm terminates after a finite number of steps. 제한된 수의 단계 후 종료된다.

  5)  **Effectiveness**. Every instruction must be basic enough to be carried out, in principle, by a person using only pencil and paper. 손으로 풀 수 있을 만큼 효과적이어야 한다.

  - It is not enough that each operation be definite as in (3); 
  - it also must be feasible. 또한 실현 가능하여야 한다.

#### Thoughts on 4) Finiteness: [Computability]

- Problem ([Post’s correspondence problem](https://en.wikipedia.org/wiki/Post_correspondence_problem) 포스트 대응 문제)
  - 결정 불가능한 결정 문제의 예시, 1946년 emil post 에 의해 고안
  - Input Size
  - Consider a finite set $P$ of ordered pairs of nonempty strings such as $P = \{(a, ab), (b, ca), (ca, a), (abc, c)\}$
  - A match of $P$ is any string w such that, for some $m > 0$ and some pairs $(u_1, v_1), (u_2, v_2), ..., (u_m, v_m) \in P$, $w = u_1 u_2...u_m = v_1 v_2...v_m$.
  - Design an algorithm that determine, given P, whether P has a match.

- Cheolsu’s algorithm

  ```c++
  For i = 1, 2, 3, ... do 
    For each permutation of P of length i, do
      If it is a match, print ‘yes’ and exit. 
      If not, continue.
  ```

  - Can this be regarded as an algorithm?

### Thoughts on Efficiency: [Complexity]

- An algorithm is regarded as efficient or good if there exist a polynomial P(n) such that the time required for solving any instance of size n is bounded above by P(n).
- NP-Complete problems:
  - Nobody has found so far any good algorithm for any problem in this class.
  - It has been proved that if a good algorithm exists for some algorithm in this class, then a good algorithm exists for all NP-Complete Problem.
- Examples
  - Suppose a CD-ROM can store up to 720MBytes of data. You have a sequence of n files of sizes $s_1, s_2, ..., s_n$  Mbytes, to dump into backup CDs. What is the minimum number of necessary CDs to store all the files?
  - Consider n tasks to be executed on CPU. All the tasks must be finished within the time requirement L (seconds). If the i-th task takes si seconds, and you can harness multiple processors, what would be the minimum number of processors needed to accomplish this?
  - Ex. L = 10, n = 6, and (s1, s2, s3, s4, s5, s6) = (5, 6, 3, 7, 5, 4)  
  - then (5, 5), (6, 4), (7, 3)

어떻게 하면 좀 더 “효율적으로” 문제를 해결할까?

##### Example 1

- Sequential search vs binary search
  - Problem: Determine whether x is in the sorted array S of n keys.
  - Inputs: positive integer n, sorted (nondecreasing order) arrays of keys S indexed from 0 to n - 1, a key x.
  - Outputs: the location of x in S (-1 if x is not in S).
- Sequential search: $T(n) = O(n)$
- Binary search: $T(n) = O(log n)$
- <img width="426" alt="스크린샷 2021-06-19 오후 3 13 01" src="https://user-images.githubusercontent.com/46957634/122632962-ea913980-d110-11eb-9b03-b6829e58c6fc.png">
  - From Neapolitan

- Why is the binary search more efficient? 왜 이진검색이 더 효율적인가?

##### Example 2

- The Fibonacci Sequence

 - Problem: Determine the nth term in the Fibonacci sequence. 

 - Inputs: a nonnegative integer n.

 - Outputs: the nth term of the Fibonacci sequence.

   $f_0 = 0$

   $f_1 = 1$

   $f_n = f_{n-1} + f_{n-2}$ for $n \geq 2$

```c++
//<recursive: divide-and-conquer>
int fib (int n) {
  if (n == 0) return 0;
  else if (n == 1) return 1;
  else return fib(n-1) + fib(n-2);
}

//<iterative: dynamic programming>
  int fib(int n) {
  index i;
  int f[0 .. n];
  f[0] = 0; 
  if (n > 0) {
  	f[1] = 1;
    for (i = 2; i <= n; i++)
      f[i] = f[i-1] + f[i-2];
  }return f[n];
}
```

- Recursive: T(n) = O(2n) 
- Iterative: *T*(n) = O(n)
- Why is the iterative version more efficient?
  - <img width="297" alt="스크린샷 2021-06-19 오후 3 19 16" src="https://user-images.githubusercontent.com/46957634/122633106-c550fb00-d111-11eb-9ca9-9cbe3b4fa085.png">
  - $T(n) > 2 ^{\frac n 2}$ for $n \geq 2$
  - Mathematical induction을 써서 증명해볼 것!
- Linear versus exponential
  - <img width="450" alt="스크린샷 2021-06-19 오후 4 11 33" src="https://user-images.githubusercontent.com/46957634/122634407-13b5c800-d119-11eb-8474-5ecb80ed0216.png">
  - From Neapolitan,

## 3. Order of Algorithms 

### $ O$ (Big $O$ Notation)

- for given two functions $f(n)$ and $g(n)$ , $g(n) = O(f(n))$ 

  $\iff \exists c \in \mathbb{R}, N \in \mathbb{N}\quad s.t. \quad g(n)\leq c\cdot f(n) $ $\forall n \geq  N$

- then we say that : $g(n)$ is big O of $f(n)$ 

```c++
x = x + 1;
for (i = 1; i <= n; i++)
	y = y + 2;
for (i = n; i >=1; i--)
	for (j = n; j >= 1; j--)
		z = z + 1;
```

- 정답 : $g(n) = c_0 + c_1 n + c_2 n^2$

- 예 : $g(n) = 5 + 6 + 7n^2 \leq 8n^2 \quad \forall n \geq 8$
  - $g(n) = O(n^2)$

#### Notes for big O

- [Note 1] The big O puts an **asymptotic** upper bound on a function. 

  - Asymptotic analysis (from Wikipedia)

    > If f(n) = n2 + 3n, then as n becomes very large, the term 3n becomes insignificant compared to n2. The function f(n) is said to be "asymptotically equivalent to n2, as n → ∞". This is often written symbolically as f(n) ~ n2, which is read as "f(n) is asymptotic to n2".

  - 계산 비용이 $0.01n^2$ 과 $100n$ 알고리즘 중 어떤 것이 더 효율적인가?

  - (Tight) upper bound

    - $37log n + 0.1n = O(n)$
    - $n2 + 10n = O(n^2)$
    - $4(log n)^2 + nlog n + 100n = O(nlog n) $
    - $n^2 + 10n = O(n^200)$ ???

  - Growth Rates of Some Common Complexity Functions

    - <img width="269" alt="스크린샷 2021-06-13 오후 3 16 51" src="https://user-images.githubusercontent.com/46957634/121797301-68110180-cc5a-11eb-9164-4e52de117e24.png">

- [Note 2] Given a cost function g(n), how do you find the proper complexity function $f(n)$ such that $g(n) = O(f(n))$?

  - Suppress lower-order terms and constant factors!
  - Example:
    - $10^3 + 10^3n + 10^-3 n^2 = O(n^2)$ then $ lim_{n \to \infty} \frac{n^2}{n} = \infty$
    - $5nlog_3 n + 3(log_2 n)^2 + n + 6n^2 = O(n^2)$ then $ lim_{n \to \infty} \frac{n}{log_en} = lim _{n \to \infty} = \infty$
    - $3(log_2 n)^2+ 0.1n = O(?)$ 
    - $2^{n+5} = O(2^n)$ ??
    - $2^{5n} = O(2^n)$ ??

#### Comparing Orders of Growth 

- How do you compare orders of growth of two functions?
  - One possible way is to compute the limit of the ratio of two functions in question.
  - $ x = lim_{n \to \infty } \frac{f_1(n)}{f_2(n)}$
    - if $x=0$, $f_1$ has a smaller order of growth than $f_2$
    - if $x=c$, $f_1$ has a same order of growth than $f_2$
    - if $x=\infty$, $f_1$ has a larer order of growth than $f_2$
  - Ex.1: $ log_2 n$  vs $ \sqrt{n}$
    - $lim_{n \to \infty} \frac{log_2 n}{\sqrt(n)} = lim_{n \to \infty} \frac{(log_2 n)'}{(\sqrt(n))'} = lim_{n \to \infty} \frac{(log_2 e)\frac{1}{n}}{\sqrt\frac{1}{2\sqrt(n)}} = $
  - Ex.2: $n! $ vs $2^n$
    - $lim_{n \to \infty} \frac{ n!}{2^n} = lim_{n \to \infty} \frac{\sqrt{2 \pi n} (\frac {n}{e})^n}{2^n}=lim_{n \to \infty }\sqrt{2 \pi n} \frac{({n})^n}{2^n e^n}$
    - stirling's formula : $n! \approx \sqrt{2 \pi n} (\frac {n}{e})^n$

### $\Omega$ (Big Omega Notation)

- for two given functions  $f(n)$  , $g(n)$ and  $g(n) = \Omega(f(n))$  $\iff$ $\exists  c \in \mathbb{R}$ and $N \in \mathbb{Z^+ \cup {0}}$ such that $g(n) \geq cf(n) $ $\forall n \geq N$

- We say that $g(n)$ is omega of  $ f(n)$ .
- The $\Omega$ puts an asymptotic lower bound on a function.
- Ex:
  - $37logn+0.1n=\Omega(n)$
  - $n^2 + 10n = \Omega(n^2)$
  - $4(logn)^2 +nlogn+100n=\Omega(nlogn)$
  - $n^{200} +10n=\Omega(n^2)$...
  - <img width="116" alt="스크린샷 2021-06-13 오후 3 20 53" src="https://user-images.githubusercontent.com/46957634/121797403-f7b6b000-cc5a-11eb-9fed-5823b8e67001.png">

### $ \Theta$ (Big Theta Notation)

- for two given functions  $f(n)$  , $g(n)$ and  $g(n) = \Theta(f(n))$  $\iff $ $g(n) = O(f(n)) $ and $g(n) = \Omega (f(n))$
- that is, $g(n) = \Theta (f(n)) $ $\iff$ $\exists  c,d \in \mathbb{R}$ and $N \in \mathbb{Z^+ \cup {0}}$ such that $g(n) \geq cf(n)$ $ \forall n \geq N$

- We say that $g(n)$ is order of $f(n)$.
- The $\Theta$ puts an asymptotic bound on a function.
- Ex:
  - $37logn+0.1n=\Theta(n)$
  - $n^2 + 10n = \Theta(n^2)$
  - $4(logn)^2 +nlogn+100n=\Theta(nlogn)$
  - $\Theta(1)<\Theta(log n)<\Theta(n)<\Theta(n log n)<\Theta(n^2)<\Theta(n^3)<\Theta(n^j)<\Theta(n^k)<\Theta(a^n)<\Theta(b^n)<\Theta(n!)$
    - for $ k>j>3$ and $b>a>1$ 
  - $O(1) $ or $O(c)$ : constant
    - $g(n) = 0.000001 \cdot n $
    - $g(n) = 1000000$ 

- $Ref. $ Neapolitan Ex. (pp.42) 19, 24, 26, 28]

### Big O, Omega, and Order

- <img width="498" alt="스크린샷 2021-06-13 오후 3 23 48" src="https://user-images.githubusercontent.com/46957634/121797483-5f6cfb00-cc5b-11eb-9e3d-4e12939a7723.png">
- $ Ref.$ [ Neapolitan Chapter 1.]

- Execution Times for Algorithms with the Given Time Complexities

<img width="522" alt="스크린샷 2021-06-13 오후 3 24 46" src="https://user-images.githubusercontent.com/46957634/121797495-83c8d780-cc5b-11eb-8b4c-eb530732d32e.png">

<img width="411" alt="스크린샷 2021-06-13 오후 3 25 25" src="https://user-images.githubusercontent.com/46957634/121797525-abb83b00-cc5b-11eb-9c00-50193f7b4e3c.png">

### Worst-Case versus Average-Case Time Complexity

- Expected value (from Wikipedia)

  - let $X$ be a random variable with a finite number of finite outcomes $x_1, x_2, ..., x_k$ occuring with probabilities $p_1, p_2, ... p_k$ respectively.
  - the Expectation of X is defined as : $E(X) = \sum_{i=1}^{k }{x_i p_i} = x_1p_1+ x_2 p_2 + ... + x_k p_k$

  - since the sum of all probabilities $p_i$ is 1 ($\sum_{i=1}^{k} {p_i}=1$) , the expected value is the weighted sum of the $x_i$ values, with the $p_i$ values being the weights

- Worst-case complexity

  - $T_W (n) = max \{ c(I)| I \in S_n \}$

- Average-case complexity

  - $T_A (n) = \sum_{I \in S_n} p(I) c(I) $

- Problem 

  - Find the index of a given value $a$ in a givven array $(a_0, a_1, ...,a _{n-1})$. if $a$ doesn't exist in the array return $-1$

  - Cost for a linear search algorithm

    - let $P_i$ be the probability such that $ a= a_i$

    - then the average cost is :

      $g(n) = 1 \cdot P_0 + 2 \cdot P_1 + 3 \cdot P_2 + ...+ n \cdot P_{n-1} + n (1 - \sum_{k=0}^{n-1} P_k)$

      $ = \sum_{k=0}^{n-1} (k+1)P_k + n (1 - \sum_{k=0}^{n-1} P_k) $

    - Ex.1. $n = 10^9$, $P_0 + P_1 + ...+ P_{10^3} = 1$ so $g(n) = O(1)$

    - Ex.2. $n = 10^9$, $P_0 + P_1 + ...+ P_{\frac n {100} }= 1$ so $g(n) = O(n)$

- 참고: Quick sort 알고리즘 → Worst-case $O(n^2)$, Average-Case $O(n log n) $

#### Reviews - Summation 

- Sums of powers

  - $\sum_{i=1}^{n} i = \frac {n(n+1)} {2}$
  - $\sum_{i=1}^{n} i^2 = \frac {n(n+1)(2n+1)} {6}$
  - $\sum_{i=1}^{n} i^3 = (\frac {n(n+1)} {2})^2$
  - $\sum_{i=1}^{n} i^4 = \frac {n(n+1)(2n+1)(3n^2+3n-1)} {30}$
  - $\sum_{i=1}^{n} i^s = \frac {(n+1)^{s+1}} {s+1} + \sum_{k=1}^{s} \frac {B_k} {s-k+1} {s \choose k} (n+1)^{s-k+1}$
    - $B_k$ is the $k^{th}$ Bernoulli Number.
  - $\sum_{i=1}^{n} i^{-s} = \prod_{p prime} \frac {1} {1 - p^{-s}} = \zeta(s)$
    - $\zeta_k$ is the Riemann zeta function

- Growth rates

  - $\sum_{i=1}^{n} i^c \in \Theta(n^{c+1})$ for real $c$ greater than $-1$

  - $\sum_{i=1}^{n} \frac 1 i \in \Theta(log n)$

  - $\sum_{i=1}^{n} c^i \in \Theta( n \cdot log(n)^{c+1})$ for real $c$ greater than $1$

  - $\sum_{i=1}^{n} log(i)^c \in \Theta(n \cdot log(n)^{c})$ for nonnegative real $c$ 

  - $\sum_{i=1}^{n} log(i)^c \cdot i^d \in \Theta(n^{d+1} \cdot log(n)^{c})$ for nonnegative real $c, d$ 

  - $\sum_{i=1}^{n} log(i)^c \cdot i^d \cdot b^i \in \Theta(n^{d} \cdot log(n)^{c} \cdot b^n)$ for nonnegative real $b>1, c, d$ 

    

- **Read** **http://en.wikipedia.org/wiki/Summation**

- http://en.wikipedia.org/wiki/List_of_mathematical_series.**

##### Reviews - Run Time Analysis

What is the worst-case time complexity of each loop?

```c++
for (i = 0; i < N; i++) 
  for (j = 0; j < N; j++)
    a[i][j] = b[i][j] + c[i][j];
```

```c++
x = 0;
 for (i = 1; i <= N; i++)
   for (j = 1; j <= i; j++) 
     x += i + j;
```

```c++
for (i = 1; i <= N; i++)
  if (i % 2 == 0) a[i] = 1;
	else a[i] = -1;

for (i = 1; i <= N; i++)
	for (j = 1; j <= N; j++) 
    a[i][j] = i + j;
```

```c++
x = 0;
 for (i = 1; i <= N; i++)
   for (j = 1; j <= i; j++) 
     //What if this is i*i?
     for (k = 1; k <= j; k++)
       x += i + j + k;
```

```c++
for (i = 1; i <= N; i++) {
  if (i % 2) {
		for (j = 1; j <= N; j++)
      a[i][j] = i + j;
  }else {
    for (j = 1; j <= N; j++) { 
    	a[i][j] = 0; 
	    for (k = 1; k <= N; k++)
        a[i][j] += k;
    }
  } 
}
```

```c++
x = 0;
for (i = 1; i<=N;i++)
  for (j = 1; j <= i*i; j++) 
    if (j % i == 0)
      for (k = 1; k <= j; k++) 
        x++;
```

- $\rightarrow$ $O(N^4)$
- What is the worst-case time complexity of each loop?

```c++
// float x[n][n+1];
for (i = 0; i <= n-2; i++)
  for (j = i+1; j <= n-1; j++) 
    for (k = i; k <= n; k++)
      x[j][k] = x[j][k] – x[i][k]*x[j][i]/x[i][i];
```

- Could this be faster?	

```c++
// n = 2^k for some positive // integer k
for (i = 1; i < N; i++) {  
  j = n;  
  while (j >= 1) {  
    // some O(1) computation   
    j = j/2;   
  }
}
```

```c++
// n: odd integer
for (i = 0; i < n; i++)    
  for (j = 0; j < n; j++)      
    s[i][j] = 0;   
s[0][(n-1)/2] = 1;   
j = (n-1)/2;
for (key = 2; key <= n*n; key++) { 
  k = (i) ? (i-1) : (n-1);   
  l = (j) ? (j-1) : (n-1); 
  if (s[k][l]) i = (i+1)%n;
  else {  
    i = k; j = l;  
  }
  s[i][j] = key;
}
```

- Magic square : Could this be faster?

| **15** | **8** | **1** | **24** | **17** |
| ------ | ----- | ----- | ------ | ------ |
| 16     | 14    | 7     | 5      | 23     |
| 22     | 20    | 13    | 6      | 4      |
| 3      | 21    | 19    | 12     | 10     |
| 9      | 2     | 25    | 18     | 11     |

```c++
// n = 2^k for some positive
// integer k
i = n;
while (i >= 1){
    j = i;
    while (j <= n){ 
      // some O(1) computation    
	    j = 2*j;   
  	  }i = i/2; 
    }

```

```c++
// compute x^n (n >= 0) 
m = n; 
power = 1; 
z = x; 
while (m > 0) {	
    while (!(m%2)) {   
         m /= 2;     
         z *= z;  
    }m--;
    power *=z; 
}
```

```c++
x = x + 1;
for (i = 1; i <= n; i++) 
	y = y + 2;
	for (i = n; i >=1; i--) 
		for (j = n; j >= 1; j--)    
			z = z + 1;
```

- time complexity. :  $ c_0 + c_1 n + c_2 n^2 = O(n^2)$

```c++
c = 0; // n > 0
for (i = 1; i <= n; i++) 
	for (j = 1; j <= n; j++)    
		for (k = 1; k <= n; k = k*2)      
			c += 2;
```

- time complexity. :  $ c( ⌊{log_2 n}⌋+1) \cdot n^2 = O(n^2)$

```c++
i = 1; j = 1; m = 0; // n > 0
while (j <= n) {  
	i++; 
	j = j + i; 
	m = m + 2;
}
```

- time complexity. :  $??= O( \sqrt n)$

## 4. 최대 부분 수열의 합 Maximum Subsequence Sum

#### Algorithm Design Example

-  $Ref.$ **[M. Weiss,** **Data Structure and Algorithm Analysis in C** (2nd**ed.), Pearson, 1997. 2.4.3]** 
-  Maximum Subsequence Sum (MSS) Problem

   - Given $N$ (possiblly negative) $ \mathbb{Z}$ $A_0, A_1, ..., A_{N-1}$ 
   - find the maximum value of $\sum_{k=i}^{j} {A_k } $ for $ 0 \leq i \leq j \leq N-1$ 

   - for convenience, the max subseuqence sum is 0 if all the integers 're <0
-  Example

   - (-2, 11, -4, 13, -5, -2).  →  MSS = 20
   - <img width="395" alt="스크린샷 2021-06-13 오후 4 00 54" src="https://user-images.githubusercontent.com/46957634/121798325-9abdf880-cc60-11eb-915f-e6c439a2810c.png">
-  Maximum Subarray Problem
-  Maximum Positive Sum Subarray Problem
-  Max. Sum Subsequence versus Max. Subsequence Sum
   - 

#### Alg of Maximum Subsequence Sum

- 길이 *n*인 정수의 수열 $a_0, a_1, ..., a_n-1$ 이 입력으로 주어져 있다. 여기서부분 수열 [*i*, *j*]라는 것은 $a_i, a_{i+1}, a_{i+2}..., , a_{j}$를 말한다.

- 본 문제는 주어진 수열의 부분 수열의 합,즉 $\sum_{i \leq k \leq j} {a_k}$ 의 최대값을 구하는 문제이다. (이때 주어진 수열의 정수가 모두 음수이면 최대 부분 수열의 합은 0 이라고 간주한다)

  - 예를 들어 다음과 같은 수열이 주어졌을 때,
    $ + 31, −41, +59, +26, −53, +58, +97, −93, −23, +84$

    최대 부분 수열은 [2,6]이며 수열의 합은 187 이 된다. 

- 이 문제는 최대 부분 수열의 합을 구하는 것이지만, 앞으로 소개할 알고리즘을 조금만 수정하면 최대 부분 수열도 쉽게 구할 수 있다. 

  > Algorithm1 : 모든 경우의 수 찾기 - $O(N^3)$
  >
  > Algorithm2 : Sum구할 때 중복 조금 피하기 - $O(N^2)$
  >
  > Algorithm3 : Divide n Conquer - $O(N log N)$
  >
  > Algorithm4 : Dynamic Programming - $O(N)$
  - ![image](https://user-images.githubusercontent.com/46957634/122639250-de6aa380-d133-11eb-9a3a-b43a8519b070.png)

##### MSS Algorithm 1

- Strategy

  - Enumerate all possibilities one at a time.

  - No efficiency is considered, resulting in a lot of unnecessary computation!

    ```
    Maxsum = 0
    for (i=0; i < n; i++){
        for (j=i; j < n; j++){
            Thissum = sum(A[i:j])
          	Maxsum = max(Thissum, Maxsum)
        }
    }
    ```
    
  - 모든 경우의 수를 하나하나 모두 따져보는 방법.

    ```c++
    int MaxSubsequenceSum(const int A[], int N){
    int ThisSum, MaxSum, i, j, k;
      //i = 리스트 왼쪽 끝 인덱스, j = 리스트 오른쪽 끝 인덱스, ThisSum = 고려 대상 부분 리스트 합, MaxSum = 문제 최종결론
    MaxSum = 0;
    for (i = 0; i < N; i++)
        for (j = i; j < N; j++)
        {
            ThisSum = 0;
            for (k = i; k <= j; k++)
                ThisSum += A[k];
            if (ThisSum > MaxSum)
                MaxSum = ThisSum;
        }
    return MaxSum;
    }
    
    ```

  - Is this for-loop OK for you?

  - Time Complexity : $ O(N^3)$

    - 𝑖와 관련된 반복문은 𝑛n번, 𝑗와 관련된 반복문은 최대 𝑛번, Thissum을 구할 때 최대 𝑛개의 요소를 계산해야 하기에
    - $\sum_{i=0}^{N-1} \sum_{j=i}^{N-1} \sum_{k=i}^{j} 1 = \frac{N^3 + 3N^2 + 2N}{6}$
    - $\sum_{j=i}^{N-1}\ (j-i+1) = \frac{(N-i+1)(N-i)}{2}$
    - $\sum_{k=i}^{j} 1 = j-i+1$

##### MSS Algorithm 2 

- Strategy

  - Get rid of the inefficiency in the innermost for-loop. Algorithm 1보다 중복을 줄이는 방법

  - ```
    Maxsum = 0
    for (i=0; i < n; i++){
	      for (j=i; j < n; j++){
	          Thissum = sum(A[i:j])
	        	Maxsum = max(Thissum, Maxsum)
	        }
	    }
	  ```
	
	  
	
	- Notice that $\sum_{k=i}^{j } {A_k} = A_j + \sum_{k=i}^{j-1} {A_k}$
	
	- ```c++
	  int MaxSubsequenceSum(const int A[], int N)
	  {
	    int ThisSum, MaxSum, i, j;
	    MaxSum = 0;
      for (i = 0; i < N; i++){
        ThisSum = 0;
  	    for (j = i; j < N; j++){
  		    ThisSum += A[j];
  	  	  if (ThisSum > MaxSum)
  	        MaxSum = ThisSum;
  	    }
  	  }return MaxSum;
  	}
    ```
    
  - time complexity : $O(N^2)$

##### MSS Algorithm 3 

- Strategy

  - Use the Divide-and-Conquer strategy.

    - 원 문제를 작은 문제로 나눠 풀고, 그 결과를 합쳐 문제를 해결하는 알고리즘

  - The maximum subsequence sum can be in one of three places.

    ```c++
    int MaxSubSum(const int A[], int Left, int Right){
    int MaxLeftSum, MaxRightSum;
    int MaxLeftBorderSum, MaxRightBorderSum;
    int LeftBorderSum, RightBorderSum;
    int Center, i;
      //종료조건
    if (Left == Right){
        if (A[Left] > 0)
    				return A[Left];
        else
            return 0;
    }
    //divide n conquer 
    Center = (Left + Right) / 2;
    //왼쪽, 오른쪽, 중간
    MaxLeftSum = MaxSubSum(A, Left, Center);
    MaxRightSum = MaxSubSum(A, Center + 1, Right);
    MaxLeftBorderSum = 0;
    LeftBorderSum = 0;
      
    //1. left ending 끝으로 하는 mss
    for (i = Center; i >= Left; i--){
        LeftBorderSum += A[i];
        if (LeftBorderSum > MaxLeftBorderSum)
            MaxLeftBorderSum = LeftBorderSum;
    }
    MaxRightBorderSum = 0;
    RightBorderSum = 0;
    //2. right ending 시작으로 하는 mss
    for (i = Center; i <= Right; i++){
        RightBorderSum += A[i];
        if (RightBorderSum > MaxRightBorderSum)
            MaxRightBorderSum = RightBorderSum;
    }
    return Max3(MaxLeftSum, MaxRightSum, MaxLeftBorderSum + MaxRightBorderSum);
    }
    int MaxSubsequenceSum(const int A[], int N) { 
    	return MaxSubSum(A, 0, N - 1); 
    }
    
    ```

- cost : $T(n) = 2T(n/2) + cn$, $T(1) = d$

- why $O(N log N)$ ?

  - $T(n) = 2T(\frac n 2) + cn$, $T(1) = d$

    $ = 2 [ 2T(\frac n {2^2}) + c \frac n 2 ] + cn $

    $= 2^2 T [ \frac n {2^2}] + 2cn $

    $= 2^3 T [ \frac n {2^3}] + 3cn =...$

    $= 2^i T [ \frac n {2^i}] + icn $

    $= 2^{log_2 n} T(1) + log_2 n \cdot cn$

    $=nT(1) + log_2 n \cdot cn$

    $= O(n) + O(n log_2 n) = O(n log_2 n)$ 

##### MSS Algorithm 4; Kadane’s algorithm 

- Strategy

  - Use the Dynamic Programming strategy. 

  - subsequence sum<0인 경우, 논리적으로 최대값이 될 수 없음에 착안한 전략

  - 만약에 sum이 음수라도 무방하고 1개 이상의 원소로 구 성된 Subsequence (subarray)를 구하는 문제라면?

    ```c++
    int MaxSubsequenceSum(const int A[], int N){
        int ThisSum, MaxSum, i;
    
        ThisSum = 0; MaxSum = 0;
        for(i = 0; i < N; i++){
            ThisSum += A[i];
            if(ThisSum > MaxSum)
                MaxSum = ThisSum;
            else if(ThisSum < 0)
                ThisSum = 0;
        }
        return MaxSum;
    }
    ```
    
  - Time Complexity : $O(n)$ 

    - for i, iteration n times, and $O(1)$ for 1 calculation

  - C Implementation

    Maximum sum rectangle in a 2D matrix (DP-27) by GeeksforGeeks

```c++
int kadane(int* arr, int* start, int* finish, int n) { int sum = 0, maxSum = INT_MIN;

*finish = -1;int local_start = 0;
for (int i = 0; i < n; ++i) {
    sum += arr[i];
    if (sum < 0) {
        sum = 0; local_start = i+1;
    }
    else if (sum > maxSum) {
        maxSum = sum;
      *start = local_start; *finish = i; }
  **}
** if (*finish != -1) return **maxSum**; // at least one non-negative number.
                                                      // When all numbers in the array are negative
maxSum = arr[0]; *start = *finish = 0;
for (int i = 1; i < n; i++)  {
    if (arr[i] > maxSum) {
        maxSum = arr[i]; *start = *finish = i;
    }
}
                                                      return maxSum; 
                                                     }

```

- Empty subsequence를 허용하면 0을 리턴 (원래 문제)
- Empty subsequence를 허용하지 않으면 음수 중 가장 큰 원소를 리턴

##### So, why do we bother with the time complexity?

## 5. Maximum Sum Subrectangle in 2D Array  

- = max sum submatrix
- Problem
  - Given an mxn array of integers, find a subrectangle with the largest sum. (In this problem, we assume that a subrectangle is **any contiguous sub-array of size 1x1 or greater** located within the whole array.)
- Note
  - What is the input size of this problem?→ (m, n)
    - If m = n→n
  - How many subrectangles are there in an mxn array?
  - For the case of m = n,
    - Design an $$O(n^6)$$ algorithm. 
    - Design an $$O(n^5)$$ algorithm. 
    - Design an $$O(n^4)$$ algorithm. 
    - Design an $$O(n^3)$$ algorithm.

| 0    | -2   | -7   | 0    |
| ---- | ---- | ---- | ---- |
| 9    | 2    | -6   | 2    |
| -4   | 1    | -4   | 1    |
| -1   | 8    | 0    | -2   |

- How many subrectangles are there in an mxn array?

  - for an $$m * n$$ rectangle, 

    $$\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   1$$

    $$= (\sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   1)(\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} 1)$$

    $$ = { \sum_{k=0}^{m-1}(m-k)}{\sum_{i=0}^{n-1}{(n-i)} }$$

    $$= \frac {m(m+1)} {2} \frac {n(n+1)} {2} = O(m^2 n^2) = O(n^4)$$ if $$m=n$$ 

  - [1D case]

#### Maximum Sum Subrectangle: A Naïve Approach

- For each subrectangle, find its sum.

- [가정] $n=m$ 

  $$\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   (j-i+1)(l-k+1) = \sum_{i=0}^{n-1} \sum_{j=i}^{n-1} {(j-i+1)} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   {(l-k+1)} $$

  let $$A$$ = $$\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} {(j-i+1)} $$

  $$A = 1*n + 2*(n-1) +3(n-2) + ... + n*1 = \sum_{i=1}^{n} {i(n-i+1)} = n \sum_{i=1}^n i - \sum_{i=1}^n i^2 + \sum_{i=1}^n i \frac{1}{6} n^3 $$ so $$O(\frac{1}{36} n^6 )$$

- Time Complexity : $O(n^6)$ 

#### Maximum Sum Subrectangle: Summed Area Table

- Table construction: $O(n^2)$
- Sum comparisons: $O(n^4)$
- Time Complexity : $O(n^4)$ 

#### Maximum Sum Subrectangle: Kadane Algo.-Based

- Idea
  - ref. [geeksforgeeks](https://www.geeksforgeeks.org/maximum-sum-rectangle-in-a-2d-matrix-dp-27/)
  - MSS(2D)의 해당 열은 어디이건 i에서 j까지 임.
  - 가능한 모든 (i, j) 조합에 대하여 MSS(1D)를 Kadane 알고리즘을 사용하여 찾음.
  - 그렇게 하기 위하여, ...

```c++
// Program to find maximum sum subarray
// in a given 2D array
#include <stdio.h>
#include <iostream>
#include <string.h>

using namespace std;
#define INT_MAX 2147483647
#define INT_MIN 2147483648
#define ROW 4
#define COL 5

// Implementation of Kadane's algorithm for
// 1D array. The function returns the maximum
// sum and stores starting and ending indexes
// of the maximum sum subarray at addresses
// pointed by start and finish pointers
// respectively.
int kadane(int* arr, int* start, int* finish, int n)
{
	// initialize sum, maxSum and
	int sum = 0, maxSum = INT_MIN, i;

	// Just some initial value to check
	// for all negative values case
	*finish = -1;

	// local variable
	int local_start = 0;

	for (i = 0; i < n; ++i)
	{
		sum += arr[i];
		if (sum < 0)
		{
			sum = 0;
			local_start = i + 1;
		}
		else if (sum > maxSum)
		{
			maxSum = sum;
			*start = local_start;
			*finish = i;
		}
	}

	// There is at-least one
	// non-negative number
	if (*finish != -1)
		return maxSum;

	// Special Case: When all numbers
	// in arr[] are negative
	maxSum = arr[0];
	*start = *finish = 0;

	// Find the maximum element in array
	for (i = 1; i < n; i++)
	{
		if (arr[i] > maxSum)
		{
			maxSum = arr[i];
			*start = *finish = i;
		}
	}
	return maxSum;
}

// The main function that finds
// maximum sum rectangle in M[][]
void findMaxSum(int M[][COL])
{
	// Variables to store the final output
	int maxSum = INT_MIN,
				finalLeft,
				finalRight,
				finalTop,
				finalBottom;

	int left, right, i;
	int temp[ROW], sum, start, finish;

	// Set the left column
	for (left = 0; left < COL; ++left) {
		// Initialize all elements of temp as 0
		memset(temp, 0, sizeof(temp));

		// Set the right column for the left
		// column set by outer loop
		for (right = left; right < COL; ++right) {

			// Calculate sum between current left
			// and right for every row 'i'
			for (i = 0; i < ROW; ++i)
				temp[i] += M[i][right];

			// Find the maximum sum subarray in temp[].
			// The kadane() function also sets values
			// of start and finish. So 'sum' is sum of
			// rectangle between (start, left) and
			// (finish, right) which is the maximum sum
			// with boundary columns strictly as left
			// and right.
			sum = kadane(temp, &start, &finish, ROW);

			// Compare sum with maximum sum so far.
			// If sum is more, then update maxSum and
			// other output values
			if (sum > maxSum) {
				maxSum = sum;
				finalLeft = left;
				finalRight = right;
				finalTop = start;
				finalBottom = finish;
			}
		}
	}

	// Print final values
	cout << "(Top, Left) ("
		<< finalTop << ", "
		<< finalLeft
		<< ")" << endl;
	cout << "(Bottom, Right) ("
		<< finalBottom << ", "
		<< finalRight << ")" << endl;
	cout << "Max sum is: " << maxSum << endl;
}

// Driver Code
int main(){
	int M[ROW][COL] = { { 1, 2, -1, -4, -20 },
						{ -8, -3, 4, 2, 1 },
						{ 3, 8, 10, 1, 3 },
						{ -4, -1, 1, 7, -6 } };

	// Function call
	findMaxSum(M);

	return 0;
}
```

결과는 아래와 같다.

```
(Top, Left) (1, 1)
(Bottom, Right) (3, 3)
Max sum is: 29
```