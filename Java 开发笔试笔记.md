## Q：Java局部变量一定要初始化吗？
	全局变量和类的成员变量无须初始化，java虚拟机会默认进行初始化；但类方法中的局部变量一定要初	始化，局部变量运行时被分配在jvm栈中，量大，生命周期短，如果虚拟机给每个局部变量都初始化一
	下，是一笔很大的开销，但变量不初始化为默认值就使用是不安全的。出于速度和安全性两个方面的综合
	考虑，解决方案就是虚拟机不初始化，但要求编写者一定要在使用前给变量赋值。 
	
## Q：Java中String类型变量的赋值问题
```java
public class Example {
    
    String str = new String("good");
    char[] ch = { 'a', 'b', 'c' };

    public static void main(String[] args) {
        Example ex = new Example();
        ex.change(ex.str, ex.ch);
        System.out.println(ex.str);
        System.out.println(ex.ch);
    }

    public void change(String str, char ch[]) {
        str = "test ok";
        ch[0] = 'g';
    }
    
}
```

## Q: Git 如何解决冲突？
