
# Java 异常

## 一. 异常分类

![Java异常分类](https://img-blog.csdn.net/20180424174531392?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

	Java 语言规范将派生于Error类或RuntimeException类的所有异常称为未检查（unchecked）异
	常；所有其他的异常称为已检查(checked)异常。
	
	一个方法必须声明所有可能抛出的checked异常，而unchecked异常要么不可控制(Error),要么就应该
	避免发生(RuntimeException)。

### 1. Error
- Error 类描述了java运行时系统的内部错误和资源耗尽错误。
- Error 由Java虚拟机生成并抛出，包括动态链接失败，虚拟机错误等。应用程序不应该抛出这种类型的对象。
- 如 OutOfMemoryError、StackOverflowException、ThreadDeath 等
	
### 2. Exception
	Exception 有两个分支： RuntimeException 和 IOException。
	
#### 2.1. IOException（checked异常）
- 当前方法知道如何处理时，用try....catch块来处理该异常；不知道如何处理时，在定义该方法时声明抛出该异常
-  常见checked异常：Java.lang.ClassNotFoundException；Java.lang.NoSuchMethodException；Java.io.IoException

#### 2.2 RuntimeException
-  RuntimeException 由系统自动检测并将它们交给缺省的异常处理程序
-  常见RuntimeException: Java.lang.ArithmeticException（算术异常）; Java.lang.ClassCastException （类型转换异常）；Java.lang.IndexOutOfBoundsException（数组越界异常）；Java.lang.NullPointerException（空指针异常）
	
## 二. 抛出异常

	编译器严格地执行 throws 说明符，如果调用了一个抛出 checked 异常的方法，就必须对它进行处理
	或者将它继续进行传递。

### 1. 抛出已经存在的异常类
找到一个合适的异常类 -> 创建这个类的一个对象 -> 将对象抛出
	
```java
String readData(Scanner in) throws EOFException {
	...
	while(...) {
		if(!in.hasNext()) { //异常发生
			if(n < len)
				throw new EOFException();
		}
	}
}
```
- EOFException：在输入过程中，遇到了一个未预期的EOF后的信号
- EOFException 类还有一个含有字符串参数的构造器，描述异常信息

### 2. 创建异常类
定义一个派生于Exception类或其子类的异常类 -> 同上

```java
class FileFormatException extends IOException {
	public FileFormatException() {}
	public FileFormatException(String gripe) {
		super(gripe);
	}
}

String readData(BufferedReader in) throws FileFormatException {
	...
	while(...) {
		if(ch == -1) { //异常发生
			if(n < len)
				throw new EOFException();
		}
		...
	}
	return s;
}
```
- 习惯上，自定义的异常类应该包含两个构造器，一个是默认的构造器，另一个是带有详细描述信息的构造器
- 超类 Throwable 的 toString 方法会将描述信息打印出来，这有利于调试

## 三. 捕获异常


### 1. try - catch
- 如果在 try 语句块中的任何代码抛出了一个在 catch 子句中说明的异常类，程序将跳过其他代码，执行catch子句中处理器代码。
- 如果 try 语句块没有抛出任何异常，那么程序跳过catch子句。
- 如果方法中的任何代码抛出了一个在catch子句中没有声明的异常类型，那么这个方法就会立即退出。

```java
try {
	...
} catch (FileNotFoundException | UnKnowHostException e) {
	e.getMessage();
} catch (IOException e) {
	e.printStackTrace();
} 
```
- e.getMessage() 得到详细的错误信息； e.getClass().getName() 得到异常对象的实际类型
- 只有当捕获的异常类型之间不存在子类关系时，才能放在同一个catch子句中

### 2. 再次抛出异常（异常链）
- 如果改变了一个供其他程序员使用的子系统，子系统故障的异常类型可能会有多种解释。在 catch 子句中可以抛出一个异常，目的是改变异常的类型
- ServletException就是这样一个异常类型，执行servlet的代码可能不想知道发生异常的细节，但想明确知道servlet是否有问题

```java
try {
	...
} catch (SQLException e) {
	Throwable se = new ServletException("database error");
	se.initCause(e); //将原始异常设置为新异常的“原因”
	throw se;
}
```
- 上面这种异常处理使用了包装技术
- 当捕获到异常时，可以使用 Throwable e = se.getCause() 重新得到原始异常

### 3. finally 子句
- 当代码抛出一个异常时，就会终止方法中剩余代码的处理，这会产生资源回收问题。finally 子句可以解决这个问题
- 不管是否有异常被捕获，finally 子句中的代码都会被执行
- try 语句可以只有 finally 子句而没有 catch 子句
- finally 子句中的 return 会覆盖原始的返回值

```java
InputStream in = new FileInputStream(...); // 读:从流中读取数据

try {
	// 1
	code that might throw exceptions;
	// 2
} catch (IOException e) {
	// 3
	show error message;
	// 4
} finally {
	// 5
	in.close();
}
// 6
```
- 情况一：代码没有抛出异常：1->2->5->6
- 情况二：抛出一个在catch子句中捕获的异常：(1)如果catch子句没有抛出异常：1->3->4->5->6; (2)如果catch子句抛出了一个异常(异常将会抛回这个方法的调用者）：1->3->5
- 情况三：抛出一个不在catch子句中捕获的异常：1->5