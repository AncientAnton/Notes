# 算法

Algorithms, Part I 创建者 普林斯顿大学 Week III

## 归并排序

分治法思想，将两个有序子序列进行合并

```
// 数组元素少于7个时，使用插入排序
if (hi <= lo + CUTOFF - 1) {
    Insertion.sort(array, lo, hi);
}

int mid = (lo + hi) / 2;
sort(array, aux, lo, mid);
sort(array, aux, mid + 1, hi);

// 数组已经是有序的，不用再次合并
if (less(array[lo], array[mid+1])) {
    return;
}

// 进行合并，先拷贝到辅助数组，再回写
merge(array, aux, lo, mid, hi);
```

或者自底向上的进行归并

```
int N = array.length;
Comparable[] aux = Arrays.copyOf(array, array.length);
for (int sz = 1; sz < N; sz = sz + sz) {
    for (int lo = 0; lo < N - sz; lo += sz + sz) {
        merge(array, aux, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
    }
}
```

## 快速排序

- 每次选取一个值，将数组划分为两部分
- 左边部分小于该值
- 右边部分大于该值
- 对左右两部分重复上述步骤

```
public void sort(Comparable[] array, int lo, int hi) {
    if (lo == hi) {
        return;
    }
    int partition = partition(array, lo, hi);
    sort(array, lo, partition - 1);
    sort(array, partition + 1, hi);
}
```

### 3路快速排序

类似快速排序，只是每次划分为3部分：小于、大于、等于

```
public void sort(Comparable[] array, int lo, int hi) {
    if (lo == hi) {
        return;
    }

    int lt = lo, gt = hi, i = lo + 1;
    Comparable v = array[lt];

    while (i <= gt) {
        int cmp = array[i].compareTo(v);
        if (cmp < 0) {
            swap(array, lt++, i++);
        } else if (cmp > 0) {
            swap(array, i, gt--);
        } else {
            i++;
        }
    }

    sort(array, lo, lt - 1);
    sort(array, gt + 1, hi);
}
```

## 总结

算法名称|原地排序|稳定|最坏|平均|最好|备注
-------|--------|---|----|----|----|----
选择排序|√       |   |N^2 / 2|N^2 / 2|N^2 / 2|需要N次交换
插入排序|√       |√  |N^2 / 2|N^2 / 4|N      |适用于N很小或者部分有序时
希尔排序|√       |   |?      |?      |N      |代码轻巧
归并排序|        |√  |N lg N |N lg N |N lg N |保证N lg N的性能，稳定
快速排序|√       |   |N^2 / 2|2 N ln N| N lg N|实际中最快，基本能保证N lg N的性能
3路快速排序|√    |   |N^2 / 2|2 N ln N|N      |有重复值时提升了快排的性能