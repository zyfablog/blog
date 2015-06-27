---
layout: post
title: 介绍Android HAL的一篇好文章
---

从Linux driver到`HAL`再到`JNI`再到Java都讲了一个遍，算是对HAL有一个基本的了解了。

其中`hw_module_t`的设计非常巧妙，每个module都会有自己的特定函数，而HAL是不知道的，所以HAL就提供了一个open函数，这个函数可以获取`HW_Device_t`， 其实这个结构体和每个module的结构体是开始对齐的。

一般的做法是把`HW_deivce_t`当作module的结构体第一个变量，这样就保证了开始对齐，在jni中，只需要做一个指针强制转换就可以了。