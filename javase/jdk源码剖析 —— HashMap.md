## jdk源码剖析 —— HashMap

**读本篇文章您将收获到的东西：**

1. **HashMap的数据结构。**
2. **HashMap的构造函数如何实现的。**
3. **HashMap扩容的原理。**
4. **HashMap的put原理。**
5. **HashMap的get原理。**

### 1、HashMap的简单介绍

HashMap的java程序猿的编程生涯中用到的还是蛮多的，我们既然用它用的这么多，那就要了解它的底层原理，要知其然更要知其所以然。

HashMap是一个典型的key-value结构，继承了Map接口，是一个无序的键值对组合（好多人管map也叫集合，这是一种错误的叫法，map不属于集合）。好，跟上，咱们继续。

### 2、HashMap的数据结构

HashMap的底层是基于Hash桶实现的，那什么又是Hash桶呢，其实就是数组加链表，大家都知道，在解决Hash冲突的时候有几种办法

1. 线性探测法

2. 平方探测法

3. 拉链法

4. 再散列函数构造法

5. 公共溢出区法。

   ……

哈希表我在这里就不详细给大家介绍了，近期我会再整理一份关于哈希表的文章，分享给大家。

Hash桶就是拉链法的实现，那么在这里就介绍一下拉链法。比如说咱们有五个数8、16、6、9、1，散列表的大小为8，就使用除留余数法进行哈希表构建，首先8 % 8 = 0；所以8就放在了哈希表的第一个位置，16 % 8 = 0，这怎么办，又得到的是0，好，咱就在哈希表的第一个位置搭建一个链表，将16挂在下面。如图所示。

![1574408643411](C:\Users\LIGHTI~1\AppData\Local\Temp\1574408643411.png)

所以就形成了链状结构，下面的我就不进行构造了。

