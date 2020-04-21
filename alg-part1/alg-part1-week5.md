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

