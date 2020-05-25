# 算法

Algorithms, Part I 创建者 普林斯顿大学 Week V

## 平衡搜索树

符号表无法保证性能，需要BST

### 2-3 搜索树

* 一个节点可以有 1 / 2 个Key
    * 2-节点：一个Key，两个子节点（左节点小于Key，右节点大于Key）
    * 3-节点：两个Key（Key1，Key2），三个子节点（左节点小于Key1，中间节点位于Key1于Key2之间，右节点大于Key2）
* 对称顺序
    * 中序遍历是升序的
* 平衡性
    * 从根节点到任意空节点的路径长度相同

#### 搜索Key

需要额外判断节点类型，2-节点 / 3-节点

#### 插入Key

1. 搜索到目标节点
2. 如果是2-节点，则变为3-节点
3. 如果是3-节点，则变为临时的4-节点
4. 4-节点从中间分裂，中间Key向上合并到父节点
5. 另外两个Key变为2-节点，并成为父节点的子节点
6. 对父节点进行可能的向上分裂合并，直到所有节点符合性质

### 红黑树

2-3 搜索树的一种实现方式

* 红色的父链接 “粘合” 3-节点的两个Key
* 黑色的父链接 连接 2-节点与3-节点

红黑树的性质

* 没有节点与两个红色的链接相连
* 从根节点到任意空节点的黑色路径长度相同
* 红色链接用于指向左节点

*只需要将红色链接水平放置，就是一个2-3 搜索树的结构*

#### 搜索Key

和普通BST的搜索操作一致

```
public Val get(Key key)
{
    Node x = root;
    while (x != null)
    {
      int cmp = key.compareTo(x.key);
      if (cmp < 0) x = x.left;
      else if (cmp > 0) x = x.right;
      else if (cmp == 0) return x.val;
    }
    return null;
}
```

#### 节点数据结构

```
private static final boolean RED = true;
private static final boolean BLACK = false;

private class Node
{
    Key key;
    Value val;
    Node left, right;
    // 父节点链接的颜色
    boolean color;
}

private boolean isRed(Node x)
{
    // 空链接为黑色
    if (x == null) return false;
    return x.color == RED;
}

// root.left.color = RED;
// root.right.color = BLACK;
```

#### 左旋

当指向右节点的链接为红色，左旋

```
// h的父节点指向x
private Node rotateLeft(Node h)
{
    // assert isRed(h.right);
    Node x = h.right;
    h.right = x.left;
    x.left = h;
    x.color = h.color;
    h.color = RED;
    return x;
}
```

#### 右旋

临时将左节点的红色链接旋转到右边，变成临时的3-节点

```
// h的父节点指向x
private Node rotateRight(Node h)
{
    // assert isRed(h.left);
    Node x = h.left;
    h.left = x.right;
    x.right = h;
    x.color = h.color;
    h.color = RED;
    return x;
}
```

#### 颜色变换

将红色链接向上传递，分裂4-节点

```
private void flipColors(Node h)
{
    // assert !isRed(h);
    // assert isRed(h.left);
    // assert isRed(h.right);
    h.color = RED;
    h.left.color = BLACK;
    h.right.color = BLACK;
}
```

#### 插入节点

```
private Node put(Node h, Key key, Value val)
{
    // 在底部插入节点，指定链接为红色
    if (h == null) return new Node(key, val, RED);

    // 递归，处理所有Case
    int cmp = key.compareTo(h.key);
    if (cmp < 0) h.left = put(h.left, key, val);
    else if (cmp > 0) h.right = put(h.right, key, val);
    else if (cmp == 0) h.val = val;

    // 左旋
    if (isRed(h.right) && !isRed(h.left)) h = rotateLeft(h);
    
    // 右旋
    if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);

    // 颜色变换
    if (isRed(h.left) && isRed(h.right)) flipColors(h);
    return h;
}
```

### B-Tree

B-Tree用于处理大量数据，是一种泛化的平衡树

* 连续的数据块
* 快速的定位某页
* 开销最小

#### 结构定义

泛化的2-3搜索树，一个节点拥有更多Key

### 树的应用

红黑树

* Java: java.util.TreeMap, java.util.TreeSet.
* C++ STL: map, multimap, multiset.
* Linux kernel: 完全公平的调度器, linux/rbtree.h.

