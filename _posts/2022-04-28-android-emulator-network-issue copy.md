---
title: Android 模拟器不能使用电脑的代理网络
date: 2022-04-28 21:24:00 +0800
categories: [Tech]
tags: [Android]
render_with_liquid: false
---

## 问题

做 Android 开发经常需要和模拟器打交道，在特定的业务场景下，需要模拟器能够使用电脑的 VPN 网络，不做一些特别的配置，模拟器是不能使用电脑 VPN 网络的。要解决模拟器的网络问题，需要弄清楚下面三个问题：
* 模拟器网络工作原理
* 网络代理的工作原理
* 如何让模拟器使用电脑的网络代理

这三个问题都能在 Google 给的 [模拟器网络搭建](https://developer.android.com/studio/run/emulator-networking) 找到答案

## 模拟器网络工作原理

官方文档在开始简略的介绍了模拟器的网络工作原理，总结下面几条
* 模拟器具备独立的网络系统，与开发机和其他模拟器的网络系统隔离
* 模拟器无法直接通过网络访问开发机或其他模拟器
* 模拟器运行独立的 router 系统，管理的 IP 地址段为 10.0.2/24
* 模拟器地址空间已经按照规则预先分配  

    IP地址     | 描述
    ----------| -------------
    10.0.2.1  | 网关
    10.0.2.2  | 主机环回地址的别名，即对应开发机的 localhost
    10.0.2.3  | 模拟器的第一 DNS 服务器地址
    127.0.0.1 | 模拟器的环回地址，不对应开发机的 127.0.0.1

## 代理的工作原理

 网络代理和现实生活中的代理，概念一致，即作为客户端和服务端的中间人，代理客户端所有的请求，从服务端拿到资源后，再返回给客户端。客户端和服务端不直接联系。
 具体的原理可以参考 [Proxy server](https://en.wikipedia.org/wiki/Proxy_server)

## 如何让模拟器能够使用电脑的代理

 如果能够给模拟器设置一个代理服务器，同时这个代理服务器又把所有请求转发给开发机的代理服务器，似乎就能解决问题。这个方案实施，需要满足两个条件
 * 模拟器可以设置全局网络代理
 * 模拟器的代理服务器可以转发请求给开发机的代理服务器

 第一个条件很简单，因为模拟器本身就支持设置全局代理。第二个条件要怎么实现呢？  
 在模拟器网络工作原理这一节，我们知道，模拟器的网络是独立于开发机的网络的，有一套独立的路由体系。但是这种独立并非完全没有联系，在预制的 IP 地址中，我们可以知道 10.0.2.2 这个地址就对应了开发机的 localhost，也即开发机的 127.0.0.1 这个 IP。所以只要我们把代理服务运行的IP(10.0.2.2) 和 端口(比如: 7890) 设置到模拟器的全局代理上，就可以让模拟器使用开发机的代理。

![wifi-detail](/assets/img/posts/android-emulator/wifi_detail.png)  
![config-proxy](/assets/img/posts/android-emulator/config_proxy.png)  

## 参考内容  

[Localhost](https://en.wikipedia.org/wiki/Localhost)  
[Loopack](https://en.wikipedia.org/wiki/Loopback)  
[Proxy Server](https://en.wikipedia.org/wiki/Proxy_server)  
[VPN](https://en.wikipedia.org/wiki/Virtual_private_network)
