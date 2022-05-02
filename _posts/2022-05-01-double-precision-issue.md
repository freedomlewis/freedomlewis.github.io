---
title: Kotlin double 精度问题
date: 2022-05-01 14:18:00 +0800
categories: [技术]
tags: [Android]
render_with_liquid: false
---

## 问题

在项目上有同事写了一段代码，主要功能是将用户输入的纯数字字符串按照美国货币格式展示，比如输入 "1234" -> "1,234"。他的实现方法是先将字符串转换成 double ，再通过 NumberFormat 将其格式化为货币格式。在字符串长度小于16时，能正常工作，但是当字符串长度大于等于16时就会出现异常，比如用户输入的字符串是 "9999999999999999" (16 个 9)，期望展示的结果是 "9,999,999,999,999,999"，实际结果是 "10,000,000,000,000,000"。
开始大家认为是输入的数字过大，超过了 double 所能表示的数据范围，数据溢出导致结果出错。这个问题超出了我的知识边界，所以一开始我也没想清楚为什么？但是心里产生了一个疑问：如果真是数据溢出了，那么 double 能够处理的数据范围是多少？带着这个问题，我在网上查阅了相关资料，大体弄明白了这个问题的原因。  

## double 在计算机中是如何存储的？

参考内容里面列出的文章，都很详细的介绍了浮点数的存储原理，我在这里再简单总结一下自己的理解：

下面这张图清晰的描述了双精度浮点数的存储原理

![double-float-format](/assets/img/posts/double-precision-float-format.png)


## 参考内容  

[Double-precision floating-point format](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)  
[浮点数的二进制表示](https://www.ruanyifeng.com/blog/2010/06/ieee_floating-point_representation.html)  
