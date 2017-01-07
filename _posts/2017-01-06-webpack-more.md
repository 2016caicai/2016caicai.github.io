---
layout: post
title: webpack多页面构建
categories: [js ]
tags: [js ]
description: webpack工程化
---


## 使用场景
之前以为webpack只是用于单页面应用，都怪自己辣鸡！！
最近公司项目重构，不知道要用什么架构，技术负责说用jq....(天呀，随便来个vue,ng,react也好啊)
我就在想怎么模块化？AMD,CMD+gulp全家桶?
直到看到下面某位大神，我赶紧fork先
[多页面webpack](https://github.com/2016caicai/webpack-MultiPage-static)
当然还有单页面的配置
天呀，一个webpack就可以做工程打包了。

## 下面说一下坑，每个人的项目可能结构都不一样，所以要自己去配置好路径，最简单当然是直接下载别人的快速启动啦

这是我的目录结构
![](/images/js/webpack-project.jpg "project js")
dist是build出来后的文件我把font也build在css里面，方便css调用
说一下比较重要的config配置;
![](/images/js/webpack-project2.jpg "project2 js")
大神通过这个函数来比较文件chunk，通过先配置config里面的入口文件，对比后再调用相应view,调用htmlplus插件，通过chunk来按需加载，具体可以看一下配置；
这里不知道为什么我的glob取出来的路径是\的而我们配置的是/的，所以之前一直报错;也replace不了;

## 坑二
jquery全局引用，因为我这里是用的bootstrap,依赖全局jquery，我比较懒，为了方便我在js下面建立一个common专门放全局js库或插件
![](/images/js/webpack-project3.jpg "project3 js")
直接使用copyplus插件来复制全局库
我看网上是很多在webpack里面配置全局的，然后在js里面require;
[like this](http://blog.csdn.net/yiifaa/article/details/51916560)

## 未解决问题，dev-server不能监听文件变化了


