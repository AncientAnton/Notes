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