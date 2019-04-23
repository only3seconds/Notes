---
# jdbc 编程
---
```java

//import java.sql.*;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JdbcTest {
    public static void main(String[] args) {
        //数据库连接
        Connection connection = null;
        //预编译的Statement，使用预编译的Statement提高数据库性能
        PreparedStatement preparedStatement = null;
        //结果集
        ResultSet resultSet = null;

        try {
            //加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");

            //通过驱动管理类获取数据库链接
            connection =  DriverManager.getConnection("jdbc:mysql://120.25.162.238:3306/mybatis001?characterEncoding=utf-8", "root", "123");
            //定义sql语句 ?表示占位符
            String sql = "select * from user where username = ?";
            //获取预处理statement
            preparedStatement = connection.prepareStatement(sql);
            //设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
            preparedStatement.setString(1, "王五");
            //向数据库发出sql执行查询，查询出结果集
            resultSet =  preparedStatement.executeQuery();
            //遍历查询结果集
            while(resultSet.next()){
                System.out.println(resultSet.getString("id")+"  "+resultSet.getString("username"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            //释放资源
            if(resultSet!=null){
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
            if(preparedStatement!=null){
                try {
                    preparedStatement.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
            if(connection!=null){
                try {
                    connection.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }

        }

    }

}
```
jdbc 编程存在的问题：

- 数据库连接，使用时创建，不使用立即释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费
- 将sql语句硬编码到java代码中，如果sql语句修改，需要重新编译java代码，不利于系统维护
- 向preparedStatement中设置参数，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护
- 遍历结果集数据时，将获取表的字段进行硬编码，不利于系统维护

---
# Mybatis 概述
---

## 1. mybatis 框架执行过程
- 1、配置mybatis的全局配置文件，SqlMapConfig.xml（名称不固定） 
- 2、通过配置文件，加载mybatis运行环境，创建SqlSessionFactory会话工厂(SqlSessionFactory在实际使用时按单例方式) 
- 3、通过 SqlSessionFactory 创建 SqlSession。SqlSession是一个面向用户接口（提供操作数据库方法），实现对象是线程不安全的，建议sqlSession应用场合在方法体内
- 4、调用sqlSession的方法去操作数据。如果需要提交事务，需要执行SqlSession的commit()方法。 
- 5、释放资源，关闭SqlSession

## 2. mybatis 开发dao
	mapper 代理开发方法
### 开发规范
	开发规范：

- mapper.xml中namespace就是mapper.java的类全路径
- mapper.xml 中 statement 的 id 和 mapper.java 中方法名一致
- mapper.xml 中 statement 的 parameterType 指定输入参数的类型和 mapper.java 的方法输入参数类型一致
- mapper.xml中statement 的 resultType指定输出结果的类型和 mapper.java 的方法返回值类型一致

### 输入映射和输出映射
输入映射：

- parameterType：指定输入参数类型可以简单类型、pojo、hashmap
- 对于综合查询，建议parameterType使用包装的pojo，有利于系统扩展

输出映射：
- resultType：查询到的列名和 resultType 指定的 pojo 的属性名一致，才能映射成功
- reusltMap：可以通过 resultMap 完成一些高级映射。如果查询到的列名和映射的pojo的属性名不一致时，通过resultMap设置列名和属性名之间的对应关系（映射关系）可以完成映射。 

	高级映射 将关联查询的列映射到一个pojo属性中。（一对一）；
			将关联查询的列映射到一个List中。（一对多）

