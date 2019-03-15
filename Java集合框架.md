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
	化，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。
	ArrayList 重写了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那
	部分内容。
	
#### 扩容

	添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用
	grow() 方法进行扩容，新容量的大小为旧容量的 1.5 倍。扩容操作需要调用 Arrays.copyOf()
	把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概
	的容量大小，减少扩容操作的次数。
	
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

	HashSet 对 HashMap 进行了一次包装，HashSet 里面有一个 HashMap（适配器模式），对
	HashSet 的函数调用都会转换成合适的 HashMap 方法
	
	add() 方法，将存放的对象当做了 HashMap 的健，value 都是相同的 PRESENT 。由于
	HashMap 的 key 是不能重复的，所以每当有重复的值写入到 HashSet 时，value 会被覆盖，但 
	key 不会收到影响，这样就保证了 HashSet 中只能存放不重复的元素。
	
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

####（1）Java7 存储结构

![](https://img-blog.csdnimg.cn/20190313213409336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	不同的 key 经过 hash 运算可能会得到相同的地址，采用链地址法以后，如果遇到相同的 hash 值
	的key 的时候，可以将它放到作为数组元素的链表上。取元素的时候通过 hash 运算的结果找到这个
	链表，再在链表中找到与 key 相同的节点，就能找到 key 相应的值了。
	
#### (2) Java8 存储结构

![](https://img-blog.csdnimg.cn/20190313214044741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- 在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)
- 根据数组元素中，第一个节点数据类型是 Node 还是 TreeNode 来判断该位置下是链表还是红黑树的

	
**重要参数**

![](https://img-blog.csdnimg.cn/20190304170933672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

####（3）Java7 put过程

- 数组初始化： 插入第一个元素的时候做一次数组的初始化，确定初始的数组大小，并计算数组扩容的阈值。
- 求 key 的 hash 值， 找到对应的数组下标
- 添加节点到链表中： 先判断是否需要扩容，需要的话先扩容，然后再将这个新的数据插入到扩容后
的数组的相应位置处的链表的表头。扩容就是用一个新的大数组替换原来的小数组，并将原来数组中的值
迁移到新的数组中。

#### (4) Java8 put过程

![](https://img-blog.csdnimg.cn/20190314102347533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- 和 Java7 稍微有点不一样的地方就是，Java7 是先扩容后插入新值的，Java8 先插值再扩容

#### (5) Java7 get 过程

时间复杂度取决于链表的长度，为 O(n)

	（1）根据 key 计算 hash 值，找到相应的数组下标
	（2）遍历该数组位置处的链表，直到找到相等(==或equals)的 key.
	
#### (6) Java8 get 过程

	（1）计算 key 的 hash 值，根据 hash 值找到对应数组下标
	（2）判断该元素类型是否是 TreeNode，如果是，用红黑树的方法取数据，如果不是，遍历链表，直
	    到找到相等(==或equals)的 key.
	    
#### （7）线程安全性

	在多线程使用场景中，应该尽量避免使用线程不安全的 HashMap，而使用线程安全的 
	ConcurrentHashMap。那么为什么说 HashMap 是线程不安全的，在并发环境下，可能会形成环状
	链表（扩容时可能造成），导致 get 操作时，cpu 空转，所以，在并发环境中使 用HashMap 是非
	常危险的。

	
### 2. ConcurrentHashMap

#### (1) Java7 ConcurrentHashMap

![](https://img-blog.csdnimg.cn/20190314105402145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

**分段锁**

- 整个 ConcurrentHashMap 由一个个 Segment 组成，Segment 通过继承 ReentrantLock 来进行加锁
- 每个 Segment 是线程安全的 HashMap。一个 ConcurrentHashMap 维护一个 Segment 数组，一个 Segment 维护一个 HashEntry 数组。
- 每次需要加锁的操作锁住的是一个 segment，只要保证每个 Segment 是线程安全的，也就实现了全局的线程安全
- concurrencyLevel：segment 数，一旦初始化以后，不可以扩容，默认为16.

**put 过程**

	（1）根据 hash 值找到相应的 segment
	（2）获取该 segment 的独占锁
	（3）添加节点
	
**get 过程**

	（1）计算 hash 值，定位 segment 
	（2）根据 hash 找到 segment 中具体位置
	（3）到这里是链表了，顺着链表进行查找
	
- get 操作的高效之处在于整个 get 过程不需要加锁，除非读到的值是空的才会加锁重读。get 方法里将要使用的共享变量都定义成 volatile.
	
**size操作**

	每个 Segment 维护了一个 count 变量来统计该 Segment 中的键值对个数。在执行 size 操作
	时，需要遍历所有 Segment 然后把 count 累计起来。
	
	ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果一
	致，那么可以认为这个结果是正确的。尝试次数使用 RETRIES_BEFORE_LOCK 定义，该值为 2，
	retries 初始值为 -1，因此尝试次数为 3。如果尝试的次数超过 3 次，就需要对每个 Segment
	加锁。

**Java8 改进**

	1. 结构上和 java8 HashMap 基本一样，链表过长时会转换为红黑树。
	
	2. JDK 1.8 的实现不是用了 Segment，Segment 属于重入锁 ReentrantLock。JDK 1.8 使
	   用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。
	   
	3. synchronized 的好处：
		（1）synchronized 的锁粒度更低；
		（2）synchronized 优化空间更大；
		（3）在大量数据操作的情况下，ReentrantLock 会开销更多的内存。


### 3. LinkedHashMap

![](https://img-blog.csdnimg.cn/20190314113100155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	 LinkedHashMap 是 HashMap 的直接子类，二者唯一的区别是 LinkedHashMap 在 HashMap
	 的基础上，采用双向链表的形式将所有 entry 连接起来，这样是为保证元素的迭代顺序跟插入顺序
	 相同。
	 
	 迭代 LinkedHashMap 时不需要像 HashMap 那样遍历整个table，而只需要直接遍历 header
	 指向的双向链表即可，也就是说 LinkedHashMap 的迭代时间就只跟entry的个数相关，而跟
	 table的大小无关。
- 使用链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序

**put()**

	从 table 的角度看，新的 entry 需要插入到对应的 bucket 里，当有哈希冲突时，采用头插法将
	新的 entry 插入到冲突链表的头部。
	
	从 header 的角度看，新的 entry 需要插入到双向链表的尾部。


### 4. TreeMap
- 基于红黑树实现

## 五. 常见问题

### 1. ArrayList 和 LinkedList 区别

### 2. HashMap 和 HashTable 区别
	
- HashMap 几乎可以等价于 Hashtable，除了 HashMap 是非 synchronized 的，并可以接受null的键(key)和值(value)，而 Hashtable 则不行.

- HashMap 的迭代器 Iterator 是 fail-fast 迭代器，而 Hashtable 的 enumerator 迭代器不是fail-fast的。

- HashMap不能保证随着时间的推移Map中的元素次序是不变的

- 哈希值的使用不同，Hashtable 直接使用对象的 hashCode。而 HashMap 重新计算hash值，而且用于代替求模。

- Hashtable 中 hash 数组默认大小是11，增加的方式是 old*2+1。HashMap 中 hash 数组的默认大小是16，而且一定是 2 的指数。

- HashTable 基于 Dictionary 类，而 HashMap 基于 AbstractMap 类

### 3. HashMap 中的 key 可以是任何对象或数据类型吗？
	HashMap 中的 key 不能是基本数据类型，也不能是可变对象。
	
	基本数据类型不能调用 hashcode 和 equals 方法
	
	可变对象是指创建后自身状态能改变的对象。换句话说，可变对象是该对象在创建后它的哈希值（由类
	的hashCode（）方法可以得出哈希值）可能被改变。
	
	如果 HashMap 的 Key的哈希值在存储键值对后发生改变，Map可能再也查找不到这个Entry了。
	
	解决办法：能定义属于自己的不可变类。我们只需要保证成员变量的改变能保证该对象的哈希值不变即
	可。
	
### 4. HashMap 和 ConcurrentHashMap 区别

### 5. Hash 解决冲突的办法

- 链地址法
- 开放地址法（向后一位）
	- 线性探测
	- 平方探测
	- 二次哈希
- 再哈希法

### 6. 构造相同hash的字符串进行攻击，这种情况应该怎么处理？JDK7如何处理

	攻击原理：服务器端用于存储这些 key-value 对的 HashMap 会被强行退化成链表
	
	解决办法：
		限制 POST 和 GET 请求的参数个数
		限制 POST 请求的请求体大小
		Web Application FireWall（WAF）
		
	Jdk1.7 HashMap 会动态的使用一个专门 TreeMap 实现来替换掉它。
	
### 7. Hashmap为什么大小是2的幂次？

	位运算的效率远远高于取余 % 运算。
	
	HashMap 的 put 方法中会根据计算的 hash 调用 indexFor 方法找到桶的下标。
	当容量一定是 2的次幂时，h & (length - 1) == h % length.



## 参考资料

[Java7/8 中的 HashMap 和 ConcurrentHashMap 全解析](http://www.importnew.com/28263.html)

[Java集合框架](https://github.com/frank-lam/fullstack-tutorial/blob/master/notes/JavaArchitecture/02-Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6.md)