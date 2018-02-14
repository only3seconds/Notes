###JDK和JRE的区别
```
JRE(Java Runtime Enviroment)是Java的运行环境。面向Java程序的使用者，而不是开发者。如果你仅下载并安装了JRE，那么你的系统只能运行Java程序。JRE是运行Java程序所必须环境的集合，包含JVM标准实现及 Java核心类库。它包括Java虚拟机、Java平台核心类和支持文件。它不包含开发工具(编译器、调试器等)。

JDK(Java Development Kit)又称J2SDK(Java2 Software Development Kit)，是Java开发工具包，它提供了Java的开发环境(提供了编译器javac等工具，用于将java文件编译为class文件)和运行环境(提 供了JVM和Runtime辅助包，用于解析class文件使其得到运行)。如果你下载并安装了JDK，那么你不仅可以开发Java程序，也同时拥有了运行Java程序的平台。JDK是整个Java的核心，包括了Java运行环境(JRE)，一堆Java工具tools.jar和Java标准类库 (rt.jar)。
```

###枚举
```java
class FreshJuice {
   enum FreshJuiceSize{ SMALL, MEDIUM , LARGE }
   FreshJuiceSize size;
}
public class FreshJuiceTest {
   public static void main(String []args){
      FreshJuice juice = new FreshJuice();
      juice.size = FreshJuice.FreshJuiceSize.MEDIUM  ;
   }
}
```

###注释规范
1、类注释
在每个类前面必须加上类注释，注释模板如下：
```java
/**
* Copyright (C), 2006-2010, ChengDu Lovo info. Co., Ltd.
* FileName: Test.java
* 类的详细说明
*
* @author 类创建者姓名
* @Date    创建日期
* @version 1.00
*/
```

2、属性注释
在每个属性前面必须加上属性注释，注释模板如下：
```java
/** 提示信息 */
private String strMsg = null;
```
3、方法注释
在每个方法前面必须加上方法注释，注释模板如下：
```java
/**
* 类方法的详细使用说明
*
* @param 参数1 参数1的使用说明
* @return 返回结果的说明
* @throws 异常类型.错误代码 注明从此类方法中抛出异常的说明
*/
```
4、构造方法注释
在每个构造方法前面必须加上注释，注释模板如下：
```java
/**
* 构造方法的详细使用说明
*
* @param 参数1 参数1的使用说明
* @throws 异常类型.错误代码 注明从此类方法中抛出异常的说明
*/
```
5、方法内部注释
在方法内部使用单行或者多行注释，该注释根据实际情况添加。
如：
```java
//背景颜色
Color bgColor = Color.RED
```

###数据类型转换
1、包装类过渡类型转换
```java
float f1=100.00f;
Float F1=new Float(f1);
double d1=F1.doubleValue();//F1.doubleValue()为Float类的返回double值型的方法
```
2、字符串与其它类型间的转换
其它类型向字符串的转换
```
 (1)调用类的串转换方法:X.toString();
 (2)自动转换:X+"";
 (3)使用String的方法:String.valueOf(X);
```
字符串作为值,向其它类型的转换
```
(1)先转换成相应的封装器实例,再调用对应的方法转换成其它类型
例如，字符中"32.1"转换double型的值的格式为:new Float("32.1").doubleValue()。也可以用:Double.valueOf("32.1").doubleValue()

(2)静态parseXXX方法
String s = "1";
short t = Short.parseShort( s );
int i = Integer.parseInt( s );

(3)Character的getNumericValue(char ch)方法
```