注意，这种HashMap的数据结构是jdk1.8以前的，jdk1.8对其数据结构进行了升级，当链下的节点数（包含头结点）的数量不小于8的时候，链状结构就会转变为树状（红黑树，对红黑树不了解的小伙伴，可以参考博主的另一篇文章[关于红黑树的介绍](https://www.lightingsui.com/2019/11/22/%e7%ba%a2%e9%bb%91%e6%a0%91%e7%9a%84%e5%ba%95%e5%b1%82%e9%98%90%e8%bf%b0/ )）结构。如图所示

![1574418729232](C:\Users\LIGHTI~1\AppData\Local\Temp\1574418729232.png)

 ### 3、属性的介绍

先给大家说一下HashMap中部分属性的含义：

| 属性字段                 | 含义                                                         |
| ------------------------ | ------------------------------------------------------------ |
| serialVersionUID         | 序列化的id                                                   |
| DEFAULT_INITIAL_CAPACITY | HashMap的默认初始容量                                        |
| MAXIMUM_CAPACITY         | HashMap的最大支持容量                                        |
| DEFAULT_LOAD_FACTOR      | 默认容载因子，用来计算容量扩充阈值的，和下面的loadFactor一起介绍 |
| TREEIFY_THRESHOLD        | 判断链状到树状转化的条件，就是链状节点数大于它就转换为树状   |
| table                    | 哈希桶的容器                                                 |
| size                     | 当前HashMap中节点的个数                                      |
| modCount                 | 使用put的次数                                                |
| threshold                | 容量的阈值，超过这个阈值就要进行HashMap的扩充，计算公式为threshold = loadFactor * curCapacity |
| loadFactor               | 容载因子，当HashMap中的数量大于loadFactor * curCapacity时需要记性HashMap的扩充 |

### 4、节点的结构

```java
/**
 * Basic hash bin node, used for most entries.  (See below for
 * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
 */
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
	// hash和key是不可变的
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    // 重写了hashcode和equals，equals相等那么hashcode就必须相等
    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```

节点的结构类数HashMap中的静态内部类，节点内存储的是key的hash码、key、value、next。key的hash码就是用来进行寻找key的，next就是指向链表的下一个节点。

### 5、构造函数

#### 构造函数1

```java
/**
 * Constructs an empty <tt>HashMap</tt> with the default initial capacity
 * (16) and the default load factor (0.75).
 */
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
```

**说明**：这个构造函数应该好多新手都在用，算是一个默认构造函数，使用它不用指定任何参数，构造函数默认指定了容载因子，默认为0.75，也就是当HashMap的节点个数大于HashMap容量的0.75时，就要进行扩容。

#### 构造函数2

```java
/**
 * Constructs an empty <tt>HashMap</tt> with the specified initial
 * capacity and the default load factor (0.75).
 *
 * @param  initialCapacity the initial capacity.
 * @throws IllegalArgumentException if the initial capacity is negative.
 */
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

**说明**：这个构造函数才是最常用的，由我们手动指定容量，但是这个容量最好是2的整数次幂并且稍大于插入数据的个数，因为有容量阈值的存在，例如我们想往HashMap中插入10个键值对，那我们虽好将容量设置为16，不光是因为16是大于10且距离10最近的2的整数次幂，还有一个原因就是当用16 * 0.75时，得到的是12，插入10个数据不会引起HashMap的自动扩容，毕竟HashMap进行resize的时间复杂度是O(n)，是消耗时间的。

此构造函数调用的是构造函数3，传入了手动设置的容量，还有默认的容载因子。

#### 构造函数3

```java
/**
 * Constructs an empty <tt>HashMap</tt> with the specified initial
 * capacity and load factor.
 *
 * @param  initialCapacity the initial capacity
 * @param  loadFactor      the load factor
 * @throws IllegalArgumentException if the initial capacity is negative
 *         or the load factor is nonpositive
 */
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        // 手动设置的容量小于0时，抛出异常
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        // 手动设置的容量大于HashMap的最大容量时，令容量等于最大容量
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        // 当指定的容载因子小于等于0或者为非数（Not a Number）的时候，抛出异常
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    this.loadFactor = loadFactor;
    // 计算容量的阈值， 使用这个构造函数先会让容量的阈值等于容量
    this.threshold = tableSizeFor(initialCapacity);
}
```

这里面大家就看到了计算容量阈值的函数，其实这步真正计算的不是容量的阈值，而是要初始化HashMap的容量，在resize函数中会让实际的初始化容量等于这个阈值，而这个阈值也会被重新计算。进入到tableSizeFor函数内部查看一下，大家看这个函数之前先平复一下心情，因为这个函数的设计实在是太精秒了，不愧是写jdk代码的大牛，属实折服了。

```java
/**
 * Returns a power of two size for the given target capacity.
 */
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

这个函数是干啥的呢？相信大家看完都有这个疑惑，这就是我前面说的，指定初始化容量时候尽量指定为2的整次幂，这步就是将我们指定的容量转化为2的整次幂的，what？这样的几行的代码就能转换？给大家计算一下。

> 假定咱们指定的初始容量为10，对应的2进制为1010，进过第一步减一之后，变为了9（1001）
>
> n |= n >>> 1，(三个箭头的符号是无符号右移，移动的过程中忽略符号)，得到的结果为1001 | 0100  = 1101
>
> n |= n >>> 2，结果为 1101 | 0010 = 1111
>
> n |= n >>> 4，结果为 1111 | 0000 = 1111
>
> n |= n >>> 8，结果为 1111 | 0000 = 1111
>
> n |= n >>> 16，结果为 1111 | 0000 = 1111
>
> 进行判断，判断主要目的就是防止指定的容量为0，为了程序的健全吗，等于0就将容量设置为1，不等于就将刚才得到的结果1111（15）进行加1，得到的最终结果就是16.（哎呀我去，精妙！）

构造函数就带大家分析到这里，大家备好救心丸，更刺激的来了！！！

### 6、HashMap的扩容

咱们将resize中的代码进行分段分析，先来分析一下数据部分的内容。

```java
// 指向hash bucket
Node<K,V>[] oldTab = table;
// HashMap的当前数组容量
int oldCap = (oldTab == null) ? 0 : oldTab.length;
// 容载阈值
int oldThr = threshold;
// 新容量，新阈值
int newCap, newThr = 0;
```

