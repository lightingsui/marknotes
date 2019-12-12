## HashMap和HashTable的区别

HashMap和Hashtable都实现了Map接口，但决定用哪一个之前先要弄清楚它们之间的分别。主要的区别有：`允许插入的值、线程安全性，同步(synchronization)，以及速度。` 

+ HashMap可以接受null(HashMap可以接受**一个**null的键值(key)和**多个**null的value，而`Hashtable则不行`（`键和值都不可以为null`）)。 
+ HashMap是`非synchronized`，而HashTable是`synchronized`，这意味着Hashtable是`线程安全`的，多个线程可以共享`一个`HashTable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。当一个线程对HashTable进行读写操作时，`会获得同步锁`，这时其他线程如果也想对当前的HashTable进行读写时，就需要当前的线程`释放同步锁`。`Java 5提供了ConcurrentHashMap`，它是HashTable的替代，比HashTable的扩展性更好。 建议如果在`jdk5以及以后`如果想使用HashTable则`尽量使用ConcurrentHashMap代替`。
+ 另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但`迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常`。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
+ 由于Hashtable是线程安全的也是synchronized，所以在`单线程环境下`它比HashMap要`慢`。如果你`不需要同步`，只需要`单一线程`，那么使用`HashMap性能要好过Hashtable`。
+ HashMap`不能保证随着时间的推移`Map中的元素次序是`不变的`。

HashMap变为同步map的方法: `Map m = Collections.synchronizeMap(hashMap)`

## 关于HashMap和HashTable的一些面试题

HashMap的工作原理是近年来常见的Java面试题。几乎每个Java程序员都知道HashMap，都知道哪里要用HashMap，知道`HashTable和HashMap`之间的区别，那么为何这道面试题如此特殊呢？是因为这道题考察的`深度很深`。这题经常出现在`高级或中高级面试中`。有的企业甚至会要求你实现HashMap来考察你的编程能力。ConcurrentHashMap和其它同步集合的引入让这道题变得更加复杂。让我们开始探索的旅程吧 ！

1. **“你用过HashMap吗？” “什么是HashMap？你为什么用到它？”** 

   答：几乎每个人都会回答“是的”，然后回答HashMap的一些特性，譬如`HashMap可以接受null键值和值`，而`Hashtable则不能`；HashMap是非synchronized;HashMap`很快`；以及HashMap储存的是键值对等等。这显示出你已经用过HashMap，而且对它相当的熟悉。但是面试官来个急转直下，从此刻开始问出一些刁钻的问题，关于HashMap的更多`基础的细节`。面试官可能会问出下面的问题： 

2. **“你知道HashMap的工作原理吗？” “你知道HashMap的get()方法的工作原理吗？”** 

   答：HashMap是`基于hashing的原理`，我们使用put(key, value)存储对象到HashMap中，使用get(key)从HashMap中获取对象。当我们给put()方法传递键和值时，我们先对`键调用hashCode()方法`，返回的hashCode用于`找到bucket位置`来储存Entry对象。”这里关键点在于指出，HashMap是在bucket中储存键对象和值对象，作为Map.Entry。这一点有助于理解获取对象的逻辑。如果你没有意识到这一点，或者错误的认为仅仅只在bucket中存储值的话（`bucket中存储了键的hash值，key、value`），你将不会回答如何从HashMap中获取对象的逻辑。这个答案相当的正确，也显示出面试者确实知道hashing以及HashMap的工作原理。但是这仅仅是故事的开始，当面试官加入一些Java程序员每天要碰到的实际场景的时候，错误的答案频现。下个问题可能是关于HashMap中的碰撞探测(collision detection)以及碰撞的解决方法： 

3. **“当两个对象的hashcode相同会发生什么？”** 

   答：从这里开始，真正的困惑开始了，一些面试者会回答因为hashcode相同，所以两个对象是相等的，HashMap将会抛出异常，或者不会存储它们。然后面试官可能会提醒他们有equals()和hashCode()两个方法，并告诉他们两个对象就算hashcode相同，但是它们可能并不相等。一些面试者可能就此放弃，而另外一些还能继续挺进，他们回答“因为hashcode相同，所以它们的`bucket位置相同`，‘碰撞’会发生。因为HashMap使用`链表`存储对象，这个Entry(包含有键值对的Map.Entry对象)会`存储在链表中`。”这个答案非常的合理，虽然有很多种处理碰撞的方法，这种方法是最简单的，也正是HashMap的处理方法。但故事还没有完结，面试官会继续问： 

4. **“如果两个键的hashcode相同，你如何获取值对象？”** 

   答：面试者会回答：当我们调用get()方法，`HashMap会使用键对象的hashcode找到bucket位置`，然后获取值对象。面试官提醒他如果有两个值对象储存在`同一个bucket`，他给出答案:将会遍历链表直到找到值对象。面试官会问因为你并没有值对象去比较，你是如何确定确定找到值对象的？除非面试者直到HashMap在链表中存储的是键值对，否则他们不可能回答出这一题。

   其中一些记得这个重要知识点的面试者会说，找到bucket位置之后，会调用`keys.equals()方法`去找到链表中正确的节点，最终找到要找的值对象。完美的答案！