###类变量赋值方法
无final修饰，声明时赋值，构造器中赋值，静态语句块或静态方法赋值
```java
class A {
    public static  String  staticA ="A" ;
    public A (){  staticA ="A2"; }
    static{  staticA ="A1"; }
    public static void toChange(){  staticA ="A3"; }
```
有final修饰，声明时赋值，声明与赋值分开可在静态语句块中赋值
```java
class B {
    public static final String  staticB ;
    static{  staticB ="B"; }
}
```
###transient 修饰符
序列化的对象包含被 transient 修饰的实例变量时，java 虚拟机(JVM)跳过该特定的变量。
该修饰符包含在定义变量的语句中，用来预处理类和变量的数据类型。
```java
public transient int limit = 55;   // 不会持久化
public int b; // 持久化
```
###短路逻辑运算符
```
当使用与逻辑运算符时，在两个操作数都为true时，结果才为true，但是当得到第一个操作为false时，其结果就必定是false，这时候就不会再判断第二个操作了。
```
###instanceof 运算符
该运算符用于操作对象实例，检查该对象是否是一个特定类型（类类型或接口类型）。
如果运算符左侧变量所指的对象，是操作符右侧类或接口(class/interface)的一个对象，那么结果为真。
例子：
```java
String name = "James";
boolean result = name instanceof String; // 由于 name 是 String 类型，所以返回真
```
###增强 for 循环(foreach)
语法格式:
```
for(声明语句 : 表达式)
{
   //代码句子
}
声明语句：声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。
表达式：表达式是要访问的数组名，或者是返回值为数组的方法。
```
实例
```java
public class Test {
   public static void main(String args[]){
      int [] numbers = {10, 20, 30, 40, 50};
      for(int x : numbers ){
         System.out.print( x );
         System.out.print(",");
      }
	}
}
```
###创建格式化字符串
输出格式化数字可以使用 printf() 和 format() 方法。
String 类使用静态方法 format() 返回一个String 对象而不是 PrintStream 对象。
String 类的静态方法 format() 能用来创建可复用的格式化字符串，而不仅仅是用于一次打印输出。
```java
System.out.printf("浮点型变量的值为 " +
                  "%f, 整型变量的值为 " +
                  " %d, 字符串变量的值为 " +
                  "is %s", floatVar, intVar, stringVar);
```
```java
String fs;
fs = String.format("浮点型变量的值为 " +
                   "%f, 整型变量的值为 " +
                   " %d, 字符串变量的值为 " +
                   " %s", floatVar, intVar, stringVar);
```
###length()方法 length属性 size()方法
 1.length()方法是针对字符串来说的，要求一个字符串的长度就要用到它的length()方法；
 2.length属性是针对Java中的数组来说的，要求数组的长度可以用其length属性；
 3.java中的size()方法是针对泛型集合说的,如果想看这个泛型有多少个元素,就调用此方法来查看!
```java
public static void main(String[] args) {
    String []list={"ma","cao","yuan"};
    String a="macaoyuan";
    System.out.println(list.length);
    System.out.println(a.length());
    List array=new ArrayList();
    array.add(a);
    System.out.println(array.size());
}
```language
```
###Arrays 类
java.util.Arrays 类能方便地操作数组，它提供的所有方法都是静态的。
```
给数组赋值：通过 fill 方法。
对数组排序：通过 sort 方法,按升序。
比较数组：通过 equals 方法比较数组中元素值是否相等。
查找数组元素：通过 binarySearch 方法能对排序好的数组进行二分查找法操作。
```
###可变参数
```java
public class VarargsDemo {
    public static void main(String args[]) {
        // 调用可变参数的方法
        printMax(34, 3, 3, 2, 56.5);
        printMax(new double[]{1, 2, 3});
    }

    public static void printMax( double... numbers) {
        if (numbers.length == 0) {
            System.out.println("No argument passed");
            return;
        }
        double result = numbers[0];
        for (int i = 1; i <  numbers.length; i++){
            if (numbers[i] >  result) {
                result = numbers[i];
            }
        }
        System.out.println("The max value is " + result);
    }
}
```
###文件 InputStream 和 OutputStream
```java
import java.io.*;

