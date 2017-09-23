#第6章 单例模式与多线程
##** 单例模式**
- Java中单例模式定义：“一个类有且仅有一个实例，并且自行实例化向整个系统提供。”

- 3要素：一是单例模式的类只提供私有的构造函数，二是类定义中含有一个该类的静态私有对象，三是该类提供了一个静态的公有的函数用于创建或获取它本身的静态私有对象。


###6.1 立即加载 ／ “饿汉模式”
** 立即加载**就是在使用类的时候已经将对象创建完毕，常见的实例办法就是直接 new 实例化
```java
public class Singleton{
    //在自己内部定义自己的一个实例，只供内部调用
    private static final Singleton instance = new Singleton();
    private Singleton(){
        //do something
    }
    //这里提供了一个供外部访问本class的静态方法，可以直接访问
    public static Singleton getInstance(){
        return instance;
    }
}
```
##6.2 延迟加载 ／ “懒汉模式”
**延迟加载** **就是在调用 get() 方法时实例才被创建，常见的办法就是在 get() 方法中进行 new 实例化
```java
public class SingletonClass{
    private static SingletonClass instance=null;
    private SingletonClass(){
    //do something
    }
    public static SingletonClass getInstance(){
        if(instance==null){
               instance=new SingletonClass();
        }
        return instance;
    }
}

```
**缺点** ：创建出 “多例”

**解决办法** 使用 **DCL** 双检查锁机制
```java
public class Singleton{
    private static volatile Singleton instance=null;
    private Singleton(){
        //do something
    }
    public static  Singleton getInstance(){
        if(instance==null){ // lock1
            synchronized(SingletonClass.class){
                if(instance==null){ // lock2
                    instance=new Singleton();
                }
            }
        }
        return instance;
     }
}

```

##6.3 使用静态内置类实现单例模式
```java
public class SingletonClass{

	private static class SingletonHandler {
    	private static SingletonClass instance = new SingletonClass();
    }

    private SingletonClass(){
    //do something
    }

    public static SingletonClass getInstance(){
        return SingletonHandler.instance;
    }
}

```

##6.4 序列化和反序列化的单例模式实现
**静态内置类可以达到线程安全，但如果遇到序列化对象，使用默认的方式运行得到的结果还是多例的**
```java
public class SingletonClass implements Serializable {
	private static final long serialVersionUID = 888L;

    private static class SingletonHandler {
    	private static final SingletonClass instance=new SingletonClass();
    }

    private SingletonClass(){
    //do something
    }

    public static SingletonClass getInstance(){
        return SingletonHandler.instance;
    }

    protected SingletonClass readResolve() throws ObjectStreamException {
    	return SingletonHandler.instance;
    }
}
```
** 保证单列的方法就是在反序列化中使用 readResolve() 方法**

##6.5 使用 static 代码块实现单列模式
** 静态代码块中的代码在使用类的时候就已经执行了**
```java
public class SingletonClass{
    private static SingletonClass instance=null;
    private SingletonClass(){
    //do something
    }
    static {
    	instance=new SingletonClass();
    }
    public static SingletonClass getInstance(){
        return instance;
    }
}

```
##6.6 使用枚举数据类型 enum 实现单例模式
- ** 在使用枚举类时，构造方法会被自动调用**
```java
public class SingletonClass {
	public enum EnumSingleton {
    connectionFactory;
    private Connection connection;
    private EnumSingleton() {
    	try {
        	String url = "jdbc.sqlserver://localhost:1079;databaseName = y2";
            String username = "sa";
            String password = "";
            String driveName = "com.microsoft.sqlserver.jdbc.SQLServerDriver";
            Class.forName(driveName);
            connection = DriverManager.getConnection(url, username, password);
        } catch (ClassNotFoundException e){
        	e.printStackTrace();
        } catch (SQLException e) {
        	e.printStackTrace();
        }
    }
    public Connection getConnection() {
    	return connection;
    }
}
	public static Connection getConnection () {
	return EnumSingleton.connectionFactory.getConnection()
	}
}
```


