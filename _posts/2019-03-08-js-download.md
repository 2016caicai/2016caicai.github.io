---
  layout: post
  title: download图片
  categories: [js]
  tags: [js]
  description: 兼容ie9+的下载图片方法
---

首先祝所有的女同胞节日快乐！

>### 背景

项目中有一个下载图片的需求，大概就是浓缩一个右键另存为的功能吧。。。

>### 问题

网上一堆使用a标签同时设置download属性，先不说有没有用，先上兼容图<br>
![download 兼容](/images/js/download1.png "js")<br>
首先不兼容ie,所以这个方案不通过<br>
<label style="color:gray">(ps:亲测a标签加上download, 假如href是网络资源还是会触发浏览器默认打开图片??)</label><br>


>### 解决方案

1.首先使用[canvas.toDataURL](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/toDataURL)把网络图片转化为base64,这里需要注意使用这个方法只对同源文件有作业，因为canvas限制了跨域资源的访问;<br>
<label style="color:gray"> (这里只能使用cross跨域设置解决，但是这个配置需要前后台一起配置，前端资源配置img.setAttribute("crossOrigin",'anonymous/白名单')，后台设置跨域*/白名名单)</label><br>
[关于corss跨域img](https://developer.mozilla.org/zh-CN/docs/Web/HTML/CORS_enabled_image)<br>
获取base64后直接改图片的mimeType，强制改成steam流类型的，替换base64数据类型‘image/octet-stream’，浏览器就会自动帮我们另存为
```
  /*
  * el is a img element
  */
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  canvas.width = el.clientWidth;
  canvas.height = el.clientHeight;
  ctx.drawImage(el, 0, 0, el.clientWidth, el.clientHeight);
  const imgData = canvas.toDataURL('png').replace('image/png', 'image/octet-stream');
  const save_link = document.createElement('a');
  save_link.href = imgData;
  save_link.download = `党员头像${(new Date()).getTime()}.png`;
  save_link.click();
```

上面是非ie浏览器的解决方案，由于ie不支持download属性

2.创建iframe,并且修改iframe的src为网络图片资源，同时监听iframe的onload事件，在onload事件里面调用浏览器的[execCommand](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)(SaveAs)来把资源文件保存下来，记得在onload后面要删除iframe
```
  const tagIframe = document.createElement('iframe');
  tagIframe.id = 'IframeReportImg';
  tagIframe.name = 'IframeReportImg';
  tagIframe.src = el.src;
  tagIframe.onload = () => {
    window.frames['IframeReportImg'].document.execCommand('SaveAs');
    $('#IframeReportImg').remove();
  };
  $('body').append(tagIframe);
```






