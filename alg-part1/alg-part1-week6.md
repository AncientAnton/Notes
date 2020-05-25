# 算法

Algorithms, Part I 创建者 普林斯顿大学 Week VI

## 哈希表

**存取效率高于lg N**

Key索引的符号表

### 哈希函数

* 空间换时间

通用实现

* 霍纳公式

```
int h = hash;
if (h == 0 && value.length > 0) {
    char val[] = value;

    for (int i = 0; i < value.length; i++) {
        h = 31 * h + val[i];
    }
    hash = h;
}
return h;
```

* 高位与低位异或

```
public static int hashCode(double value) {
    long bits = doubleToLongBits(value);
    return (int)(bits ^ (bits >>> 32));
}
```

### 解决冲突

* 散链法
    * 每个桶中存储链表的头部节点
    * 哈希值相同则放到链表的尾部即可
* 开放寻址法（线性探查）
    * 找到对应的哈希桶
    * 若已被占用，则继续往下找到空桶
    * 如果到达最后一个桶，则从头开始查找

### 数据结构

* java.util.TreeMap
