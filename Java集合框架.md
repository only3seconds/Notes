# Java 集合框架

![](https://img-blog.csdnimg.cn/20190313195610936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	Java 集合框架位于 java.util 包下，主要包括 Collection（接口）、Map接口）和集合工具类 ;
	Collection 包括 List, Set, Queue

## 一. List

### 1. ArrayList

![](https://img-blog.csdnimg.cn/20190313210110896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

#### 概览

	基于动态数组实现,数组的默认大小为 10实现了 RandomAccess 接口, 支持随机访问;
	
#### 序列化

	ArrayList 具有动态扩容特性, 保存元素的数组使用 transient 修饰，声明数组默认不会被序列
	化。ArrayList 重写了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那
	部分内容。
	
#### 扩容

	添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用grow() 
	方法进行扩容，新容量的大小为旧容量的 1.5 倍。扩容操作需要调用 Arrays.copyOf() 把原数组整
	个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，
	减少扩容操作的次数。
	
#### 删除元素

	需要调用 System.arraycopy() 将 index+1 后面的元素都复制到 index 位置上。
	
#### Fail-Fast

	fail-fast 机制在遍历一个集合时，当集合结构被修改，会抛出 Concurrent Modification
	Exception。
	
	modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元
	素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。
	
	在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 
	Concurrent Modification Exception。
	
	sremove() 方法会让 expectModcount 和 modcount 相等，所以是不会抛出这个异常。
	
- 为了获得线程安全的 ArrayList，可以调用 Collections.synchronizedList(new ArrayList<>()); 返回一个线程安全的 ArrayList，也可以使用 concurrent 并发包下的 CopyOnWriteArrayList 类

### 2. Vector

#### 同步

	它的实现与 ArrayList 类似，但是使用了 synchronized 进行同步。
	
#### ArrayList 与 Vector

	Vector 是同步的,开销比 ArrayList 大， 访问速度慢 ，不建议使用
	Vector 每次扩容请求其大小的 2 倍空间,而 ArrayList 是 1.5 倍。
	
#### Vector 替代方案

- 方案一：为了获得线程安全的 ArrayList，可以使用 Collections.synchronizedList(); 得到一个线程安全的 ArrayList。

```java
List<String> list = new ArrayList<>();
List<String> synList = Collections.synchronizedList(list);
```
- 方案二：使用 concurrent 并发包下的 CopyOnWriteArrayList 类

```java
List<String> list = new CopyOnWriteArrayList<>();
```

### 3. LinkedList

#### 概览

	LinkedList 底层是基于双向链表实现的。
	
	LinkedList 同时实现了 List 接口和 Deque 接口，也就是说它既可以看作一个顺序容器，又可以
	看作一个队列（Queue），同时又可以看作一个栈（Stack）。
	
	当你需要使用栈或者队列时，可以考虑使用 LinkedList ，一方面是因为 Java 官方已经声明不建议
	使用 Stack 类，更遗憾的是，Java里根本没有一个叫做 Queue 的类（它是个接口名字）。
	
	关于栈或队列，现在的首选是 ArrayDeque，它有着比 LinkedList （当作栈或队列使用时）有着更
	好的性能。
	
	LinkedList没有实现同步（synchronized），如果需要多个线程并发访问，可以先采用 
	Collections.synchronizedList() 方法对其进行包装。
	
#### add()

![](https://img-blog.csdnimg.cn/20190313204526283.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	add(E e)，该方法在 LinkedList 的末尾插入元素，因为有 last 指向链表末尾，在末尾插入元素
	的花费是常数时间。
	
	add(int index, E element)，该方法是在指定下表处插入元素，需要先通过线性查找找到具体位
	置，然后修改相关引用完成插入操作。
	
#### remove()

![](https://img-blog.csdnimg.cn/20190313205321727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70))

#### get()

	使用二分查找来看 index 离 size 中间距离来判断是从头结点正序查还是从尾节点倒序查。
	这样的效率是非常低的，特别是当 index 越接近 size 的中间值时。
	
#### ArrayList 和 LinkedList

	ArrayList 基于动态数组实现，LinkedList 基于双向链表实现；
	ArrayList 支持随机访问，LinkedList 不支持；
	LinkedList 在任意位置添加删除元素更快。
	
## 二. Set

### 1. HashSet
- 基于哈希实现，支持快速查找，O(1)
- 失去了元素的插入顺序信息
- 不支持有序性操作，如查找某个范围内的元素

### 2. LinkedHashSet
- 具有 Hashset 的查找效率
- 内部使用链表维护了元素的插入顺序

### 3. TreeSet
- 基于红黑树实现，查找效率不如 HashSet, O(logN)
- 支持有序性操作

## 三. Queue

### 1. LinkedList
- 可以用它来支持双向队列


### 2. PriorityQueue
- 基于堆结构实现
- 可以用来实现优先队列

## 四. Map

### 1. HashMap

### Java7 HashMap

**（1）存储结构**

![](https://img-blog.csdnimg.cn/20190313213409336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	不同的 key 经过 hash 运算可能会得到相同的地址，采用链地址法以后，如果遇到相同的 hash 值的
	key 的时候，可以将它放到作为数组元素的链表上。取元素的时候通过 hash 运算的结果找到这个链
	表，再在链表中找到与 key 相同的节点，就能找到 key 相应的值了。


	
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

**（1）存储结构**
![](https://img-blog.csdnimg.cn/20190313214044741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- 在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)
- 根据数组元素中，第一个节点数据类型是 Node 还是 TreeNode 来判断该位置下是链表还是红黑树的

**（2）put 过程**

	和 Java7 稍微有点不一样的地方就是，Java7 是先扩容后插入新值的，Java8 先插值再扩容
	
**（3）get 过程**

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


### 2. LinkedHashMap
- 使用链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序


### 3. TreeHashMap
- 基于红黑树实现



## 参考资料

[Java7/8 中的 HashMap 和 ConcurrentHashMap 全解析](http://www.importnew.com/28263.html)