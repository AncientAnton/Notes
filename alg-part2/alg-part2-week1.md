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

## 连通分量

## 难点
