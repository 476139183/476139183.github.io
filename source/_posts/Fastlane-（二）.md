---
title: Fastlane （二）
date: 2018-09-18 16:59:29
password:
tags:
top:
categories:
photos:
  - https://pic1.zhimg.com/v2-ac01e78370df1fbbcf031f3de1b33a5c_r.jpg
---

# 打包到 AppStore #
[上篇文章]() 我们已经成功的打包app到蒲公英了。那么我们也可以尝试将app上传到AppStore.
废话不多说，先简单的打包一个 ipa 到 AppStore 。      

```
  desc "release"
  lane :release do
  sigh # 更新证书
  gym(
        scheme:"your need up schemeName", # scheme,
	     configuration: "Release", # 配置 Release or Debug
        export_method: "app-store")       # 打包类型，前文已经讲过

   deliver(submit_for_review: false, #是否自动提交审核
           skip_screenshots: true,  # 是否跳过截图
	        skip_metadata: true,  # 是否跳过元数据（标题，描述）
           force: true)
  end
```
关于`scheme `的设置请查看[文档](https://developer.apple.com/library/archive/documentation/IDEs/Conceptual/xcode_guide-continuous_integration/ConfigureBots.html#//apple_ref/doc/uid/TP40013292-CH9-SW3)

调用

```
fastlane release
```

在项目无误的情况下,提交成功！
![success](https://ws4.sinaimg.cn/large/006tNbRwgy1fvezjej76pj30ve0ge766.jpg)


# 了解更多 #


在[上篇文章]()的基础上，因为我们选择的是自定义，所以并没有创建 `Deliverfile`，`screenshots`和`metadata`目录，也就没有app的更多的设置,比如提交的各种信息，截图等。我们可以使用如下命令进行创建

```
deliver init
```
此时会要求输入你的 `Your Apple ID Username` 和 `password`,`The Bundle Identifier of your App`.(因为我已经登录过了。所以并未出现要输入账号的密码)。

![](https://upload-images.jianshu.io/upload_images/1533163-d4cae722f895945e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/787)

> 如果开发者账号上没有对应的bundle id的App，会提示是否创建一个新的App,这里我们选择否(n)，因为这样快速创建的App设置的信息有限，只能设置App名称等一些基本信息，我们可以通过produce工具创建一个可以设置更多信息的App

# 编辑 #
我们可以通过编辑 `Deliverfile`来设置app的相关属性:

```
# Apple ID用户名，账号名称
username "xxxx@xxxxx.com" 

# bundle identifier
app_identifier "xxx.com.cn" # The bundle identifier of your app

# 版权声明
copyright "深圳xxxx有限公司"

# 支持的语言
supportedLanguages = {
    "cmn-Hans" => "zh-Hans"
}

# 副标题
subtitle(
  'zh-Hans' => "副标题"
)

# App应用名称
name(
  'zh-Hans' => "应用名称"
)

# 关键字
keywords(
  'zh-Hans' => "各种关键字,用逗号隔开"
)



# 支持网址
support_url({
  'zh-Hans' => "http://baidu.com"
})


# App价格
price_tier 0

# 应用程序图标的路径
# app_icon './fastlane/metadata/AppIcon.png'

# 屏幕截图的文件夹的路径
screenshots_path './fastlane/screenshots'

# 指定上传ipa路径,
ipa './Ipa/xxxx.ipa'

# 自动发布 app: false,则需要手动发布
automatic_release false

# 提交审核信息:加密, idfa 等，对应的是提交审核的时候，需选择的选项
submission_information({    
    export_compliance_encryption_updated: false,
    export_compliance_uses_encryption: false,
    content_rights_contains_third_party_content: false,
    add_id_info_uses_idfa: false
})

# 应用审核小组的联系信息 app 审核信息
app_review_information(
  first_name: "段",
  last_name: "xx",
  phone_number: "+86 18237485911",
  email_address: "xxxx@ apple.com.cn",
  demo_user: "测试登录账号",
  demo_password: "测试登录密码",
  notes: "这里可以写一些备注，完全和网页一致的写法就行了"
)

```

更多[编辑内容](https://www.jianshu.com/p/867b397c7939)

# 目录 #
等待初始化完成之后，工程目录下就多了一个 fastlane目录，其内容如下：
![](http://cc.cocimg.com/api/uploads/20170519/1495177333947263.png)
> `metadata` 是包含应用在ITC上面的各种信息，可以使用它配置我们的ITC，建议使用 `Deliverfile`，因为`Deliverfile`会覆盖 `metadata` 的设置
> screenshots 屏幕截图数据。

咱们来看主要的，Appfile 和 Fastfile, Deliverfile。
## Appfile ##

Appfile用来存放 `app_identifier`，`apple_id`和 `team_id`。 了解详情，它的格式是这样的：

```
app_identifier "com.xxx.xxx" # app的bundle identifier
apple_id "xxx@xxx.com" # 你的Apple ID
 
team_id "XXXXXXXXXX" # Team ID
···
```

你也可以为每个lane(后面会讲到)提供不同的 `app_identifier`, `apple_id` 和 `team_id`，例如：

```
app_identifier "com.aaa.aaa"
apple_id "aaa@aaa.com"
team_id "AAAAAAAAAA"
 
for_lane :inhouse do
  app_identifier "com.bbb.bbb"
  apple_id "bbb@bbb.com"
  team_id "AAAAAAAAAA"
end
```
配置好`Appfile`，fastlane就不会每一次都询问你的账号和id了

## Fastfile ##

Fastfile 管理你所创建的 lane ，`了解详情`。它的格式是这样的：


```
···
# 自动更新fastlane 工具
# update_fastlane
 
#需要的fastlane的最小版本，在每次执行之后会检查是否有新版本，如果有会在最后末尾追加新版本提醒
fastlane_version "2.30.1"
 
#默认使用平台是 ios，也就是说文件可以定义多个平台
default_platform :ios
 
platform :ios do
  before_all do
    # ENV["SLACK_URL"] = "https://hooks.slack.com/services/..."
    cocoapods
 
  end
 
  desc "Runs all the tests"
  lane :test do
    scan
  end
 
  desc "提交一个新的Beta版本到 Apple TestFlight"
  desc "This will also make sure the profile is up to date"
  lane :beta do
    # match(type: "appstore") # more information: https://codesigning.guide
    gym(scheme: "Docment") # Build your app - more options available
    pilot
 
    # sh "your_script.sh"
  end
 
  desc "部署一个新版本到App Store"
  lane :release do
    # match(type: "appstore")
    # snapshot
    gym(scheme: "Docment") # Build your app - more options available
    deliver(force: true)
    # frameit
  end
 
  # 你可以定义自己的lane
 
  #执行lane成功后的回调
  after_all do |lane|
    # slack(
    #   message: "Successfully deployed new App Update."
    # )
  end
 
  # 如果流程发生异常会走这里并终止
  error do |lane, exception|
    # slack(
    #   message: exception.message,
    #   success: false
    # )
  end
end
```

系统级lane      

| 执行顺序 | 方法名 | 说明 |
| ------ | ------ | ------ |
| 1 | before_all | 在执行 lane 之前只执行一次。 |
| 2 | before_each | 每次执行 lane 之前都会执行一次。 |
| 3 | lane | 自定义的任务。 |
| 4 | after_each | 每次执行 lane 之后都会执行一次。 |
| 5 | after_all | 在执行 lane 成功结束之后执行一次。 |
| 6 | error | 在执行上述情况任意环境报错都会中止并执行一次。 |


我们也可以定义一个自己的lane：

```
desc "企业版"
  lane :inHouse do
  gym(scheme: "XXX",
      export_method:"enterprise",
      output_directory "./build", # 打包后的 ipa 文件存放的目录
      output_name "XXX"  # ipa 文件名
   )
  end
```

其中一个lane就是一个任务，里面是一个个的action组成的工作流。      
利用目前支持的工具可以做所有包含自动化和可持续化构建的每个环节，详情请查看[上篇文章的工具类]()


## Deliverfile ##

交付文件。在这个文件里面可以设置iTunes connect的所有配置项，例如：

release_notes，此版本新增内容。

copyright，版权信息。

submit_for_review，上传完成后是否直接提交新版本进行审查。

force，跳过HTML报告文件验证。

...

请在设置 `release_nores` 、`support_url` 、`private_url` 等配置的时候，采用 hash 的方式写，[国家代码](https://www.cnblogs.com/Mien/archive/2008/08/22/1273950.html)，例如:

```
release_notes(
	# 中国
	'zh-Hans' => ENV['RELEASE_NOTES'],
	# 澳大利亚
	'en-au' => ENV['RELEASE_NOTES_AU'],
	# 美国
	'en-us' => ENV['RELEASE_NOTES_US']
)
```
具体的配置可以在文章最后查看.

# 工具使用 #
### 使用produce，在iTunes Connect或者Apple Developer Center上创建新的iOS应用程序 ###
进入项目根目录，找到fastlane文件夹下的Fastfile文件， 编辑文件，内容如下：

```
platform :ios do
 desc "create new app"
 lane :produce_app do
   produce(
     username: "xxxx@gmail.com",# APPle ID
     app_name: "小鱼小鱼吃虾米",#App 名称
     app_identifier: "com.test.www.FastlaneTest",#App bundle id
     app_version: "1.0",#App 版本
     sku: "10023500",#App sku
     language: "Simplified Chinese",#App 语言，默认English
   )
 end
end
```

![](https://upload-images.jianshu.io/upload_images/1533163-dabebfdee644cc43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/855)

__备注：language这个选项不能直接写Chinese，语言选项如下__    
*["Brazilian Portuguese", "Danish", "Dutch", "English", "English_Australian", "English_CA", "English_UK", "Finnish", "French", "French_CA", "German", "Greek", "Indonesian", "Italian", "Japanese", "Korean", "Malay", "Norwegian", "Portuguese", "Russian", "Simplified Chinese", "Spanish", "Spanish_MX", "Swedish", "Thai", "Traditional Chinese", "Turkish", "Vietnamese"]*

在terminal中执行fastlane produce_app命令，创建成功提示如下：
![](https://upload-images.jianshu.io/upload_images/1533163-ea43827f2e8ffd7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/855)

![](https://upload-images.jianshu.io/upload_images/1533163-361175ae5018d974.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/949)

### 使用cert创建签名证书(Certificates) ###
在Fastfile中添加以下内容
#### 生成开发证书: ####

```
lane :create_certificates do
    cert(
      username: "xxxx@gmail.com",# APPle ID
      development: true,# 创建一个开发证书
      force: "false",#即使存在现有证书，也要创建证书，默认为false
      output_path: "./certs/development",#存储所有证书和私钥的目录的路径
    )
  end
```
结果如下：
![](https://upload-images.jianshu.io/upload_images/1533163-bc94a147af5bdb6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/857)

#### 生成生产证书: ####

```
lane :create_dcertificates do
    cert(
      username: "xxxx@gmail.com",# APPle ID
      development: false,# 创建一个开发证书
      force: "false",#即使存在现有证书，也要创建证书，默认为false
      output_path: "./certs/distribution",#存储所有证书和私钥的目录的路径
    )
  end

```
![](https://upload-images.jianshu.io/upload_images/1533163-cc38aa742b08fc2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/845)

__开发和生产证书，修改development的boolean值及output_path路径即可__

### 使用sigh 生成描述文件(mobileprovision) ###
在Fastfile中
#### 生成开发mobileprovision ####
```
lane :get_dev_provisioningProfile do
  get_provisioning_profile(
    username: "xxxx@gmail.com",# APPle ID
    development: true,# 创建一个开发mobileprovision
    app_identifier: "com.test.www.FastlaneTest",#bundle identifier
    output_path: "./certs/development",#存储mobileprovision的目录的路径
    filename: "fastlane_dev.mobileprovision",#用于生成的供应配置文件的文件名（必须包含.mobileprovision）
  )
end

```
![](https://upload-images.jianshu.io/upload_images/1533163-db43c3e1861439bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/853)

#### 生成生产mobileprovision ####
```
lane :get_provisioningProfile do
   get_provisioning_profile(
     username: "xxxx@gmail.com",# APPle ID
     development: false,# 创建一个开发证书
     app_identifier: "com.test.www.FastlaneTest",#bundle identifier
     output_path: "./certs/distribution",#存储mobileprovision的目录的路径
     filename: "fastlane_distr.mobileprovision",#用于生成的供应配置文件的文件名（必须包含.mobileprovision）
   )
 end
```
![](https://upload-images.jianshu.io/upload_images/1533163-a5eaaeed82991868.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/853)
找到 `certs/development&&distribution`目录下的证书及`.mobileprovision`文件，并安装到电脑上
![](https://upload-images.jianshu.io/upload_images/1533163-de34c42dd8886483.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/595)

### 使用gym来打包ipa ###

示例   

```
lane :inhouse do
  # 以下两个action来自fastlane-plugin-versioning，
  # 第一个递增 Build，第二个设定Version。
  # 如果你有多个target，就必须指定target的值，否则它会直接找找到的第一个plist修改
    increment_build_number_in_plist(target: 'FastlaneTest')
    increment_version_number_in_plist(
      target: 'FastlaneTest',
      version_number: '1.0'
    )
    # gym用来编译ipa
    gym(scheme: 'FastlaneTest',#打包scheme
    export_method:"enterprise",#打包类型app-store, ad-hoc, enterprise, development
    output_directory: "./build",#ipa打包存放路径
    output_name: 'inhouse.ipa'#ipa名称
    )
  end
```


## 其他 ##
* 这里是官方提供的一些 [例子](https://github.com/fastlane/examples)。      
* 想了解 fastlane 命令的话可以执行 `fastlane --help`      
* 查看可用任务的列表，可以执行命令 `fastlane lanes`
* 可以安装一些插件来辅助，查看插件 `fastlane search_plugins`, 比如修改版本号的 [versioning](https://github.com/SiarheiFedartsou/fastlane-plugin-versioning),以及 同样功能的 `fastlane-plugin-versioning`
安装上面的插件,使用 `fastlane add_plugin [name] # 安装方法`
比如      
```
fastlane add_plugin versioning
```

* 多个 lane 的话实际上是可以相互调用的，这个其实特别实用。例如：

```
default_platform :ios
 
platform :ios do
 
  lane :prepare do
    cocoapods
    match
  end
 
 
  desc 'fastlane build'   'fastlane build type:adhoc'
  lane :build do |options|
    # 调用上面的 prepare 任务
    prepare
 
    case options[:type]
    when 'adhoc'
      adhoc
    else
      appstore
    end
  end
 
 
  lane : adhoc do
  ···
  end
 
  lane : appstore do
  ···
  end
 
end
```
我们可以在 Fastfile 文件中添加一个函数来设置version号和build号。

```
default_platform :ios
 
def prepare_version(options)
    increment_version_number(
        version_number: options[:version]
    )
 
    increment_build_number(
        build_number: options[:build]
    )
end
```

然后可以在一个lane中使用这个函数：

```
lane :appstore do |options|
   ···
    prepare_version(options)
   ···
end
```

然后执行这个lane的时候：

```
fastlane appstore version:2.4.0 build:2.0
```


# 其他 #

[相关文章](https://www.jianshu.com/p/ecaca8ac86e3)

[fastlane 实现自定义Action](https://www.jianshu.com/p/44bbe1a31b52)

[使用fastlane自动增加版本号](https://www.jianshu.com/p/46118a562b5b)

#### [Deliverfile 编辑示例](https://docs.fastlane.tools/actions/deliver/) ####
```
# app_identifier
app_identifier ENV['APP_IDENTIFIER']
# 用户名,Apple ID电子邮件地址
username ENV['APPLE_ID']
# 团队ID
team_id ENV['TEAM_ID']
# 团队name
team_name ENV['TEAM_NAME']
# copyright
copyright ENV['COPYRIGHT']
# 关键字
keywords(
    'zh-Hans' => ENV['KEYWORDS'],
)
# 新版本修改记录
release_notes(
    # 中国
    'zh-Hans' => ENV['RELEASE_NOTES'],
    # 澳大利亚
    'en-au' => ENV['RELEASE_NOTES_AU'],
    # 美国
    'en-us' => ENV['RELEASE_NOTES_US']
)
# 支持网址
support_url(
    # 中国
    'zh-Hans' => ENV['SUPPORT_URL'],
    # 澳大利亚
    'en-au' => ENV['SUPPORT_URL_AU'],
    # 美国
    'en-us' => ENV['SUPPORT_URL_US']
)
# 隐私政策网址 国家代码 https://www.cnblogs.com/Mien/archive/2008/08/22/1273950.html
privacy_url(
    # 中国
    'zh-Hans' => ENV['PRIVACY_URL'],
    # 澳大利亚
    'en-au' => ENV['PRIVACY_URL_AU'],
    # 美国
    'en-us' => ENV['PRIVACY_URL_US']
)
# 上传完成后提交新版本进行审查
submit_for_review false
# 跳过HTML报告文件验证
force true
# 启用iTC的分阶段发布功能 灰度发布
phased_release true
# 应用审核小组的联系信息 app 审核信息
app_review_information(
  first_name: "xx",
  last_name: "xx",
  phone_number: "+86 18888888888",
  email_address: "xxxx",
  demo_user: "test1@test.com",
  demo_password: "test123"
)
...
```


最后，附上一个Fastfile脚本和一些自定义Actions：
https://github.com/thierryxing/Fastfiles

另外，Fastlane也提供了一些国外团队的Example：
https://github.com/fastlane/examples

