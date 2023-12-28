# JCF(Java集合框架) - 面试题分析（面向面试题的学习）

Java Collection Framework，JCF，Java集合框架，相关内容和面试对答

[Java集合框架 - 飞书文档]: https://ls8sck0zrg.feishu.cn/wiki/AQDbwsUG8iRH1bkxPtWcnJpDnxf

-

## A. 概述/总体架构

JCF一共提供15种容器接口，其中只能存放引用类型的对象（基础数据类型需要转换后Integer、Double...再放入）

JCF中的内容分为两大类：

- Collection - 存储Object对象；
- Map - 实际是关联式容器，存储<Key, Object>，本质是存储<Object,Object>，比单纯的Collection多了关联式对应关系，所以多一部分内容；

Java提供Map->Collection之间转换的快捷方法，需要的时候可以直接调用，很方便：

- 什么场景下需要这种转换？

  ​	因为Map存储的信息能比Collection多出关联关系的部分，当只需要对其中一类Object进行处理时，将这一类单独转换成Collection从而进行快速操作，会很方便！比如：在某类Object中查找/排序某项内容，就可以转换后再对这个Collection进行方法调用，可以很大程度上简化操作。

![image-20231219042903374](/Users/boyan/Library/Application Support/typora-user-images/image-20231219042903374.png)

- 其中虚线表示实现，实现表示继承；
- Stack 已经在JDK1.6中就被 **Deque** 取代了；
- 右下方主要标注了：不同实现时所用的数据结构；

------

## B. 容器特性原理

### 1 - Map<Object-Key, Object> ：关联式容器

#### 1.1 - TreeMap

基于红黑树实现的一种提供顺序访问的Map（这里顺序访问指的是对树的中序遍历）

- 实现SortedMap接口,能够把元素根据键Key排序。默认是升序列,使用teraton遍历时会拿到一个升序的记录序列。
- 因为需要排序，所以key需要定义比较大小的逻辑，具体顺序可以由指定的Comparator来决定，或者根据键的自然顺序来判断。

##### 1.1.1* - Object的大小比较 - Comparable / Comparator

- 基础包装类：Integer,Sting等可以默认支持排序（eg. 如果是String类型，会默认按照ASC码比较大小）
- 自定义类：通过**实现“Comparable”接口或自定义“Comparator”实现**的：
  - “Comparable”接口是可以在自定义类中修改时使用的，通过重写“compareTo()”和定义比较规则。
  - “Comparator”是在不想/无法修改源码的情况下使用的，新建类定义比较方法。
- 两种排序方式：
  - 自然排序：由加入到TreeSet中的对象本身指定比较规则，这是默认的排序方式 - “Comparable”接口；
    - 自然顺序同样需要符合一个约定，就是 compareTo 的返回值需要和 equals 一致，否则就会出现模棱两可情况。

  - 客户化/自定义排序：在创建TreeSet对象时指定比较规则 - “Comparator”新创建类；
    - 在“2.2.1 - TreeSet”部分有具体实例 - 新创建Comparator类的应用实例：


```java
import java.util.*;
// EmployeeComparator
public class EmployeeComparator implements Comparator<Employee>{
  // 自定义比较规则：compare(o1,o2);
  public int compare(Employee c1,Employee c2){
    if(c1.getName().compareTo(c2.getName())>0) return -1;
    if(c1.getName().compareTo(c2.getName())<0) return 1;
    return 0;
  }
}
```

```java
// 在构造TreeSet实例时调用这个类的：TreeSet(Comparator comparator() 构造方法：
Set<Employee> set = new TreeSet<Employee>(new EmployeeComparator());

Employee employee1=new Employee("Tom",15);
Employee employee3=new Employee("Jack",16);
Employee employee2=new Employee("Mike",26);

set.add(employee1);
set.add(employee2);
set.add(employee3);

for(Employee e: set)
  System.out.println(e.getName()+" "+e.getAge());
```



#### 1.2 - HashMap - 包含哈希结构的原理介绍

##### 1.2.1 - 基本特点

-  基于散列表,访问速度快。进行put或者get操作,可以达到常数时间的性能。

-  元素之间没有顺序性（一看见Hash就知道了）
-  HashMap最多只允许一条记录的键Key为null，但允许多条记录的值为null; 

