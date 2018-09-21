---
title: jenkins 安装
date: 2018-09-19 17:39:58
password:
tags:
top:
categories:
photos:
---
## 一、简述 ##

1. `Jenkins` 本身是用 `java` 语言开发的，所以安装 `Jenkins` 之前，要保证你的电脑有`jdk`，如果没有，请到参考[《MAC安装JDK及环境变量配置》]() ，另外建议git、ant、maven、gradle、groovy等工具也一并安装好，方便与这些构建工具集成。
 
## 二、Jenkins的安装 ##

### 1.1 jenkins.pkg ###
进入[Jenkins的官网](https://jenkins.io/index.html) ,然后下载并安装Jenkins

> 也可以直接点击下载[最新版本](http://mirrors.jenkins-ci.org/osx/latest)
 
 ***1.*** 点击链接进入官网
 
 ![](http://upload-images.jianshu.io/upload_images/3008243-84988033422d9c16.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ***2.*** 点击download
 
 ![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvezxiaiajj31ga11egsn.jpg) 
 
 ***3.*** 我选择的是Mac OS X下载并安装
 
 ***4.*** 点击直接安装即可，在下图中，选择自定义，取消 `start at boot as“jenkins”`，一般不需要自启动
 ![](http://upload-images.jianshu.io/upload_images/3008243-26bfb9a02d0f5d07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
### 1.2 命令行安装 ###
 
命令行使用 `homebrew` 安装

```
$ brew search jenkins
jenkins
$ brew install jenkins

```

启动jenkins    

`$ jenkins`

 
### 2. 进入页面 ###

安装完成后，会直接跳转到如下界面，如果没有跳转或者跳转失败，可通过执行如下命令 `open /Applications/Jenkins/jenkins.war` 进入到该界面
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvfywcg9ewj30ri09tabf.jpg)
 
 
> 如果打开没有反应，启动不了，就在浏览器直接打开：[http://localhost:8080/](http://localhost:8080/) ，就可以进入到上面界面了。
 
 
>> 发现还是打不开， cd进入到jenkins.war包所在目录,执行以下命令:

```
java -jar jenkins.war --httpPort=8080
```

> httpPort指的就是Jenkins所使用的http端口,这里指定8080(默认的就是这个),可根据具体情况来修改。待Jenkins启动后,在浏览器页面输入以下地址:       
[http://localhost:8080](http://localhost:8080)

> 这个时候可能会报一个错误。如果出现了上面的问题，原因可能是Java环境有问题，重新安装JDK环境即可。
 
 
 ***6.*** 观察图中的路径，将地址中的文件打开，或者打开你的终端，执行`cat`命令（注意：该命令仅在安装Jenkins的过程中执行才有效；安装成功后，再在终端中执行是无效的）：    

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvfyxf1rl8j30vg054wga.jpg)     
 
 ***7.*** 然后将我们得到的 `password` 输入到 `Administrator password` 中，即可进入如下界面, 选第一个,接着安装一些建议的插件，安装过程中，有的插件可能会安装失败，可以跳过，之后需要对应的插件时，再重新安装即可，具体后面会讲到：
 
 ![](http://upload-images.jianshu.io/upload_images/3008243-cf1635160604dc56?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ***8.*** 插件安装完成后，可能会卡在如下界面，不会自动跳转，刷新下界面即可：
 
 ![](http://upload-images.jianshu.io/upload_images/3008243-03f427bb5a3553da?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ***9.*** 在刷新后的界面中注册，输入用户名和密码，如下：
 
 ![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvezybgexnj31iw1byado.jpg)
 
 ***10.*** 注册成功后，点击”Start using Jenkins”
 
 ![](http://upload-images.jianshu.io/upload_images/3008243-bb59d4a3085e0e4c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 

## 三、Jenkins的使用 ##

### 安装常用插件 ###


* Xcode
* Keychains and provisioning profiles management

.... 

如下图，进行管理:      
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvezz4ovfnj31kw0nxn2d.jpg)
 
### 项目实战 ###
#### 1.新建一个自由风格的软件项目 ####

#### 2.配置git地址 ####
![](http://upload-images.jianshu.io/upload_images/80578-98eb62f65d91d0b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 立即构建下，提示构建成功 ###
![](http://upload-images.jianshu.io/upload_images/80578-747f024d24255bab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么构建后的项目在哪里呢？
通过系统管理->系统设置->主目录 点击高级，可以查看到
![](http://upload-images.jianshu.io/upload_images/80578-a7d73b8be1152ced.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 其他 ##

### 卸载 ###

Jenkins的安装方式不同（[Mac 安装 Jenkins](http://www.cnblogs.com/ihojin/p/mac-install-jenkins.html)），卸载方法也不同

#### 一.通过安装包安装的卸载方式 ####

* 方法1：打开终端，执行命令 /Library/Application Support/Jenkins/Uninstall.command

* 方法2：Finder 前往文件夹：`/Library/Application Support/Jenkins`，双击 `Uninstall.command`

#### 二.通过 Homebrew 安装的卸载方式 ####

* 打开终端 `brew uninstall jenkins`

完成之后再次打开http://localhost:8080  访问不了，说明卸载成功

