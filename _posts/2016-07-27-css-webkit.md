---
layout: post
title: css高级属性
categories: [css ]
tags: [css ]
description: css
---

## webkit
1. 蒙板属性 -webkit-mask（相关的属性有-webkit-mask-clip、 -webkit-mask-position 和-webkit-mask-repeat等）
```
    <img src="kate.png" style="-webkit-mask-box-image: url(mask.png) 75 stretch;">

```
![](/images/css/mask1.jpg "css mask")
添加蒙板图片
![](/images/css/mask2.jpg "css mask2")
原图片
![](/images/css/mask3.jpg "css mask3")
添加后的效果
![](/images/css/mask4.jpg "css mask4")
还有更多蒙板效果
``` 
    <img src="kate.png" style="-webkit-border-radius: 10px; -webkit-mask-image: -webkit-gradient(linear, left top, left bottom, from(rgba(0,0,0,1)), to(rgba(0,0,0,0)))">
```
[cssmask详细地址](https://webkit.org/blog/181/css-masks/)


2. 字体边框 -webkit-text-stroke（配合使用color: transparent属性，你还可以创建镂空的字体）

![](/images/css/text-stroke1.jpg "css stroke1")
```
    h1 {-webkit-text-stroke: 2px blue}
```
![](/images/css/text-stroke2.jpg "css stroke2")
```
    h1 {color: transparent;-webkit-text-stroke: 1px red;}
```
3. 强制断行-webkit-nbsp-mode（可以改变&nbsp;空白符的行为，强制文字在它被用到的地方断行，设置值为space）
4. 缩放 zoom: reset
通常来说，zoom是一个IE专用的属性。但是webkit也开始支持它了，而且使用值reset，webkit可以实现不错的效果(有趣的是，IE不支持这个值)。它让你重设掉浏览器中正常的缩放行为——如果某个元素被声明了zoom:reset，页面上的其它元素在用户放大页面的时候都会跟着放大。
注：其实，我们常用来禁用chrome强制字体大小的时候用到的-webkit-text-size-adjust:none;也是可以实现类似的效果，不同的是，设置该属性的元素内的文字不会被放大/缩小，但是页面上的其它元素则会变化——神飞
5. margin不合并 -webkit-margin-collapse
![](/images/css/margin-collapse-1.jpg "css collapse")
通常，两个相邻的元素的margin会折叠起来(collapse)。这意味着第一个元素的底部的边距和第二个元素的头部边距会被合并到一起。
最常见的例子就是两个相邻的<p>元素会共享他们的margin值。想要控制这个表现，我们可以使用-webkit-margin-collapse及其分拆后的-webkit-margin-top-collapse、-webkit-margin-bottom-collapse等属性。默认值是collapse，值separate则停止共享margin值，也就是说，第一个元素的底部边距和第二个元素的头部边距会正常叠加
6. 倒影 -webkit-box-reflect
![](/images/css/box-reflex.jpg "css reflect")
还记得几乎每个网站都把他们的网站logo或者头部的文字做成倒影的那个年代吗？谢天谢地，那个年代已经过去了，但是如果你要在一些按钮、导航、或者其他UI元素上更好的使用这个技术，-webkit-box-reflect是更好的选择。
这个属性接受above、below、left和right四个关键词，它们设置倒影的方向，它们和一个设置元素和它的倒影建的距离的数字一起使用。同时，蒙板图片也是同样支持的(看上面的-webkit-mask部分，不要搞混了哈)。倒影会自动生成并对布局没有影响。下面的元素只用了CSS，第二个按钮用了-webkit-box-reflect属性。