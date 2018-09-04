---
title: c函数实用函数
date: 2018-09-03 10:56:00
tags:
---
我有一个疑问，Class类对象是在main函数之前的dyld阶段加载进内存的，那它是在内存的静态区还是堆区存储着？ 

好了，疑问先放着，我们进入正题，在iOS开发中我们经常遇到 浮点数的 四舍五入、进位、摸位 方法。

我们可以通过速查表查看所有的C函数，`https://baike.baidu.com/item/Objective-C函数速查实例手册?fr=aladdin`    

这里列出常用的几个函数：    
1，四舍五入法    
    `float numberToRound`;
    
```
    int result;

    numberToRound = 5.61;

    result = (int)roundf(numberToRound);

    NSLog(@"roundf(%.2f) = %d", numberToRound, result);

    // 输出 roundf(5.61) = 6



    numberToRound = 5.41;

    result = (int)roundf(numberToRound);

    NSLog(@"roundf(%.2f) = %d", numberToRound, result);

    // 输出 roundf(5.41) = 5

```

2、进位方法
    float numberToRound;    
    `int result`;    
    

    numberToRound = 5.61;

    result = (int)ceilf(numberToRound);

    NSLog(@"ceilf(%.2f) = %d", numberToRound, result);

    // 输出 ceilf(5.61) = 6



    numberToRound = 5.41;

    result = (int)ceilf(numberToRound);

    NSLog(@"ceilf(%.2f) = %d", numberToRound, result);

    // 输出 ceilf(5.41) = 6

3、摸位方法    
    `float numberToRound`;

    int result;

    numberToRound = 5.61;

    result = (int)floorf(numberToRound);

    NSLog(@"floorf(%.2f) = %d", numberToRound, result);

    // 输出 floorf(5.61) = 5



    numberToRound = 5.41;

    result = (int)floorf(numberToRound);

    NSLog(@"floorf(%.2f) = %d", numberToRound, result);

    // 输出 floorf(5.41) = 5

