# Annotation 注解
```
注解如同一张标签。
Java 注解用于为 Java 代码提供元数据。作为元数据，注解不直接影响你的代码执行，但也有一些类型的
注解实际上可以用于这一目的。Java 注解是从 Java5 开始添加到 Java 的。
```
---

## 1. 注解语法

### 1.1 注解的定义
- 定义一个TestAnnotation的标签

##### code：
```java
public @interface TestAnnotation {
}
```

### 1.2 注解的属性
- 1.注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型.
- 2.注解中属性可以用 default 关键值设置默认值；如果没有默认值，使用的时候需要进行赋值。

##### code：
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    int id() default 0;
    String msg() default "Hello World!;
}

@TestAnnotation(id=3,msg="Hello Annotation!")
public class Test {
}
```

### 1.3 注解的应用
- 将TestAnnotation标签贴在类Test上

##### code：
```java
@TestAnnotation
public class Test {
}
```

### 1.4 元注解
- 1.元注解是一种基本注解，可以注解到注解上。
- 2.元注解有@Retention、@Documented、@Target、@Inherited、@Repeatable 5 种。

#### 1.4.1 @Retention
它解释说明这个注解的存活时间。

- 1.RetentionPolicy.SOURCE 注解只在源码阶段保留； 
- 2.RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中;
- 3.RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中.

##### code：
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
}
```

#### 1.4.2 @Documented
它的作用是能够将注解中的元素包含到 Javadoc 中

#### 1.4.3 @Target
它指定了注解运用的地方。

- 1.ElementType.ANNOTATION_TYPE 可以给一个注解进行注解；
- 2.ElementType.CONSTRUCTOR 可以给构造方法进行注解；
- 3.ElementType.FIELD 可以给属性进行注解；
- 4.ElementType.LOCAL_VARIABLE 可以给局部变量进行注解；
- 5.ElementType.METHOD 可以给方法进行注解；
- 6.ElementType.PACKAGE 可以给一个包进行注解；
- 7.ElementType.PARAMETER 可以给一个方法内的参数进行注解；
- 8.ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举。

#### 1.4.4 @Inherited
如果一个父类被 @Inherited注解过的注解进行注解的话，它的子类没有被任何注解应用的话，这个子类就继承了父类的注解。

##### code：
```java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@interface Test {}

@Test
public class A {}

public class B extends A {}
```
子类B会继承父类A的Test注解。

#### 1.4.5 @Repetable
@Repeatable 是 Java 1.8 才加进来的。当注解的值可以同时取多个时使用。

##### code：
```
@interface Persons {
    Person[]  value();
}

@Repeatable(Persons.class)
@interface Person{
    String role default "";
}

@Person(role="artist")
@Person(role="coder")
@Person(role="PM")
public class SuperMan{

}
```
##### 代码解释：
- 1.Persons是容器注解，容器注解就是用来存放其他注解的注解；
- 2.Persons 是一张总的标签，上面贴满了 Person 这种同类型但内容不一样的标签。把 Persons 给一个 SuperMan 贴上，相当于同时给他贴了画家、程序员、产品经理的标签。

## 2. Java预置的注解

### 2.1 @Deprecated
这个注解是用来标记过时的元素。

### 2.2 @Override
提示子类要复写父类中被 @Override 修饰的方法。

### 2.3 @SuppresswWarnings
告诉编译器忽略指定的警告，不用在编译完成后出现警告信息。

### 2.4 @SafeVarargs
主要目的是处理可变长参数中的泛型，此注解告诉编译器：在可变长参数中的泛型是类型安全的。

##### code:
```java
public class VarargsWaring {  
    @SafeVarargs  
    public static<T> T useVarargs(T... args){  
        System.out.println(args.length);  
        return args.length > 0?args[0]:null;  
    }  
}  

//调用
public class safe {  
    public static void main(String[] args) {  
        System.out.println(VarargsWaring.useVarargs(new ArrayList<String>()));  
    }  
}  
```

