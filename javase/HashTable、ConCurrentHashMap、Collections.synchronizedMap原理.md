# HashTable、ConCurrentHashMap、Collections.synchronizedMap原理

## HashTable

HashTable是快速失败的，在使用Iterator进行遍历的时候，不允许修改容器中的值。

但是为什么HashTable是线程安全的呢，因为在HashTable内部，给每个方法都加上了同步锁Synchronized，也就是悲观锁，每次只能确保一个线程对HashTable进行操作，其它线程是都需要排队的，虽然它支持高并发，但是，他的并发度是不够的，很容易造成大规模阻塞。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_09-40-04.png)

那么他的数据结构是怎么样的呢？他的数据结构和jdk1.7中HashMap的数据结构是一样的，采用数组加链表的方式。节点同样都是Entity，架构如下

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_09-46-02.png)

接下来看一下HashTable的扩容方式，当容量不够的时候，会扩容为原来的容量的2倍加1，这一点和HashMap的不同的，HashMap只扩容为原来的2倍

```java
int newCapacity = (oldCapacity << 1) + 1;
```

再来说一说为什么他不允许键值为空，在HashTable的内部，做了特殊的判断，插入null的键值会抛出异常

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_09-55-36.png)

> 当key为空时，null.hashCode()也会抛出NPE

其实这还不是最终的原因，本质的原因是在多线程的环境下，如果使用get进行取值，取出来的为null，那么就不知道此值是不存在还是value本来就为null，无法使用containsKey进行判断此key是否存在，为什么无法判断key是否存在？在这期间，可能有多少线程都操作的了HashTable，可能线程把这个key给删了，也有可能本身就不存在。因此不允许空的key-value插入。

还有他的默认容量是多少呢？通过源码可以看出，他的默认容量大小为11。容载因子还是0.75

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_10-06-34.png)

另一个关心的问题就是put的原理，咱们来看一下put的原理。

put的原理是根据key定位到具体的Entry节点，如果此节点存在链状结构，那么就会遍历此链表，判断此key是否等于其中的key，如果没有相等的，就将待插入的节点插到链表头部。如果容量不够，则进行扩容操作。

get的原理其实比put还要简单，来看一下，就是根据key定位到链表，然后遍历链表找数据，找不到则返回null

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_22-32-45.png)

## Collections.synchronizedMap

来看一下他的实现原理，他其实也是加了互斥锁mutex，如果我们在构造方法中传入了mutex，就使用我们传入的互斥锁，如果没有传入，就是用当前的对象锁。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_10-10-25.png)

然后在方法上，全部加上synchronized，类似于HashTable

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_10-12-02.png)

## ConCurrentHashMap

这可是一个重头戏，面试非常常问的话题，涉及到了高并发。主要对比他在jdk1.7和jdk1.8之间的不同

**jdk1.7**

两个版本的不同主要是在数据结构和锁，jdk1.7中的数据结构采用分段式设计，segment数组 + HashEntry数组 + 链表实现，hash冲突采用拉链法处理。

![img](https://upload-images.jianshu.io/upload_images/3994601-2da9a1460800f68f.png?imageMogr2/auto-orient/strip|imageView2/2/w/478/format/webp)

接下来看看构造方法，看看默认容量和容载因子为多少，在jdk1.7中，容载因子为0.75，默认容量也为16。

那么put的原理又是什么呢，在put的时候如何保证的高并发呢？其实，在构造方法中，可以指定并发量，也就是segment数组的数量，理论上，segment数组的数量是多少，并发量就是多少，在进行put的时候，会根据key定位到具体的segment节点，得到HashEntry数组，并且对此segment节点加锁，然后再根据hash(key)定位到具体的HashEntry，判断此处此HashEntry是否为空，如果为空则直接插入，如果不为空则循环链表，把节点插在链表头部。同样，ConCurrentHashMap也不支持空值的key-value。

get的原理就是比较简单了。根据key的hash值定位到具体的segmen节点中的HashEntry，然后从该链表中找节点，ConCurrentHashMap的get方法是非常高效的，全程都不需要上锁

```java
public V get(Object key) {
        Segment<K,V> s; // manually integrate access methods to reduce overhead
        HashEntry<K,V>[] tab;
    	// 根据key计算hash 
        int h = hash(key);
        long u = (((h >>> segmentShift) & segmentMask) << SSHIFT) + SBASE;
    	// 定位到具体的segment节点中的HashEntry节点
        if ((s = (Segment<K,V>)UNSAFE.getObjectVolatile(segments, u)) != null &&
            (tab = s.table) != null) {
            for (HashEntry<K,V> e = (HashEntry<K,V>) UNSAFE.getObjectVolatile
                     (tab, ((long)(((tab.length - 1) & h)) << TSHIFT) + TBASE);
                 e != null; e = e.next) {
                K k;
                if ((k = e.key) == key || (e.hash == h && key.equals(k)))
                    return e.value;
            }
        }
        return null;
    }
```



**jdk1.8**

可以看出ConCurrentHashMap在jdk1.7中的缺点，遍历链表的效率太低了，因此，在jdk1.8中，它也效仿了jdk1.8中的HashMap，采用数组 + 链表 + 红黑树的数据结构，并且有原来的分段式锁换成了CAS + Synchronized锁，其它的地方并没有改变。

> CAS介绍可以参考博客<https://juejin.im/post/5e4664df6fb9a07c9c6a4b02> 

那么扩容方式以及初始容量是怎样的呢？默认初始容量还是16

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_22-39-03.png)

在transfer方法中，有这么一句话

```java
 Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];
```

由此可见，扩容方式也是变为原来的2倍。但是在扩容的时候会发现，如果有多个线程同时要扩容，那么这些线程之间会互相帮助，帮助彼此进行扩容。

put的底层实现比较麻烦一些，他是这样的，先根据key的hash定位到具体的Node，如果此节点为null，则自旋插入，如果不为null，此时使用同步锁Synchronized加锁，如果此节点对应的是红黑树，则采用红黑树的方式插入节点，如果为链表，则采用链表的方式插入，采用的是尾插。如果是链表插入，那么插入完成之后也要检测一下节点数量是否大于等于8，大于等于8则需要将链表转为红黑树。然后再判断此时容器内节点的数量是否大于阈值，大于阈值需要扩容。

get的原理和jdk1.7的差不多，根据key的hash值定位到Node，如果Node的key和hash等于待查找的key和hash，则直接返回此节点，否则判断是遍历二叉树还是遍历链表，最终返回值，都找不到返回null。

> jdk1.7和jdk1.8中ConCurrentHashMap相对比，jdk1.8查找效率更高一点，毕竟引入了红黑树

