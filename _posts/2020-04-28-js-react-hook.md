---
  layout: post
  title: react+hook
  categories: [docker]
  tags: [docker]
  description: react hook下做数据共享
---


>### 背景

自从使用react hook后，基本上数据都封在了hook内部，或者是通过props来进行传递，无独有偶，在我做看板可视化的时候，我把看板设计成了两层tab的结构，意味着两层路由的情况，数据有内容有交叉，也无法通过props来传递，这时候就想到组件通信的第三种方式 第三方状态管理<br>
常用的第三方状态管理 比如 <code>redux</code> <code>mobx</code><br>
就在我纠结于要选哪个的时候， 我看到了...<br>
![images](/images/js/reacthook/usereducer.png)<br>
<code>React.createContext</code> <code>useContext</code> <code>useReducer</code>是 React 16.8 引入的新 API<br>

>### React.createContext
<code>React.createContext</code>： 返回一个上下文对象，里面包含一个Provider组件，使用这个组件可以解决旧Context无法订阅上下文变化的问题
首先创建Context
```
  import React from 'react';

  export const initValue:{
    [props: string]: []
  } = {
    running: [],
    product: [],
    handle: [],
    cache: {}
  }

  export default React.createContext(initValue)
```
在上层组件使用Porvider订阅上下文
```
<Context.Provider value={[store, dispatch]}>
  <MyComponent />
  <Switch>
  <Route path={`${match.url}/product`} component={ProductAsync} />
  <Route path={`${match.url}/handle`} component={HandleAsync} />
  <Route path={`${match.url}/running`} component={RunningAsync} />
  <Redirect from={match.url} to={`${match.url}/product`} />
</Switch>
</Context.Provider>
```
这里 <code>MyComponent</code> 和兄弟路由可以通过获取<code>Context</code>来共享store,和触发dispatch

>### useReducer
<code>useReducer</code>：主要实现简单的store 和 dispatch,也是useState的底层实现。
具体使用如下：<code>const [state, dispatch] = useReducer(reducer, initialArg, init);</code>

这里的reducer也和我们平时写的reducer一样
```
  const [store, dispatch] = useReducer(function (state: any, action: any) {
    switch(action.type) {
      case 'updateStore': return { ...state, [action.key]: action.data };
      case 'getAsyncRuning': _getAsyncRuning(action); return state;
      case 'getAsyncProduct': _getAsyncProduct(action); return state;
      case 'updateCache': return {...state, cache: action.data};
      default: throw new Error('Unexpected action');
    }
  }, initValue);
```
每次调用dispatch会返回最新的store.
```
  const _getAsyncHand = async() => {
    const data = await api.get('/ssmp-customized/api/board/board_hand', {params: { companyId: '' }})
      .then(({data}: any) => data.data);
    dispatch({type: 'updateStore', data, key:'handle' })
  }
```

这里经过测试，发现直接把reducer放在useReducer里面会触发两次dispatch, [Stack Overflow](https://stackoverflow.com/questions/54715188/react-hook-rendering-an-extra-time/54716601#54716601)上也发现很多人出现了这个问题,原因其实也是和我们hook的特性，或者说是函数组件有关，每次更新状态会重新执行函数组件，包括我们的<code>function reducer</code>,usereducer不知道这个reducer是否是有变化，所以useReducer 的dispatchAction会重新触发

解决方案
```
const reducer = useRef(function (state: any, action: any) {
    switch(action.type) {
      case 'updateStore': return { ...state, [action.key]: action.data };
      case 'getAsyncRuning': _getAsyncRuning(action); return state;
      case 'getAsyncProduct': _getAsyncProduct(action); return state;
      case 'updateCache': return {...state, cache: action.data};
      default: throw new Error('Unexpected action');
    }
  });
```
万能的useRef用来缓存reducer;


>### useContext
<code>useContext</code>：可访问全局状态，避免一层层的传递状态

在目标组件内使用
```
  const [store = { running: [], cache: {} }, dispatch] = useContext(Context);
```

### 最后效果

![效果](/images/js/reacthook/effect.gif)
所有数据由上层路由管理，切换路由也可以直接由store中提取缓存数据，算是一丢丢的性能优化










