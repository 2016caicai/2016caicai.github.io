---
  layout: post
  title: axios又掉坑
  categories: [http]
  tags: [http]
  description: axios 做服务请求
---

## 由来

之前做了一个vue-koa的前后台脚手架，想着里面大多数的库都更新了想着来个大更新，抛弃了vue-resource,假装潮流的用上了axios,结果又掉坑里面了，想着上次是直接掉坑弃axios,用上了fetch,这次一定要弄清楚

## 问题

后台接收不到request.body,且contentType时有时五如下图
![client post 代码](/images/axios/post.png "http")
![client delete 代码](/images/axios/delete.png "http")
![server delete 代码](/images/axios/server.png "http")
在axios拦截代码中可以看到delete的请求header为空，还有data也是undefined,但是在put请求中又是正常的(好气哦)
![console delete 代码](/images/axios/console.png "http")

## 原因

我决定看源码，mmp, 源码很少啊，一下就找到了就是下面这段
```
  // Axios.js
  utils.forEach(['delete', 'get', 'head', 'options'], function forEachMethodNoData(method) {
    /*eslint func-names:0*/
    Axios.prototype[method] = function(url, config) {
      return this.request(utils.merge(config || {}, {
        method: method,
        url: url
      }));
    };
  });

  utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
    /*eslint func-names:0*/
    Axios.prototype[method] = function(url, data, config) {
      return this.request(utils.merge(config || {}, {
        method: method,
        url: url,
        data: data
      }));
    };
  });
  // axios.js
  utils.forEach(['delete', 'get', 'head'], function forEachMethodNoData(method) {
    defaults.headers[method] = {};
  });

  utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
    defaults.headers[method] = utils.merge(DEFAULT_CONTENT_TYPE);
  });
```
上面自己定义了所有的axios[method],主要分两类，一种是get类，即不带data,且header 为空,一种是post类，后来想了一下为什么这么分，好像restful api就是这么定义的啊（打脸中），delete本来就是属于带params的，所以去掉了data


## 解决方案

### 霸王硬上弓

硬着头皮就是传data,借着axios,直接定义一个config如下骚操作,直接覆盖了axios的default config
![delete code1 代码](/images/axios/delete1.png "http")
这里没写header,是因为默认会判断传入的值是什么来替换header
```
  // Hook up interceptors middleware
  var chain = [dispatchRequest, undefined];
  var promise = Promise.resolve(config);

  this.interceptors.request.forEach(function unshiftRequestInterceptors(interceptor) {
    chain.unshift(interceptor.fulfilled, interceptor.rejected);
  });

  this.interceptors.response.forEach(function pushResponseInterceptors(interceptor) {
    chain.push(interceptor.fulfilled, interceptor.rejected);
  });
```
这里截获了请求，dispatchRequest就是调用了判断类型的方法，
```
  transformRequest: [function transformRequest(data, headers) {
    normalizeHeaderName(headers, 'Content-Type');

    if (utils.isFormData(data) ||
      utils.isArrayBuffer(data) ||
      utils.isBuffer(data) ||
      utils.isStream(data) ||
      utils.isFile(data) ||
      utils.isBlob(data)
    ) {
      return data;
    }
    if (utils.isArrayBufferView(data)) {
      return data.buffer;
    }
    if (utils.isURLSearchParams(data)) {
      setContentTypeIfUnset(headers, 'application/x-www-form-urlencoded;charset=utf-8');
      return data.toString();
    }
    if (utils.isObject(data)) {
      setContentTypeIfUnset(headers, 'application/json;charset=utf-8');
      return JSON.stringify(data);
    }
    return data;
  }],
```
 当传入data为object时更改contenttype为application/json;charset=utf-8

 ### 退而求其次

 当然是跟随restful 大佬啦，改成用params来传递，当然服务接口也得改，这里就不弄了，大概知道就好了