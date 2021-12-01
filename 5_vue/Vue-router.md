### 10、Vue-router 路由守卫有哪些 ？
vue路由守卫有三种，分别为：
- 1、全局守卫“router.beforeEach”；
- 2、组件内的守卫“beforeRouteEnter”；
- 3、路由独享守卫“beforeEnter”。
#### 一、全局守卫
1. router.beforeEach((to,from,next)=>{})
2. 回调函数中的参数，to：进入到哪个路由去，from：从哪个路由离开，next：函数，决定是否展示你要看到的路由页面。
3. 如下例：router/index.js中设置全局守卫,index.js中，有一个路由实例化对象router。
在main.js中设置守卫已是全局守卫。如下，判断to.path当前将要进入的路径是否为登录或注册，如果是就执行next()，展示当前界面。如果不是，就弹出alert，然后移至登录界面。这样就可实现，用户在未登录状态下，展示的一直是登录界面。
```
router.beforeEach((to,from,next)=>{
    if(to.path == '/login' || to.path == '/register') 
    { next(); }
    else{
     alert('您还没有登录，请先登录');next('/login');}
   })
```
4. 全局后置钩子router.afterEach((to,from)=>{})
5. 判断store.gettes.isLogin === false 是否登录
- 只有两个参数，to：进入到哪个路由去，from：从哪个路由离。
- 如下，每次切换路由时，都会弹出alert，点击确定后，展示当前页面,路由不会变化
```
router.afterEach((to,from)=>{  alert("after each");})
```
5. 判断store.gettes.isLogin === false 是否登录
#### 二.组件内的守卫
1. 到达这个组件时，beforeRouteEnter:(to,from,next)=>{}
- 在Admin.vue文件中，点击转到admin路由时，执行beforeRouteEnter函数
- to，from参数与上面使用方法一致。next回调函数略有不同。
- 如下例，data 组件内守卫有特殊情况，如果我们直接以beforeRouteEnter:(to,from,next)=>{ alert("hello" + this.name);}进行访问admin页面，会发现alert输出hello undefined。这是因为，现在访问不到我们的data属性，执行顺序是不一致，这与的声明周期有关。在执行完之前，data数据还未渲染。所以这里，next()会给一个对应的回调，帮助完成。
![3cec3a71fc38f1dabcf7cb92f79ff1cd.png](en-resource://database/4513:1)
2. 离开这个组件时，beforeRouteLeave:(to,from,next)=>{}
- 点击其他组件时，判断是否确认离开。确认执行next()；取消执行next(false)，留在当前页面。
![2870330bf0c77ad13c383bb8dd0d2135.png](en-resource://database/4515:1)
#### 三、路由独享的守卫
1. beforeEnter:(to,from,next)=>{}，用法与全局守卫一致。只是，将其写进其中一个路由对象中，只在这个路由下起作用。