5. **“如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？”** 

   答：非你真正知道HashMap的工作原理，否则你将回答不出这道题。默认的`负载因子大小为0.75`，也就是说，当一个map填`满了75%`的bucket时候，和其它集合类(如ArrayList等)一样，将会创建原`来HashMap大小的两倍的bucket数组`，来重新调整map的大小，并将`原来的对象放入新的bucket数组中`。这个过程叫作`rehashing`，`因为它调用hash方法找到新的bucket位置。` 

6. **“你了解重新调整HashMap大小存在什么问题吗？”** 

   答：你可能回答不上来，这时面试官会提醒你当`多线程`的情况下，可能产生条件竞争(race condition)。

   当重新调整HashMap大小的时候，确实存在条件竞争，因为如果`两个线程`都发现HashMap需要重新调整大小了，它们`会同时试着调整大小`。在调整大小的过程中，存储在链表中的`元素的次序会反过来`，因为移动到新的bucket位置的时候，`HashMap并不会将元素放在链表的尾部`，而是放在`头部`，这是为了`避免尾部遍历`(tail traversing)。如果条件竞争发生了，那么就`死循环了`。这个时候，你可以质问面试官，为什么这么奇怪，要在`多线程的环境下使用HashMap呢`？

7. **"为什么String, Interger这样的wrapper类适合作为键？"**

   答：为什么String, Interger这样的wrapper类适合作为键？ String, Interger这样的wrapper类作为HashMap的键是再适合不过了，而且`String最为常用`。因为String是`不可变的`，也是`final的`，而且已经`重写`了`equals()和hashCode()方法了`。其他的wrapper类也有这个特点。不可变性是必要的，因为为了要计算hashCode()，就要防止键值改变，如果键值在放入时和获取时返回不同的hashcode的话（重新修改`键的值`，可能会`造成hash值的不同`，导致了`存进去的hash值`和`要取的hash值不同`，就会`取不出来`），参考代码

   ```java
   	HashMap<Student, Integer> map = new HashMap<>(16);
       Student student1 = new Student("zhangsan", 11);
       map.put(student1, 1);
   
   	// 输出结果: 修改之前的hash值 = -1461067304
       System.out.println("修改之前的hash值 = " + student1.hashCode()); 
   
       student1.setAge(12);
   
   	// 输出结果：修改之后的hash值 = -1461067303
       System.out.println("修改之后的hash值 = " + student1.hashCode());
   
       Integer integer = map.get(student1);
   
   	// integer = null
       System.out.println("integer = " + integer);
   
   //原因：由于存进去map的时候，hash值为-1461067304，但是当修改对象的属性值后，hash值变为了-1461067303，调用get去找的时候，会先将key也就是这个对象进行hashing，得到-1461067303，然后去bucket中去寻找，结果可能找不到这个hash值，如果找打了这个hash值，可能就是hash冲突才找到的，真要是碰到这种hash冲突，那么就会去对应的链表中进行遍历，看key的真正值能不能找到，这可想而知，肯定是找不到的
   ```

   不可变性还有其他的优点如`线程安全`。如果你可以仅仅通过将某个field声明成final就能保证hashCode是不变的，那么请这么做吧。因为`获取对象`的时候要`用到`equals()和hashCode()方法，那么键对象正确的重写这两个方法是非常重要的。如果两个不相等的对象`返回不同的hashcode`的话，那么`碰撞的几率`就会`小`些，这样就能`提高HashMap的性能`。 

8. **"我们可以使用自定义的对象作为键吗？"**

   答：这是前一个问题的延伸。当然你可能使用任何对象作为键，只要它`遵守`了equals()和hashCode()方法的`定义规则`，并且当对象插`入到`Map中之后将`不会再改变`了。如果这个自定义对象是不可变的，那么它已经满足了作为键的条件，因为当它创建之后就已经不能改变了，`key不改变的好处就是存进去的就一定能找出来`。 

9. **"我们可以使用CocurrentHashMap来代替Hashtable吗？"**

   答：这是另外一个很热门的面试题，因为ConcurrentHashMap越来越多人用了。我们知道Hashtable是synchronized的，但是`ConcurrentHashMap同步性能更好`，因为它仅仅`根据同步级别`对`map的一部分进行上锁`。ConcurrentHashMap当然可以代替HashTable，`但是HashTable提供更强的线程安全性`。看看[这篇博客](http://javarevisited.blogspot.sg/2011/04/difference-between-concurrenthashmap.html)查看Hashtable和ConcurrentHashMap的区别。 