-  非线程安全，需要保证线程安全推荐使用并发包中的ConcurrentHashMap，HashMap没有做并发处理，在多线程使用场景的情况下会出问题；
-  实现：
  - Java7：采用(数组+链表)实现
  - Java8：采用(数组+链表+红黑树)实现。
-  **HashMap 的性能表现非常依赖于哈希码的有效性，请务必掌握 hashCode 和 equals 的一些基本约定，**比如：
   - equals 相等，hashCode 一定要相等。
   - 重写了 hashCode 也要重写 equals。
   - hashCode 需要保持一致性，状态改变返回的哈希值仍然要一致。
   - equals 的对称、反射、传递等特性。

##### 1.2.2 - 数据结构 - 实现：数组+链表+红黑树

哈希表<K,V>：是一种结合数组（顺序存储结构）随机访问 + 链式结构 处理哈希冲突的，综合两者优点，以空间换时间的快速查找的数据结构；

HashMap 基础结构：

1. <K,V>结点：Java 7中称**Entry**，Java 8中称**Node**，Java 8中如果转换成红黑树就使用**TreeNode**(增加lchild、rchild、parent引用地址..)；
   - 包含4方面内容：
     - Key：<K,V>中的Key，是Map中的唯一标识；
     - Value：<K,V>中的Value，是值Object；
     - Hash：Key对应的哈希值，存储在Node中可以更快的定位元素；
     - Next：用于解决冲突，存储后一个Node地址（链式存储结构）；
2. bucket array 桶数组：一维数组，存储<K,V>**键值对结点**，数组下标对应处理过的Hash值；
3. 链式结构处理冲突：在Java8中，为了解决哈希冲突，多个Node对应同一个Hash值（数组下标）时，使用链式结构连接多个Node。
   1. 链表的时间复杂度是O(n)，每次冲突使用**头插法**，在bucket array和Node1之间插入冲突的新Node；
   2. 为了提高性能对链表结构的应用进行长度限制：当哈希表中，Node容量达到64，并且链表中元素达到8个，就升级为红黑树链式结构；
   3. 升级成红黑树结构：
      - 基础键值对结点从Node->TreeNode；
      - 查找的时间复杂度：O(N)->O(logN)；
      - 红黑树访问顺序：顺序访问 == 中序遍历；

<img src="/Users/boyan/Library/Application Support/typora-user-images/image-20231219135120607.png" alt="image-20231219135120607" style="zoom:30%;" />

##### 1.2.3 - 调用方法

###### 	1 - 构造函数

```java
public HashMap(int initialCapacity, float loadFactor){// ...}
```

​	hashMap初始化的时候,可以指定初始容量和负载因子。如果不指定会有默认值：初始容量指定了初始table的大小,负载系数用来指定自动扩容的临界值。当entry的数量超过capacity+load factor时,容器将自动扩容并重新哈希。对于插入元素较多的场景,将初始容量设大可以减少重新哈希的次数。

- initialCapacity:初始容量，默认值16 
- loadFactor:负载因子，默认0.75，元素个数达到总容量的75%时,会触发扩容操作。

###### 	2 - get 查找

- 通过Hash(x)，计算得到bucket array数组下标；
- 遍历数组下标对应链式结构（链表/红黑树）并通过唯一标识K的Key.equals(k)判断是否找到目标键值对结点；

###### 	3 - put 插入

- 先执行类似get的过程，查看是否已存在；
- 如果确定不存在，执行add，使用头插法，通过改变引用位置插入新键值对结点Node；

###### 	4 - remove 删除

- 先找到key之对应的目标键值对结点Node

- 如果确定存在，执行删除操作，即通过改变引用位置进行删除；

##### 1.2.4* - 链表 vs 红黑树 - 各操作的最坏时间复杂度

- 链表的CRUD操作具有O(N)的时间复杂度；

- 红黑树的CRUD操作都具有O(logN)的时间复杂度；

#### 1.3 - LinkedHashMap - HashMap的子类

