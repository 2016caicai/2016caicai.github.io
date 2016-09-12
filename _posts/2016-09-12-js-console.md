---
layout: post
title: js console提高开发效率
categories: [js ]
tags: [js ]
description: js
---

## console方法里面用得最多的就是console.log（）
与console.log（）用法差不多的有
			console.info('信息');
           console.error('错误');
           console.warn('警告');
## 占位符
支持的占位符有：字符（%s）、整数（%d或%i）、浮点数（%f）和对象（%o）
 console.log("%d年%d月%d日",2011,3,26);
 ![](/images/js/console.jpg "js console")
 
## 信息分组
console.group("第一组信息");
  
       　　　　console.log("第一组第一条:1111111");
  
       　　　　console.log("第一组第二条:2222222");
  
       　　console.groupEnd();
  
   　　    console.group("第二组信息");
  
       　　　　console.log("第二组第一条:333333");
  
       　　　　console.log("第二组第二条:444444444");
  
   　　    console.groupEnd();
   ![](/images/js/console1.jpg "js console1")
   
## 查看对象的信息
var info = {
	blog:"2016caicai.github.io",
	QQ:1196363729,
	message:"我只是一个切图仔"
};
console.dir(info);
![](/images/js/console2.jpg "js console2")

## 函数调用
    add3();
    function add3(){ return add2();}
    function add2(){return add1();}
    function add1(){return add();}
    function add(){console.trace(); console.log('1')}
	![](/images/js/console3.jpg "js console3")

## 判断变量是否是真
	var result = 1;
        　　console.assert( result );
        　　var year = 2014;
        　　console.assert(year == 2018 );
		
## 计时功能
console.time()和console.timeEnd()，用来显示代码的运行时间。
console.time("控制台计时器一");
    　　for(var i=0;i<1000;i++){
    　　　　for(var j=0;j<1000;j++){}
    　　}
    　　console.timeEnd("控制台计时器一");
	![](/images/js/console4.jpg "js console4")

## console.profile()的性能分析
性能分析（Profiler）就是分析程序各个部分的运行时间，找出瓶颈所在，使用的方法是console.profile()。
	function All(){
                alert(11);
    　　　　     for(var i=0;i<10;i++){
                    funcA(1000);
                 }
    　　　　    funcB(10000);
    　　    }
   
       　　function funcA(count){
       　　　　for(var i=0;i<count;i++){}
       　　}
  
       　　function funcB(count){
       　　　　for(var i=0;i<count;i++){}
       　　}
  
       　　console.profile('性能分析器');
       　　All();
       　　console.profileEnd();
	   ![](/images/js/console5.jpg "js console5")
	   这个在火狐浏览器下得firebug才有其实本身浏览器就有profiler测试，但是比较多的内容

