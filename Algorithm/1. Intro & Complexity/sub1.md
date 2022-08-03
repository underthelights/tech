---
layout: article
title: Intro & Complexity
tags: alg
category: alg
picture_frame: shadow
use_math: true

---


## 5. Maximum Sum Subrectangle in 2D Array  

- = max sum submatrix
- Problem
  - Given an mxn array of integers, find a subrectangle with the largest sum. (In this problem, we assume that a subrectangle is **any contiguous sub-array of size 1x1 or greater** located within the whole array.)
- Note
  - What is the input size of this problem?→ (m, n)
    - If m = n→n
  - How many subrectangles are there in an mxn array?
  - For the case of m = n,
    - Design an $O(n^6)$ algorithm. 
    - Design an $O(n^5)$ algorithm. 
    - Design an $O(n^4)$ algorithm. 
    - Design an $O(n^3)$ algorithm.

| 0    | -2   | -7   | 0    |
| ---- | ---- | ---- | ---- |
| 9    | 2    | -6   | 2    |
| -4   | 1    | -4   | 1    |
| -1   | 8    | 0    | -2   |

- How many subrectangles are there in an mxn array?

  - for an $m * n$ rectangle, 

    $\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   1$

    $= (\sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   1)(\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} 1)$

    $ = { \sum_{k=0}^{m-1}(m-k)}{\sum_{i=0}^{n-1}{(n-i)} }$

    $= \frac {m(m+1)} {2} \frac {n(n+1)} {2} = O(m^2 n^2) = O(n^4)$ if $m=n$ 

  - [1D case]

#### Maximum Sum Subrectangle: A Naïve Approach

- For each subrectangle, find its sum.

- [가정] $n=m$ 

  $\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   (j-i+1)(l-k+1) = \sum_{i=0}^{n-1} \sum_{j=i}^{n-1} {(j-i+1)} \sum_{k=0}^{m-1} \sum_{l=k}^{m-1}   {(l-k+1)}$

  let $A =\sum_{i=0}^{n-1} \sum_{j=i}^{n-1} {(j-i+1)}$

  $A = 1*n + 2*(n-1) +3(n-2) + ... + n*1 
  \\= \sum_{i=1}^{n} {i(n-i+1)} \\= n \sum_{i=1}^n i - \sum_{i=1}^n i^2 + \sum_{i=1}^n i \frac{1}{6} n^3$ 
  
  - so $O(\frac{1}{36} n^6 )$

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