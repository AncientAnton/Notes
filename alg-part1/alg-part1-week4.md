# 算法

Algorithms, Part I 创建者 普林斯顿大学 Week IV

## 优先级队列

- 使用最小二叉堆，根节点小于子节点
- swim 子节点上浮，与比自己大的父节点交换
- sink 父节点下沉，与最小的子节点交换

```
private int N;
private Comparable[] keys;

private void swim(int i) {
    while (i > 0 && less(i, i / 2)) {
        exch(i, i / 2);
        i /= 2;
    }
}

private void sink(int i) {
    while ((2 * i + 1) < N) {
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        if (less(left, i)) {
            if (right < N && less(right, left)) {
                exch(i, right);
                i = right;
            } else {
                exch(i, left);
                i = left;
            }
        }
    }
}
```

队列的插入与删除

```
public void insert(Comparable key) {
    keys[N++] = key;
    swim(N - 1);
}

public Comparable delMax() {
    Comparable max = keys[N - 1];
    exch(0, N--);
    sink(0);
    return max;
}
```

### 堆排序

- 自底向上建堆
- 交换堆顶元素
- 维持堆的性质

```
public static void sort(Comparable[] a)
{
    int N = a.length;
    for (int k = N/2; k >= 1; k--)
        sink(a, k, N);
    while (N > 1)
    {
        exch(a, 1, N);
        sink(a, 1, --N);
    }
}
```

算法名称|原地排序|稳定|最坏|平均|最好|备注
-------|--------|---|----|----|----|----
堆排序  |√       |  |2 N lg N |2 N lg N |N lg N |保证N lg N的性能，原地排序

## 基础符号表

## 符号表

- 有序
- 泛型
- Key-Value
- 链表-数组-红黑树实现

```
void put(Key key, Value val);

Value get(Key key);

void delete(Key key);

boolean contains(Key key);

boolean isEmpty();

int size();

Iterable<Key> keys();
```

## 二叉搜索树

二叉搜索树是对称有序的二叉树

- 左子树的所有节点的值都小于根节点
- 右子树上的所有节点的值都大于根节点

搜索树

*向下遍历*

```
public Value get(Key key)
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

插入节点

*使用递归的写法*

```
public void put(Key key, Value val)
{ root = put(root, key, val); }

private Node put(Node x, Key key, Value val)
{
    if (x == null) return new Node(key, val);
    int cmp = key.compareTo(x.key);
    if (cmp < 0)
        x.left = put(x.left, key, val);
    else if (cmp > 0)
        x.right = put(x.right, key, val);
    else if (cmp == 0)
        x.val = val;
    return x;
}
```

树的高度

- 理想情况：ln N
- 最坏情况：N

最大值与最小值

- 显而易见
- 最大值，优先往右子树找
- 最小值，优先往左子树找

下限Floor与上限Ceiling

- 下限：小于给定值的最大值

```
public Key floor(Key key)
{
    Node x = floor(root, key);
    if (x == null) return null;
    return x.key;
}

public Node floor(Node x, Key key)
{
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    
    if (cmp == 0) return x;
    
    if (cmp < 0) return floor(x.left, key);

    Node t = floor(x.right, key);
    if (t != null) return t;
    else return x;
}
```

- 上限：大于给定值的最小值

```
public Key ceiling(Key key)
{
    Node x = ceiling(root, key);
    if (x == null) return null;
    return x.key;
}

public Node ceiling(Node x, Key key)
{
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    
    if (cmp == 0) return x;
    
    if (cmp > 0) return ceiling(x.right, key);

    Node t = ceiling(x.left, key);
    if (t != null) return t;
    else return x;
}
```

计算子树的大小

- put()操作时，在Node中维护count变量

```
x.count = 1 + size(x.left) + size(x.right);
```

rank

- 小于Key的节点数量

```
public int rank(Key key)
{ 
    return rank(key, root);
}

private int rank(Key key, Node x)
{
    if (x == null) return 0;
    int cmp = key.compareTo(x.key);
    if (cmp < 0) return rank(key, x.left);
    else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right);
    else if (cmp == 0) return size(x.left);
}
```

遍历

- 前序遍历
- 中序遍历
- 后续遍历
- 分层遍历

```
public void travelsal(Node x) {
    if (x == null) return;
    doSth(x);
    travelsal(x.left);
    travelsal(x.right);
}
```

删除

- 删除最小项

```
public void deleteMin()
{
    root = deleteMin(root);
}
private Node deleteMin(Node x)
{
    if (x.left == null) return x.right;
    x.left = deleteMin(x.left);
    x.count = 1 + size(x.left) + size(x.right);
    return x;
}
```

- Hibbard deletion

删除任意值

```

public void delete(Key key)
{
    root = delete(root, key);
}

private Node delete(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp < 0) x.left = delete(x.left, key);
    else if (cmp > 0) x.right = delete(x.right, key);
    else {
        if (x.right == null) return x.left;
        if (x.left == null) return x.right;
        
        Node t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    }
    x.count = size(x.left) + size(x.right) + 1;
    return x;
}
```