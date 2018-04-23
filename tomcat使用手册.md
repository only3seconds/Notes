# tomcat使用手册
```
Tomcat是一种web服务器，主要用来部署web应用
```

## **启动/关闭**

### 1. 在终端运行tomcat
apache-tomcat-8.5.20文件夹下bin目录

- ./catalina.sh run 启动当前目录下的tomcat，并将日志输出在当前控制台
	control + c 关闭tomcat
- ./startup.sh  启动当前目录下的tomcat，日志信息不在控制台输出，可在tomcat/logs/catalina.out查看启动日志
	./shutdown.sh 关闭当前目录下的tomcat
	
### 2. 在IDEA里启动tomcat

## **查看tomcat是否启动成功**
- ps -a | grep tomcat 查看tomcat进程是否在运行

## **部署一个web项目**
- 将项目打war包
- 将war包复制到apache-tomcat-8.5.20文件夹的webapps目录下，即部署成功

## **部署多个web项目**
部署两个tomcat,复制apache-tomcat-8.5.20文件夹，修改其中一个tomcat的端口，在tomcat/conf/server.xml下修改3个端口。

