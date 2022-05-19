---
title: Android Studio 手动关联源码
date: 2022-05-16 17:53:00 +0800
categories: [技术]
tags: [Android]
render_with_liquid: false
---

## 备注
如果只是方便在 `Android Studio` 中查看 `Android Framework` 的源码，那么最好的方法还是直接通过 `Android Studio` 的 `Android SDK` 页面下载。本文描述的问题和解决方案是在网络出问题的情况下遇到的，正常开发过程应该不会有类似的问题，仅做记录和参考。

## 问题
可能是网络出了问题，当通过 `Android Studio` 下载 `API Level 31` 的源码时，总是提示失败，到 SDK 配置的页面， 在勾选 `Show Package Details` 的情况下，也没看到 `API Level 31` 的 source 下载选项，误以为是不是 google 没有提供这个版本的源码，但是项目的 `Compile SDK Level` 正好配置的 31，在这种情况下，如果想看 `Android framework` 的源码，只能通过修改 `Compile SDK Level` 和 手动关联源码的方式来解决。项目上还有其他协同开发的同事，冒然修改 Compile SDK 不是很合适。那就剩手动关联源码的解决方案了。

## 方案
要手动关联源码，首先需要知道 `Android Studio` 的源码关联机制是怎么工作的，这个有很多资料可查，不在本文详述。  
解决步骤：
* 找到存储源码和对应 SDK 版本关联关系的配置文件
* 手动添加下载的源码和 SDK 的关联关系

### 配置文件
在 `Android Studio` 上，关联关系存储在如下位置：  
~/Library/Application Support/Google/AndroidStudio{version}/options/jdk.table.xml  
比如我本地环境的配置文件存储在：~/Library/Application Support/Google/AndroidStudio2021.1/options/jdk.table.xml  

### 添加配置
如果相关 SDK 的配置节点已经存在，则只需检查 `<sourcePath>` 节点是否存在，如果不存在，则添加对应的路径
```
<jdk version="2">
  <name value="Android API 31 Platform" />
  <type value="Android SDK" />
  <version value="version 11.0.11" />
  <homePath value="$USER_HOME$/Library/Android/sdk" />
  <roots>
    <annotationsPath>
      <root type="composite">
        <root url="jar://$USER_HOME$/Library/Android/sdk/platforms/android-31/data/annotations.zip!/" type="simple" />
      </root>
    </annotationsPath>
    <classPath>
      <root type="composite">
        <root url="jar://$USER_HOME$/Library/Android/sdk/platforms/android-31/android.jar!/" type="simple" />
        <root url="file://$USER_HOME$/Library/Android/sdk/platforms/android-31/data/res" type="simple" />
      </root>
    </classPath>
    <javadocPath>
      <root type="composite" />
    </javadocPath>
    <sourcePath>
      <root type="composite">
        <root url="file://$USER_HOME$/Library/Android/sdk/sources/android-31" type="simple" />
      </root>
    </sourcePath>
  </roots>
  <additional jdk="Android Studio default JDK" sdk="android-31" />
</jdk>
```

### 重启 Android Studio


 
