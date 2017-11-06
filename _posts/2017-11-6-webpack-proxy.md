---
layout: post
title: 关于proxy踩坑
categories: [webpack]
tags: [webpack]
description: webpack proxy做api跨域请求和服务器文件路径代理
---


## 目的

### 实现api跨域请求和服务器文件路径代理

前端为localhost: 8080
后端为localhost: 3333
在开发阶段使用webpack的devserver模拟服务端环境，因为使用前后端分离架构，前后端口不一样，所以需要跨域请求，方便开发更新，同时设置publicPath公共路径（相对于webpack.config文件），可以模拟访问前端的静态文件，由于publicPath与后段配置的静态文件路径不一样，所以需要设置文件路径代理

## 配置

设置devserver的proxy对象，
```
  devserver: {
    proxy:{
      ‘/api/*’：{
        target:host, 
        secure: boolean，
        bypass：function(req, res, proyOptions){
          if (rea.headers.accept.indexOf('html')!==-1){
            console.log('Skipping proxy for browser request.')
            return '/index.html';
          }
        }
      }，
      '/*.csv': {
        target: 'http://localhost:3333',
        secure: false
      }
    }
    // 数组对象
    proxy: [
      {
        context: ['/api-v1/**', '/api-v2/**'],
        target: 'https://other-server.example.com',
        secure: false
      }
    ]
  }
```
通过设置代理对象,‘/api/*’指在api路径下的所有请求，target为代理的请求域，'/*.csv'根域名下的所有.csv结尾的文件的请求，这边由于我的后端使用了koa-static，设置静态文件路径，所以可能和实际的文件路径有差异，这里我的文件是放在files里面，所以在开发阶段可以依靠publicPath的公共路径／files/*.scv来访问到,但是build到服务器上后，由于做了访问跳转，所有的请求都重定位到index.html,导致文件下载错误，所以为了需要用koa-static配置files静态路径，然后设置，proxy,target为服务器根目录域名，文件会自动挂载到静态路径上。
[还有更多的参数介绍在这里](http://webpack.github.io/docs/webpack-dev-server.html#proxy)
