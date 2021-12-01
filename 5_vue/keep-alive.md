### 对于keep-alive的理解，他是如何实现的，具体缓存是什么？

keep-alive是vue内置的一个组件，而这个组件的作用就是能够缓存不活动的组件，一般情况下，组件进行切换的时候，默认会进行销毁，如果有需求，某个组件切换后不进行销毁，而是保存之前的状态，那么就可以利用keep-alive来实现。
keep-alive是一个虚拟的组件，不会渲染成真正的dom。
比如：查看一个搜索页面的详情，再返回看这个搜索列表的时候。
一、.生命周期钩子
被包含在 <keep-alive> 中创建的组件，会多出两个生命周期的钩子: activated 与 deactivated

- activated
在组件被激活时调用，在组件第一次渲染时也会被调用，之后每次keep-alive激活时被调用。

- deactivated
在组件被停用时调用

二. 在keep-alive上有两个属性以及配合router-view使用

有两个参数include - 字符串或正则表达，只有匹配的组件会被缓存exclude - 字符串或正则表达式，任何匹配的组件都不会被缓存
```
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <keep-alive>
      <router-view />
    </keep-alive>
    
  </div>
</template>
```
这时默认是home和about组件都缓存了，因此可以使用include.
```
<keep-alive include="home">
   <router-view />
</keep-alive>

```
此外还可以使用路由中的meta属性
```
{
      path: '/',
      name: 'home',
      meta:{
        keepAlive:true
      },
      component: Home
    }

```
三、需要注意的地方
- <keep-alive> 先匹配被包含组件的 name 字段，如果 name 不可用，则匹配当前组件 componetns 配置中的注册名称。
- 当匹配条件同时在 include 与 exclude 存在时，以 exclude 优先级最高(当前vue 2.4.2 version)。比如：包含于排除同时匹配到了组件A，那组件A不会被缓存。
- 包含在 <keep-alive> 中，但符合 exclude ，不会调用activated 和 deactivated。