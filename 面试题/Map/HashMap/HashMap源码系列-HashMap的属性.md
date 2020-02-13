# HashMap源码系列-One

## HashMap的属性

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
 // 序列号
 private static final long serialVersionUID = 362498820763181265L;  
 // 默认容量
 static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
 // 最大容量
 static final int MAXIMUM_CAPACITY = 1 << 30; 
 // 默认容载因子
 static final float DEFAULT_LOAD_FACTOR = 0.75f;
 // 转换红黑树的阈值
 static final int TREEIFY_THRESHOLD = 8;
 // 红黑树转换链表的阈值
 static final int UNTREEIFY_THRESHOLD = 6;
 // 最大容量
 static final int MIN_TREEIFY_CAPACITY = 64;
 // 存储数组
 transient Node<K,V>[] table;
 // Key-Value
 transient Set<Map.Entry<K,V>> entrySet;
 // 当前存储的实际容量
 transient int size;
 // 对HashMap的操作次数
 transient int modCount;
 // 扩容的阈值
 int threshold;
 // 容载因子
 final float loadFactor;
}
```

**容载因子**

容载因子越大，table数组中存储的数据越密集，碰撞的可能性就越大。容载因子越小，存储越稀疏，碰撞的可能性就越小，不过浪费存储空间。

**转换红黑树阈值**

当存储数据的链表节点大于等于8时候，就会将链表转换为红黑树。根据泊松分布，当容载因子为0.75的时候，单个槽内及节点数大于等于8的概率为亿分之六。

**红黑树转换为链表的阈值**

当红黑树内的节点小于等于6时，就会将红黑树转化为链表