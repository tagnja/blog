---
title: 常见的算法设计策略
date: 2019-09-22 18:00:47
categories: 
- 计算机基础
- 算法
tags: Algorithms
---

## 算法策略

算法策略是指设计和解决一类问题的通用方法或模板。也可以称为算法范式（Algorithm Paradigm）。常见的算法策略，如动态规划、分治、贪心和回溯等。每个算法策略有它的设计思路，解题步骤。算法策略往往提供了优化算法的功能，使用算法策略比直接法（枚举法，穷举法）有更好的效率。



## 动态规划

### 介绍动态规划

动态规划（Dynamic Programming）是一种数学优化方法和一种算法策略。这个方法是 Richard Bellman 在1950 年发明的。它指的是通过递归的方式，将问题分解为更简单的子问题，从而简化一个复杂的问题。

动态规划问题必须具备两个属性：最优子结构和重叠的子问题。如果一个问题有最优子结构，但没有重叠的子问题，那么它是一个分治问题。

**最优子结构（Optimal Substructure）**。如果一个问题可以通过“将问题分解为子问题和递归地找到子问题的最优解”来最优地解决，那么称它有最优子结构。也可以说，得到一个问题的最优解可以通过子问题的最优解来实现。如果一个问题具有最优子结构，较大问题和它的子问题之间的存在关联，这种关系称为 Bellman 方程。

**重叠的子问题（Overlapping Sub-Problem）**。求解过程中递归地求解相同的子问题，没有产生新的子问题。也可以说，问题和子问题存在一个固定的关系表达式，如 F(n) = F(n-1) + F(n-2) ，F(n) = F(n-1) * 2 + 1。所有的子问题可以用一个固定的算法代码去实现。

解决动态规划问题的两种方法：

- **自顶向下方法（Top-down Approach）**。将子问题的结果存储在列表中，当我们要解决新的子问题时，我们会首先检查是否已经解决。如果结果已经记录在列表中，可以直接使用它，否则我们解决子问题并将其结果添加到列表中。如：我们可以通过 F(n) = F(n-1) + F(n-2) 得到问题的结果，先检查子问题结果列表中有没有子问题 F(n-1) 和 F(n-2) 的结果，如果有就直接使用，没有就求解这两个子问题。依次往下递归，每个子问题只计算一次。 
- **自底向上方法（Bottom-up Approach）**。一旦按照子问题递归地制定了问题地解决方案，我们可以尝试自底向上地去解决问题。自底向上地方法大部分不需要记录每个子问题的结果，只需要替换记录几个需要地子问题结果。具体过程：通过使用较小的子问题的结果迭代生成越来越大的子问题结果。如，已知子问题 F40 和 F41 的结果，可以直接计算出 F42 的结果。

### 解题步骤

1\. 判断一个问题是否是动态规划问题。

判断问题是否具有最优子结构和重叠的子问题等特性。

2\. 构造出问题与子问题之间的关系表达式

常见的动态规划问题的子问题关系表达式，见附录一

3\. 使用自顶向下或者自底向上求解。



### 例子

求第 n 个斐波那契数。

直接法简单的解决方法。T(n) = O(n ^ 2), S(n) = O(1)

```
int fib(int n)
{
	if (n <= 1)
		return n;
	return fib(n-1) + fib(n-2);
}
```

动态规划自顶向下方法。T(n) = O(n), S(n) = O(n)

```
int fib(int n, auto &lookup)
{
	if (n <= 1)
		return n;
	if (lookup.find(n) == lookup.end())
		lookup[n] = fib(n-1, lookup) + fib(n-2, lookup);
	return lookup[n];
}
```

动态规划自底向上方法。T(n) = O(n), S(n) = O(1)

```
int fib(int n)
{
	if (n < 1)
		return n;
	int prev = 0, curr = 1;
	for (int i = 0; i < n-1; i++)
	{
		int newFib = prev + curr;
		prev = curr;
		curr = newFib;
	}
	return curr;
}
```



## 分治

### 介绍分治

分治（Divide and Conquer）是一个基于多分支递归的算法设计范式。它将一个问题递归的分解为两个或多个子问题，直到它们足够简单能够直接求解。

### 解题步骤

分析问题得到分解策略。

根据分解策略，递归地求解。

### 例子

参考快速排序、归并排序算法。



## 贪心

### 介绍贪心算法

贪心算法（Greedy Algorithm）是一个算法范式。它在每个阶段做出局部最优选择，以寻求全局最优。

### 解题步骤

根据贪心策略一小段一小段的求解，最后得到问题的最优解。



## 回溯

### 介绍回溯

回溯（Backtracking）是一种算法范式，用于查找某些计算问题的所有结果。它逐步的构建候选对象，一旦确定候选对象无法得到有效的结果立刻将其放弃。

