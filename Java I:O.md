# Java I/O

Java I/O 使用了装饰者模式来实现

Java 的 I/O 大概可以分成以下几类：

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 新的输入/输出：NIO

## 一. 磁盘操作: File
File 类可以用于表示文件和目录的信息，但是它不表示文件的内容。

## 二. 字节操作: InputStream 和 OutputStream

![](https://img-blog.csdnimg.cn/20190315144653898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

	Java I/O 使用了装饰者模式来实现。以 InputStream 为例，
	InputStream 是抽象组件，
	FileInputStream 是 InputStream 的子类，属于具体组件，提供了字节流的输入操作
	FilterInputStream 属于抽象装饰者，装饰者用于装饰组件，为组件提供额外的功能，例如
	BufferedInputStream 是具体装饰者，为 FileInputStream 提供缓存的功能。
	
## 三. 字符操作：Reader 和 Writer

不管是磁盘还是网络传输，最小的存储单元都是字节，而不是字符。但是在程序中操作的通常是字符形式的数据，因此需要提供对字符进行操作的方法。

- InputStreamReader 实现从字节流解码成字符流；
- OutputStreamWriter 实现字符流编码成为字节流。

编码就是把字符转换为字节，而解码是把字节重新组合成字符。

## 四. 对象操作：Serializable

### 1. Java 序列化的定义

- 序列化：Java序列化是指把Java对象转换为字节序列的过程。对象序列化最主要的用处就是在传递和保存对象的时候，保证对象的完整性和可传递性。序列化是把对象转换成有序字节流，以便在网络上传输或者保存在本地文件中。序列化后的字节流保存了Java对象的状态以及相关的描述信息。序列化机制的核心作用就是对象状态的保存与重建。

- 反序列化：而Java反序列化是指把字节序列恢复为Java对象的过程；客户端从文件中或网络上获得序列化后的对象字节流后，根据字节流中所保存的对象状态及描述信息，通过反序列化重建对象。

### 2. 如何实现序列化和反序列化，底层怎么实现

只有实现了 Serializable 或 Externalizable 接口的类的对象才能被序列化，否则抛出异常！

**（1）JDK类库中序列化和反序列化API**

	java.io.ObjectOutputStream：表示对象输出流；
	java.io.ObjectInputStream：表示对象输入流；
	
**（2）JDK类库中序列化的步骤**

步骤一：创建一个对象输出流，它可以包装一个其它类型的目标输出流，如文件输出流：

	ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("D:\\object.out"));
	
步骤二：通过对象输出流的writeObject()方法写对象：

	oos.writeObject(new User("xuliugen", "123456", "male"));
	
**（3）JDK类库中反序列化的步骤**

步骤一：创建一个对象输入流，它可以包装一个其它类型输入流，如文件输入流：

	ObjectInputStream ois= new ObjectInputStream(new FileInputStream("object.out"));
	
步骤二：通过对象输出流的readObject()方法读取对象：

	User user = (User) ois.readObject();
	
### 3. 序列化注意事项

- 序列化时，只对对象的状态进行保存，而不管对象的方法；
- 当一个父类实现序列化，子类自动实现序列化，不需要显式实现 Serializable 接口；
- 当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化；
- 并非所有的对象都可以序列化；
- 声明为static和transient类型的成员数据不能被序列化。因为static代表类的状态，transient代表对象的临时数据；
- 如果一个对象的成员变量是一个对象，那么这个对象的数据成员也会被保存！这是能用序列化解决深拷贝的重要原因；

### 4. 常见的序列化协议

- COM
- XML & SOAP
- JSON（JavaScript Object Notation）
- Thrift
- Protobuf
- Avro

## 五. 网络操作：Socket

**Stock通信实现流程:**

1. 服务器绑定端口：server = new ServerSocket(PORT)
2. 服务器阻塞监听：socket = server.accept()
3. 服务器开启线程：new Thread(Handle handle)
4. 服务器读写数据：BufferedReader PrintWriter
5. 客户端绑定IP和PORT：new Socket(IP_ADDRESS, PORT)
6. 客户端传输接收数据：BufferedReader PrintWriter
	
**Socket的特点:**

1. Socket基于TCP链接，数据传输有保障
2. Socket适用于建立长时间链接
3. Socket编程通常应用于即时通讯

## 六. Java 中的 NIO，BIO，AIO

**同步IO和异步IO**

- 同步IO：读写IO时代码等数据返回后才继续执行后续代码;代码编写简单，CPU执行效率低；JDK提供的java.io是同步IO

- 异步IO：读写IO时仅发出请求，然后立即执行后续代码;代码编写复杂，CPU执行效率高;JDK提供的java.nio是异步IO

### 1. BIO：Block-IO

BIO 是一种同步阻塞的通信模式，用户进程发起一个IO操作以后，必须等待IO操作的真正完成后，才能继续运行；

![](https://img-blog.csdnimg.cn/2019031515504037.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- 服务器提供IP地址和监听的端口，客户端通过TCP的三次握手与服务器连接，连接成功后，双方才能通过套接字(Socket)通信。

- 在读取数据较慢时（比如数据量大、网络传输慢等），大量并发的情况下，其他接入的消息，只能一直等待，这就是最大的弊端。

**总结：**BIO模型中通过 Socket 和 ServerSocket 完成套接字通道的实现。阻塞，同步，建立连接耗时。

### 2. NIO: New-IO

NIO 是一种同步非阻塞的通信模式：用户进程发起一个IO操作以后，可做其它事情，但用户进程需要经常询问IO操作是否完成，这样造成不必要的CPU资源浪费。

![](https://img-blog.csdnimg.cn/20190315160350746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- **Channel**：通道，和流不同，通道是双向的。NIO可以通过Channel进行数据的读、写和同时读写操作。通道分为两大类：一类是网络读写（SelectableChannel），一类是用于文件操作（FileChannel），这里使用的 SocketChannel 和 ServerSocketChannel 都是SelectableChannel的子类。

- **Selector**：多路复用器，Selector会不断地轮询注册在其上的 Channel，如果某个Channel处于就绪状态，会被Selector轮询出来，然后通过SelectionKey可以取得就绪的Channel集合，从而进行后续的IO操作。服务器端只要提供一个线程负责Selector的轮询，就可以接入成千上万个客户端，这就是JDK NIO 库的巨大进步。

- **Buffer**：缓冲区，它是NIO与BIO的一个重要区别。BIO是将数据直接写入或读取到Stream对象中。而NIO的数据操作都是在缓冲区中进行的。缓冲区实际上是一个数组。

**总结：**NIO模型中通过 SocketChannel 和 ServerSocketChannel 完成套接字通道的实现。非阻塞/阻塞，同步，避免TCP建立连接使用三次握手带来的开销。 

### 3. AIO：Asynchronous IO

AIO 是一种异步非阻塞通信模式：用户进程发起一个IO操作然后，立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知。类比Future模式

- 服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理.

- AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。

- AIO 并没有采用NIO的多路复用器，而是使用异步通道的概念。其read，write方法的返回类型都是Future对象。而Future模型是异步的，其核心思想是：去主函数等待时间。


**总结：**AIO模型中通过AsynchronousSocketChannel和AsynchronousServerSocketChannel完成套接字通道的实现。非阻塞，异步。






