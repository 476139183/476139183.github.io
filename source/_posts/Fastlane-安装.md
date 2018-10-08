---
title: Fastlane 安装 和 发布到蒲公英
date: 2018-09-18 14:53:54
password:
tags:
  - 自动化
  - Fastlane
top:
categories:
  - 工具
photos:
  - https://github.com/fastlane/fastlane/raw/master/fastlane/assets/fastlane_text.png
---

# 前沿 #

>在使用Fastlane打包之前，你的项目必须是正确的。我的意思是说，你得用原生的方式成功Archive，并可以upload你的测试项目！否则会导致Fastlane打包失败
<!--more-->
当你看到这篇文章的时候，说明你已经厌烦不停的打包等一系列`没有技术含量的、乏味`的工作,然后会去寻找偷懒的方法，于是找到了 `Fastlane` ，因为 [Fastlane](https://github.com/fastlane/fastlane) 在网上有很多资料，也介绍了蛮多，这里只是简单的点出一些重点。        

Fastlane命令是一个流程控制的命令行工具(CLI)，通过内部集成action和第三方的action完成一系列控制流程。运行Fastlane命令行工具，会读取当前目录或者 `./fastlane` 目录下的 `Fastfile` 配置文件。

> 在Fastfile中：

>> `action` :  Fastlane中的每一条命令都是一个扩展(action)，下面提到的deliver，sigh之类的工具本身是CLI，但是在Fastlane中内嵌了对他们支持的action         
>> `lane` : Fastlane中流程的合集，每一个动作即可以是action，也可以是其他的lane。语法和ruby中的rake非常像

# Fastlane组件 #

`不是一个人的王者，而是团队的荣誉`, Fastlane 是一套基于 Ruby 的工具集合，它包括了诸多工具:

* 测试
 * `scan` => 自动运行测试工具，并且可以生成漂亮的HTML报告 
* 证书，配置文件
 * `cert` => 自动创建管理iOS代码签名证书
 * `sigh` => 是用来创建、更新、下载、修复Provisioning Profile的工具
 * `pem` => 自动生成、更新推送配置文件
 * `match` => 一个新的证书和配置文件管理工具,它会把所有需要用到的证书传到git私有库上，任何需要配置的机器直接用match同步回来就不用管证书问题了，小团队福音啊！
* 截图
 * `snapshot` => 用Xcode7推出的UI test功能实现自动化截图
 * `frameit` => 可以把截的图片自动套上一层手机的物理边框
* 编译
 * `gym` => Fastlane家族的自动化编译工具，和其他工具配合的非常默契
* 发布
 * produce => 如果你的产品还没在iTunes Connect(iTC)或者Apple Developer Center(ADC)建立，produce可以自动帮你完成这些工作
 * deliver => 自动上传截图，APP的元数据，二进制(ipa)文件到iTunes Connect
* TestFlight管理
 * `pilot` => 管理TestFlight的测试用户，上传二进制文件
 * `boarding` => 建立一个添加测试用户界面，发给测试者，可自行添加邮件地址，并同步到iTC

> 更多工具可以去查看[官方文档](https://docs.fastlane.tools)

# 操作流程 #
### 环境 ###

#### 检查 Xcode CLT 是否安装 ####

```
xcode-select --install
```
如果终端提示
```
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
```
说明已经安装成功

#### Ruby ####

确保已经安装好 `ruby`、`rubygems`、`bundler`，Fastlane要求Ruby版本，当前最新版本要求ruby版本>=2.1。使用以下命令查看ruby版本:
```
ruby -v
```

相关链接:      

[《MAC机中安装RUBY环境》](http://www.cnblogs.com/foxting/p/4520829.html)      
[《Ruby系列文章之6 ---OS X 10.8.1 系统 HomeBrew 的安装和简单使用》](https://blog.csdn.net/maojudong/article/details/7918291/)

### 安装fastlane ###
最新的安装命令如下：[解决方法](https://blog.csdn.net/top_roboo/article/details/52087192)

```
sudo gem install -n /usr/local/bin fastlane

```
彻底解决方法 [解除 OSX 10.11 Rootless 的方法](https://www.macx.cn/thread-2167166-1-5.html?mod=viewthread&tid=2167166&extra=page%253D5&page=1) 

> 该命令可以用来更新fastlane

如果不是最新版本 还可以在项目文件里面执行指定版本来更新

```
sudo gem install -n /usr/local/bin/ fastlane --version2.104.0

```
检查版本 fastlane    

```
fastlane -v
```    

安装参考于该[文章](https://www.jianshu.com/p/abc2063f0981)


### 初始化 ###
打开[Terminal(终端)](sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist)，进入项目根目录下，初始化fastlane

```
fastlane init
```

出现如下选项，`一般选择第4项`：      
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvelhb6udrj30ve0k8zor.jpg) 
 选项含义:
    
 * ① 自动化截图
 * ② 将测试版分发自动化到TestFlight
 * ③ 自动上传、发布到App Store
 * ④ 手动设置 - 手动设置您的项目以使您的任务自动化
 
然后，输入开发者账号密码      

![](https://upload-images.jianshu.io/upload_images/1533163-1fd4760349f624e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/587)

以上就表示成功安装了Fastlane

如果遇到下面的问题，一直卡在不动
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fveljinsb7j30vo0aiq50.jpg)

那么可以不用管他，`关闭终端`或者`终止命令`即可。

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvello1s6ej305i03j74m.jpg)
## 发布到蒲公英（可选）##
详情请移步[蒲公英](https://www.pgyer.com/doc/view/fastlane)

### 安装蒲公英的 fastlane 插件 ###
在`项目根`目录下，安装蒲公英插件:      

```
fastlane add_plugin pgyer
```

稍等几秒钟
如果出现类似下面的信息，就说明安装成功了：
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvet3cqvatj30vc08i40m.jpg)
期间会询问是否可以修改文件信息，选择`y`即可

### 编辑 ###
Fastlane 会自动在 App 目录中生成 fastlane 目录，其中就会有 Fastlane 的配置文件 fastlane/Fastfile，这里，我们用 vim 打开：

```
vim ./fastlane/Fastfile
```
然后，我们找到我们在用的工作流（action），在 build_app 指令后，加入蒲公英插件的配置信息。例如：

```
lane :beta do
  build_app(export_method: "development")
  pgyer(api_key: "7f15xxxxxxxxxxxxxxxxxx141", user_key: "4a5bcxxxxxxxxxxxxxxx3a9e")
end
```

> 注意：

>以上的 `api_key` 和 `user_key`，请开发者在自己账号下的 `应用管理` - `App概述` - `API` 中可以找到，并替换到以上相应的位置。
>在 Xcode 8.3 和 Xcode 8.3 以后的版本中，对于 `build_app` 的 `export_method` 的值，需要根据开发者的打包类型进行设置，可选的值有：`app-store`、`ad-hoc`、`development`、`enterprise`。对于 Xcode 8.3 以下的版本，则不需要设置 `export_method`。

### 打包并自动上传 App 到蒲公英 ###
经过以上配置后，就可以使用 Fastlane 来打包 App，并自动上传到蒲公英了。在终端下，定位到项目所在目录，输入以下命令即可：      

```
fastlane beta
```

然后就充满激情的等待      

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvdrmzvucwg305a05aq3g.gif)      

最后打包成功提示

![蒲公英](https://ws1.sinaimg.cn/large/006tNbRwgy1fvelgi3hnaj30vi0cmjsz.jpg)


# 其他 #

### 卸载 ####

```
gem/brew uninstall fastlane
```

### 删除 ###

在项目中手动删除 fastlane文件夹即可。


# 后续 #

#### 插件安装格式 ####

fastlane add_plugin [name],需要到项目根目录下执行。

fastlane update_plugins插件更新，同上，需要cd到项目根目录下。

完成了发布到蒲公英之后肯定不再仅仅满足于此，毕竟 Fastlane 是集成一系列的自动化工具的，后面会进行一些详细的操作，比如上传到 `APP Store Connect`.详情请移步 [Fastlane (二)]()。