回溯是解决约束满足问题的重要工具。它可以快速测试是否能得出满足条件的有效结果。如填字游戏，数独，棋盘等问题。回溯算法通常比对所有对象的暴力枚举（Brute Force Enumeration）更有效率，因为它通过一个测试减少了很多候选对象。

### 解题步骤

1\. 判断是否是一个回溯问题。

2\. 设定一个开始点，进行循环遍历。

3\. 当前结果是否满足条件。如果是，打印结果。如果不是，循环递归所有的有效的下一个候选对象。

### 例子

N Quuens problem.

```cpp
#include <iostream>
#include <cstring>
using namespace std;

#define N 8

int count = 0;

bool isSafe(char mat[][N], int row, int col)
{
	for (int i = 0; i < row; i++)
	{
		if (mat[i][col] == 'Q')
		{
			return false;
		}
	}
	
	for (int i = row, j = col; i >= 0 && j >= 0; i--, j--)
	{
		if (mat[i][j] == 'Q')
		{
			return false;
		}
	}
	
	for (int i = row, j = col; i >= 0 && j < N; i--, j++)
	{
		if (mat[i][j] == 'Q')
		{
			return false;
		}
	}
	
	return true;
}

void NQueen(char mat[][N], int row)
{	
	if (row == N)
	{
		cout << ++count << endl;
		for (int i = 0; i < N; i++)
		{
			for (int j = 0; j < N; j++)
			{
				cout << mat[i][j] << " ";
			}
			cout << endl;
		}
		cout << endl;
		return;
	}
	
	// place Queen at every square in current row r
    // and recur for each valid movement 
	for (int k = 0; k < N; k++)
	{
		if (isSafe(mat, row, k))
		{
			// place queen on current square
			mat[row][k] = 'Q';
			// recur for next row
			NQueen(mat, row+1);
			// backtrack and remove queen from current square 
			mat[row][k] = '-';
		}
	}
	return;
}

int main()
{
	// mat[][] keeps track of position of Queens in current configuration
	char mat[N][N];
	
	// initalize mat[][] by '-'
    memset(mat, '-', sizeof mat);
	
	int row = 0;
	NQueen(mat, row); // N=8 has 92 possible N-Queen result 
	return 0;
}
```



## 关于算法设计的思路

1\. 思考问题的直接法的解决方法。

2\. 考虑优化。是否存在大量重复的操作？是否存在不必要的操作？

3\. 考虑算法设计策略。

问题的局部最优解能不能得到全局最优。如果能，考虑使用贪心算法。

问题能不能递归地分解为子问题求解（是否具有最优子结构）。如果能，考虑使用分治法，或者动态规划。



## 附录一

常见的动态规划问题的子问题关系表达式。

Fibonacci

```
         | n                  (n <= 1)
Fib(n) = |
         | Fib(n-1) + F(n-2)   (n > 1)
```

Longest Common Subsequence (LCS)

```
             | 0                              (i == 0 or j == 0)
LCS[i][j] =  | LCS[i-1][j-1] + 1              (X[i-1] = Y[j-1])
             | max(LCS[i-1][j], LCS[i][j-1])  (X[i-1] != Y[j-1])
```

Shortest Common Supersequence (SCS)

```
             | i+j                            (i=0 or j=0)
SCS[i][j] =  | SCS[i-1][j-1] + 1              (X[i-1] = Y[j-1])
             | min(SCS[i-1][j] + 1, SCS[i][j-1] + 1)  (X[i-1] != Y[j-1])
```

Longest Increasing Subsequence (LIS)

```
         | 1    (n=1)
LIS(n) = | 
         | max(L(i))    0 < i <= n
```

The Levenshtein distance (Edit distance)

```
             | max(i, j)                       when min(i, j) = 0
             
dist[i][j] = | dist[i - 1][j - 1]              when X[i-1] == Y[j-1]
             
             | 1 + minimum { dist[i - 1][j],   when X[i-1] != Y[j-1]
             |               dist[i][j - 1],
             |               dist[i - 1][j - 1] }
```



References

[1] [Dynamic programming - Wikipedia](https://en.wikipedia.org/wiki/Dynamic_programming)

[2] [How to solve a Dynamic Programming Problem ? - Geeksforgeeks](https://www.geeksforgeeks.org/solve-dynamic-programming-problem/)

[3] [Introduction to Dynamic Programming - Techie Delight](https://www.techiedelight.com/introduction-dynamic-programming/?v=1)

[4] [Divide-and-conquer algorithm - Wikipedia](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm)

[5] [Backtacking - Wikipedia](https://en.wikipedia.org/wiki/Backtracking)