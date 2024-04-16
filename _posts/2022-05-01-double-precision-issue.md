---
title: Kotlin double 精度问题
date: 2022-05-01 14:18:00 +0800
categories: [Tech]
tags: [Android]
render_with_liquid: false
---

## 问题

在项目上有同事写了一段代码，主要功能是将用户输入的纯数字字符串按照美国货币格式展示，比如输入 "1234" -> "1,234"。他的实现方法是先将字符串转换成 double ，再通过 NumberFormat 将其格式化为货币格式。在字符串长度小于16时，能正常工作，但是当字符串长度大于等于16时就会出现异常，比如用户输入的字符串是 "9999999999999999" (16 个 9)，期望展示的结果是 "9,999,999,999,999,999"，实际结果是 "10,000,000,000,000,000"。
开始大家认为是输入的数字过大，超过了 double 所能表示的数据范围，数据溢出导致结果出错。这个问题超出了我的知识边界，所以一开始我也没想清楚为什么？但是心里产生了一个疑问：如果真是数据溢出了，那么 double 能够处理的数据范围是多少？带着这个问题，我在网上查阅了相关资料，大体弄明白了这个问题的原因。  

## double 的存储原理

下面这张图清晰的描述了双精度浮点数的存储原理

![double-float-format](/assets/img/posts/double/double-precision-float-format.png)

## double 的数据范围

根据存储原理，11 位指数位能够表示的数据范围是 10^-308^ 和 10^308^ 之间的数字，具有完整的 15-17 位十进制数字精度。

## 整数的精度限制

53 位有效数字精度提供 15 到 17 位有效十进制数字精度 (2^53^ ≈ 1.11 × 10^16^)  
* 可以精确表示从 -2^53^ 到 2^53^（-9,007,199,254,740,992 到 9,007,199,254,740,992）的整数

* 2^53^ 和 2^54^ 之间的整数 = 18,014,398,509,481,984 舍入为 2 的倍数（偶数）

* 2^54^ 到 2^55^ 之间的整数 = 36,028,797,018,963,968 舍入为 4 的倍数

## double 与 string 的转换

按照 IEEE 754 标准，如果将一个至多 15 位有效数字的十进制字符串，转换为 double 表示，然后再转换为十进制字符串，则最终结果与原始的字符串匹配。

![string-to-double](/assets/img/posts/double/string-double-convert.png)

但是对于超过 15 位的字符串，只能够保证，将一个至少 17 位的 double 转换为十进制字符串，然后再转换为 double 表示时，最终结果必须与原始数字匹配。

![double-to-string](/assets/img/posts/double/double-to-string.png)

## 参考内容  

[Double-precision floating-point format](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)  
[浮点数的二进制表示](https://www.ruanyifeng.com/blog/2010/06/ieee_floating-point_representation.html)  
