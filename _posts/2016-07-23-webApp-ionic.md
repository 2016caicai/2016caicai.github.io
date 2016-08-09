---
layout: post
title: ionic
categories: [ionic ]
tags: [ionic ]
description: ionic
---


ionic

## 中文文档
[中文文档地址](http://www.ionic.wang/js_doc-index.html)




## 简介
Ionic(ionicframework)一款接近原生的Html5移动App开发框架 创建精彩的应用 从这里开始


## 开始我的应用开发

### 1. 首先说一下配置文件
(吐槽一下，这个环境配置完，我感觉像被掏空，实在恶心，相比mui太麻烦了，你至少需要以下内容Java JDK  Apache Ant Android SDK
  NodeJS)
[这篇学习笔记很好，安卓的和ios的都有（ps：ios的没有土豪机的就算了哈，要用到xcode）](http://wenku.baidu.com/link?url=i7oWgJ4nAvyaY7sGpzN7FQSp0bMwKnk6wZJ6euN6oKW1dbGrd5KjLsaqDNsEa-7zfB9iA3qeCWlL-rYkdS18KdvqW7nv7Fr7rk0lerhcqvO)

### 2. 开始一个应用
进入cmd cd 进入一个文件夹 运行 ionic start myApp tabs

![这样就创建了一个项目](/images/ionic/start.jpg "start app")
我这里报了一个node错误，但是还是可以构建成功，所以请忽略
运行项目 cd myApp 
选择配置Android环境
ionic platform add android
打包生成APP
ionic build android(如果是ios相应的要把android换成ios就可以了)
如果 build 错误一般就是SDK版本问题
或者下面这两个路径有问题
![](/images/ionic/build.jpg "build app")
我们可以进入本地项目看一下
![](/images/ionic/tree.jpg " app tree")
其中www里面就是我们要编写的内容
bulid 出来的安装包就是在 myApp\platforms\android\build\outputs\apk\下面（不过不知道为什么有两个，反正都可以）
如果你装了虚拟机或者连了手机，你可以ionic run android
就会自动安装打开app了。
如果你想用网页调试，你可以运行 ionic serve，就会马上弹出网页（这里最爽的是你不用频繁刷新页面了，你更改后ctrl s就会马上同步到网页上去） 
![](/images/ionic/serve.jpg " app serve")
这里我改了tabs.html里面的title,保存后回到网页

如下图所示
![](/images/ionic/s_after.jpg " app s_after")



## 参考资料

* [入门配置](http://www.itwap.net/ArticleContent.aspx?id=26)