### 2.5 @FunctionalInterface
函数式接口注解，Java 1.8 版本引入的新特性。函数式接口 (Functional Interface) 就是一个具有一个方法的普通接口。

## 3. Spring注解

### 3.1 @Autowired
- 1.自动装配，其作用是为了消除Java代码里面的getter/setter与bean属性中的property.
- 2.@Autowired接口注入，使用@Qualifier注解，括号里面的应当是Car接口实现类的类名.

##### code:
```java
public interface Car
{
    public String carName();
}

@Service
public class BMW implements Car
{
    public String carName()
    {
        return "BMW car";
    }
}

@Service
public class Benz implements Car
{
    public String carName()
    {
        return "Benz car";
    }
}

@Service
public class CarFactory
{
    @Autowired
    @Qualifier("BMW")
    private Car car;
    
    public String toString()
    {
        return car.carName();
    }
}
```

### 3.2 @Resource
与@Autowired作用相似。

@Autowired 和 @Resource的区别：

- 1.@Autowired默认按照byType方式进行bean匹配，@Resource默认按照byName方式进行bean匹配

- 2.@Autowired是Spring的注解，@Resource是J2EE的注解. Spring属于第三方的，J2EE是Java自己的东西，建议使用@Resource注解，以减少代码和Spring之间的耦合.

### 3.3 @Controller
- 1.控制器，注入服务
- 2.@Controller 用于标记在一个类上，被Controller标记的类就是一个控制器，这个类中的方法，就是相应的动作。使用它标记的类就是一个SpringMVC Controller 对象。分发处理器将会扫描使用了该注解的类的方法.
- 3.@RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径.
- 4.@PathVariable 注解用来获取请求url中的动态参数.
- 5.@Responsebody 注解表示该方法的返回的结果直接写入 HTTP 响应正文（ResponseBody）中，一般在异步获取数据时使用.返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等s时，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区.
- 6.由前端JS传来的数据一般为字符串，当我们在后台赋予Date类型是则会报错。我们可以利用@InitBinder注解来注册编辑器，提前进去数据类型转换

##### code:
```java
@Controller
//设置想要跳转的父路径
@RequestMapping(value = "/")
public class Sample {
    //如需注入，则写入需要注入的类
    //@Autowired
    
            // 设置方法下的子路经
            @RequestMapping(value = "/method")
            public String Action1() {
            	return "helloWorld";
            }
            
            @RequestMapping(value = "user/login/{id}/{name}/{status}")
            @ResponseBody
            public User Action2(@PathVariable int id, @PathVariable String name, 
            					@PathVariable boolean status) {
            		//返回一个User对象响应ajax的请求
            		return new User(id, name, status);
            }
            
            @InitBinder
            public void initBinder(WebDataBinder binder) {
            	SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            	dateFormat.setLenient(false);
            	binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, true));
            	}
 }
```

### 3.4 @Service
- 1.服务，注入dao.
- 2.使得spring配置文件里面只有一个自动扫描的标签，增强Java代码的内聚性并进一步减少配置文件.
- 3.声明类是一个bean，其他的类才可以使用@Autowired将该类作为一个成员变量自动注入.

##### code:
```java
@Service
@Scope("prototype")
public class Zoo
{
    @Autowired
    private Monkey monkey;
    @Autowired
    private Tiger tiger;
    
    public String toString()
    {
        return "MonkeyName:" + monkey + "\nTigerName:" + tiger;
    }
}
```
Spring默认产生的bean是单例的,配置 @Scope 为"prototype"表示原型即每次都会new一个新的对象。

### 3.5 @Repository
- 1.dao,实现dao访问.
- 2.@Repository(value="userDao")注解是告诉Spring，让Spring创建一个名字叫“userDao”的UserDaoImpl实例。

### 3.6 @Component
把普通pojo实例化到spring容器中，相当于配置文件中的<bean id="" class=""/>