public class fileStreamTest{
  public static void main(String[] args) throws IOException {
    File f = new File("a.txt");
    FileOutputStream fop = new FileOutputStream(f);
    OutputStreamWriter writer = new OutputStreamWriter(fop, "UTF-8");
    writer.append("中文输入"); // 写入到缓冲区
    writer.append("\r\n");
    writer.append("English");
    writer.close(); //关闭写入流,同时会把缓冲区内容写入文件,所以上面的注释掉
    fop.close(); // 关闭输出流,释放系统资源

    FileInputStream fip = new FileInputStream(f);
    InputStreamReader reader = new InputStreamReader(fip, "UTF-8");
    StringBuffer sb = new StringBuffer();
    while (reader.ready()) {
      sb.append((char) reader.read()); // 转成char加到StringBuffer对象中
    }
    System.out.println(sb.toString());
    reader.close(); // 关闭读取流
    fip.close(); // 关闭输入流,释放系统资源
  }
}
```
###Scanner类
``可以通过 Scanner 类来获取用户的输入``
```java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in); // 从键盘接收数据
        //Scanner sc=new Scanner(new File("test.txt")); //从文件接收数据
        float f = 0.0f;
        System.out.print("输入小数：");
        if (scan.hasNextFloat()) {
            // 判断输入的是否是小数
            f = scan.nextFloat();
            System.out.println("小数数据：" + f);
        } else {
            System.out.println("输入的不是小数！");
        }
        scan.close();
    }
}
```
``输入的时候字符都是可见的，所以Scanner类不适合从控制台读取密码。从Java SE 6开始特别引入了Console类来实现这个目的。若要读取一个密码，可以采用下面这段代码``

```java
Console cons = System.console();
String username = cons.readline("User name: ");
char[] passwd = cons.readPassword("Password: ");
```
``
为了安全起见，返回的密码存放在一维字符数组中，而不是字符串中。在对密码进行处理之后，应该马上用一个填充值覆盖数组元素。
``

### StringTokenizer
``通过StringTokenizer类可以分解输入的整行得到的带空格的字符串。默认情况下，StringTokenizer以空格，制表符，换行符和回车符作为分割依据。``
```java
import java.util.Scanner;
import java.util.StringTokenizer;

class Test {
    public static void main(String[] args) {
        Scanner scanner=new Scanner(System.in);
        System.out.println("输入数据：");
        StringTokenizer stringTokenizer=new StringTokenizer(scanner.nextLine());
        System.out.println("分隔后：");
        while(stringTokenizer.hasMoreTokens()){
            System.out.println(stringTokenizer.nextToken());
        }
    }
}
```
结果
```
输入数据：
runoob com
分隔后：
runoob
com
```
### final 关键字
```
final声明类可以把类定义为不能继承的，即最终类；用于修饰方法，该方法不能被子类重写；被定义为 final 的实例变量不能被修改。

