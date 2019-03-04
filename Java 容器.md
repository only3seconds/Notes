# Java 容器
	Java 容器主要包括 Collection 和 Map 两种;
	Collection 包括 List, Set, Queue
	
---
# List
---

## 1. ArrayList
- 基于动态数组实现
- 实现了 RandomAccess 接口, 支持随机访问
- ArrayList 具有动态扩容特性, 保存元素的数组使用 transient 修饰，声明数组默认不会被序列化。ArrayList 重写了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。
- 删除元素时需要调用 System.arraycopy() 对元素进行复制，因此删除操作成本很高。
- 添加元素时容量不够时，需要进行扩容，使得新容量为旧容量的 1.5 倍
- 为了获得线程安全的 ArrayList，可以调用 Collections.synchronizedList(new ArrayList<>()); 返回一个线程安全的 ArrayList，也可以使用 concurrent 并发包下的 CopyOnWriteArrayList 类

## 2. Vector
- 和 ArrayList 类似，但它是线程安全的
- 开销比 ArrayList 大， 访问速度慢 ，不建议使用
- Vector 每次扩容请求其大小的 2 倍空间

## 3. LinkedList
- 基于双向循环链表实现，只能顺序访问，但是能够快速插入和删除元素
- LinkedList 可以用作栈、队列、双向队列

---
# Set
---

## 1. HashSet
- 基于哈希实现，支持快速查找，O(1)
- 失去了元素的插入顺序信息
- 不支持有序性操作，如查找某个范围内的元素

## 2. LinkedHashSet
- 具有 Hashset 的查找效率
- 内部使用链表维护了元素的插入顺序

## 3. TreeSet
- 基于红黑树实现，查找效率不如 HashSet, O(logN)
- 支持有序性操作

---
# Queue
---

## 1. Linked List
- 可以用它来支持双向队列


## 2. PriorityQueue
- 基于堆结构实现
- 可以用来实现优先队列

---
# Map
---

## 1. HashMap

### Java7 HashMap

**（1）存储结构**

![](https://javadoop.com/blogimages/map/1.png)

	HashMap 里面是一个数组然后数组中每个元素是一个单向链表。
	
**（2）重要参数**

![](https://img-blog.csdnimg.cn/20190304170933672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)


**（3）put 过程**

	（1）数组初始化： 插入第一个元素的时候做一次数组的初始化，确定初始的数组大小，并计算数组扩容
	  	的阈值。
	（2）求 key 的 hash 值， 找到对应的数组下标
	（3）添加节点到链表中： 先判断是否需要扩容，需要的话先扩容，然后再将这个新的数据插入到扩容后
	的数组的相应位置处的链表的表头。扩容就是用一个新的大数组替换原来的小数组，并将原来数组中的值
	迁移到新的数组中。
		
**（4）get 过程**

时间复杂度取决于链表的长度，为 O(n)

	（1）根据 key 计算 hash 值，找到相应的数组下标
	（2）遍历该数组位置处的链表，直到找到相等(==或equals)的 key。
	
### Java8 HashMap
![](https://javadoop.com/blogimages/map/2.png)

- 在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)
- 根据数组元素中，第一个节点数据类型是 Node 还是 TreeNode 来判断该位置下是链表还是红黑树的

**（1）put 过程**

	和 Java7 稍微有点不一样的地方就是，Java7 是先扩容后插入新值的，Java8 先插值再扩容
	
**（2）get 过程**

	（1）计算 key 的 hash 值，根据 hash 值找到对应数组下标
	（2）判断该元素类型是否是 TreeNode，如果是，用红黑树的方法取数据，如果不是，遍历链表，直到找到相等(==或equals)的 key

	
### Java7 ConcurrentHashMap
![](https://javadoop.com/blogimages/map/3.png)

**（1）分段锁**
- 整个 ConcurrentHashMap 由一个个 Segment 组成，Segment 通过继承 ReentrantLock 来进行加锁
- 每个 Segment 是线程安全的 HashMap
- 每次需要加锁的操作锁住的是一个 segment，只要保证每个 Segment 是线程安全的，也就实现了全局的线程安全
- concurrencyLevel：segment 数，一旦初始化以后，不可以扩容

**（2）put 过程**
	（1）根据 hash 值找到相应的 segment
	（2）获取该 segment 的独占锁
	（3）添加节点
	
**（3）get 过程**
	（1）计算 hash 值，定位 segment 
	（2）根据 hash 找到 segment 中具体位置
	（3）到这里是链表了，顺着链表进行查找

	
### Java8 ConcurrentHashMap
	1. 结构上和 java8 HashMap 基本一样，链表过长时会转换为红黑树。不过它要保证线程安全性
	2. JDK 1.8 的实现不是用了 Segment，Segment 属于重入锁 ReentrantLock。而是使用了内置
		锁 synchronized，因为：
	（1）synchronized 的锁粒度更低；
	（2）synchronized 优化空间更大；
	（3）在大量数据操作的情况下，ReentrantLock 会开销更多的内存。


## 2. LinkedHashMap
- 使用链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序


## 3. TreeHashMap
- 基于红黑树实现


---
# 参考资料
---
[Java7/8 中的 HashMap 和 ConcurrentHashMap 全解析](http://www.importnew.com/28263.html)