B-Tree及其变式

* B+ tree B*tree B# tree
* Windows: NTFS
* Mac: HFS, HFS+
* Linux: ReiserFS, XFS, Ext3FS, JFS
* Databases: ORACLE, DB2, INGRES, SQL, PostgreSQL

## 平衡搜索树的几何学应用

处理几何数据（比如2D上的矩形相交问题）

* CAD / 游戏 / 电影 / 数据库

### 一维范围计数

有序符号表的扩展

* 插入 / 删除 / 搜索
* 范围搜索 / 范围计数

如何实现

数据结构|插入|范围计数|范围搜索
-------|----|--------|------
无序列表| 1| N| N
有序数组| N |log N| R + log N
目标| log N| log N| R + log N
N = 键的数量
R = 需要匹配键的数量

### 正交线段交点

N条垂直或者水平的线段，寻找所有交点

* 暴力法 N * N
* 扫描法
  * x坐标降序排列
  * 从左至右扫描
  * 扫描到水平线段左端点，将y坐标插入BST
  * 扫描到水平线段右断点，将y坐标从BST删除
  * 扫描到垂直线段，则对线段y坐标做范围搜索，找到相交点

### KD Tree

从一维扩展到多维

#### 2维范围搜索

* 划分正方形网格
    * 数据具有集群倾向
* 2D Tree
    * 递归的均分分空间
* 4D Tree
* BSP Tree

#### 2D Tree

构造2D Tree

* 插入节点构造BST
* 奇数层垂直划分 / 偶数层水平划分

1. 搜索矩形中包含的点

* 从根节点开始递归向下
* 通过划分方式，判断矩形所在的子树
* 继续向下搜索左 / 右子树

一般情况的复杂度：R + log N
最坏情况的复杂段：R + √N

2. 搜索最邻近点

* 从根节点开始递归向下
* 维护一个最小距离的邻近点
* 继续往下搜索，判断左子树右子树是否可能有更近的点存在
* 直到没有更可能距离更近的点存在

一般情况的复杂度：log N
最坏情况的复杂段：N

3. 蜂群 / 鱼群模拟

Boids算法

* 避免碰撞：远离 k 最近的 Boid。
* 羊群中心：指向 k 最接近的 Boid 的质量中心。
* 速度匹配：将速度更新到 k 最接近的 Boid 的平均值。

#### 2D 到 KD

从一维 到 二维 再到多维

* 2D中奇数层垂直划分，偶数层水平划分，循环进行
* KD中每次划分都划分一个维度，直到K个维度 level = (i mod K)

#### N-Body模拟

模拟粒子之间的引力作用

* 大量的粒子无法全部模拟
* 如果某些粒子距离某个群很远，可以将那个群当作一个聚合的粒子
* 使用中心质量进行计算，而不是计算全部粒子

### 范围搜索树

一维

```
public class IntervalST<Key extends Comparable<Key>, Value>

    IntervalST()

    void put(Key lo, Key hi, Value val)

    Value get(Key lo, Key hi)

    void delete(Key lo, Key hi)

    Iterable<Value> intersects(Key lo, Key hi)

```

插入节点

* 左边界作为BST的Key
* 存储该节点能达到的最大的右边界
* （在向下插入新节点，比较该值与新节点的右边界）

搜索区间(lo, hi)

* 当前节点相交，则返回
* 左子树为空，则搜索右子树
* 左子树的最大右边界比lo小，则搜索右子树
* 否则搜索左子树

```
Node x = root;
while (x != null)
{
    if (x.interval.intersects(lo, hi)) return x.interval;
    else if (x.left == null) x = x.right;
    else if (x.left.max < lo) x = x.right;
    else x = x.left;
}
return null;
```

使用红黑树实现，保证性能

### 正交矩形搜索

扫描线算法（sweep-line algorithm）

* 从左至右扫描垂直线段
* 维护区间搜索树，保存与扫描线相交的区间（矩形的y坐标区间）
* 矩形左端点，插入矩形的y坐标区间
* 矩形右端点，删除矩形的y坐标区间

寻找N的矩形的R个相交结果

* x坐标放入优先级队列/排序 - N log N
* y坐标区间放入搜索树- N log N
* 从搜索树中删除y坐标区间 - N log N
* y坐标区间搜索 - N log N + R log N