声明类：final class 类名 {//类体}
声明方法：修饰符(public/private/default/protected) final 返回值类型 方法名(){//方法体}
```
**注:被声明为 final 类的方法自动地声明为 final，但是实例变量并不是 final**

###父类的引用指向子类的对象
```java
class Animal{
   public void move(){
      System.out.println("动物可以移动");
   }
}

class Dog extends Animal{
   public void move(){
      System.out.println("狗可以跑和走");
   }
   public void bark(){
      System.out.println("狗可以吠叫");
   }
}

public class TestDog{
   public static void main(String args[]){
      Animal a = new Animal(); // Animal 对象
      Animal b = new Dog(); // Dog 对象

      a.move();// 执行 Animal 类的方法
      b.move();//执行 Dog 类的方法
      b.bark();//报错 
   }
}
```
```language
该程序将抛出一个编译错误，因为b的引用类型Animal没有bark方法
```
###Java 数据结构
```
枚举（Enumeration）
位集合（BitSet）
向量（Vector）
栈（Stack）
字典（Dictionary）
哈希表（Hashtable）
属性（Properties）
集合框架(Collection)
```
###遍历 ArrayList
```java
import java.util.*;

public class Test{
 public static void main(String[] args) {
     List<String> list=new ArrayList<String>();
     list.add("Hello");
     list.add("World");

     //第一种遍历, 使用foreach遍历List
     for (String str : list) {
        System.out.println(str);
     }

     //第二种遍历，把链表变为数组相关的内容进行遍历
     String[] strArray=new String[list.size()];
     list.toArray(strArray);
     for(int i=0;i<strArray.length;i++)
     {
        System.out.println(strArray[i]);
     }

    //第三种遍历, 使用迭代器进行相关遍历
     Iterator<String> ite=list.iterator();
     while(ite.hasNext())//判断下一个元素之后有值
     {
         System.out.println(ite.next());
     }
 }
}
```
###遍历 Map
```java
import java.util.*;

public class Test{
     public static void main(String[] args) {
      Map<String, String> map = new HashMap<String, String>();
      map.put("1", "value1");
      map.put("2", "value2");

      //第一种：二次取值 (普遍使用)
      for (String key : map.keySet()) {
       System.out.println("key= "+ key + " and value= " + map.get(key));
      }

      //第二种
      Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
      while (it.hasNext()) {
       Map.Entry<String, String> entry = it.next();
       System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
      }

      //第三种：推荐，尤其是容量大时
      for (Map.Entry<String, String> entry : map.entrySet()) {
       System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
      }

      //第四种
      for (String v : map.values()) {
       System.out.println("value= " + v);
      }
     }
}

```
### 泛型
```java
public class GenericTest {
    public static void main(String[] args) {
        List<String> name = new ArrayList<String>();
        List<Integer> age = new ArrayList<Integer>();
        List<Number> number = new ArrayList<Number>();

        name.add("icon");
        age.add(18);
        number.add(314);

		getData(name);
        getData(age);
        getData(number);

        getUperNumber(name);//报错 getUperNumber()方法中的参数已经限定了参数 泛型上限为Numbe
        getUperNumber(age);
        getUperNumber(number);
   }

   public static void getData(List<?> data) {
      System.out.println("data :" + data.get(0));
   }

   public static void getUperNumber(List<? extends Number> data) {
          System.out.println("data :" + data.get(0));
       }
}
```
``
<? extends T>表示该通配符所代表的类型是T类型的子类。
<? super T>表示该通配符所代表的类型是T类型的父类。
``
###创建一个线程
```
通过实现 Runnable 接口；
通过继承 Thread 类本身；
通过 Callable 和 Future 创建线程。
```
###MySQL连接
```java
import java.sql.*;

public class MySQLDemo {
    // JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB";

    // 数据库的用户名与密码，需要根据自己的设置
    static final String USER = "root";
    static final String PASS = "123456";

    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        try{
            // 注册 JDBC 驱动
            Class.forName("com.mysql.jdbc.Driver");

            // 打开链接
            System.out.println("连接数据库...");
            conn = DriverManager.getConnection(DB_URL,USER,PASS);

            // 执行查询
            System.out.println(" 实例化Statement对象...");
            stmt = conn.createStatement();
            String sql;
            sql = "SELECT id, name, url FROM websites";
            ResultSet rs = stmt.executeQuery(sql);

            // 展开结果集数据库
            while(rs.next()){
                // 通过字段检索
                int id  = rs.getInt("id");
                String name = rs.getString("name");
                // 输出数据
                System.out.print("ID: " + id);
                System.out.print(", 站点名称: " + name);
            }

            // 完成后关闭
            rs.close();
            stmt.close();
            conn.close();
        }catch(SQLException se){
            // 处理 JDBC 错误
            se.printStackTrace();
        }catch(Exception e){
            // 处理 Class.forName 错误
            e.printStackTrace();
        }finally{
            // 关闭资源
            try{
                if(stmt!=null) stmt.close();
            }catch(SQLException se2){
            }// 什么都不做
            try{
                if(conn!=null) conn.close();
            }catch(SQLException se){
                se.printStackTrace();
            }
        }
        System.out.println("Goodbye!");
    }
}
```
```
mysql
驱动：com.mysql.jdbc.Driver
URL：jdbc:mysql://machine_name:port/dbname
注：machine_name：数据库所在的机器的名称；
port：端口号，默认3306
```