---
title: OpenGL EL (二)
date: 2018-09-18 09:19:50
password:
tags:
 - OpenGL
 - 进阶
top:
categories:
 - iOS
photos:
  - https://pic4.zhimg.com/v2-7df2c429b9a823419871ff2f910c4b55_1200x500.jpg
---

Open GL 是不可能再学了，这辈子都不可能，学又学不会，只能随便贴一些图凑才能维持的了生活。幸好百度里面有很多大佬，个个都是人才，说话又好听，还乐于助人，写了很多教程，我超喜欢抄袭百度里面的东西。

<!--more-->

是的，很遗憾，我还是咬牙继续看下去。

![](https://ws3.sinaimg.cn/large/006tNc79gy1fvpgwbrpcsg30f709tqv7.gif)

退后，我要开始装逼了。。。

## 前沿 ##
[上一篇 OpenGL ES (一) ](https://476139183.github.io/2018/09/06/OpenGL-ES-一/) 提到了一些基本的概念，这一次不出意外，还是继续了解一些基本的 Api 或者 使用流程

## 正文 ##
和任何程序一样，OpenGL程序需要输入，然后经过渲染管线，即一系列的着色器（着色器贯穿本书的始终），最后得到一个二维图像（像素矩阵），见下图。

![](https://images2015.cnblogs.com/blog/339551/201604/339551-20160407230518609-1222931067.jpg)

所以在调用OpenGL API进行绘制图像之前，先将所需数据加载到显存中，以便于OpenGL在绘制时对其进行相关处理。填充后的代码如下：

```
 1 #include <iostream>
 2 #include "StdAfx.h"
 3 
 4 GLuint Buffer_ID;
 5 const int BUFFER_NUMBER = 1;
 6 
 7 GLuint VAO_ID;
 8 GLuint VAO_NUMBER = 1;
 9 
10 const int VERTICES_NUMBER = 6;
11 const int vPosition = 0;
12 
13 void Initialize()
14 {
15     //---------------------准备数据-------------------------------
16     GLfloat vertices[VERTICES_NUMBER][2] = 
17     {
18         { -0.90, -0.90 },
19         {  0.85, -0.90 },
20         { -0.90,  0.85 },
21 
22         {  0.90, -0.85 },
23         {  0.90,  0.90 },
24         { -0.85,  0.90 }
25     };
26 
27     // 生成缓存对象
28     glGenBuffers(BUFFER_NUMBER, &Buffer_ID);
29 
30     // 绑定缓存对象
31     glBindBuffer(GL_ARRAY_BUFFER, Buffer_ID);
32 
33     // 填入数据
34     glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
35 
36     //-------------------设置顶点数据属性------------------------------
37     // 生成顶点数组对象
38     glGenVertexArrays(VAO_NUMBER, &VAO_ID);
39 
40     // 绑定顶点数组对象
41     glBindVertexArray(VAO_ID);
42 
43     // 设置顶点属性
44     glVertexAttribPointer(vPosition, 2, GL_FLOAT, GL_FALSE, 0, BUFFER_OFFSET(0));
45     glEnableVertexAttribArray(vPosition);
46 }
47 
48 void display()
49 {
50     glClear(GL_COLOR_BUFFER_BIT);
51 
52     glBindVertexArray(VAO_ID);
53     glDrawArrays(GL_TRIANGLES, 0, VERTICES_NUMBER);
54 
55     glFlush();
56 }
57 
58 int main(int argc, char **argv)
59 {
60     glutInit(&argc, argv);
61     glutInitDisplayMode(GLUT_RGBA);
62     glutInitWindowSize(512, 512);
63     glutInitContextVersion(3, 3);
64     glutInitContextProfile(GLUT_CORE_PROFILE);
65     glutCreateWindow(argv[0]);
66 
67     glewExperimental = TRUE;
68     if (glewInit())
69     {
70           std::cerr << "Unable to initialize GLEW... Exiting..." << std::endl;
71         std::exit(EXIT_FAILURE);
72     }
73 
74     Initialize();
75     glutDisplayFunc(display);
76     glutMainLoop();
 }
```

## 数据输入步骤 ##

任何系统都有输入输出（I/O）系统，如计算机硬件系统中有输入设备和输出设备；每一个编程语言都有自己的输入命令（类）和输出命令（类）；对于一个算法来说，也有其输入和输出。

对于我们图形绘制系统来说，自然也少不了输入和输出。由于数据的输入只需要执行一次就可以，故写在Initialize函数中，并在main函数是执行。

本例要绘制两个三角形，输入的数据自然就是两个三角形的顶点数据。由于绘制的是平面三角形，我们可以不指定z方向的坐标值（深度值）。16~25行的二维顶点数组是存放在内存中的，图形绘制是在显卡中执行的，所以需要将这些数据加载到显存中。这里出现了OpenGL编程中第一个重要的概念—— `缓存对象（Buffer Object）`。顾名思义，这一对象主要就是用来存放数据的，在这里，我们使用缓存来存放顶点数据。下面，我们来看看程序中是怎么使用缓存对象来加载顶点数据的。

加载顶点数据到显存用了3条OpenGL API来实现数据的加载。

### 声明一个缓存对象ID ###
I：使用 `glGenBuffer` 声明一个缓存对象ID。编程语言中通过变量的方式来标识内存中的数据；操作系统中通过各种ID来感知各个实体，如进程标识符PID来标识进程，线程标识符TID来标识线程。OpenGL也是通过ID来标识各种对象。由于这里只要使用一个缓存对象，所以只要生成一个缓存ID即可，但要注意，这条指令可以生成多个缓存对象。

### 绑定其中一个缓存 ###

II：使用glBindBuffer来绑定其中一个缓存。刚才已经提到，缓存对象可以有多个，那OpenGL怎么知道要当前操作的是哪个缓存对象呢？这就需要使用glBindBuffer命令——这个命令的作用就是激活（Activate）其中一个缓存对象。参数很简单，就是刚才生成的缓存ID。

### 分配内存并拷贝数据到显存 ###

III：使用glBufferData来分配内存并拷贝数据到显存。这一步是我们最终目的——将数据从内存拷贝至显存。这个函数和C语言中内存拷贝memcpy很类似，函数签名为：

```
void glBufferData(GLenum target, GLsizeiptr size, const GLvoid *data, GLenum usage);
```

> target　　—— 刚才绑定（激活）的缓存对象，这可以看做memcpy的目的地址；

> size　　　—— 这就是数据的大小，这和memcpy中的数据大小是一样的；

> data　　 —— 源数据的指针，这和memcpy中的源数据指针是一样的；

> usage　　—— 这个参数指定这个数据的用法，主要是为了优化OpenGL的内存管理——根据使用方法确定最优显存分配方案。 

通过使用上述三条OpenGL API，我们就完成了数据从内存加载到缓存的功能。到此为止，故事还没有结束，OpenGL在获取顶点数据时并不知道缓存对象中的数据如何解析，所以需要告诉OpenGL，刚才上传的数据的格式是怎么样的。这就引入了第二个对象——顶点数组对象及顶点属性的概念。顶点数组对象就是用来描述刚才上传的顶点数据特征的一个对象，下面就继续来分析与顶点数组对象的相关API。


I：使用glGenVertexArray声明一个顶点数组对象ID。这和缓存对象ID是一样的，都是为了便于OpenGL的组织管理；

II：使用glBindVertexArray来激活其中的一个顶点数组对象，和缓存对象也是类似的；

III：使用glVertexAttribPointer接口来填充当前绑定的顶点数组对象。这个函数的功能和缓存对象的glBindBuffer命令是一样的，只是对于缓存对象来说，只要拷贝一下数据就可以了，而这里需要填充顶点属性数据（就像填充一个结构体一样）。这个函数的参数比较多，其函数签名为：

```
void glVertexAttribPointer(GLuint index, GLsize size, GLint size, GLenum type, GLboolean normalized, GLsizei stribe, const GLvoid *pointer);
```

>index　　  　　——这是指定在该顶点在着色器中的属性。

> size　　   　　  ——该参数指定了每个顶点有几个分量，本例中二维顶点，故设为2；

> type　　  　　  ——该参数指定了顶点中分量的数据类型，这里顶点的坐标分量是浮点型数据，故设为GL_FLOAT；

> normalized　　——该参数表示顶点存储前是否需要进行归一化；

> stride　　　　  ——该参数指定两个顶点数据之间间隔的字节数，在本例中，顶点是连续存储的，故设为0；

> pointer　　　　——顶点数据在缓存对象中起始地址，在本例中，因为缓存对象中只存放了一个顶点数组，所以这一值设为0。

IV: 使用glEnableVertexAttribArray来启用与index索引相关联的顶点数组。虽然前面设置了顶点数组属性，但如果没有启用的话，数据依然无法被OpenGL拿到。

2. 图形绘制步骤

数据及其格式设置后之后，就是根据这一数据进行图形的绘制。这部分代码是写在display函数中的，这一函数可能会调用多次。在这个显示函数中，最重要的一个OpenGL API就是glDrawArray函数——绘制基本图形，其函数签名如下：

void glDrawArray(GLenum mode, GLint first, GLsizei count);

mode　　——指定你要绘制的图元类型，比如三角形是GL_TRIANGLES，直线就是GL_LINES，闭合的直线就是GL_LINE_LOOP，顶点就是GL_POINTS。本例中要绘制三角形，故设为GL_TRIANGLES。

first　　　——指定绘制图形时的起始顶点，本例中从第0个顶点开始；

count　　——要绘制的顶点数，本例中设置为6。

给这个函数设置不同的值，将出现不同的效果——可以使用不同的顶点来绘制不同的图形。

剩下的，三个接口：

glClear(GLbitfield mask);

清空指定的缓存数据。每一次新的绘制，当然需要将上一次绘制过程中产生的一些数据给清空，以防止其对后一次绘制产生影响。在OpenGL中有三种缓存数据，分别是颜色缓存，深度缓存和模板缓存。其中深度缓存只有在三维的情形中才用到。本例中清空了颜色缓存。

glFlush()；

这个接口是一个同步接口——等待绘制完成再往下执行。这里需要说明的是，OpenGL采用的是客户机-服务器模式运作的——我们的应用程序就是客户机，显卡就是服务器。每一次执行OpenGL API相当于给显卡发送一条命令，一般情况下，这些命令是以异步的方式执行的。如果我们应用程序需要等显卡命令执行完毕才能往下执行，就需要调用这个函数。

最后一个，glBindVertexArray——绑定操作对象，即glDrawArray绘制的是当前绑定的顶点数组。在本例中（只限本例）是可以不调用的，因为在Initialize函数中已经调用过了，并且display函数中没有其他的绑定。

至此，我们运行程序，应该能够看到绘制出来的是两个白色的三角形。

## 第三步：添加着色器 ##

我们先来看看OpenGL中的绘制管线，如下图所示：




参考文章:
[OpenGL学习之路](https://www.cnblogs.com/android-blogs/p/5454698.html)