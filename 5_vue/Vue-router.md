# vue-router基本使用
- install安装
- index.js文件中import导入
- Vue.use(Router)
例子：
![在这里插入图片描述](https://img-blog.csdnimg.cn/139c484f55f344428f88507fa6e65904.png)
注意：`{path:'/login/:value'可以接受参数}`
- 创建路由实例，并配置路由
```
const routet = new Router({
	router,//注册上面的路由信息
	mode:'hash',//路由后面带上#
	//mode:'history',//history路由
})
```
- 把路由实例export出去

注意：chrildren中定义子路由信息

# router-view 

# router-link

```
// 字符串
<router-link to="apple"> to apple</router-link>
// 对象
<router-link :to="{path:'apple'}"> to apple</router-link>
// 命名路由
<router-link :to="{name: 'applename'}"> to apple</router-link>
//直接路由带查询参数query，地址栏变成 /apple?color=red
<router-link :to="{path: 'apple', query: {color: 'red' }}"> to apple</router-link>
// 命名路由带查询参数query，地址栏变成/apple?color=red
<router-link :to="{name: 'applename', query: {color: 'red' }}"> to apple</router-link>
//直接路由带路由参数params，params 不生效，如果提供了 path，params 会被忽略
<router-link :to="{path: 'apple', params: { color: 'red' }}"> to apple</router-link>
// 命名路由带路由参数params，地址栏是/apple/red
<router-link :to="{name: 'applename', params: { color: 'red' }}"> to apple</router-link>
```
（一）`<router-link>`组件的属性有：

to 、replace、 append、  tag、  active-class、 exact 、 event、  exact-active-class

1、to（必选参数）：类型string/location

2、tag：类型: string    默认值: "a" 

如果想要 <router-link> 渲染成某种标签，例如` <li>`。 于是我们使用 tag prop 类指定何种标签，同样它还是会监听点击，触发导航。

```
<router-link :to="{name:'schedule',query:{aa:'dsd'}}"  tag="li">跳转</router-link>
```
如果此时我们想要在这个li标签中添加a标签,如下所示，可以不为a标签添加href属性即可哦

```
<router-link :to="{name:'schedule',query:{aa:'dsd'}}"  tag="li">
	<a>跳转</a>
</router-link>
```
在这种情况下，`<a> `将作为真实的链接 (它会获得正确的 href 的)，而 "激活时的CSS类名" 则设置到外层的` <li>`。

 

3、active-class   类型: string    默认值: "router-link-active"

设置 链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 linkActiveClass 来全局配置。

```
<router-link :to="{name:'schedule',query:{aa:'dsd'}}"  tag="a"  active-class="red">跳转</router-link>
```

默认值通过路由的构造选项 linkActiveClass 来全局配置,如下示例：

```
export default new Router({
  mode:'history',
  linkActiveClass:'is-active',
  routes: [
    {
      path:'/about',
      component:about
    }
]
})
```

4、exact-active-class   类型: string    默认值: "router-link-exact-active"

配置当链接被精确匹配的时候应该激活的 class。注意默认值也是可以通过路由构造函数选项 linkExactActiveClass 进行全局配置的。

 

5、exact     类型: boolean     默认值: false

按照这个规则，每个路由都会激活`<router-link to="/">`！想要链接使用 "exact 匹配模式"，则使用 exact 属性：

```
<li><router-link to="/">全局匹配</router-link></li>
<li><router-link to="/" exact>严格匹配</router-link></li>
```

简单点说，第一个的话，如果地址是/aa,或/aa/bb，……都会匹配成功，

但加上exact，只有当地址是/时被匹配，其他都不会匹配成功

6、event       类型: string | Array`<string> `   默认值: 'click'          声明可以用来触发导航的事件。可以是一个字符串。

<ro`uter-link to="/document" event="mouseover">document</router-link>`
如果我们不加event，那么默认情况下是当我们点击document的时候，跳转到相应的页面，但当我们加上event的时候，就可以改变触发导航的事件，比如鼠标移入事件

7、replace   类型: boolean  默认值: false   

设置 replace 属性的话，当点击时，会调用 router.replace() 而不是 router.push()，于是导航后不会留下 history 记录。

 

8、append    类型: boolean   默认值: false     设置 append 属性后，则在当前 (相对) 路径前添加基路径

 

9、另外，加上target属性为"_blank",依然可以打开一个新的页面

```
<router-link :to="{name:'schedule',query:{aa:'dsd'}}"  tag="a" append  target="_blank" >
 	跳转
</router-link>
```

# Vue-router 路由守卫有哪些 ？

vue路由守卫有三种，分别为：
- 1、全局守卫“router.beforeEach”；
- 2、组件内的守卫“beforeRouteEnter”；
- 3、路由独享守卫“beforeEnter”。
- 
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
5. 全局后置钩子router.afterEach((to,from)=>{})
6. 判断store.gettes.isLogin === false 是否登录
- 只有两个参数，to：进入到哪个路由去，from：从哪个路由离。
- 如下，每次切换路由时，都会弹出alert，点击确定后，展示当前页面,路由不会变化

```
router.afterEach((to,from)=>{  alert("after each");})
```
7. 判断store.gettes.isLogin === false 是否登录

#### 二.组件内的守卫

**1. 到达这个组件时，beforeRouteEnter:(to,from,next)=>{}**

- 在Admin.vue文件中，点击转到admin路由时，执行beforeRouteEnter函数
- to，from参数与上面使用方法一致。next回调函数略有不同。
- 如下例，data 组件内守卫有特殊情况，如果我们直接以beforeRouteEnter:(to,from,next)=>{ alert("hello" + this.name);}进行访问admin页面，会发现alert输出hello undefined。这是因为，现在访问不到我们的data属性，执行顺序是不一致，这与的声明周期有关。在执行完之前，data数据还未渲染。所以这里，next()会给一个对应的回调，帮助完成。
![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-y2dX61My-1662297961528)(en-resource://database/4513:1)\]](https://img-blog.csdnimg.cn/3843d583d84143ae9d73941c627eec09.png)

**2. 组件有不同的参数时，beforeRouteUpdate:(to,from,next)=>{}**
**3. 离开这个组件时，beforeRouteLeave:(to,from,next)=>{}**
- 点击其他组件时，判断是否确认离开。确认执行next()；取消执行next(false)，留在当前页面。
![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-1P2JMRJi-1662297961530)(en-resource://database/4515:1)\]](https://img-blog.csdnimg.cn/39b1abc4f1f044878d8d7c584a2c5515.png)

#### 三、路由独享的守卫
1. beforeEnter:(to,from,next)=>{}，用法与全局守卫一致。只是，将其写进其中一个路由对象中，只在这个路由下起作用。
