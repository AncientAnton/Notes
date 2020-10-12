# 算法

Algorithms, Part II 创建者 普林斯顿大学 Week I

# 无向图

基本概念

* 节点/结点
* 路径
* 环

常见问题

* 寻找路径
* 寻找最短路径
* 寻找环

* 欧拉回路
    * 图里是否存在一个包含了每一条边一次的环
* 汉密尔顿回路
    * 存在一个只用了每个结点一次的环

* 是否连通
* 最小生成树
    * 用最短的边的集合 连接所有的结点
* 双向连接
    * 是否存在一个结点， 如果移除它会导致整个图不连通

* 平面性
    * 能否在平面中画出这个图，且所有边不相交
* 图的同构
    * 两个图，虽然它们的结点名各不相同，但他们代表了同一个形状

## 图的API

* 结点 0 ~ V - 1 的编号

```
public class Graph
  Graph(int V)                    创建N个节点的空图
  Graph(In in)                    从In流创建图
  void addEdge(int v, int w)      添加v到w的边
  Iterable<Integer> adj(int v)    与v相邻的节点集合
  int V()                         节点数量
  int E()                         边数量
  String toString()               toString
```

* 计算度/入度/出度

## 图的表示

* 边的集合
* 邻接矩阵 V x V大小
    * 对于每条V-W边，adj[v][w] = adj[w][v] = true
* 邻接列表
    * 对于每个节点，维护一个相邻节点的列表

```
public class Graph
{
    private final int V;
    private Bag<Integer>[] adj;

    public Graph(int V)
    {
        this.V = V;
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++)
        adj[v] = new Bag<Integer>();
    }

    public void addEdge(int v, int w)
    {
        adj[v].add(w);
        adj[w].add(v);
    }

    public Iterable<Integer> adj(int v)
    { 
        return adj[v]; 
    }
}
```

实践中采用邻接列表

* 大部分情况下是稀疏图
    * 节点多
    * 平均入度/出度低

## 深度优先搜索

迷宫问题

* Tremaux搜索算法
    * 想象一个小球，后面牵着线
    * 在迷宫中前进，记录经过的位置
    * 无法继续往前时，则回溯路径

* 系统的搜索整个图
* 模拟探索迷宫

```
DFS (vertex v)
---------------
标记v为已访问
递归的访问所有未访问的v的邻接点
```

### 实现

```
public class Paths {
    Paths(Graph G, int s); // 从s开始寻找
    boolean hasPathTo(int v) // s是否可以到达v
    Iterable<Integer> pathTo(int v) // s到达v的所有路径
}
```

具体实现

```
public class DepthFirstPaths
{
    private boolean[] marked;
    private int[] edgeTo;
    private int s;

    public DepthFirstPaths(Graph G, int s)
    {
        // 初始化...
        dfs(G, s);
    }
    private void dfs(Graph G, int v)
    {
        marked[v] = true;
        for (int w : G.adj(v))
        {
            if (!marked[w])
            {
                dfs(G, w);
                edgeTo[w] = v;
            }
        }
        
    }
}
```

### 结论

* DFS 标记连接到 s 的所有顶点的时间，与其与其入度出度的总和成比例。
* DFS 后，可以在恒定时间内找到连接到 s 的顶点
并可以找到一个路径 s（如果存在）的时间与其长度成比例。
* edgeTo[] 是 s 为根节点的树的父链接表示

```
public boolean hasPathTo(int v)
{ 
    return marked[v]; 
}

public Iterable<Integer> pathTo(int v)
{
    if (!hasPathTo(v)) return null;
    Stack<Integer> path = new Stack<Integer>();
    for (int x = v; x != s; x = edgeTo[x])
    {
        path.push(x);
    }
    path.push(s);
    return path;
}
```

## 广度优先搜索

* 将顶点s加入FIFO队列
* 重复，直到队列为空
    * 从队列中删除顶点 v。
    * 添加 v 所有未标记的相邻顶点并标记它们。

