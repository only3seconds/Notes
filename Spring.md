# Spring 

Spring 是一站式框架，在 java ee 三层结构中，每一层都提供不同的解决技术：

- Web 层：Spring MVC
- Service 层：Spring IOC
- Dao 层：Spring 的 jdbcTemplate

## 一. IOC (Inversion of Control)

### 1. 理解

- 正常的情况下，比如有一个类，在类里面有方法（不是静态的方法），调用类里面的方法，创建类的对象，使用对象调用方法，创建类对象的过程，需要new出来对象;

- 通过控制反转，对象的创建不是通过 new 方式实现的，而是交给 Spring 配置来创建类对象并控制对象的生命周期和对象间的关系；

- IOC 的意思是控件反转也就是由容器控制程序之间的关系，把控件权交给了外部容器，之前的写法，由程序代码直接操控，而现在控制权从应用代码中转到了外部容器，所以称之为控制反转。换句话说之前用 new 的方式获取对象，现在由 spring 给你这个对象。至于怎么给你就是DI（依赖注入）了。

### 2. IOC 两种操作方式

- ioc的配置文件方式;
- ioc的注解方式.

### 3. IOC 优点

- 解耦合，开发更方便组织分工；
- 高层不依赖于底层（依赖倒置）；
- 使应用更容易测试。

### 4. 依赖注入（DI）

所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的控制。DI依赖注入，向类里面属性注入值 ，依赖注入不能单独存在，需要在 IOC 基础上完成操作。

### 5. Spring bean 的生命周期

## 二. AOP (Aspect Oriented Programming）

### 1. 理解

- AOP 称为面向切面编程，面向切面编程（aop）是对面向对象编程（oop）的补充

- 在程序开发中主要用来解决一些系统层面上的问题，比如日志，事务，权限等待，Struts2的拦截器设计就是基于AOP的思想，是个比较经典的例子。

### 2. 实现原理

- 动态代理（利用反射和动态编译将代理模式变成动态的）

### 3. 优点

- 各个步骤之间的良好隔离性
- 源代码无关性
- 松耦合
- 易扩展
- 代码复用


## 三. Spring MVC

### 1. 工作流程

![](https://img-blog.csdnimg.cn/20190328110748721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

[快速记忆](https://blog.csdn.net/qq_32575047/article/details/80416015)

## 四. Spring & Spring MVC & Spring Boot

Spring 是一个轻量级的 java 开发框架，它的核心是控制反转（IOC）和面向切面（AOP）,针对于开发的WEB层(springMvc)、业务层(Ioc)、持久层(jdbcTemplate)等都提供了多种配置解决方案；

Spring MVC 是spring基础之上的一个MVC框架，主要处理web开发的路径映射和视图渲染，属于spring框架中WEB层开发的一部分；

spring boot 的理念是默认大于配置，它集成了快速开发的spring多个插件，同时自动过滤不需要配置的多余的插件，简化了项目的开发配置流程。
Spring boot 具有以下特性：

- 快速开发单个微服务。

- 无需再像 Spring 那样搞一堆繁琐的 xml 文件的配置；可以自动配置 Spring。将 bean 注入改为使用注解注入的方式(@Autowire)，并将多个 xml、properties 配置浓缩在一个 appliaction.yml 配置文件中。

- 整合常用依赖（开发库，例如spring-webmvc、jackson-json、validation-api和tomcat等），提供的 POM 可以简化 Maven 的配置。当我们引入核心依赖时，SpringBoot 会自动引入其他依赖。

- 内嵌了 Tomcat 等容器，可以直接跑起来，用不着再做部署工作了。



# Hibernate

	hibernate 是一个数据持久层的框架,只管怎么去访问及操作数据库的数据.
	
# Mybatis


### Hibernate vs Mybatis

- Hibernate 是全自动的，Mybatis 是半自动的；自动指的是 hibernate 具有良好的映射机制，不需要手动写 sql 和结果映射，半自动指的是 mybatis 在做高级查询时需要手写 sql 和结果查询。


- 在 sql 优化方面，Hibernate 会把表中的所有列查询出来，Mybatis 是自己指定需要查询哪些列，所以在 sql 优化方面 Mybatis 比 Hibetnate 更灵活。当然，hibernate允许自己指定查什么，但是就会破坏hibernate开发的简洁性。

- Hibernate自带日志统计，而 Mybatis需要依靠log4j来实现。

- Hibernate 强大的数据库无关性，只需在xml文件中配置驱动和方言，就可以实现和不同数据库的交互。而mybatis的所有sql和所使用的数据库有直接关系，一旦变更数据库，那么你的sql语句基本就是要修改了。

- mybatis比hibernate更加适合开发复杂查询的项目，且更容易上手

Mybatis 优势：

- MyBatis可以进行更为细致的SQL优化，可以减少查询字段；

- MyBatis容易掌握，而Hibernate门槛较高。

Hibernate 优势：

- Hibernate的DAO层开发比MyBatis简单，Mybatis需要维护SQL和结果映射。
- Hibernate对对象的维护要比MyBatis好，对增删改查的对象的维护要方便。
- Hibernate数据库移植性很好，MyBatis的数据库移植性不好，不同的数据库需要写不同SQL。
- Hibernate有更好的二级缓存机制，可以使用第三方缓存。MyBatis本身提供的缓存机制不佳。


