---
layout: post
title: mui 
categories: [mui ]
tags: [mui ]
description: mui
---


mui
页面show方法使用心得，预加载页面时会把脚本也同时加载，如果有异步加载数据就会在父页面调用，这样有事会出现bug,
所以我喜欢把ajax封装成函数，然后在页面show的时候调用show方法，具体实例；
1. var ws=plus.webview.currentWebview();//获取当前页面
2. window.addEventListener('show',function(){
	cajax();
	})//页面 show后才调用方法
3. function cajax(){mui.ajax(url,{data,success,error});}