​	是HashMap的子类，继承了所有HashMap的特性，并维护了一个双向链表。`LinkedHashMap` 中的双向链表实际上同时负责完成两个独立的任务：1. 解决哈希冲突 2. 维护元素的插入或访问顺序。

	1. 解决哈希冲突：从单链表->双向链表实现；
	2. 维护Node的插入和访问顺序（put、 get、 compute等都算作“使用”）：顺序为插入顺序或者最近最少使用(LRU)顺序，具体使用哪个可以在构造时用参数指定。此LRU 机制可以用于建设对象的缓存和淘汰机制,将最近很少访问的对象给释放掉。
	 - 插入顺序比较好维护，每次在头插即可；
	 - **访问顺序（access order）**，其内部的双向链表需要动态地维护这个变化的顺序。这意味着每次访问（通过 `get` 或 `put` 方法）一个元素时，`LinkedHashMap` 都会调整该元素在双向链表中的位置，将其移动到链表的末尾，以反映最近的访问顺序。
	   - 按访问顺序维护元素的特性使得 `LinkedHashMap` 非常适合用作缓存（如 LRU 缓存）。在这样的应用中，经常访问的元素会被保留在缓存中，而最少访问的元素（位于链表头部）会首先被移除。
	   - 尽管动态维护访问顺序会带来额外的开销，但由于 `LinkedHashMap` 的双向链表结构和哈希表的高效查找能力，这种开销通常是可接受的，特别是在访问操作远多于插入和删除操作的场景中。

#### ~~1.4 - HashTable~~

​	功能与hashMap类似,且具有并发安全性。但是不再推荐使用,因为并发效率低。

​	所以在不需要考虑的并发的场景用HashMap,需要并发的场景用ConcurrentHashMap。一般不再使用HashTable。

------

### 2 - Collection\<Object>：一般容器

#### 2.1 - List - 线性表

##### 2.1.1 - ArrayList 基于动态数组

<img src="/Users/boyan/Library/Application Support/typora-user-images/image-20231220021017978.png" alt="image-20231220021017978" style="zoom:40%;" />

基于动态数组实现,所以基本与数组特性一致。

- 性能:查找效率高,支持快速随机访问。数组中间插入和删除数据效率低,需要前后挪动数据。

- size：当前数组实际存储的元素个数; 

- capacity：表示数组能容的元素个数。

  - 注意: new一个数认aralist,初给化的容量是0，只有当第一次插入元素之后，容量会变为DEFAUT_CAPACITY=10; ·

  - 扩容：当插入新元素时，会先校验级组大小是否能容纳所有元素，如果不满足时需要扩容，扩容会创建一个新的效组，将现有效组数据据贝到新数组;；

  - 扩容大小：每次数组容量的增长大约是其原容量的15倍。这种操作的代价是很高的，因此在实际使用时我们应该尽量避免数组容量的扩张。

  - 当我们可预知要保存的元素的多少时，要在构造rrayLis实例时，就指定其容量，以避免数组扩容的发生。

    - ```java
      ArrayList<String> list = new ArrayList<>(50);		// 可选：是否在初始化时规定ArrayList实例的capacity；
      ```

  - 或者根据实际插入数据情况,提前通过调ensureCapacily方法来手动增加ArrayList实例的容量,以减少递增式再分配的数量。(例如我已知后面会陆续插入100个数据,那就可以提前调用ensure-Capadih请保数组可以容的100个,而不是等插入过程中再去多次扩容)。

- 底层采用Object[]数组存储数据,能容纳任何类型的对象。

##### 2.1.2 - LinkedList 

​	基于双向链表实现（Doubly Linked List）维护头尾两个指针：prev，element，next；需要遍历的操作时间复杂度都是O(N)，只需要变换指针；

##### 2.1.3* - “线程不安全” - ArrayList LinkedList都是

###### 1 - 什么是 “线程安全” vs “线程不安全”？

​	在多线程编程中，当一个数据结构或算法在多线程环境下使用时，如果没有进行适当的同步或并发控制，就可能会导致数据的不一致性或状态的混乱，这种情况被称为“线程不安全”。

​	“线程安全”，比如Vector，具备使用线程同步能力，规定多线程互斥写入Vector，就称Vector是线程安全的。

###### 2 - 为什么 `ArrayList` 和 `LinkedList` 是线程不安全的？

