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
