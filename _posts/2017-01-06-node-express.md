---
layout: post
title: express+mongoose
categories: [node]
tags: [node]
description: 使用express做简单后台接口增删改查
---


## 配置安装express和mongoose
	
express比较简单
``` 
	npm express -g 就可以了
	express '项目名称'
	cd '项目'
	npm init (配置package)

```


mongoose 先去[官网](http://www.mongodb.org/)下载mis安装包，一路next;
```
	1.创建D:/MongoDB(monggo启动文件),D:/MongoDBData(monggo数据),D:/MongoDBStart(快捷启动),
	2.解压mongodb-win32-i386-1.8.1.zip到D:/MongoDB
	3.在D:/MongoDBStart下面新建MongoDB.bat，内容写入mongod --dbpath D:\MongoDBData，每次只要双击这个就可以启动mongo了
	4.配置环境变量D:/MongoDB/bin/到系统环境变量中
```


[这是我的项目地址](https://github.com/2016caicai/express-mongo)