---
title: MAC安装JDK及环境变量配置
date: 2018-09-19 17:45:36
password:
tags:
top:
categories:
photos:
---

# 安装JDK #

1. 访问[Oracle官网](http://www.oracle.com)，浏览到首页的底部菜单 ,然后按下图提示操作:     
    
![](http://images2015.cnblogs.com/blog/877813/201601/877813-20160113135011647-96008710.png)

<!--more-->

2. 点击“JDK DOWNLOAD”按钮：

![](http://images2015.cnblogs.com/blog/877813/201601/877813-20160113135051835-861363050.png)

3. 选择“Accept Lisence Agreement”同意协议:

![](http://images2015.cnblogs.com/blog/877813/201601/877813-20160113135219866-685515947.png)

4. 点击Mac OS X x64后面的下载链接：
![](http://images2015.cnblogs.com/blog/877813/201601/877813-20160113135306585-54248836.png)

下载完成后点击安装包，按提示即可完成安装。

5. 打开Finder,可以在下图所示的路径中找到安装好的jdk 1.8.0_40.jdk:(建议直接使用`/Library/Java`前往,因为我居然找不到该目录)

![](http://images2015.cnblogs.com/blog/877813/201604/877813-20160416153110191-1153954158.png)

6. 其中Contents下的Home文件夹，是该JDK的根目录。

![](http://images2015.cnblogs.com/blog/877813/201604/877813-20160416153449941-591903183.png)

>其中：

> `bin目录`下存放JDK用于开发的一些终端命令工具。常见的工具如：
“javac”的作用是将java源文件编译为class文件(即自解码文件)；
“java”命令的作用是运行class文件。
 
> `db目录`下是java开发的一个开源的关系型数据库；
 
> `include目录`下是一些C语言的头文件；
 
> `jre目录`下JDK所依赖的java运行时；
 
> `lib目录`下存放JDK开发工具所依赖的一些库文件；
 
> `man目录`下存放JDK开发工具的说明文档。

7. 安装好JDK后需要配置JDK的环境变量，方法请参考：[Mac下如何配置环境变量](http://www.cnblogs.com/quickcodes/p/5398709.html)，方法有部分不对，下面以配置JDK的环境变量为例，示范如何配置环境变量，后续在安装Android、Maven、MongoDB等时都会需要配置相应的环境变量。

# 配置JDK的环境变量 #


1. 打开终端。（cd /Library/Java/JavaVirtualMachines/jdk-10.0.2.jdk）

2. 如果你是第一次配置环境变量，可以使用 `touch ~/.bash_profile` 创建一个 `.bash_profile` 的隐藏配置文件(如果你是为编辑已存在的配置文件，则使用 `open -e ~/.bash_profile` 命令)：

![](http://images2015.cnblogs.com/blog/877813/201604/877813-20160416164957082-1418925235.png)

3. 输入 `open -e ~/.bash_profile` 命令：

![](http://images2015.cnblogs.com/blog/877813/201604/877813-20160416165627113-2136824168.png)

> ps:如果没有权限 就使用`sudo touch ~/.bash_profile`,而编辑的时候我是用sublime打开的。

4. 输入如下配置：

```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-10.0.2.jdk/Contents/Home
PATH=$JAVA_HOME/bin:$PATH:.
CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
export JAVA_HOME
export PATH
export CLASSPATH
```

然后保存关闭该窗口。（我的是jdk-10.0.2.jdk）

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv5of7a721j30zk0mwacf.jpg)

5.使用"source ~/.bash_profile"使配置生效 然后输入”java -version”，如果看到jdk版本为10.0.2则说明配置已经生效：

6.输入 echo $JAVA_HOME 显示刚才配置的路径

![](https://ws1.sinaimg.cn/large/0069RVTdgy1fv5jtc8u1ej30fu0a6mxk.jpg)




![](http://images2015.cnblogs.com/blog/877813/201604/877813-20160416170843160-212359895.png)
