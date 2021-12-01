### vue和angular的区别
**Vuejs的特点：**
1.轻量级的框架
2.双向数据绑定
3.指令
4.模块化，目前最热的方式是在项目中直接使用ES6的模块化，结合Webpack进行项目打包。
5.组件化，创造单个component后缀为.vue的文件，包含template(html代码)，script(es6代码),style(css样式)。


**angularjs的特点：**

1.数据的双向绑定：view层的数据和model层的数据是双向绑定的，其中之一发生更改，另一方会随之变化，这不用你写任何代码！
2.代码模块化，每个模块的代码独立拥有自己的作用域，model，controller等。
3.强大的directive可以将很多功能封装成HTML的tag,属性或者注释等，这大大美化了HTML的结构，增强了可阅读性。

4.依赖注入，将这种后端语言的设计模式赋予前端代码，这意味着前端的代码可以提高重用性和灵活性，未来的模式可能将大量操作放在客户端，服务端只提供数据来源和其他客户端无法完成的操作。

**vue和angular的区别：**
相同点：

* 都支持指令：内置指令和自定义指令。
* 都支持过滤器：内置过滤器和自定义过滤器。
* 都支持双向数据绑定。

不同点：

* vueJS简单易学,而angularJS的上手较高。
* AngularJS的学习成本高，比如增加了Dependency Injection特性，而Vue.js本身提供的API都比较简单、直观。
* angularJS的指令都是ng-xxx，而vueJS的指令都是v-xxx
* angularJS的所有指令和方法都是绑定在$scope上的，而vueJS是new出来一个实例，所有的方法和指令都在这个实例上，一个页面上可以有多个vue实例，但是angularJS的对象只能有一个。
* angularJS是由google开发和维护的，vueJS是由个人维护的。
* vueJS一般用于移动端的开发，而angularJS一般应用于大型的项目。