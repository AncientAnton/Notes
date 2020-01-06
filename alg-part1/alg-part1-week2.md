# 算法

Algorithms, Part I 创建者 普林斯顿大学 Week II

## Stacks And Queues

- 栈   `LIFO` = `last in first out`
- 队列 `FIFO` = `first in first out`

同属于基础的集合：添加、删除、迭代等等操作

### stacks

```
void push(T item);

T pop();

boolean isEmpty();

int size();
```

- 链表实现

1. 常见的Node结构
2. 操作方便

- 数组实现

1. 使用数组
2. 扩容（容量翻倍）
3. 缩容（只有1/4容量使用时，缩减到1/2）

### queues

```
void enqueue(String item);

String dequeue();

boolean isEmpty();

int size();
```

- 使用链表实现

## 基础排序

掌握基本的排序算法

### 选择排序

每次遍历，找到右边的最小值，然后交换到左边

```
for (int i = 0; i < N; ++i) {
    int min = i;
    for (int j = i + 1; j < N; ++j) {
        if (lessThan(array[j], array[min])) {
            min = j;
        }
    }
    swap(array, i, min);
}
```

### 插入排序

每次遍历，将右边的值，插入到左边的有序序列中

```
for (int i = 0; i < N; ++i) {
    for (int j = i; j > 0; --j) {
        if (lessThan(array[j], array[j - 1])) {
            swap(array, j, j - 1);
        } else {
            break;
        }
    }
}
```

### 希尔排序

将序列分为多个部分，进行插入排序

```
int h = 1;
int N = array.length;
while (h < N / 3) {
    h = h * 3 + 1;
}
while (h >= 1) {

    for (int i = h; i < N; ++i) {
        for (int j = i; j >= h; j = j - h) {
            if (lessThan(array[j], array[j - h])) {
                swap(array, j, j - h);
            } else {
                break;
            }
        }
    }

    h = h / 3;
}
```

### 洗牌算法

对于每个位置i，随机一个小于i的位置，然后进行互换

```
int N = array.length;
Random random = new Random();
for (int i = 0; i < N; ++i) {
    int r = random.nextInt(i);
    swap(array, i, r);
}
```

### 凸包问题（Convex Hull）

> 二维：给定N个坐标点，凸包可想象为一条刚好包著所有点的橡皮圈
> 多维：在一个实数向量空间V中，对于给定集合X，所有包含X的凸集的交集S被称为X的凸包。
> X的凸包可以用X内所有点(X1，...Xn)的线性组合来构造。

- Graham扫描法

> 先找到凸包上的一个点，然后从这个点开始按时针方向（顺/逆都可以），逐个找凸包上的点（极角排序）

步骤：

1. 找到纵坐标最小的点（一定是凸包上的点）
2. 从这个点开始，计算极角，将极角最小的点加入凸包
3. 重复步骤2，直到所有点都计算完毕