#                   java基础笔记

-------

1. [java集合类](#java集合类)
2. [多线程](#多线程)
3. [异常与IO流](异常与IO流)
4. [java语言基础](java语言基础)

​                                               

###  java集合类

- **Collection**(单列集合)

  - List(有序的，可重复的，可为null)
    - ArrayList:线程不安全，查询速度快，底层是数组来维护的。
    - LinkedList:线程不安全，增删改快，底层链表来维护的。
    - Vector:线程安全，但速度慢，已被ArrayList替代，底层是数组维护的。
  - Set(无序的，不可重复的)
    - HashSet：HashSet按照hash算法来存取元素的，具有很好的存取和查找性能。当向HashSet中存取元素时，会根据经过hash算法得到这个元素的hashcode值存储在对应的位置。
      - LinkedHashSet(LikedHashSet是HashSet的子类，它也是根据元素的HashCode值进来决定元素的存储位置，但它能够同时使用链表来维护元素的添加次序)
    - SortedSet
      - TreeSet:不重复且有序，底层通过TreeMap实现，值按照升序排列。
  - Queue
    - LinkedList:(基于双向链表实现，实现了List接口，Deque接口)

- **Map**(双列集合)

  - hashMap
  - weakhashMap
  - SortedMap
    - TreeMap(基于红黑树实现，按升序排列Key)

- **如图所示**

   ![java集合结构](../image/4.png)



​            **这个看起来可能有点复杂，你可以看如下这张图，可能更清晰一点(一些常用的)**

​             ![java集合类结构](../image/5.jpg)

- **ArrayList，LinkedList和Vetor的实现原理与区别**

  1. 从线程安全方面比较

     - ArrayList 不具有线程安全性，用在单线程环境中。LinkedList 也是线程不安全的，如果在并发环境下使用它们，可以用 Colletions 类中的静态方法 synchronizedList()对ArrayList 和 LinkedList 进行调用即可。Vector 是线程安全的，即它的大部分方法都包含有关键字 synchronized。Vector 的效率没有 ArrayList 和 LinkedList 高。

  2. 从扩容机制方面考虑

     -   从内部实现机制来讲，ArrayList 和 Vector 都是使用 Object 的数组形式来存储的。若容量不够，ArrayList 扩容后的容量是之前的 <font color="red">1.5 倍(初始值为10)，</font>然后把之前的数据通过Array.copyOf()拷贝到新建的数组。Vector 默认情况下扩容后的容量是之前的<font color="red"> 2 倍</font>。Vector可以 通过capacityIncrement设置容量增量，而 ArrayList不可以。

       ​     **可变长度数组的原理：当元素个数超过数组的长度时，会产生一个新数组，将原数组的数据复制到新数组，再将新的元素添加到新数组中。**

  3.  效率对比	
  
     ​    ArrayList 和 Vector 中，从指定的位置（用 index）检索一个对象，或在集合的末尾插入、删除一个对象的时间是一样的，可表示为 O(1)。但是，如果在集合的其他位置增加或移除元素那么花费的时间是 O（n）。LinkedList 中，在插入、删除集合中任何位置的元素所花费的时间都是O(1)，但它在索引一个元素的时候比较慢 O（n）。所以，如果只是查找特定位置的元素或只在集合的末端增加、移除元素，那么使用Vector 或 ArrayList 都可以。如果是对其它指定位置的插入、删除操作，最好选择 LinkedList。 
  
- **集合中的** **fail-fast** **机制 **

  ​    假设存在两个线程（线程1、线程 2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合 A 的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这 个时候程序就会抛出ConcurrentModificationException异常，从而产生fail-fast机制。

  ​    产生的原因：当调用容器的iterator()方法返回 Iterater 对象时，把容器中包含对象的个数赋值给了一个变量 expectedModCount,在调用 next（）方法时，会比较expectedModCount 与容器中实际对象的个数是否相等，若二者不相等，则抛出ConcurrentModificationException 异常。如果在遍历集合的同时，需要删除元素的话，可以用 iterator 里面的remove()方法删除元素。在遍历的时候不要修改结构，除非使用迭代器的remove方法(删除当前迭代的方法)。

- **hashmap实现原理**

  ​        HashMap的主干是一个Entry数组。Entry（Entry是HashMap中的一个静态内部类，既是数组元素又是链表的头节点）是HashMap的基本组成单元，每一个Entry包含一个key-value键值对以及一个指向链表下一结点的指针。**简单来说，HashMap由数组+链表组成的，数组是HashMap的主体，链表则主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap中的链表出现越少，性能才会越好**

  ![hashmap底层原理](./image/5.png)

**<font color="red">put方法的实现：</font>1.**对key做null检查。如果key是null，会被存储到table[0]，因为null的hash值总是0。

2.调用key的hashcode()方法，并计算hash值。**hash值用来找到存储Entry对象的数组的索引**。

3.如果两个key有相同的hash值(也叫冲突)，他们会以链表的形式来存储。

4· 如果在刚才计算出来的索引位置没有元素，直接把Entry对象放在那个索引上。

5· 如果索引上有元素，然后会进行迭代，一直到Entry->next是null。当前的Entry对象变成链表的下一个节点。

6.在迭代的过程中，会调用equals()方法来检查key的相等性，如果这个方法返回true，它就会用当前Entry的value来替换之前的value。

<font color="red">**get方法的实现**：</font>1.对key进行null检查。如果key是null，table[0]这个位置的元素将被返回。

2.key的hashcode()方法被调用，然后计算hash值，根据hash值查询要获取的Entry对象在table数组中的精确的位置。

3.在获取了table数组的索引之后，会迭代链表，调用equals()方法检查key的相等性，如果equals()方法返回true，get方法返回Entry对象的value，否则，返回null。

<font color="red">**hashMap是2的整数次幂的原因：**</font>如果数组进行扩容，数组长度会发生变化，而存储位置 index = h&(length-1),index也可能会发生变化，需要重新计算index，这样会保证index-1低位全为1，而扩容后只有一位差异，也就是多出了最左位的1，这样在通过 h&(length-1)的时候，只要h对应的最左边的那一个差异位为0，就能保证得到的新的数组索引和老数组索引一致。

<font color="red">**hashMap线程不安全的体现：**</font>在多线程情况下，会导致hashmap出现链表闭环，一旦进入了闭环get数据，程序就会进入死循环，所以导致HashMap是非线程安全的。

<font color="red">**JDK1.7中**</font>

使用一个Entry数组来存储数据，用key的hashcode取模来决定key会被放到数组里的位置，如果hashcode相同，或者hashcode取模后的结果相同（hash collision），那么这些key会被定位到Entry数组的同一个格子里，这些key会形成一个链表。在hashcode特别差的情况下，比方说所有key的hashcode都相同，这个链表可能会很长，那么put/get操作都可能需要遍历这个链表，时间复杂度在最差情况下会退化到O(n)。

<font color="red">**JDK1.8中**</font>

使用一个Node数组来存储数据，但这个Node可能是链表结构，也可能是红黑树结构；如果插入的key的hashcode相同，那么这些key也会被定位到Node数组的同一个格子里。如果同一个格子里的key不超过8个，使用链表结构存储。如果超过了8个，那么会调用treeifyBin函数，将链表转换为红黑树。那么即使hashcode完全相同，由于红黑树的特点，查找某个特定元素，也只需要O(log n)的开销。也就是说put/get的操作的时间复杂度最差只有O(log n)。





- **currenthashmap原理**

   ConcurrentHashMap与HashMap相比，最关键的是要理解一个概念：**segment**。 Segment其实就是一个Hashmap 。Segment也包含一个HashEntry数组，数组中的每一个HashEntry既是一个键值对，也是一个链表的头节点。 Segment对象在ConcurrentHashMap集合中有2的N次方个，共同保存在一个名为**segments的数组**当中。

  ![currenthashmap](../image/6.png)



ConcurrentHashMap是一个双层哈希表。在一个总的哈希表下面，有若干个子哈希表。优势主要在于： **每个segment的读写是高度自治的，segment之间互不影响**。这称之为“锁分段技术”。不同的segment可以并发的写入，相同的segment只允许一个线程进行操作。

**ConcurrentHashMap的具体读写：**

<font color="red">**Get方法：**</font>

1.为输入的Key做Hash运算，得到hash值。

2.通过hash值，定位到对应的Segment对象。

3.再次通过hash值，定位到Segment当中数组的具体位置。

<font color="red">**Put方法：**</font>

1.为输入的Key做Hash运算，得到hash值。

2.通过hash值，定位到对应的Segment对象。

3.获取可重入锁。

4.再次通过hash值，定位到Segment当中数组的具体位置。

5.插入或覆盖HashEntry对象。

6.释放锁。

- <font color="red">HashTable原理</font>

  <font color="red">Put方法：</font>

   1.判断value是否为空，为空则抛出异常

2. 计算 key 的 hash 值，并根据 hash 值获得 key 在 table 数组中的位置 index，如果 table[index] 元素不为空，则进行迭代，如果遇到相同的 key，则直接替换，并返回旧 value
3. 否则，我们可以将其插入到 table[index] 位置。

<font color="red">Get方法</font>:

相比较于 put 方法，get 方法则简单很多。其过程就是首先通过 hash()方法求得 key 的哈希值，然后根据 hash 值得到 index 索引（上述两步所用的算法与 put 方法都相同）。然后迭代链表，返回匹配的 key 的对应的 value；找不到则返回 null。

<font color="red">Hashtable 与 HashMap 的简单比较</font>:

1. HashTable 基于 Dictionary 类，而 HashMap 是基于 AbstractMap。Dictionary 是任何可将键映射到相应值的类的抽象父类，而 AbstractMap 是基于 Map 接口的实现，它以最大限度地减少实现此接口所需的工作。
2. HashMap 的 key 和 value 都允许为 null，而 Hashtable 的 key 和 value 都不允许为 null。HashMap 遇到 key 为 null 的时候，调用 putForNullKey 方法进行处理，而对 value 没有处理；Hashtable遇到 null，直接返回 NullPointerException。
3. Hashtable 是同步的，而HashMap则不是。所以有人一般都建议如果是涉及到多线程同步时采用 HashTable，没有涉及就采用 HashMap，但是在 Collections 类中存在一个静态方法：synchronizedMap()，该方法创建了一个线程安全的 Map 对象，并把它作为一个封装的对象来返回。

**<font color="red">锁分段技术</font>**

​    HashTable容器在竞争激烈的并发环境下表现出效率低下的原因，是因为所有访问HashTable的线程都必须竞争同一把锁，那假如容器里有多把锁，每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是**ConcurrentHashMap所使用的锁分段技术，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁。**这里“按顺序”是很重要的，否则极有可能出现死锁，在ConcurrentHashMap内部，段数组是final的，并且其成员变量实际上也是final的，但是，仅仅是将数组声明为final的并不保证数组成员也是final的，这需要实现上的保证。这可以确保不会出现死锁，因为获得锁的顺序是固定的.



**<font color="red">ConcurrentHashMap 1.7与1.8的区别</font>**

改进一：取消segments字段，直接采用transient volatile HashEntry<K,V>[] table保存数据，采用table数组元素作为锁，从而实现了对每一行数据进行加锁，进一步减少并发冲突的概率。

改进二：将原先table数组＋单向链表的数据结构，变更为table数组＋单向链表＋红黑树的结构。对于hash表来说，最核心的能力在于将key hash之后能均匀的分布在数组中。



**<font color="red">WeakHashMap<K,V></font>** 

​    弱引用是用来描述非必需对象的，被弱引用关联的对象只能生存到下一次垃圾收集发生之前，当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象，因此如果没有强引用来引用WeakHashMap<K,V>中的entry，则在GC时将会被清除。



**<font color="red">HashSet</font>**

​    **对于 HashSet 而言，它是基于 HashMap 实现的，HashSet 底层使用HashMap 来保存所有元素**，因此HashSet的实现比较简单，相关 HashSet 的操作，基本上都是直接调用底层 HashMap 的相关方法来完成。**HashSet 中的元素都存放在 HashMap 的 key 上面，而 value 中的值都是统一的一个 private static final Object PRESENT = new Object();**

​    