这回咱们接下来看逻辑的第一部分，这里需要说明一下，HashMap的第一次创建容器不是调用构造函数的时候创建，而是第一次使用put的时候才创建，免得有小伙伴看到下面的代码产生疑惑。

```java
// 如果当前的容量大于0的时候
if (oldCap > 0) {
    // 当前容量大于等于最大的容量（其实也就是指等于最大容量，达到了不可再扩充的地步）
    if (oldCap >= MAXIMUM_CAPACITY) {
        // 将容量的阈值指定为Integer的最大值，也就是为（2的31次幂）-1，就是将阈值调到最大，相当于可以插         // 满最大的容量，变相的对容量进行扩充
        threshold = Integer.MAX_VALUE;
        return oldTab;
    }
    // 容量小于最大值
    else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
             oldCap >= DEFAULT_INITIAL_CAPACITY)
        // 新容量左移一位，也就是将老容量乘2，同时将容载阈值乘2
        newThr = oldThr << 1; // double threshold
}
else if (oldThr > 0) // initial capacity was placed in threshold
    // 容量为0，并且容阈值子大于0，对应的是使用构造函数2或者构造函数3进行初次初始化
    // 将新容量设为容载阈值，新的容载阈值将在下一段代码块内设置
    newCap = oldThr;
else {               // zero initial threshold signifies using defaults
    // 对应的情况就是使用无参的构造函数进行初始化
    // 将新容量指定为默认的容量
    newCap = DEFAULT_INITIAL_CAPACITY;
    // 进行容载因子的计算：16 * 0.75 = 12
    newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
}
// 对应的是else if (oldThr > 0) 情况，这里就是设置新的容载阈值
if (newThr == 0) {
    // 计算容载阈值
    float ft = (float)newCap * loadFactor;
    newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
              (int)ft : Integer.MAX_VALUE);
}
// 将容载阈值设置为新的容载阈值
threshold = newThr;
@SuppressWarnings({"rawtypes","unchecked"})
// 建立新的hash bucket
Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
table = newTab;
```

接下来就是数据转义了，这块仅仅对应之前hash bucket中有数据的情况下，初次建立hash table的时候不会进入此处。

**注意**：移接数据的时候会重新计算每个节点key的hash值，重新分配位置。

这里我就不详细进行分析了。

```java
if (oldTab != null) {
    for (int j = 0; j < oldCap; ++j) {
        Node<K,V> e;
        if ((e = oldTab[j]) != null) {
            oldTab[j] = null;
            if (e.next == null)
                newTab[e.hash & (newCap - 1)] = e;
            else if (e instanceof TreeNode)
                ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
            else { // preserve order
                Node<K,V> loHead = null, loTail = null;
                Node<K,V> hiHead = null, hiTail = null;
                Node<K,V> next;
                do {
                    next = e.next;
                    if ((e.hash & oldCap) == 0) {
                        if (loTail == null)
                            loHead = e;
                        else
                            loTail.next = e;
                        loTail = e;
                    }
                    else {
                        if (hiTail == null)
                            hiHead = e;
                        else
                            hiTail.next = e;
                        hiTail = e;
                    }
                } while ((e = next) != null);
                if (loTail != null) {
                    loTail.next = null;
                    newTab[j] = loHead;
                }
                if (hiTail != null) {
                    hiTail.next = null;
                    newTab[j + oldCap] = hiHead;
                }
            }
        }
    }
}
return newTab;
```

### 7、put的底层原理