### 结论

BFS 计算最短路径（边缘数最少的）从 s 到图形中与 E + V 成正比的所有其他顶点。
* 正确性： 队列始终由零个或多个顶点组成。距离 k，后跟距离 k = 1 的零个或多个顶点。
* 运行时间： 连接到 s 的每个顶点都访问一次。

### 实现

```
public class BreadthFirstPaths
{
    private boolean[] marked;
    private int[] edgeTo;
    …
    private void bfs(Graph G, int s)
    {
        Queue<Integer> q = new Queue<Integer>();
        q.enqueue(s);
        marked[s] = true;
        while (!q.isEmpty())
        {
        int v = q.dequeue();
        for (int w : G.adj(v))
        {
            if (!marked[w])
            {
                q.enqueue(w);
                marked[w] = true;
                edgeTo[w] = v;
            }
        }
    }
}
```

### 应用

* 通信网络中的最少跳数
* Kevin Bacon Numbers
    * 每个表演者一个顶点，每个影片一个顶点。
    * 将影片连接到影片中出现的所有表演者。
    * 计算最短路径，从 s = Kevin Bacon
* Erdös numbers
    * 埃尔德什的埃尔德什数为0
    * 与他直接合作写论文的人的埃数为1
    * 与埃数为1的人合写论文的人埃数为2

## 连通分量

### 连通性查询

* 顶点 v 与 w 是连通的，如果v 与 w 之间存在路径
* 在常数时间查询 v 与 w 是否连通

```
public class CC

    CC(Graph G) // 查找图 G 中的连通分量

    boolean connected(int v, int w) // 判断 v 与 w 是否连通
    
    int count() // 连通分量的数量

    int id(int v) // 所属连通分量的标识符
```

* 连通 是等价关系
    * 自反性：v 连通到 v 。
    * 对称性：如果 v 连通到 w，则 w 连通到 v。
    * 传播性：如果 v 连通到 w，w 连通到 x，则 v 连通到 x。

### 实现

* 将所有顶点 v 初始化为未标记。
* 对于每个未标记的顶点 v
* 通过 DFS 以标识所有作为同一连通分量的顶点。

```
public class CC
{
    private boolean[] marked;
    private int[] id;
    private int count;
    
    public CC(Graph G)
    {
        marked = new boolean[G.V()];
        id = new int[G.V()];
        for (int v = 0; v < G.V(); v++)
        {
            if (!marked[v])
            {
                dfs(G, v);
                count++;
            }
        }
    }

    public int count()
    { 
        return count; 
    }

    public int id(int v)
    { 
        return id[v]; 
    }

    private void dfs(Graph G, int v)
    {
        marked[v] = true;
        id[v] = count;
        for (int w : G.adj(v))
            if (!marked[w])
            dfs(G, w);
    }
}
```

### 应用

* 粒子检测：给定粒子的灰度图像，标识BLOB实体。
    * 顶点：像素。
    * 边缘：两个相邻像素之间，灰度值 >= 70。
    * BLOB：连通的组件包括 20-30 像素。
* 粒子跟踪：跟踪移动粒子。

## 难点

* 判断二分图
    * 顶点集V可分割为两个互不相交的子集，并且图中每条边依附的两个顶点都分属于这两个互不相交的子集
* 查找环
* The Seven Bridges of Königsberg 柯尼斯堡七桥问题
    * 河中心有两个小岛。小岛与河的两岸有七条桥连接。在所有桥都只能走一遍的前提下，如何才能把这个地方所有的桥都走遍？
    * 欧拉回路。是否有一个环，只使用每个边一次？
    * 只有奇顶点数量不大于2个时才成立
* 查找一个环，该循环仅使用每个边一次
    * Hamiltonian cycle 汉密尔顿回路（经典 NP 完全问题）
* 查找一个环，访问每个顶点正好一次
* 除了顶点名称之外，两个图形是否相同？
* 求有向图中强连通分量？
    * 基于 DFS 的线性时间平面算法，Tarjan在20世纪70年代发现的

