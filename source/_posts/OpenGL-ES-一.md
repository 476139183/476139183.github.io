---
title: OpenGL ES (一)
date: 2018-09-06 15:34:08
password:
tags:
top:
categories:
photos: 
  - http://d-pic-image.yesky.com/1080x-/uploadImages/2017/014/00/FP43CP909564.jpg

---


<!--
<div align=center>![1](http://d-pic-image.yesky.com/1080x-/uploadImages/2017/014/00/FP43CP909564.jpg)<div>
-->

最近，斗破苍穹电视剧上映的，里面的梗就不用多提， `三十年河东，三十年河西，莫欺少年穷` 指不定口碑低开高走呢。        
人生也是一样, 无法预料,学历代表你的过去，能力代表你的现在，学习代表你的将来.         
所以学无止境，需精益求精，不断的学习，才能蹒跚前行。    

<!----more--->

## 概述 ##



> OpenGL ES （Open Graphics Library for Embedded Systems）是访问类似 iPhone 和 iPad 的现代嵌入式系统的 2D 和 3D 图形加速硬件的标准。
把程序提供的几何数据转换为屏幕上的图像的过程叫做渲染。
GPU 控制的缓存是高效渲染的关键。容纳几何数据的缓存定义了要渲染的点、线段和三角形。
OpenGL ES 3D 的默认坐标系、顶点和矢量为几何数据的描述提供了数学基础。
渲染的结果通常保存在帧缓存中。有两个特别的帧缓存，前帧缓存和后帧缓存，它们控制着屏幕像素的最终颜色。
OpenGL ES 的上下文保存了 OpenGL ES 的状态信息，包括用于提供渲染数据的缓存地址和用于接收渲染结果的缓存地址。

OpenGL ES 是 OpenGL 的子集，它移除了 OpenGL 中冗余的函数，使其更易学也更容易在移动图形硬件中实现。

OpenGL ES 是基于 C 语言的 API ，所以可以无缝移植到 Objective—C 中，然后通过创建上下文来接收命令和操纵帧缓存。

在 iOS 中，使用 OpenGL ES 时，还可以使用 GLKit 框架中的 GLKView 将 OpenGL ES 绘制的内容渲染到屏幕上，并且可以使用 GLKViewController 来管理 GLKView 视图。另外，还可以使用 CAEAGLLayer 图层将动画与视图相结合。

> 但是，需要注意的是，当应用处于后台状态时，不能调用 _OpenGL ES_ 中的函数，否则应用便会被终止，而且 OpenGL ES 中的上下文也不支持在同一时刻被不同的线程访问。

作为新手入门，我打算先了解一下 OpenGL 的一些基本概念

## 坐标系 ##

弄清楚坐标系很重要，它可以把几何图像和代数方程关联起来，是图像绘制的 布局的开始。

### iOS 手机坐标系 ###

二维坐标系，原点在左上角，x 轴向右，y 轴向下，x y 取值范围为屏幕分辨率：

![手机坐标](http://farm6.staticflickr.com/5192/7420067916_889152557b.jpg)

### OpenGL 坐标系 ###

三维坐标系，原点在中间，x 轴向右，y 轴向上，z 轴朝向我们，x y z 取值范围都是 [-1, 1]：

![OpenGL坐标](https://images2017.cnblogs.com/blog/1207934/201707/1207934-20170727100049437-925043765.png)

### 纹理（texture） 坐标 ###

为了能够把纹理映射到三角形，我们需要指定三角形的每一个顶点各自对应纹理的哪个部分。这样每一个顶点就会关联着一个纹理坐标，用来标明该从纹理图像的那一个人部分采样（采集片段颜色）。之后再图形的其他片段上进行片段插值。    

纹理坐标是二维坐标系，使用纹理坐标获取纹理颜色叫做采样，其原点在左下角，s（x）轴向右，t（y）轴向上，x y 取值范围都是 [0, 1]。      
下面的图片展示了我们是如何把纹理坐标映射到三角形上：

![纹理坐标](http://7xkc7a.com1.z0.glb.clouddn.com/20170116148453793035367.jpg?imageView2/0/format/jpg)


## OpenGL 组成 ##

 OpengGL 主要包括三个要素： 原件（Primitives） 缓冲区（Buffers） 光栅化（Rasterisation）

### 原件 ###

原件 包括 点, 线 三角形。有的OpenGL版本还包括四边形，但在OpengGL ES中只有这三种原件

OpenGL 绘制的都是图形，包括形状和填充，基本形状是三角形。
每个形状都有顶点，Vertix，顶点的序列就是一个图形。
图形有所谓的正反面，如果我们看向一个图形，它的顶点序列是逆时针方向，那我们看到的就是正面。


### 缓存区 ###

缓冲区包括三个概念: `帧缓冲区`(Frame Buffers)   `渲染缓冲区`(Render Buffers)  `缓冲区对象`(Buffer Objects)
 
 *  帧缓冲区 `是GL储存渲染结果的地方,为GPU指定存储渲染结果的区域`，它可以包含多个图像，也就是包含多个渲染缓冲区。更通俗点，可以理解成存储屏幕上最终显示的一帧画面的区域.      
  
 *  渲染缓冲区 也就是一个图像，也被称作颜色缓冲区.因为它本质上是存储要显示的颜色.例如颜色图像，深度图像，模版图像（决定每一个位置是可以的掩膜）等。
   
 *  缓冲区对象 就是程序员提供给OpenGL的数据，分为结构类和索引类。前者被称为 “数组缓冲区对象” 或者 “顶点缓冲对象”（“Array Buffer Object or Vertex Buffer Object”），即用来描述模型的数组，如顶点数组，纹理数组等；后者被称为 “索引缓冲区对象”（“Index Buffer Object”），是对上述数组的索引。



### 光栅化 ###
![光栅化](http://7xkc7a.com1.z0.glb.clouddn.com/20170112706113F85123B-6006-4633-9D8C-C4C4DB4BA3AC.png)

光栅化的过程就是根据缓冲区对象里提供的数据 经过渲染 从3D模型得到2D模型的过程。得到的2D图像会根据帧缓冲区的配置来决定是直接送到屏幕显示 或是 做别的用处。      

`在光栅化阶段，基本图元被转换为供片段着色器使用的片段（Fragment`），Fragment 表示可以被渲染到屏幕上的像素，它包含位置，颜色，纹理坐标等信息，这些值是由图元的顶点信息进行插值计算得到的。这些片元接着被送到片元着色器中处理。这是从顶点数据到可渲染在显示设备上的像素的质变过程。

在片段着色器运行之前会执行裁切（Clipping）。裁切会丢弃超出你的视图以外的所有像素，用来提升执行效率。

## 流水线 ##

从2.0版本开始，OpenGL支持可变的流水线，也就是说，程序员 可以通过 Shader（一种程序，一段包含着色信息的源代码字符串）来控制GPU渲染的过程。

Shader 也叫着色器，用来描述如何绘制（渲染）， GLSL 是 OpenGL 的 编程语言，全称全称就叫 OpenGL Shader Language。

 Shader 分为 `Vertex Shader` 和 `Fragment Shader`。这两种Shader是成对出现的，GPU先执行前者，后执行后者。

### Vertex Shader（顶点着色器） ###

Vertex Shader 对于3D模型网格的每一个顶点执行一次，主要是确定改顶点的最终位置。Vertex Shader 还会准备并输出一些变量，传给 Fragment Shader 使用。 简单点就是 控制顶点的绘制，指定坐标、变换等。

### Fragment Shader（片元着色器） ###

![](http://7xkc7a.com1.z0.glb.clouddn.com/20170112997054573CDB0-956C-4A00-837F-6BDA9BA4F550.png)

Fragment Shader(也叫片段着色器) 对于最终得到的2D图像的每一个像素处理一次。3D物体的表面最终显示成什么样将有它决定。例如为模型的可见表面添加纹理，处理光照 阴影的影响等等，都在这里做。 Fragment Shader 的输出是一个 RGBA 格式的像素颜色值。 简单点就是 控制形状内区域渲染，纹理填充内容。

### 着色器的使用流程 ###

着色器源代码和OpenGL源代码不是一起编译的，所以要特别强调我刚才说的“着色器是一段包含着色信息的源代码字符串”。所以，OpenGL源代码肯定是和工程一起编译的，但是着色器源代码是在运行期编译的。你可能会问，着色器的源代码是一个字符串怎么编译呢？所以OpenGL ES提供了一套运行期动态编译的流程：

（1）创建着色器：glCreateShader

（2）指定着色器源代码字符串：glShaderSource

（3）编译着色器：glCompileShader

（4）创建着色器可执行程序：glCompileShader

（5）向可执行程序中添加着色器：glAttachShader

（6）链接可执行程序：glLinkProgram

## OpenGL 有关概念 ##

OpenGL 提供一组API，各显卡制造商和系统制造商来实现这组API。       

EGL 是另一组API，主要是系统制造商实现，它负责OpenGL和原生窗口系统之间的接口，用于把OpenGL 渲染的结果显示在屏幕上。      

EGAL 是Apple公司对EGL做了修改，自己实现的一套屏幕显示接口，一般只用于iOS和 OS X 系统中。

关系如下图所示


![关系图](https://ws2.sinaimg.cn/large/006tNbRwgy1fv0xlv41zej30sg0lcwjc.jpg)


参考文章：      
https://www.2cto.com/kf/201706/647894.html       
https://blog.csdn.net/wangyuchun_799/article/details/7736928       
https://www.jianshu.com/p/ce287a5460cd