```java
/**
 * Associates the specified value with the specified key in this map.
 * If the map previously contained a mapping for the key, the old
 * value is replaced.
 *
 * @param key key with which the specified value is to be associated
 * @param value value to be associated with the specified key
 * @return the previous value associated with <tt>key</tt>, or
 *         <tt>null</tt> if there was no mapping for <tt>key</tt>.
 *         (A <tt>null</tt> return can also indicate that the map
 *         previously associated <tt>null</tt> with <tt>key</tt>.)
 */
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

当我们使用put的时候，put又调用了另一个函数，并且计算出来了key的hash值，传入到了putVal函数中，咱们移入到此函数中。

```java
/**
 * Implements Map.put and related methods.
 *
 * @param hash hash for key
 * @param key the key
 * @param value the value to put
 * @param onlyIfAbsent 如果为true，不可以修改hash bucket中已有的值
 * @param evict 如果此值为false，则可以创建hash bucket
 * @return previous value, or null if none
 */
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;

    if ((tab = table) == null || (n = tab.length) == 0)
        // 当hash bucket中为null，或者长度为0的时候，就是第一次进行put的情况
        // 进行resize
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 在hash bucket的数组中查找，对应的地方没有值
        // 就在数组中进行插入
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        
        /**
          * 在数组中根据hash吗找到了对应的位置，但是此位置中存在与之相同的key，则进行记录，下一步会进行
          * 值的覆盖
          */
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        
        // hash值位于红黑树节点中，则进行查找遍历，插入到对应位置
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // hash值在数组中找到了，但是对应位置的key不相等，则在链中进行查找
            for (int binCount = 0; ; ++binCount) {
                // 遍历一次链中的节点就将计数器自加1
                if ((e = p.next) == null) {
                    // 找到了链表的尾部就将节点插入到链表尾部
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        // 当链中的节点数大于7的时候，则将链状结构转化为红黑树，不是8吗，咋又变成7了，
                        // 8是因为包括了数组中的那个节点，不算数组中对应hash值相等的那个节点就是7
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 在遍历的过程中找打了与之key相等的那个节点，那么就进行值的更新
                    break;
                p = e;
            }
        }
        
        // 之前记录重复值的地方就是在这里进行覆盖
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    
    // 使用put的次数进行自增1
    ++modCount;
    
    if (++size > threshold)
        // 如果实际节点数量的值大于容载阈值，则进行扩容
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

### 8、get的底层原理

```java
/**
 * Returns the value to which the specified key is mapped,
 * or {@code null} if this map contains no mapping for the key.
 *
 * <p>More formally, if this map contains a mapping from a key
 * {@code k} to a value {@code v} such that {@code (key==null ? k==null :
 * key.equals(k))}, then this method returns {@code v}; otherwise
 * it returns {@code null}.  (There can be at most one such mapping.)
 *
 * <p>A return value of {@code null} does not <i>necessarily</i>
 * indicate that the map contains no mapping for the key; it's also
 * possible that the map explicitly maps the key to {@code null}.
 * The {@link #containsKey containsKey} operation may be used to
 * distinguish these two cases.
 *
 * @see #put(Object, Object)
 */
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

get和put都是一个套路，又调用了别的函数，看实际调用的函数

```java
/**
 * Implements Map.get and related methods.
 *
 * @param hash hash for key
 * @param key the key
 * @return the node, or null if none
 */
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    
    // 进行判断，hash bucket不为空或者对应的hash值位置存在key-value的情况才能get到，为空则get到的为	//null
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
           	// hash bucket 数组中对应位置存在key-value，直接返回此处的值，此种get的时间复杂度为O(1)
            return first;
        if ((e = first.next) != null) {
            // 在hash bucket数组中不存在key对应的value
            if (first instanceof TreeNode)
                // 红黑树就进入到树中进行查找
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                // 如果是链表就遍历链表进行查找，查找的时间复杂度为O(n)
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

### 总结

1. HashMap数据结构式hash bucket，但是jdk1.8之后当链表节点数大于等于8时，链表就被转化为了红黑树。
2. HashMap共有三个常用的构造函数，分别为默认的构造函数，指定初始容量的构造函数，指定容载因子的构造函数。
3. resize中的情况：原HashMap中已经存在节点，需要进行翻倍扩容，并且容载阈值也将扩容，所以再指定容量时尽量指定合适的容量，否则再次扩容后可能会浪费大量的空间，而且扩容的时间复杂度为O(n)，尽量减少扩容的次数。
4. put的原理就是现在hash bucket数组中对应的位置进行插入，如果对应位置已经存在key-value，如果key相等，则进行值的覆盖，不相等则遍历链表或者是红黑树，在链表的尾部进行插入。插入之后如果链表的长度大于等于8，则将链表转化为红黑树。
5. get和put差不多，现在数组中进行查找，存在则直接返回值，不存在就去链表或红黑树中找，找到直接返回。