# 有向图

* 查找路径
* 最短路径
* 拓扑排序
* 强连通
* 闭包(v, w) = v -> w && w -> v
* PageRank

## 实现

```
public class Digraph

    private final int V;
    private final Bag<Integer>[] adj;

    // 创建N个节点的空有向图
    public Digraph(int V) 
    {
        this.V = V;
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++)
            adj[v] = new Bag<Integer>();
    }

    Digraph(int N) 

    Digraph(In in) // 创建有向图

    // 添加一条 v -> w 的边
    public void addEdge(int v, int w)
    {
        adj[v].add(w);
    }

    // v 指向的所有节点
    public Iterable<Integer> adj(int v)
    { 
        return adj[v]; 
    }

    int V() // 节点数量

    int E() // 边数量

    Digraph reverse() // 反转有向图

    String toString() // 字符串
```

## 有向图的搜索

### 可达性搜索

DFS 

* 找到 v 可到达的所有节点
    * 标记 v 为已访问
    * 递归搜索所有 v 指向的未访问节点

应用场景

* 程序控制流分析
    * Dead-code 不可达代码检测
    * Infinite-loop Code 无限循环代码检测
* 标记-清除垃圾收集器

BFS 

* 重复，直到队列为空：
    * 从队列中删除顶点 v。
    * 添加从 v 指向的所有未标记顶点到队列，并标记。

应用场景

* 多源-最短路径
* 网页爬虫

### 拓扑排序

优先级调度

* 给定一组有优先级的任务，子任务需要在父任务完成后调度

DAG = Direct acyclic graph

* 有向无环图
* 使用DFS实现拓扑排序
* 返回后序的反向结果

实现

```
public class DepthFirstOrder
{
    private boolean[] marked;
    private Stack<Integer> reversePost;
    
    public DepthFirstOrder(Digraph G)
    {
        reversePost = new Stack<Integer>();
        marked = new boolean[G.V()];
        for (int v = 0; v < G.V(); v++)
        if (!marked[v]) dfs(G, v);
    }

    private void dfs(Digraph G, int v)
    {
        marked[v] = true;
        for (int w : G.adj(v))
        if (!marked[w]) dfs(G, w);
        reversePost.push(v);
    }

    public Iterable<Integer> reversePost()
    { 
        return reversePost; 
    }
}
```

DFS也可以实现环侦测

### 强连通分量

等价关系：v -> w 之间存在路径 && w -> v 之间存在路径

* Core OR
* 线性时间DFS算法 (Tarjan)
* Kosaraju-Sharir算法
* Cheriyan-Mehlhorn算法

Kosaraju-Sharir算法

反向图。G 中的强组件与 GR 中的强组件相同。
内核 DAG.将每个强组件收缩到单个顶点中。
想法。
•计算内核 DAG 中的拓扑顺序（反向后单）。
•运行 DFS，按反向拓扑顺序考虑顶点。

两遍DFS

1. 在 G(R) 中DFS，计算反向后序串
2. 在 G 中运行 Dfs，以 G(R) 的反向后序串顺序来访问

实现

```
public class KosarajuSharirSCC
{
    private boolean marked[];
    private int[] id;
    private int count;

    public KosarajuSharirSCC(Digraph G)
    {
        marked = new boolean[G.V()];
        id = new int[G.V()];
        DepthFirstOrder dfs = new DepthFirstOrder(G.reverse());
        for (int v : dfs.reversePost())
        {
            if (!marked[v])
            {
                dfs(G, v);
                count++;
            }
        }
    }

    private void dfs(Digraph G, int v)
    {
        marked[v] = true;
        id[v] = count;
        for (int w : G.adj(v))
        if (!marked[w])
            dfs(G, w);
    }

    public boolean stronglyConnected(int v, int w)
    { 
        return id[v] == id[w]; 
    }
}
```
