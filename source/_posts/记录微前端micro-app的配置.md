---
title: 记录微前端micro-app的配置
date: 2022-09-23 22:31:25
---
## 介绍：

基座应用react

文章发布时，涉及到的依赖包版本：

```
{
  "@micro-zoe/micro-app": "^0.8.4",
}
复制代码
```

文章默认您已经大致浏览过micro-app官方文档，所以最好请先阅读官方文档：[cangdu.org/micro-app/d…](https://link.juejin.cn?target=https%3A%2F%2Fcangdu.org%2Fmicro-app%2Fdocs.html%23%2Fzh-cn%2Fstart "https://cangdu.org/micro-app/docs.html#/zh-cn/start")

我目前大多数的项目都是hash路由，本来想着基座和子应用都配置成hash路由，但是我没找到官方的例子，自己也没试出来。。。  
最终选择了 基座应用history路由、子应用hash路由的方式 *（注：后面已全部改为history路由）*

更多实现方式：[cangdu.org/micro-app/d…](https://link.juejin.cn?target=https%3A%2F%2Fcangdu.org%2Fmicro-app%2Fdocs.html%23%2Fzh-cn%2Fframework%2Fintroduce "https://cangdu.org/micro-app/docs.html#/zh-cn/framework/introduce")

下面说一下 需要做的修改：

# 基座应用

## 1、修改路由模式为history

### webpack搭建的

```
import { BrowserRouter, HashRouter, useRoutes } from 'react-router-dom'
...
function App() {
  return (
    <BrowserRouter>
      <RouteElement />
    </BrowserRouter>
  )
}
export default App
复制代码
```

webpack.config.js -- 2022.02.17更新  
config.js一共需要改两个地方:

```
...
output: {
  ...
  publicPath: '/', // history路由
  ...
},
...
...
devServer: {
  ...
  historyApiFallback: true, // history路由
},
复制代码
```

### 如果是umi的项目

需要查文档修改config

## 2、安装@micro-zoe/micro-app

```
npm install @micro-zoe/micro-app
复制代码
```

## 3、入口文件

引入microApp并start

```
import ReactDOM from 'react-dom'
import App from './App'
import microApp from '@micro-zoe/micro-app'

microApp.start({
  // 本地启动时 sockjs-node报错 要不然会一直刷新
  plugins: {
    modules: {
      app1: [
        {
          loader(code) {
            if (process.env.NODE_ENV === 'development' && code.indexOf('sockjs-node') > -1) {
              code = code.replace('window.location.port', 8052) // 这里需要修改成子应用的端口
            }
            return code
          },
        },
      ],
    },
  },
})
ReactDOM.render(<App />, document.getElementById('root-main')) // 这里index.html里的root div最好换个名字（root-main），以免和子应用的冲突
复制代码
```

## 4、路由配置中增加一个子应用的路由

这里不需要每个页面加一个路由  
比如，子应用有 order/page1 和 order/page2 这两个路由，这里只需要添加一个childApp（这个是自己起名的），然后主应用配置菜单的时候，配置 childApp#/order/page1 和 childApp#/order/page2 这两个菜单就可以了

```
  ...
  // 子应用1
  {
    path: 'childApp',
    element: () => import('@/pages/childApp'),
  },
  ...
复制代码
```

## 5、/pages/childApp/index.jsx

因为React不支持自定义事件，所以我们需要引入一个polyfill。  
`在<micro-app>标签所在的文件顶部`添加polyfill，注释也要复制。

```
/** @jsxRuntime classic */
/** @jsx jsxCustomEvent */
import jsxCustomEvent from '@micro-zoe/micro-app/polyfill/jsx-custom-event' // 

function Index() {
  // name(必传)：应用名称
  // url(必传)：应用地址，会被自动补全为http://localhost:3000/index.html
  // baseroute(可选)：基座应用分配给子应用的基础路由，就是上面的 `/my-page`
  return (
    <div>
      <h1>子应用</h1>
      <micro-app name="app1" url="http://localhost:8052/" baseroute="/childApp"></micro-app>
    </div>
  )
}
export default Index
复制代码
```

# 子应用

## webpack.config devServer增加headers支持跨域

webpack.config.js

```
  devServer: {
    headers: {
      'Access-Control-Allow-Origin': '*',
    },
  },
复制代码
```

chainWebpack

```
  config.devServer.headers({
    'Access-Control-Allow-Origin': '*',
  })
复制代码
```

## publicPath

**如果自动补全失败，可以采用运行时publicPath方案解决。**

这是由webpack提供的功能，会在运行时动态设置webpack.publicPath

**步骤1:**  在子应用src目录下创建名称为`public-path.js`的文件，并添加如下内容

```
// __MICRO_APP_ENVIRONMENT__和__MICRO_APP_PUBLIC_PATH__是由micro-app注入的全局变量
if (window.__MICRO_APP_ENVIRONMENT__) {
  // eslint-disable-next-line
  __webpack_public_path__ = window.__MICRO_APP_PUBLIC_PATH__
}复制代码Error复制成功
复制代码
```

**步骤2:**  在子应用入口文件的`最顶部`引入`public-path.js`

```
// entry
import './public-path'复制代码Error复制成功
复制代码
```

子应用配置完毕。

# 数据通信

我这里主要是实现2种场景：  
1、共享token和login的userInfo  
2、子应用token失效要通知基座应用，清除token、userInfo并跳转登录页

更多通信方式：[cangdu.org/micro-app/d…](https://link.juejin.cn?target=https%3A%2F%2Fcangdu.org%2Fmicro-app%2Fdocs.html%23%2Fzh-cn%2Fdata "https://cangdu.org/micro-app/docs.html#/zh-cn/data")

## 1、共享token和login的userInfo

### 基座应用`setGlobalData`

```
import microApp from '@micro-zoe/micro-app'

...
microApp.setGlobalData({ userInfo: login.userInfo })
...
复制代码
```

### 子应用获取数据

```
const globalData = window.microApp?.getGlobalData() // 返回全局数据
console.log('子应用拿到的 globalData', globalData);
复制代码
```

## 2、子应用token失效的情况

### 子应用`microApp.dispatch`

```
// 子应用token失效的时候 触发：
function handleTokenFail() {
  console.log('子应用token 失效')
  window.microApp?.dispatch({ type: 'token失效' }) // 关键代码
}
复制代码
```

### 基座应用监听

入口文件增加 `microApp.addDataListener` 代码

```
import ReactDOM from 'react-dom'
import App from './App'
import microApp from '@micro-zoe/micro-app'

microApp.start({
  // sockjs-node报错 要不然会一直刷新
  plugins: {
    modules: {
      app1: [
        {
          loader(code) {
            if (process.env.NODE_ENV === 'development' && code.indexOf('sockjs-node') > -1) {
              code = code.replace('window.location.port', 8052)
            }
            return code
          },
        },
      ],
    },
  },
})

function dataListener(data) {
  console.log('来自子应用my-app的数据', data)
}

/**
 * 绑定监听函数
 * appName: 应用名称
 * dataListener: 绑定函数
 * autoTrigger: 在初次绑定监听函数时如果有缓存数据，是否需要主动触发一次，默认为false
 */
microApp.addDataListener('app1', dataListener)

// // 解绑监听my-app子应用的函数
// microApp.removeDataListener(appName: string, dataListener: Function)

// // 清空所有监听appName子应用的函数
// microApp.clearDataListener(appName: string)

ReactDOM.render(<App />, document.getElementById('root-main'))
复制代码
```

或者`microApp.addDataListener`和`microApp.removeDataListener`也可以写在最外面的layout里

# 总结

其实大部分的用法和修改，官方文档都已经介绍的非常详细了，这里只是结合自己的项目的一些修改，可以看到micro-app相比qiankun对项目入侵非常少，而且涉及到的api很少且更容易理解。  
后面会持续关注：）
