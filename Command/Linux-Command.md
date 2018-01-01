- cp -r ../Downloads/apache-tomcat-8.5.20 ./projectTest
将上级目录中的../Downloads/apache-tomcat-8.5.20文件夹复制到当前目录，命名为projectTest

- rm -rf docs examples host-manager manager
删除当前目录下的 docs examples host-manager manager 文件/文件夹

- ps -a | grep tomcat
ps是进程查看命令； -a显示终端所有进程，包括其他用户进程； “|”是管道，意思是前面ps的输出做为后面的输入； grep（Global Regular Expression Print)命令是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来然后grep；tomcat是在所有进程里查找与字符tomcat有关的进程，并显示出来。

- kill  -9 pid
	必杀令；9：SIGKILL，can't be caught or ignored
	
- pkill -f tomcat
pkill 终止进程； -f The pattern is normally only matched against the process name. When -f is set, the full command line is used.

- tail -f catalina.out
显示 catalina.out 文件最新追加的内容

- mv xxl-job-admin-1.9.0-SNAPSHOT.war xxl-job-admin.war 
mv命令用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录中。