1. **同时修改问题**：如果多个线程同时对 `ArrayList` 或 `LinkedList` 进行修改（如添加、删除元素），内部数据结构可能会进入不一致的状态，因为这些操作本身并没有进行同步控制。

2. **迭代问题**：在多线程同时进行迭代和修改时，可能会抛出 `ConcurrentModificationException`，因为迭代器不能保证在结构被并发修改时的一致性。

###### 3 - 由 不安全 -> 安全：使用 `Collections.synchronizedList()` 包装

可以使用 `Collections.synchronizedList()` 方法将线程不安全的列表（如 `ArrayList` 或 `LinkedList`）包装成线程安全的列表。

这样做的原因和方法如下：

1. **同步封装**：`Collections.synchronizedList()` 返回一个线程安全的列表包装器，它通过在每个方法上加上同步块（synchronized block），来保证对列表的操作在多线程环境中的安全性。

2. **使用方法**：
   ```java
   List<String> safeArrayList = Collections.synchronizedList(new ArrayList<String>());
   List<String> safeLinkedList = Collections.synchronizedList(new LinkedList<String>());
   ```
   这段代码创建了线程安全的 `ArrayList` 和 `LinkedList`。

###### 4 - 为什么这种方法有效？

​	因为确保了对列表的所有操作都是原子的，即在一个时间点上只有一个线程能够执行操作。这就消除了并发修改的问题，使得在多线程环境中对列表的操作变得安全。

######  5 - 注意事项

​	虽然 `Collections.synchronizedList()` 提供了线程安全的包装，但它可能会引入性能上的开销，因为每个操作都需要获得对象的锁。此外，对于更复杂的并发需求，如同时读取和写入操作，可能需要更高级的并发控制策略，比如使用 `java.util.concurrent` 包中的并发集合。

##### ~~2.1.4 - Vector~~

​	Vector基于数组实现，是Java早期提供的线程安全的动态数组，但特性与ArrayList类似，一般如果不需要线程安全的场景下不再使用；

------

#### 2.2 - Set - 集合

​	数据元素集合，和数学中的集合概念一致，集合内的数据元素：无序、唯一不重复；

##### 2.2.1 - TreeSet

- 基于红黑树实现：

  - 使用二叉树原理，对元素进行排序，插入元素对需要调整做的结构，将元素放到指定位量。支持自然顺序访问，但是add、delete、contains等操作要相对低效O(log(N))

- TreeSet支持两种排序方式：

  - 自然排序：由加入到TreeSet中的对象本身指定比较规则。这是默认的排序方式。
  - 客户化/自定义排序：在创建TreeSet对象时指定比较规则。

  **1．自然排序**

  ​	在JDK中，有一部分类实现了Comparable接口，如Integer、Double和String等。Comparable接口有一个compareTo(Object o)方法，用来指定对象的比较规则，它的返回值为整数类型。对于表达式x.compareTo(y)，如果返回值为0，表示x 和y相等；如果返回值大于0，表示x大于y；如果返回值小于0，表示x小于y。

  ​	TreeSet调用对象的compareTo()方法比较集合中对象的大小，然后进行升序排列，这种排序方式称为自然排序。

  ​	使用自然排序时，加入TreeSet的对象必须：1. 是同类型的 2. 类型必须 implements Comparable interface；

  **2．客户化/自定义排序**

  ​	java.util.Comparator接口用于指定比较规则，它的compare(Object object1,Object object2)方法用于比较两个对象的大小。如果希望TreeSet仅按照Employee对象的name属性进行降序排列，可以先创建一个实现Comparator接口的EmployeeComparator类。

##### 2.2.2 - HashSet - 底层通过HashMap实现数据存储

- HashSet中所有元素的Value值统一为“PRESENT”，是dummy value to associate with an Object in the backing HashMap，虚拟值；
- HashSet中所有操作基本上都是调用HashMap的方法实现的，所以非常简单；

##### 2.2.3 - LinkedHashSet - 底层通过LinkedHashMap实现数据存储

- HashSet的子类，直接super调用，实现更简单。

------

#### 2.3 - Queue - *会在数据结构中重点掌握使用

两者都是Java集合框架 (JCF) 中非常有用的数据结构，适用于不同的应用场景。这里简单介绍一下：

