# 刷题之路

Leetcode-CN 题目梯度整理

# 字符串

编号|题目|难度
---|----|----
13|罗马数字转整数|简单
14|最长公共前缀|简单
28|实现 strStr()|简单
3|无重复字符的最长子串|中等
5|最长回文子串|中等
6|Z字型变换|中等
8|字符串转换整数 (atoi)|中等
93|复原IP地址|中等
10|正则表达式匹配|困难
32|最长有效括号|困难
44|通配符匹配|困难
72|编辑距离|困难
76|最小覆盖子串|困难



# 二分查找

编号|题目|难度
---|----|----

# 双指针

编号|题目|难度
---|----|----

# 二叉树

编号|题目|难度
---|----|----

# 堆

编号|题目|难度
---|----|----

# 栈

编号|题目|难度
---|----|----

# 滑动窗口

编号|题目|难度
---|----|----

# 哈希表

编号|题目|难度
---|----|----

# 字典树

编号|题目|难度
---|----|----

# 线段树

编号|题目|难度
---|----|----

# BFS

编号|题目|难度
---|----|----

# DFS

编号|题目|难度
---|----|----

# 记忆化搜索

编号|题目|难度
---|----|----

# 动态规划

编号|题目|难度
---|----|----

# 设计

编号|题目|难度
---|----|----
225|用队列实现栈|中等
146|LRU缓存机制|中等
208|实现 Trie (前缀树)|中等
295|数据流的中位数|困难
297|二叉树的序列化与反序列化|困难
460|LFU缓存|困难

# 进阶

# Session 1 Follow-Up Questions I

* Two Sum
* Two sum II
* Triangle Count
* Kth Smallest Number In Sorted Matrix
* Kth Largest in N Arrays
* Kth Smallest Sum In Two Sorted Arrays

# Session 2 Data Structure I

* Union Find 并查集
* Find the Connected Component in the Undirected Graph
* Find the Weak Connected Component in the Directed Graph
* Number of Islands
* Number of Islands II
* Graph Valid Tree
* Surrounded Regions
* Trie 字典树
  * Implement Trie
* Hash vs Trie
  * Word Search II
  * Add and Search Word
* Typeahead 搜索引擎

* Sweep-Line 扫描线
  * Number of Airplane in the sky
* Summary:
  * Union Find: 集合合并，查找元素在集合里面
  * Trie: 快速找到一个元素，一个字母一个字母查找
  * Sweep-line: 区间拆分

# Session 3 Data Structure II

* Heap 堆: PriorityQueue
  * Trapping Rain Water
  * Trapping Rain Water II
* 小技巧: 矩阵从外向内遍历技巧
  * Building Outline
* Question? 重复元素怎么办？
  * Data Stream Median
  * Sliding Window Median
* 小技巧
  * Sliding Window的题目可以拆解为下面两步：（1）加一个元素；（2）删一个元素.
* Deque 双端队列
  * Sliding Window Maximum
  * Sliding Window Matrix Maximum

# Session 4 Two Pointers

1. 对撞型或者相会型:

* Two sum类
  * Two sum II
  * Triangle Count
  * Trapping Rain Water
  * Container With Most Water
  * Sum Closest
  * 4 Sum
  * 3 Sum
  * k sum
* Partition类
  * Kth Largest Element [Quick Select / Quick Sort]
  * Nuts & Bolts Problem
  * Sort Colors
  * Partition Array by Odd and Even
  * Sort Letters by Case
  * Valid Palindrome
  * Wiggle sort II

2. 前向型或者追击型

* 窗口类
  * Minimum Size Subarray Sum
  * Longest Substring Without Repeating Characters
  * Minimum Window Substring
  * Longest Substring with At Most K (two) Distinct Characters
  * Remove Nth Node From End of List
* 快慢类
  * Find the Middle of Linked List
  * Linked List Cycle I
  * Linked List Cycle II

3. 两个数组两个指针（并行型）

两个数组各找一个元素，使得和等于target：（1）找一种；（2）找全部种类.

* The Smallest Difference
* Merge Two Sorted Lists

# Session 5 Dynamic Programming I

1. 滚动数组优化

```
f[i] = max(f[i-1], f[i-2] + A[i]); 
转换为 f[i%2] = max(f[(i-1)%2]和 f[(i-2)%2]);
```

* House Robber
* House Robber II
* Maximal Square
* 小技巧
  * 网格类的题目
  * 正方形用右下角作为定位角
  * 长方形可以用左上角和右下角作为定位角
* 二维滚动数组相关题目
  * Unique Paths
  * Minimum Path Sum
  * Edit Distance

2. 记忆化搜索

* Longest Increasing Subsequence
* Longest Increasing Continuous Subsequence 2D

3. 博弈类DP

* Coins in a line
* Coins in a line II
* Coins in a line III

# Session 6 Dynamic Programming II

1. 区间类DP 特点

* 求一段区间的解max/min/count
* 转移方程通过区间更新
* 从大到小的更新
  * Stone Game
  * Burst Ballons
  * Scramble String
  * coin in a line III

2. 背包类DP

* BackPack
* BackPack IV
* BackPack II
* K Sum
* Minimum Adjustment Cost

# Session 7 Follow-Up Questions II

* Find Peak Element
* Find Peak Element II
* 第K大问题
  * Kth Largest Element
  * Kth Smallest Number In Matrix
  * Kth Smallest Number In Two Arrays
  * Subarray Sum
  * Submatrix Sum
  * Subarray Sum II
* 循环连续子序列
  * Continuous Subarray Sum
  * Continuous Subarray Sum II
* 旋转排序
  * Wiggle Sort
  * Wiggle Sort II

# 参考

* [Nine Chapter Basic](https://github.com/zhechris/NineChapterBasic)
* [Nine Chapter Senior](https://github.com/Louis9546/Nine_Chapter_Senior)