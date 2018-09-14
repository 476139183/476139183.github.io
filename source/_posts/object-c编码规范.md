---
title: object-c编码规范
date: 2018-09-03 11:02:54
tags: 
top: 100
---

# 总览 #

参考资料[Google Objective-C Style Guide](https://zh-google-styleguide.readthedocs.io/en/latest/google-objc-styleguide/)   
    
   老大是一位重度代码洁癖的处女座，对代码规范非常苛刻，以前经常改动我的代码就为了他能看的顺眼。我记得曾在我提交的茫茫代码行中愣是指出某处的空格少了一个(囧)。    
    其实我们花时间在某一方面，必然在某一方面有所收获，在代码上花时间，那么必然对技术有所提升，对泡妞上花时间，必然在泡妞上有更多的心得。。。  写博客也是因为老大的建议，即便写的不深，很是粗糙，但是经常写博客，花时间总结，可以记录自己的整个学习过程，也有助于自我思考，自我表达。< 屁话太多了吧(路过人员的心声) >      
    
	前沿：其实 每一门开发语言都有自己的代码规范，我们应该遵循它的代码规范去使用它，这就是这篇文章的来源。    
	

<!--more--> 

## 背景知识 ##
    
 Objective-C是一个C语言的扩展语言，非常动态，非常的“面向对象”，它被设计成既拥有复杂的面向对象设计理念又可以轻松使用与阅读的语言，也是Mac OS X和iPhone开发的首选语言。

   Cocoa是Mac OS X的主要应用框架，提供迅速开发各种功能的Mac OS X应用的Objective-C类集合。

  Apple已经有一个很好也被广泛接受的Objective-C的编码规范，Google也有类似的C++编码规范，这份Objective-C编码规范很自然是Apple和Google的共同推荐的组合。所以，在阅读本规范前，确保你已经阅读了:

  * [Apple's Cocoa Coding Guidelines](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/FrameworkImpl.html)     
  * [Google's Open Source C++ Style Guide](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/contents/) 

  > 注意所有已在Google的C++编码规范里的禁用条款在Objective-C里也适用，除非本文档明确指出反对意见。 
  
本文档旨在描述可供可适用于所有Mac OS X代码的Objective-C(包括Objective-C++)编码规范和实践。规范中的许多条款已经改进也不断的被其他的项目和团队所证明其指导性。Google的相关开源项目都遵守此规范。

  Google已经发布了一份作为[Google Toolbox for Mac project](https://code.google.com/p/google-toolbox-for-mac/) (文档中简称为`GTM`)的组成部分的遵守本规范的开源代码。这份开放代码也是本文很好的例证(原文看不太懂－－Code meant to be shared across different projects is a good candidate to be included in this repository. )

  注意本文不是Objective-C的教学指南，我们假设读者已经了解语言。如果你是一个Objective-C的初学者或需要重温，请阅读[The Objective-C Programming Language](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html) .


示例 

 人们说一个例子胜过千言万语，所以就让我们用例子来让你感受以下编码规范的风格，留间距，命名等等。

 下例是一份头文件，展示对@interface 声明正确的注释和留间距    
 
iOS代码 

``` objc
//  GTMFoo.h  
//  FooProject  
//  
//  Created by Greg Miller on 6/13/08.  
//  Copyright 2008 Google, Inc. All rights reserved.  
//  
```  

``` objc
#import <Foundation/Foundation.h>  
  
// A sample class demonstrating good Objective-C style. All interfaces,  
// categories, and protocols (read: all top-level declarations in a header)  
// MUST be commented. Comments must also be adjacent to the object they're  
// documenting.  
//  
// (no blank line between this comment and the interface)  
@interface GTMFoo : NSObject {  
 @private  
  NSString *foo_;  
  NSString *bar_;  
}  
  
// Returns an autoreleased instance of GMFoo. See -initWithString: for details  
// about the argument.  
+ (id)fooWithString:(NSString *)string;  
  
// Designated initializer. |string| will be copied and assigned to |foo_|.  
- (id)initWithString:(NSString *)string;  
  
// Gets and sets the string for |foo_|.  
- (NSString *)foo;  
- (void)setFoo:(NSString *)newFoo;  
  
// Does some work on |blah| and returns YES if the work was completed  
// successfuly, and NO otherwise.  
- (BOOL)doWorkWithString:(NSString *)blah;  
  
@end  
```

 __注意：有朋友喜欢在全局变量命名时以_开头，其实我不建议，比如 NSString *_foo; 因为在阅读时很容易让人误会是property属性，我们应该尽量让阅读者可以方便的理解代码，而不是出现诱导因素。__

  下例是一份源文件，展示对接口的@implementation 的实现的正确注释和留间隔。它也包括了主要方法如getters,setters,init ,和dealloc 的相关实现。  
    
iOS代码    

``` objc
//  
//  GTMFoo.m  
//  FooProject  
//  
//  Created by Greg Miller on 6/13/08.  
//  Copyright 2008 Google, Inc. All rights reserved.  
//  
```
``` objc
#import "GTMFoo.h"  
  
  
@implementation GTMFoo  
  
+ (id)fooWithString:(NSString *)string {  
  return [[[self alloc] initWithString:string] autorelease];  
}  
  
// Must always override super's designated initializer.  
- (id)init {  
  return [self initWithString:nil];  
}  
  
- (id)initWithString:(NSString *)string {  
  if ((self = [super init])) {  
    foo_ = [string copy];  
    bar_ = [[NSString alloc] initWithFormat:@"hi %d", 3];  
  }  
  return self;    
}  
  
- (void)dealloc {  
  [foo_ release];  
  [bar_ release];  
  [super dealloc];  
}  
  
- (NSString *)foo {  
  return foo_;  
}  
  
- (void)setFoo:(NSString *)newFoo {  
  [foo_ autorelease];  
  foo_ = [newFoo copy];    
}  
  
- (BOOL)doWorkWithString:(NSString *)blah {  
  // ...  
  return NO;  
}  
  
@end  
```


## 间隔与格式化 ##

### 空格对tab键 ##

  仅使用空格，缩进两个。

  我们使用空格用于缩进，不要在编码时使用tab键，你应该设置你的编辑器将tab键转换成对应的空格。


### 行长度 ###
    
  代码中的每行文本不要超过80个字符的长度。

  尽管Objective-C正变得比C++更加繁冗，为了保持规范的互通性，我们还是决定保持80字符长度的限制。这比你想象中的更容易做到。

  我们知道本条款是有争议的，但已有此多的代码已经遵从了本条款，即使只是保持一致性也是一个充足的理由。

  你可以在Xcode里清楚地发现代码中的违规，设置  Xcode > Preferences > Text Editing > Show page guide. (之后就可以在代码编辑区域里看到一条指定字符长度的指示线了)


## 方法声明与定义 ##
  留一个空格在 - 或 + 和 返回类型 之间，但参数列表里的参数之间不要留间隔。
 方法应该写成这样:    
 
iOS代码 

``` objc
- (void)doSomethingWithString:(NSString *)theString {  
  ...  
}  
```

星号前的空格是可选的，你可以根据原来的代码风格自行决定。

如果参数过多，推荐每个参数各占一行。使用多行的情况下，以参数前的冒号用于对齐:     
        (很遗憾这里仅有Google Chrome浏览器能看出是冒号对齐的......)     
        
iOS代码

``` objc
- (void)doSomethingWith:(GTMFoo *)theFoo  
                   rect:(NSRect)theRect  
               interval:(float)theInterval {  
  ...  
}  
```

当第一个关键字比其他的短时，后续行至少缩进四个空格。这样你可以让后续的关键字垂直对齐，而不是用冒号对齐:     
iOS代码 

```
- (void)short:(GTMFoo *)theFoo  
    longKeyword:(NSRect)theRect  
    evenLongerKeyword:(float)theInterval {  
  ...  
}  
```


### 方法调用 ###

 方法调用的格式和方法声明时的格式时一致的，如果格式风格可选，遵从原有代码的风格。

 调用应该将所有参数写在一行:   
    
iOS代码 

``` objc
[myObject doFooWith:arg1 name:arg2 error:arg3];  
```

 或者每个参数一行，用冒号对齐:(对齐效果如前说明)    
 
iOS代码

``` objc
[myObject doFooWith:arg1  
               name:arg2  
              error:arg3];  

```
注意:不要使用如下风格的写法

iOS代码

``` objc
[myObject doFooWith:arg1 name:arg2  // some lines with >1 arg  
              error:arg3];  
  
[myObject doFooWith:arg1  
               name:arg2 error:arg3];  
  
[myObject doFooWith:arg1  
          name:arg2  // aligning keywords instead of colons  
          error:arg3];  

```
在声明和定义时，如果因为关键字长度使就算有四个空格在前仍然无法用冒号对齐，那么就让后续行缩进四个空格而不是冒号对齐:    

iOS代码

```
[myObj short:arg1  
    longKeyword:arg2  
    evenLongerKeyword:arg3];  
```


### @public 和 @private ###

权限控制符@public 和@private 缩进一个空格.

类似C++的public,protected,private:

iOS代码

``` objc
@interface MyClass : NSObject {  
 @public  
  ...  
 @private  
  ...  
}  
@end  
```


### 异常 ###
 
每个异常标签的@ 和开括号({ )写在统一行，标签和开括号间隔一个空格，同样适用于@catch 语句。

如果你决定使用Objective-C的异常，那么就按如下格式，不过你最好先看看Avoid Throwing Exceptions(见后)条款，了解为何你不应使用异常。    

iOS代码

```
@try {  
  foo();  
}  
@catch (NSException *ex) {  
  bar(ex);  
}  
@finally {  
  baz();  
}  
```


## 命名 ##
 
命名规则对于维护代码来说是非常重要的。Objective-C方法名往往很长，不过这也有好处，读代码就像读散文(放屁),让很多注释变得毫无意义。

写纯Objective-C代码时，我们基本上遵守标准Objective-C naming rules ，这些规则和C++的规则有很大的不同。比如Google的C++代码规范推荐变量名构词之间使用下划线隔开，而本文档推荐驼峰法，也是Objective-C社区的标准。

所有类，类别, 方法，以及变量如包括缩写，则缩写部分使用全大写的缩写(Initialisms )形式。这遵守Apple的标准，比如URL,TIFF以及EXIF。

  当写Objective-C++代码时，情况就不是那么单一了。许多项目需要实现带一些Objective-C代码的跨平台的C++APIs或者连接后台的C++代码与前台的原生Cocoa代码.这会造成两种规范直接冲突。

  我们的解决方法是根据方法/函数风格来决定。如果在@implementation 块，就使用Objective-C的命名规则；如果在C++的方法实现块，就使用C++的命名规则。避免了实体变量和本地变量在一个函数内命名规则冲突的情况，而这种情况是对可读性的极大损害。

### 文件命名 ###
 
  文件名反映了它所包含的实现类的名字，遵从你所在项目的习惯。

  文件扩展名使用如下规则
  
```
.h     	C/C++/Objective-C header file
 
.m     	Objective-C implementation file
 
.mm    	Objective-C++ implementation file
 
.cc    	Pure C++ implementation file
 
.c     	C implementation file

```


类别的文件名应该包含扩展类的名字，比如 `GTMNSString+Utils.h` or `GTMNSTextView+Autocomplete.h` 


### Objective-C++ ###

在一份源文件里，Objective-C++代码遵守当前方法/函数的风格

为了尽量减少不同命名风格间的冲突，使用当前方法的风格。如果在@implementation块，使用Objective-C命名规则，如果在C++类的函数实现块，使用C++命名规则。    

iOS代码 

``` objc
// file: cross_platform_header.h  
  
class CrossPlatformAPI {  
 public:  
  ...  
  int DoSomethingPlatformSpecific();  // impl on each platform  
 private:  
  int an_instance_var_;  
};  

```
  
``` objc
// file: mac_implementation.mm  
#include "cross_platform_header.h"  
  
// A typical Objective-C class, using Objective-C naming.  
@interface MyDelegate : NSObject {  
 @private  
  int instanceVar_;  
  CrossPlatformAPI* backEndObject_;  
}  
- (void)respondToSomething:(id)something;  
@end  
@implementation MyDelegate  
- (void)respondToSomething:(id)something {  
  // bridge from Cocoa through our C++ backend  
  instanceVar_ = backEndObject->DoSomethingPlatformSpecific();  
  NSString* tempString = [NSString stringWithInt:instanceVar_];  
  NSLog(@"%@", tempString);  
}  
@end  
  
// The platform-specific implementation of the C++ class, using  
// C++ naming.  
int CrossPlatformAPI::DoSomethingPlatformSpecific() {  
  NSString* temp_string = [NSString stringWithInt:an_instance_var_];  
  NSLog(@"%@", temp_string);  
  return [temp_string intValue];  
}  

```

### 类命名 ###

   类名(不包括类别和协议名)应该用大写开头的驼峰命名法。

   在应用级别的代码里，尽量不要使用带前缀的类名。每个类都有相同的前缀不能提高可读性。不过如果是编写多个应用间的共享代码，前缀就是可接受并推荐的做法了(型如 GTMSendMessage )。（注：笔者建议加入前缀，因为这并不是单单为了提高可读性，也为了与引入进来的类做区分，所以合适的前缀是有必要的）

### 类别命名 ### 

   类别命名应该以两三个字符的分类前缀作为一个项目或通用的公用部分。类别名应该包含类的扩展。

   举个例子，如果我们想要创建一个基于NSString 的类别用于解析，我们应该把类别放到名字是GTMNSString+Parsing.h 的文件里，而类别本身的名字则是GTMStringParsingAdditions (是的，我们明白这个类别和其文件名字不匹配，但这个文件可能还包括其他用于解析相关的类别)。类别的方法应该都使用一个前缀(型如gtm_myCategoryMethodOnAString ),以防止Objective-C代码在单名空间里冲突。如果代码本来就不考虑共享或在不同的地址空间(address-space)，方法命名规则就没必要恪守了。

### Objective-C 方法命名 ###

   方法使用小写开头的驼峰法命名，每个参数都应该小写开头。

   方法名应该尽可能读起来像一句话，参数名就相对方法名的补充说明(比如convertPoing:fromRect: 或者 replaceCharactersInRange:withString: )，详见Apple's Guide to Naming Methods 

   存取(Accessor)方法应该一致的在"取(getting)"的时候直接用变量名而不是在签名加"get",如下:    
   
iOS代码 

```
- (id)getDelegate;  // AVOID  
  
- (id)delegate;    // GOOD  
```
   
   不过这仅针对Objective-C代码，C++代码仍然遵循自己的代码规范。

### 变量命名 ###

   变量名使用小写开头的驼峰法，类成员变量名最后加一个下划线，比如:myLovalVariable, myInstanceVariable_ . 下面看不懂，原文Members used for KVO/KVC bindings may begin with a leading underscore iff use of Objective-C 2.0's @property isn't allowed. 

#### 一般变量命名 ####
        
   不要使用匈牙利命名法去标记语法，比如静态类型或变量类型(int或pointer之类的)。使变量名尽量可以推测其用途属性具有描述性。别一心想着少打几个字母，让你的代码可以迅速被理解更加重要。比如:
   
iOS代码

```
// AVOID  
int w;  
int nerr;  
int nCompConns;  
tix = [[NSMutableArray alloc] init];  
obj = [someObject object];  
p = [network port];  
  
// GOOD  
int numErrors;  
int numCompletedConnections;  
tickets = [[NSMutableArray alloc] init];  
userInfo = [someObject object];  
port = [network port];  
```

#### 实例变量 ####

   实例变量用驼峰法命名并后缀下划线，就像 `usernameTextField_` . 然而我们允许一种例外就是用KVO/KVC去绑定一个实体变量而Objective-C 2.0 不能用(因为操作系统的限制)的情况，此时也可用前缀下划线的方法给每个变量命名。如果可以使用Objective-C 2.0，`@property` 和 `@synthesize` 提供了遵守命名规范的解决方法。

#### 常量 ####
   
   常量(预定义，枚举，局部常量等)使用小写k开头的驼峰法，比如 `kInvalidHandle` , `kWritePerm` . 


## 注释 ##

尽管写起来很痛苦，但注释仍然是使代码保持可读性的极端重要的方式。下面的条款描述了你应该注释什么以及在哪里做注释。但是记住:即使注释是如此重要，最好的代码还是自说明式的。起一个有意义的名字比起一个晦涩的名字然后在用注释去解释它好的多。

   当你写注释的时候，记住注释是写给读者，即下一个要理解你的代码并继续开发的人。"下一个"完全可能就是你自己。

   同样，所有C++编码规范的条款仍然适用，只是增加了一些条款，如下.

### 文件注释 ###

   每个文件的开头都是版权声明，接着是文件内容的描述。

   法律声明和作者栏 

   每个文件都应该包含如下信息:

   一份版权声明(比如 Copyright 2008 Google Inc .)
   许可版本 为项目选择合适的许可版本(比如Apache 2.0, BSD, LGPL, GPL)

   如果你把别人写的文件做了相当大的改动，就把自己添加到作者栏去。这样别的开发者就方便联系这个文件的实际开发人员了。

### 声明部分注释 ###

   每个接口，类别，协议都必须伴随描述它的用途以及如何整合的注释。
   
iOS代码

```objc
// A delegate for NSApplication to handle notifications about app  
// launch and shutdown. Owned by the main app controller.  
@interface MyAppDelegate : NSObject {  
  ...  
}  
@end  
```

   如果已经在文件的顶部写了接口的详细描述，你也可以简单的写如"见文件顶部的完整描述"，当然要有这些注释的顺序安排。

   此外public接口的每个方法都应该添加关于函数，参数，返回值以及副作用的注释。       

   文档默认类都是同步的，如果类实例可以多线程访问，必须要加上额外的说明。     

### 实现部分注释 ###

  使用竖线引用变量或符号，而不是用引号。    

   这可以减少歧义，特别是当符号本身就是个常见的词时，可能使句子显得支离破碎，比如符号是"count":    
   
iOS代码     
    
```
// Sometimes we need |count| to be less than zero.        
```
         
   或者是对于那些已经存在引号的情况     
   
iOS代码    

``` objc
// Remember to call |StringWithoutSpaces("foo bar baz")|  
```

### 对象所有权 ###

   使指针所有权的模型尽可能清晰，当它属于Objective-C的使用惯例时(不懂，原文是Make the pointer ownership model as explicit as possible when it falls outside the most common Objective-C usage idioms. )

   实例变量指向NSObject派生类的对象时都假定是retain的，如果它们不是retain的则需要加上"weak"的文档说明。对应的，实体变量如果标记上IBOutlets则是假定为非retain的，若实际上用了retain，就必须加上"strong"的说明。

   当实例变量指向核心库，C++或其他非Objective-C对象时，必须永远用注释说明是strong还是weak的。必须注意为了支持Objective-C对象里的自动化C++对象的封装是默认被关闭的的(这句话有歧义，原文是Be mindful that support for automatic C++ objects encapsulated in Objective-C objects is disabled by default)，这里 有说明。

  strong和weak说明的文档示例:
  
  iOS代码 

``` objc
@interface MyDelegate : NSObject {  
 @private  
  IBOutlet NSButton* okButton_;  // normal NSControl  
  IBOutlet NSMenu* myContextMenu_;  // manually-loaded menu (strong)  
  
  AnObjcObject* doohickey_;  // my doohickey  
  MyController* controller_;  // so we can send msgs back (weak, owns me)  
  
  // non-NSObject pointers...  
  CWackyCPPClass* wacky_;  // some cross-platform object (strong)  
  CFDictionaryRef* dict_;  // (strong)  
}  
@end  
```

```
   strong
   对象会在类中retain 
   weak
   对象不会在类中retain   (比如一个委托)
```

## Cocoa和Objective-C特性 ##

### 成员变量应该定义为@private ###
 
iOS代码

```
@interface MyClass : NSObject {  
 @private  
  id myInstanceVariable_;  
}  
// public accessors, setter takes ownership  
- (id)myInstanceVariable;  
- (void)setMyInstanceVariable:(id)theVar;  
@end  
```

### 明确指定初始化函数 ###

   * 注释并说明指定的初始化。

   明确指定初始化对想要子类化你的类的时候时很重要的。那样，子类化时只需要做一个或多个初始化去保证初值即可。这也有助于在以后调试你的类时明了初始化流程。

### 重写指定初始化函数 ###

   当重写一个子类并需要init... 方法，注意要重写父类的指定初始化方法。

   如果你没有正确重写父类的指定初始化方法，你的初始化方法可能不会被调用，这会导致很多微妙而难以排除的错误。

### 初始化 ###

   没必要在初始化方法里把变量初始化为0 或者nil ,这是多余的。

   所有新分配内存的对象内容都初始化为0(除了 isa )，所以不要在init 方法里做无谓的重初始化为0的操作。


### 保持公有API简明 ###

   保持你的类简单，避免"厨房水槽"似的APIs，如果一个方法没必要公开就不要公开。使用私有类别保证公开头文件的简洁。

   和C++不同，Objective-C无法区分公有私有方法，因为它全是公有的。因此，除非就是为了让用户调用所设计，不要把其他的方法放到公有API里。这样可以减少不期调用的可能性。这还包括重写父类的方法。对于那些内部实现的方法，在实现文件里使用类别而不是将方法定义在公有头文件里。
   
iOS代码    

``` objc
// GTMFoo.m  
#import "GTMFoo.h"  
  
@interface GTMFoo (PrivateDelegateHandling)  
- (NSString *)doSomethingWithDelegate;  // Declare private method  
@end  
  
@implementation GTMFoo(PrivateDelegateHandling)  
...  
- (NSString *)doSomethingWithDelegate {  
  // Implement this method  
}  
...  
@end  
```

   在Objective-C 2.0之前，如果你在私有@interface 里声明了一个方法，但忘记在主@implementation 文件里实现了，编译器不会有什么反应(这是因为你没有在不同的类别里实现这些私有方法)。解决方案在是把函数写到@implementation 里并指明类别。

   如果你用的是 Objective-C 2.0，你应该使用类扩展 而不是声明私有类别，如下:
   
iOS代码    

``` 
@interface GMFoo () { ... }  
```
     
   如此就可以保证函数做了声明但没有在@implememtation 里实现的时候编译器会警报。

   再者，"private"方法并不是真正的private，你可能会无意间重写了父类的一个"private"方法，这回导致bug的涌现。总的来说，私有方法应该使用更特别的名字以阻止子类化时并不期望的重写。

   最后，对于绝大多数类而言，Objective-C的类别是将@implelemtation做可理解的分块，添加新的应用级别的功能的最佳途径。比如，与其在你的项目里随便找个类来实现字符串的"中间截断"功能，不如创建一个新的NSString 类别。


## #import 和 #include ##
  

   用 #import 导入Objective-C 或 Objective-C++头 文件，用 #include 导入C或C++头 文件

### 根据头文件的语言去选择合适的导入方式。###

   当导入的头文件使用Objective-C或Objective-C++语言时，使用#import .
   当导入标准C或 C++头文件时，使用#include . 头文件应该使用自己的#define 重加载保护

   有些Objective-C头文件没有#define 重加载保护，所以只应该用#import 导入。因此Objective-C头文件只应该被Objective-C源文件或其他的Objective-C头文件所导入。这种情况下全部使用#import 是合适的。

   标准C和C++头文件不包含任何Objective-C元素都可以被一般的C或C++文件导入。因为标准C和C++里根本没有#import ，所以也只能用#include 导入。在Objective-C代码中使用#include 一致的导入这些头文件。

   本条款有助于跨平台项目的无意错误。一位Mac开发者引入一份新C或C++头文件时可能会忘记添加#define重加载保护，因为在Mac上用#import 导入文件不会引发问题，但在别的使用#include 的平台就可能出问题。在所有平台一致的使用#include 意味着要么全部成功要么全部失败，避免了那种另人沮丧的一些平台上可以运作而另一些不行的情况。

 iOS代码 

``` objc
#import <Cocoa/Cocoa.h>  
#include <CoreFoundation/CoreFoundation.h>  
#import "GTMFoo.h"  
#include "base/basictypes.h"  
```


### 使用框架根 ###
 
   导入框架根的头文件而不是分别导入框架头文件

   看起来从Cocoa或Foundation这些框架里导入个别的文件很不错，但实际上你直接导入框架根头文件效率更高。框架根已经被预编译故可更快的被加载。还有，记住用#import 指令而不是#include 导入Objective-C的框架。

iOS代码

``` objc 
#import <Foundation/Foundation.h>     // good  
  
#import <Foundation/NSArray.h>        // avoid  
#import <Foundation/NSString.h>  
``` 


### 构建时即设定autorelease ###

   当创建新的临时对象时，在同一行代码里就设定autorelease而不是写到这个方法的后面几行去

   即使这样可能会造成一些轻微的延迟，但这样避免了谁不小心把release 去掉，或在release 之前就return 而造成的内存泄露，如下
   
iOS代码    

``` objc 
// AVOID (unless you have a compelling performance reason)  
MyController* controller = [[MyController alloc] init];  
// ... code here that might return ...  
[controller release];  
  
// BETTER  
MyController* controller = [[[MyController alloc] init] autorelease];  
```


   优先autorelease而非retain 

### 对象赋值时尽量采用autorelease 而不是retian 模式。 ###

   当把一个新创建的对象赋予一个变量的时候，第一件要做的事情就是先释放原来变量指向的对象以防止内存泄露。这里也有很多"正确的"方法去做这件事。我们选择autorelease时因为它更不倾向于出错。小心在密集的循环里可能会很快填满autorelease池，而且它也确实会降低效率，但权衡下来还是可以接受的。
   
iOS代码  

```objc
- (void)setFoo:(GMFoo *)aFoo {  
  [foo_ autorelease];  // Won't dealloc if |foo_| == |aFoo|  
  foo_ = [aFoo retain];  
}  
```

   以声明时的顺序dealloc处理实例变量 

   dealloc 应该用在@interface 声明时同样的顺序处理实例变量，这也有助于评审者鉴别。

   代码评审者检查或修正dealloc 的实现要确保所有retain 的实例变量都获得了释放。

   为了简化评审dealloc ，将释放retain 的实例变量代码保持和@interface 里声明的顺序一致。如果dealloc 调用了其他方法去释放实例变量，添加注释说明那些实例变量被这些方法所处理了。

### Setters copy NSStrings ###

   在NSString 上调用Setters 方法时，永远使用copy 方式。(不太懂，原文是Setters taking an NSString, should always copy  the string it accepts. )

   永远不要retain 一个字符串，这可以防止调用者在你不知到的情况下修改了字符串。不要以为你可以改变NSString 的值，只有NSMutableString 才能做到。
   
iOS代码 

``` objc
- (void)setFoo:(NSString *)aFoo {  
  [foo_ autorelease];  
  foo_ = [aFoo copy];  
}  
```

 避免抛出异常 

   不要 @throw Objective-C的异常，不过你还是要做好准备捕获第三方以及系统调用抛出的异常。

   我们的确在编译时加入了-fobjc-exceptions 指令(主要是为了获得@synchronized )，但我们并不@throw 。当然在使用第三方库的时候是允许使用@try，@catch, 以及@finally 的。如果你确实使用了，请务必明确到文档中哪个方向你想抛出什么异常。

   除非你写的代码想要泡在MacOS 10.2或更之前，否则不要使用NS_DURING, NS_HANDLER, NS_ENDHANDLER, NS_VALUERETURN and NS_VOIDRETURN 这些宏。

   另外你要小心当写Objective-C++代码的时候，如果抛出Objective-C异常，那些栈上的对象不会被清理。示例:
   
iOS代码 

``` objc
class exceptiontest {  
 public:  
  exceptiontest() { NSLog(@"Created"); }  
  ~exceptiontest() { NSLog(@"Destroyed"); }  
};  
  
void foo() {  
  exceptiontest a;  
  NSException *exception = [NSException exceptionWithName:@"foo"  
                                                   reason:@"bar"  
                                                 userInfo:nil];  
  @throw exception;  
}  
  
int main(int argc, char *argv[]) {  
  GMAutoreleasePool pool;  
  @try {  
    foo();  
  }  
  @catch(NSException *ex) {  
    NSLog(@"exception raised");  
  }  
  return 0;  
}  
```
   将会有如下输出:    
   
   iOS代码    
   
```
2006-09-28 12:34:29.244 exceptiontest[23661] Created         
2006-09-28 12:34:29.244 exceptiontest[23661] exception raised  
```

   注意这里的析构函数永远没有机会被调用。这是在你想用栈上的智能指针比如shared_ptr,linked_ptr ,还有STL对象的时候不得不关注的一个核心问题。我们不得不痛心地说，如果你一定要在Objective-C++代码里抛出异常，那就请一定使用C++的异常。永远不要重新抛出一个Objective-C的异常，也不允许在异常块即@try,@catch,@finally 里生成栈上的 C++对象(比如std::string, std::vector 等).

### nil检查 ###

   仅在校验逻辑流程时做nil检查。

   使用nil检查不是为了防止程序崩溃，而是校验逻辑流程。向一个空对象发送一条消息是由Objective-C运行时处理的。方法没有返回结果，你也可以安心走下去.然而这里也有一种，依执行架构不同而返回尺寸和OS X的版本(这段不懂)(见)Apple's documentation for specifics 。

   注意这里和C/C++的空指针检查是完全不同的，在那些环境里，并不处理空指针情况并可能导致你的应用程序崩溃。不过你仍要自己确保提领的指针不为空。

### BOOL类型陷阱 ###

  整形的转换为 BOOL 型的时候要小心。不要直接和YES做比较。

   BOOL 在Objective-C里被定义为unsigned char，这意味着它不仅仅只有YES (1)和NO (0)两个值。不要直接把整形强制转换为BOOL 型。常见的错误发生在把数组大小，指针的值或者逻辑位运算的结果赋值到BOOL型中，而这样就导致BOOL 值的仅取决于之前整形值的最后一个字节，有可能出现整形值不为0但被转为NO的情况。应此把整形转为BOOL型的时候请使用ternery操作符，保证返回YES 或NO 值。

   在BOOL，_BOOL 以及bool (见C++ Std 4.7.4, 4.12以及C99 Std 6.3.1.2)之间可以安全的交换值或转型。但BOOL 和Boolean 之间不可，所以对待Boolean 就像上面讲的整形一样就可以了。在Objective-C函数签名里仅使用BOOL 。

   对BOOL值使用逻辑运算(&&, ||, ! )都是有效的，返回值也可以安全的转为BOOL型而不需要ternery操作符。
   
iOS代码 

```
// AVOID  
- (BOOL)isBold {  
  return [self fontTraits] & NSFontBoldTrait;  
}  
- (BOOL)isValid {  
  return [self stringValue];  
}  
  
// GOOD  
- (BOOL)isBold {  
  return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;  
}  
- (BOOL)isValid {  
  return [self stringValue] != nil;  
}  
- (BOOL)isEnabled {  
  return [self isValid] && [self isBold];  
}  
```

   还有，不要把 BOOL 型变量直接与 YES 比较。这样不仅对于精通C的人很有难度，而且此条款的第一点也说明了这样做未必能得到你想要的结果。
   
iOS代码 

```
// AVOID  
BOOL great = [foo isGreat];  
if (great == YES)  
  // ...be great!  
  
// GOOD  
BOOL great = [foo isGreat];  
if (great)  
  // ...be great!  
```

## 属性 ##

   属性总的来说是遵循如下告诫的: 属性是Objective-C 2.0的特性，所以只能跑在iPhone以及MacOS X 10.5(leopard)或更高的版本。点表示法访问属性是不被允许的。

### 命名 ###

   一个有属性关联实例变量都要在后面加下划线，而该属性的名称就是实例变量不加尾部的下划线的名字。

   使用@synthesize 标识以正确的重命名属性。
   
iOS代码

``` objc
@interface MyClass : NSObject {  
 @private  
  NSString *name_;  
}  
@property(copy, nonatomic) NSString *name;  
@end  
  
@implementation MyClass  
@synthesize name = name_;  
@end  
```

### 位置 ###

   在类接口声明里，属性的声明必须紧挨着实例变量块声明之后。类定义里，属性的定义必须紧挨着@implementation块。缩进和@interface或@implementation是一样的。    
   
iOS代码 

``` objc
@interface MyClass : NSObject {  
 @private  
  NSString *name_;  
}  
@property(copy, nonatomic) NSString *name;  
@end  
  
@implementation MyClass  
@synthesize name = name_;  
- (id)init {  
...  
}  
@end  
```

### 对字符串使用拷贝特性 ###

   NSString 类型的属性应该永远声明为带有copy attribute 

   这点是因为NSString 的setters 永远使用copy 而不是retain 。

   永远不要对CFType属性使用synthesize 

   CFType类型应该永远遵守@dynamic的实现规则。

   因为CFType类型不能有reatin 属性特性，开发者必须自己维护。(下面不懂，原文In the rare case that you do actually want assignment it is better to make that completely clear by actually implementing the setter and getter and commenting why that is the case. )

  列出所有的实现指令 

  为所有属性使用实现指令，即使默认是@dynamic

  尽管@dynamic是默认的，显式的列出所有属性实现指令还是让类中的每个属性更加清楚。
  
iOS代码 

``` objc
// AVOID  
@interface MyClass : NSObject  
@property(readonly) NSString *name;  
@end  
  
@implementation MyClass  
.  
.  
.  
- (NSString*)name {  
  return @"foo";  
}  
@end  
  
// GOOD  
@interface MyClass : NSObject  
@property(readonly) NSString *name;  
@end  
  
@implementation MyClass  
@dynamic name;  
.  
.  
.  
- (NSString*)name {  
  return @"foo";  
}  
@end  
```

### 原子性 ###

   属性使用过犹不及。默认情况，所有的synthesized setters 以及 getters  默认都是原子性的，显然所有的get，set方法都过度使用了同步方法。所以除非你确实需要原子性，否则都将你的属性声明为nonatomic 

### 点表示法 ###

   我们禁止使用点表示法是基于如下理由的:      
   
   * 点表示法不过是标准函数调用的语法糖，它的可读性很有争议。也不过就是换个形式调用函数而已。      
   * 它使得提领操作含糊不请.首先看: [foo setBar:1] 很清楚你就明白这是Objective-C对象上的操作。那么看 foo.bar = 1 就不太清楚到底是对Objective-C的对象还是结构体/公用体/C++类进行操作了。      
   * 它使得函数调用看起来就像getters         
   
iOS代码 
 
```objc
NSString *upperCase = @"foo".uppercaseString;  
```
     
   不仅使人困惑，也很难在代码审核的时候检查出来。     
   
   * 它隐藏了函数调用
    
   iOS代码 
   
```
bar.value += 10;  
```
 这实际上做了两次函数调用(一次get一次set)，而如果你的类很复杂的话，你发现一大堆事都在幕后做完了。

## Cocoa模式 ##

### 委托模式 ###

 委托对象不应该是retain 

 一个实现委托模式的类应该:    
   1. 实例变量命名为delegate_ 以显示这是个委托       
   2. 因此，访存方法名就为delegate 和setDelegate      
   3. delegate_ 变量不能设为retain       

### MVC模式 ###

   * 将模型与视图分离，将控制器从视图和模型中分离，回调APIs使用@protocol 

   * 模型与视图分离: 不要对模型或数据源的表现形式做任何假设。保持数据与表现层的接口的抽象性。模型不对视图有任何了解(一个好的实践就是询问你自己是否能对数据有多个不同形式的表现方法)

   * 控制器从视图和模型中分离: 不要把"业务逻辑"放到视图相关的类；这会导致代码可复用性下降。让控制器的类承管代码，但不要对表现形式做太多假设。

   * 用@protocol 定义回调APIs: 如果不是所有的方法都必须实现就使用@optional (例外:在Objecitve-C 1.0的时候，@optional 还不可用，所以用类别去定义一个"非标准协议(informal protocol)")



最后的最后，这篇文章会尽量的完善，不停的矫正，以便让自己形成一个良好的代码习惯
