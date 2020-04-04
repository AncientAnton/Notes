# Java 基础集合


## Collection

1. Set

* TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。

* HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。

* LinkedHashSet：具有 HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序。

2. List

* ArrayList：基于动态数组实现，支持随机访问。

* Vector：和 ArrayList 类似，但它是线程安全的。

* LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列。

3. Queue

* LinkedList：可以用它来实现双向队列。

* PriorityQueue：基于堆结构实现，可以用它来实现优先队列。

## Map

* TreeMap：基于红黑树实现。

* HashMap：基于哈希表实现。

* HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable 不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。

* LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。

## 迭代器

```
public interface Iterable<T> {
    Iterator<T> iterator();
}

public interface Iterator<E> {
    boolean hasNext();

    E next();

    default void remove() {
        throw new UnsupportedOperationException("remove");
}
```

## 源码解析

* ArrayList

使用数组存储元素

```
transient Object[] elementData;
private int size;
```

添加元素前，检查是否需要扩容

```
public boolean add(E e) {
    ensureCapacityInternal(size + 1);
    elementData[size++] = e;
    return true;
}
```

如果首次添加，则进行扩容，默认为10

```
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}
```

在原有基础上扩容，默认扩容一半，比如4 => 4 + 2

```
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

* Vector
    * 与ArrayList类似
    * 使用`synchronized`进行同步
    * 设置capacityIncrement控制增长的容量
    * 不设置则翻倍增长

* LinkedList

使用双向链表存储数据

```
transient int size = 0;

transient Node<E> first;

transient Node<E> last;

private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

支持队列操作

```
// Dueue的区别在于操作是双向的
// 添加了addFirst addLast等方法
public interface Queue<E> extends Collection<E> {

    boolean add(E e); // 添加元素到队列头

    boolean offer(E e); // 与add一样，只是不严格要求立刻改变容量

    E remove(); // 移除队列头并返回，不能为null，队列为空抛出异常

    E poll(); // 移除队列头并返回，可以返回null

    E element(); // 返回队列头，不能为null，队列为空抛出异常

    E peek(); // 返回队列头，可以为null
}
```

* HashMap


重要参数

```
    /**
    * 默认初始容量16，必须为2的N次方
    */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * 最大容量，小于 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

    /**
     * 负载参数
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * 将桶从链表转为红黑树的阈值
     */
    static final int TREEIFY_THRESHOLD = 8;

    /**
     * 将桶从红黑树转为链表的阈值
     */
    static final int UNTREEIFY_THRESHOLD = 6;

    /**
     * 最小的桶数目
     */
    static final int MIN_TREEIFY_CAPACITY = 64;
```

单链表节点

```
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
```

计算哈希值

```
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

存储数据

```
    /**
     * 桶
     */
    transient Node<K,V>[] table;

    /**
     * 供 keySet() 与 values() 视图使用
     */
    transient Set<Map.Entry<K,V>> entrySet;

    /**
     * 存储的元素数目
     */
    transient int size;

    /**
     * 修改次数，并发控制
     */
    transient int modCount;

    /**
     * capacity * load factor 
     * 下一次resize的阈值
     */
    int threshold;

    /**
     * 负载参数
     */
    final float loadFactor;
```

添加

```
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

/**
* 
*
* @param hash 哈希值
* @param key 键
* @param value 值
* @param onlyIfAbsent 若为true，则不改变当前已存在的值
* @param evict 若为false，则哈希表正在创建过程中
* @return 键对应已存在的前一个值，若无则返回空
*/
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
            boolean evict)
```

转为红黑树


* LinkedHashMap
    * 继承了HashMap
    * 维护双向链表来维护插入/LRU顺序

```
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}

transient LinkedHashMap.Entry<K,V> head;

transient LinkedHashMap.Entry<K,V> tail;
```

* WeakHashMap