##### 2.3.1 - Deque - 普通队列和栈操作

​	`Deque`（双端队列）是一个线性集合，允许在两端（头部和尾部）添加和移除元素。这种灵活性使得它可以作为普通队列（先进先出，FIFO）和栈（后进先出，LIFO）使用。

1. **实现**： `Deque` 接口在JCF中有几种实现，可以用数组或链表，包括 `ArrayDeque` 和 `LinkedList`。

   `ArrayDeque` 是基于数组的高效实现，而 `LinkedList` 除了实现 `Deque` 接口外，还实现了 `List` 接口。

2. **操作**： 提供了一系列方法来操作两端的元素，如 `addFirst`、`addLast`、`removeFirst`、`removeLast`，以及检索但不移除元素的方法，如 `peekFirst` 和 `peekLast`。

3. **用途**： `Deque` 可以用作队列、栈或者双向队列，适用于需要这种双向操作的场景。

##### 2.3.2 - PriorityQueue - 带优先级的Queue队列数据结构

​	基于堆结构实现，特性与堆结构类似；

​	PiortyQueue 是在JDK15中被引入的，其与 Queue的区别在于元素出队顺序是与优先级相关的，即总是忧先级最高的元素先出队。

- PriorityQueue利用了二叉堆的数据结构来实现的,底层使用可变长的数组来存储数据；
- PriorityQueue通过堆元素的上浮和下沉,实现了在O(logN)的时间复杂度内插入元素和删除堆顶元素；
- PriorityQueue是非线程安全的,且不支持存储 NULL和non-comparable的对象；
- PriorityQueue默认是小顶堆,但可以接收一个 Comparator作为构造参数,从而来自定义元素优先级的先后。

​	在面试中可能更多的会出现在手撕算法的时候，典型例题包括：堆排序、求第K大的数、带权图的遍历等，所以需要会熟练使用才行。

------

## C. 思考问答自测

[Java集合框架 - 面试与分析 - 飞书文档]: https://ls8sck0zrg.feishu.cn/wiki/EfBZwvKK5iBnLpkFkrxchHsVnAd

#### 1 - 讲一下HashMap的工作原理？

#### 2 - HashMap的put流程？

#### 3 - HashMap的长度为什么是2的幂次方？

#### 4 - HashMap多线程操作导致死循环问题？

#### 5 - HashMap 和 HashTable 有什么区别？

#### 6 - HashMap, LinkedHashMap, TreeMap有什么区别？

#### 7 - 为什么 ConcurrentHashMap比 HashTable效率高？

#### 8 - 说说 List, Set, Queue, Map 四者的区别？

#### 9 - ArrayList和Array(数组)的区别？

#### 10 - ArrayList 与 LinkedList 区别？

#### 11 - Comparable和 Comparator的区别？

#### 12 - 比较 HashSet、 LinkedHashSet 和 TreeSet 三者的异同？

#### 13 - ArrayDeque 与 LinkedList的区别？

#### 14 - 说一说 PriorityQueue？

------

## D. 进阶阅读与问题

[08 对比Vector、ArrayList、LinkedList有何区别？-极客时间]: https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/Java%20%e6%a0%b8%e5%bf%83%e6%8a%80%e6%9c%af%e9%9d%a2%e8%af%95%e7%b2%be%e8%ae%b2/08%20%20%e5%af%b9%e6%af%94Vector%e3%80%81ArrayList%e3%80%81LinkedList%e6%9c%89%e4%bd%95%e5%8c%ba%e5%88%ab%ef%bc%9f-%e6%9e%81%e5%ae%a2%e6%97%b6%e9%97%b4.md
[09 对比Hashtable、HashMap、TreeMap有什么不同？-极客时间]: https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/Java%20%e6%a0%b8%e5%bf%83%e6%8a%80%e6%9c%af%e9%9d%a2%e8%af%95%e7%b2%be%e8%ae%b2/09%20%20%e5%af%b9%e6%af%94Hashtable%e3%80%81HashMap%e3%80%81TreeMap%e6%9c%89%e4%bb%80%e4%b9%88%e4%b8%8d%e5%90%8c%ef%bc%9f-%e6%9e%81%e5%ae%a2%e6%97%b6%e9%97%b4.md

