---
layout: post
title: vue响应数据绑定原理
categories: [vue]
tags: [vue]
description: vue数据绑定原理简单实现
---


## 核心
### Object.defineProperty
使用Object.defineProperty来重写对象的getter和setter数据访问方法，使用对象setter方法来设置监控对象更新，getter方法设置监控对象订阅。
### 发布订阅的设计模式
使用主题设置对vue对象的data属性进行数据监控，与绑定对应数据的node对象进行视图自动更新绑定，配合node对象便利来进行事件订阅。
## 具体代码
下面是html代码，以id为app的区块作为绑定数据区域。
```
  <div id="app">
    <input v-model="test" class="name"/>
    <p>{{test}}</p>
  </div>
```
vue 构造器对指定id容器进行绑定
```
  function Vue (options) {
    let { data, el } =options;
    this.data = data;
    let dataObj = this.data;
    objRebuild(dataObj, this)//对原对象属性重新设置
    var flagNode = nodeToFragment(document.getElementById(el),this)//更新node内容
    document.getElementById(el).appendChild(flagNode)
  }
  var vm = new Vue({
    el:"app",
    data:{
      name: 'rick',
      p: "i am p",
      test: "i am test"
    }
  })
```  
劫持所有的app下面的所有node元素
> 使用fragment把所有的node元素使用firstChild获取后识别属性重新赋值node内容后重新组装回app下面。
```
  function nodeToFragment (node,vm) {
    var flag = document.createDocumentFragment();
    var child;
    while( child = node.firstChild){
      initstate(child, vm)//数据绑定
      flag.appendChild(child)
    }
    return flag
  }
```
数据初始化绑定
```
  function initstate (node,vm) {
    //便利子层node这里只写了一层
    if(node.childNodes && node.childNodes.length){
      initstate.call(null,node.childNodes[0],vm)
      return
    }
    //检测{{}}内容
    var reg = /\{\{(.*)\}\}/;
    //node元素，遍历属性，查找v-model属性值
    if(node.nodeType === 1){
      var attr = node.attributes;
      for(var i =0;i<attr.length;i++){
        if(attr[i].nodeName == "v-model"){
          var name = attr[i].nodeValue
          node.value = vm.data[name];
          //监控input内容的改变，对vue实例的数据进行更新
          document.addEventListener('input', function (e) {
            vm.data[name] = e.target.value;  
          })
          node.value = vm.data[name]
          node.removeAttribute('v-model');
        }
      }
    }
    //文本元素直接赋值操作
    if(node.nodeType === 3 && reg.test(node.nodeValue)){
      node.nodeValue = trim(vm.data[RegExp.$1])
      new Watcher(vm, node, RegExp.$1);//添加监控
    }
  }
```
对数据进行初始化和绑定
```
  function objRebuild(obj,vm){、
    //实例化一个发布者，
    var dep = new Dep();
    //遍历vue实例下面的所有的属性
    Object.keys(obj).forEach((key) => {
      var val = obj[key]
      //对属性进行重新设置getter和setter
      Object.defineProperty(obj,key,{
        // 添加订阅者到队列
        get: function () {if(Dep.target)dep.addSub(Dep.target);return val},
        // 发布消息到所有订阅者
        set: function (newVal) {
          if(val === newVal) return ;
          val = newVal;
          dep.notify()
        }
      })
    })
  }
```
设置订阅者，这里非常巧妙的利用全局的对象Dep来添加到队列，
使用this.update更新方法来触发get方法设置该订阅者的初始值
```
  function Watcher (vm, node, name) {
    Dep.target = this;
    this.name = name;
    this.node = node;
    //this.value = undefined;
    this.vm = vm.data;
    this.update();
    Dep.target = null;
  }
  Watcher.prototype = {
    update: function () {
      this.get();
      this.node.nodeValue = this.value;
    },
    get: function () {
      this.value = this.vm[this.name];
    }
  }
```
发布者队列和方法
```
  function Dep () {
    this.subs = [];
  }
  Dep.prototype = {
    // 添加到队列
    addSub: function (sub){ this.subs.push(sub);},
    //发布消息广播 触发监听对象的update方法来重新设置监听对象的value值，从而更新所绑定的node的nodevalue，实现视图的更新
    notify: function () {
      this.subs.forEach(function (sub) {
        sub.update();
      })
    }
  }
```


![最终效果](/images/vue/vue.jpg "vue")