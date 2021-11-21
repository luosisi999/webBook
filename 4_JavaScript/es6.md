**一、es5和es6的区别，说一下你所知道的es6**

ECMAScript5，即ES5，是ECMAScript的第五次修订，于2009年完成标准化ECMAScript6，即ES6，是ECMAScript的第六次修订，于2015年完成，也称ES2015ES6是继ES5之后的一次改进，相对于ES5更加简洁，提高了开发效率ES6新增的一些特性：

- **let/const**：let声明变量和const声明常量，两个都有块级作用域ES5中是没有块级作用域的，并且var有变量提升，在let中，使用的变量一定要进行声明
- **箭头函数**：ES6中的函数定义不再使用关键字function()，而是利用了()=>来进行定义
- **模板字符串**：模板字符串是增强版的字符串，用反引号（`）标识，可以当作普通字符串使用，也可以用来定义多行字符串
- **解构赋值**：ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值
- **for of循环** ：for...of循环可以遍历数组、Set和Map结构、某些类似数组的对象、对象，以及字符串
- **import、export导入导出**ES6标准中，Js原生支持模块(module)。将JS代码分割成不同功能的小块进行模块化，将不同功能的代码分别写在不同文件中，各模块只需导出公共接口部分，然后通过模块的导入的方式可以在其他地方使用
- **set数据结构**：Set数据结构，类似数组。所有的数据都是唯一的，没有重复的值。它本身是一个构造函数
- ... 展开运算符可以将数组或对象里面的值展开；还可以将多个值收集为一个变量
- **修饰器 @decorator**是一个函数，用来修改类甚至于是方法的行为。修饰器本质就是编译时执行的函数
- **class 类**的继承ES6中不再像ES5一样使用原型链实现继承，而是引入Class这个概念11）async、await使用 async/await, 搭配promise,可以通过编写形似同步的代码来处理异步流程, 提高代码的简洁性和可读性async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成
- **Promise**是异步编程的一种解决方案，比传统的解决方案（回调函数和事件）更合理、强大
- **Symbol**是一种基本类型。Symbol 通过调用symbol函数产生，它接收一个可选的名字参数，该函数返回的symbol是唯一的
- **Proxy代理**使用代理（Proxy）监听对象的操作，然后可以做一些相应事情

**二、使用箭头函数应注意什么？**

- 用了箭头函数，this就不是指向window，而是父级（指向是可变的）
- 不能够使用arguments对象
- 不能用作构造函数，这就是说不能够使用new命令，否则会抛出一个错误
- 不可以使用yield命令，因此箭头函数不能用作 Generator 函数

**三、ES6的模板字符串有哪些新特性？并实现一个类模板字符串的功能**

- 基本的字符串格式化。
- 将表达式嵌入字符串中进行拼接。
- 用${}来界定在ES5时我们通过反斜杠()来做多行字- 符串或者字符串一行行拼接。
- ES6反引号(``)就能解决类模板字符串的功能
```
let name = 'web';
let age = 10;
let str = '你好，${name} 已经 ${age}岁了'
str = str.replace(/\$\{([^}]*)\}/g,function(){
     return eval(arguments[1]);
   })
console.log(str);//你好，web 已经 10岁了
```

**四、介绍下 Set、Map的区别？**

应用场景Set用于数据重组，Map用于数据储存Set：
Set
- 成员不能重复
- 只有键值没有键名，类似数组
- 可以遍历，方法有add, delete,has

Map:
- 本质上是健值对的集合，类似集合
- 可以遍历，可以跟各种数据格式转换

**五、ECMAScript 6 怎么写 class ，为何会出现 class？**

ES6的class可以看作是一个语法糖，它的绝大部分功能ES5都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法
```
//定义类
class Point { 
  constructor(x,y) { 
      //构造方法
       this.x = x; //this关键字代表实例对象
       this.y = y; 
  } toString() {
       return '(' + this.x + ',' + this.y + ')'; 
  }
}
```


**六、Promise**

- Promise解决了什么问题？
可以解决回掉函数存在的问题（回调地狱，思路很不清晰）等，
思路：基于上面的问题，考虑把我们程序的延续交给另一个团体，而是希望它返回给我们一个可以知道它何时完成的能力，然后我们的代码可以决定下一步做什么呢？
Promise是异步编程的一种解决方案：从语法上讲，promise是一个对象，从它可以获取异步操作的消息；从本意上讲，它是承诺，承诺它过一段时间会给你一个结果。promise有三种状态：pending(等待态)，fulfiled(成功态)，rejected(失败态)；状态一旦改变，就不会再变。创造promise实例后，它会立即执行。一般来说我们会碰到的回调嵌套都不会很多，一般就一到两级，但是某些情况下，回调嵌套很多时，代码就会非常繁琐，会给我们的编程带来很多的麻烦，这种情况俗称——回调地狱。

这时候我们的promise 就应运而生、粉墨登场了。
promise的详细介绍[https://blog.csdn.net/zemprogram/article/details/86564055](https://blog.csdn.net/zemprogram/article/details/86564055)

- promise三种状态：pending(等待态)，fulfiled(成功态)，rejected(失败态)；
- 两个过程：padding -> fulfilled(当pending->fulfill，执行resovled函数，进入then)、padding -> rejected（当pending->rejectd时，会进入catch）
- promise怎么从pending转换成resovled?

**七、setTimeout、Promise、Async/Await 的区别**

事件循环中分为宏任务队列和微任务队列
其中setTimeout的回调函数放到宏任务队列里，等到执行栈清空以后执行promise.then里的回调函数会放到相应宏任务的微任务队列里，等宏任务里面的同步代码执行完再执行async函数表示函数里面可能会有异步方法，await后面跟一个表达式
async方法执行时，遇到await会立即执行表达式，然后把表达式后面的代码放到微任务队列里，让出执行栈让同步代码先执行

看代码
```
setTimeout(function(){console.log(4)},0);
new Promise(function(resolve){
    console.log(1)
    for( var i=0 ; i<10000 ; i++ ){
        i==9999 && resolve()
    }
    console.log(2)
}).then(function(){
    console.log(5)
});
console.log(3);
```
结果是1,2,3,5,4
因为 setTimeout 是属于 macrotask（宏任务）的，而整个 script 也是属于一个 macrotask（宏任务）, promise.then 回调 是 microtask（微任务） ,执行过程大概如下：
1、由于整个 script 也属于一个 macrotask, 由于会先执行 macrotask 中的第一个任务，再加上promise 构造函数因为是同步的，所以会先打印出 1和2
2、然后继续同步执行末尾的 console.log(3) 打印出3
3、此时 setTimeout 被推进到 macrotask 队列中， promise.then 回调被推进到 microtask 队列中
4、由于在第一步中已经执行完了第一个 macrotask , 所以接下来会顺序执行所有的 microtask, 也就是 promise.then 的回调函数，从而打印出5
5、microtask 队列中的任务已经执行完毕，继续执行剩下的 macrotask 队列中的任务，也就是 setTimeout, 所以打印出 4

**八、使用结构赋值，实现两个变量的值的交换**

```
let a = 1;let b = 2;
[a,b] = [b,a];
```

**九、设计一个对象，键名的类型至少包含一个symbol类型，并且实现遍历所有key**

```
let name = Symbol('name');
 let product = {
    [name]:"洗衣机",    
    "price":799
  };
  Reflect.ownKeys(product);
```

**十、下面Set结构，打印出的size值是多少**

```
let s = newSet();
s.add([1]);s.add([1]);
console.log(s.size);
```
答案：2
两个数组[1]并不是同一个值，它们分别定义的数组，在内存中分别对应着不同的存储地址，因此并不是相同的值都能存储到Set结构中，所以size为2

**十一、Promise 中reject 和 catch 处理上有什么区别**

- reject 是用来抛出异常，catch 是用来处理异常
- reject 是 Promise 的方法，而 catch 是 Promise 实例的方法
- reject后的东西，一定会进入then中的第二个回调，如果then中没有写第二个回调，则进入catch
- 网络异常（比如断网），会直接进入catch而不会进入then的第二个回调

**十二、使用class 手写一个promise**

```
//创建一个Promise的类
  class Promise{
    constructor(executer){//构造函数constructor里面是个执行器
      this.status = 'pending';//默认的状态 pending
      this.value = undefined//成功的值默认undefined
      this.reason = undefined//失败的值默认undefined
      //状态只有在pending时候才能改变
      let resolveFn = value =>{
        //判断只有等待时才能resolve成功
        if(this.status == pending){
          this.status = 'resolve';
          this.value = value;
        }
      }
      //判断只有等待时才能reject失败
      let rejectFn = reason =>{
        if(this.status == pending){
          this.status = 'reject';
          this.reason = reason;
        }
      }    
      try{
        //把resolve和reject两个函数传给执行器executer
        executer(resolve,reject);
      }catch(e){
        reject(e);//失败的话进catch
      }
    }
    then(onFufilled,onReject){
      //如果状态成功调用onFufilled
      if(this.status = 'resolve'){
        onFufilled(this.value);
      }
      //如果状态失败调用onReject
      if(this.status = 'reject'){
        onReject(this.reason);
      }
    }
  }
```

**十三、如何使用Set去重**

```
let arr = [12,43,23,43,68,12];
let item = [...new Set(arr)];
console.log(item);//[12, 43, 23, 68]
```

**十四、理解 async/await以及对Generator的优势**

async await 是用来解决异步的，async函数是Generator函数的语法糖
使用关键字async来表示，在函数内部使用 await 来表示异步
async函数返回一个 Promise 对象，可以使用then方法添加回调函数
当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句

async较Generator的优势：
- 内置执行器。Generator 函数的执行必须依靠执行器，而 Aysnc 函数自带执行器，调用方式跟普通函数的调用一样
- 更好的语义。async 和 await 相较于 * 和 yield 更加语义化　　
- 更广的适用性。yield命令后面只能是 Thunk 函数或 Promise对象，async函数的await后面可以是Promise也可以是原始类型的值
- 返回值是 Promise。async 函数返回的是 Promise 对象，比Generator函数返回的Iterator对象方便，可以直接使用 then() 方法进行调用

**十五、forEach、for in、for of三者区别**

- forEach更多的用来遍历数组
- for in 一般常用来遍历对象或json
- for of数组对象都可以遍历，遍历对象需要通过和Object.keys()
- for in循环出的是key，for of循环出的是value

**十六、说一下es6的导入导出模块**

导入通过import关键字
```
// 只导入一个
import {sum} from "./example.js"
// 导入多个
import {sum,multiply,time} from "./exportExample.js"
// 导入一整个模块
import * as example from "./exportExample.js"
```
导出通过export关键字
```
//可以将export放在任何变量,函数或类声明的前面
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
//也可以使用大括号指定所要输出的一组变量
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;
export {firstName, lastName, year};
//使用export default时，对应的import语句不需要使用大括号
let bosh = function crs(){}
export default bosh;
import crc from 'crc';
//不使用export default时，对应的import语句需要使用大括号
let bosh = function crs(){}
export bosh;
import {crc} from 'crc';
```

**十七、js数组遍历的几种方式**

  forEach,for循环，some,map,filter

1.forEach()
作用：遍历数组，取出数组中的每一项
参数：function(value,index,array){}
返回值：没有返回值

2.filter()
作用：遍历数组，筛选出满足条件的项，将满足条件的项放到新数组中，并且返回
参数：function(value,index,array){}
返回值：返回存放了满足条件的项的新数组
如果发现回调函数，返回了true，就将当前的value，放到新数组中

3.some()
作用：遍历数组，判断是否有满足条件的元素，如果有返回true,如果没有返回false 查找数组唯一的元素
参数：function(value,index,array){}
返回值：返回true/false

如果发现回调函数，返回了true,就会停止遍历

4.map()
map()生成一个新数组 其结果是该数组每一个元素 调用指定函数的 
返回值：符合条件的数组

5.every()
every() 用来检测该数组中每一个元素 是否都满足指定函数的条件，返回的是 布尔值 如果给定是一个空数组，则返回true

**十八：promise的优点和缺点**
- promise的缺点
1. 无法取消Promise,一旦新建它就会立即执行，无法中途取消。
2. 如果不设置回调函数，promise内部抛出的错误，不会反应到外部。
3. 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

- promis的优点
1. 解决回调地狱（Callback Hell）问题
（1）有时我们要进行一些相互间有依赖关系的异步操作，比如有多个请求，后一个的请求需要上一次请求的返回结果。过去常规做法只能 callback 层层嵌套，但嵌套层数过多的话就会有 callback hell 问题
（2）如果使用 promises 的话，代码就会变得扁平且更可读了。前面提到 then 返回了一个 promise，因此我们可以将 then 的调用不停地串连起来。其中 then 返回的 promise 装载了由调用返回的值。
2. 更好地进行错误捕获
多重嵌套 callback 除了会造成上面讲的代码缩进问题，更可怕的是可能会造成无法捕获异常或异常捕获不可控。


**十九、判断数组的方法**

1. 从原型入手:Array.prototype.isPrototypeOf(obj);
利用isPrototypeOf()方法，判定Array是不是在obj的原型链中，如果是，则返回true,否则false。
2. 从构造函数入手:obj instanceof Array
3. 跨原型链调用toString(): Object.prototype.toString.call(obj)
每一个继承 Object 的对象都有 toString方法，如果 toString 方法没有重写的话，会返回 [Object type]，其中 type 为对象的类型。但当变量类型不为对象时，使用 toString 方法会直接返回数据内容的字符串，所以我们需要使用call或者apply方法来改变toString方法的执行上下文。
4. ES5新增的方法：Array.isArray()

**二十、函数防抖和节流**
1、防抖（debounce）：所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。
```
function debounce(func, wait) {
    let timeout;
    return function () {
        let context = this;
        let args = arguments;

        if (timeout) clearTimeout(timeout);
        
        timeout = setTimeout(() => {
            func.apply(context, args)
        }, wait);
    }
}
```
2、节流（throttle）：所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。节流会稀释函数的执行频率。

对于节流，一般有两种方式可以实现，分别是时间戳版和定时器版。
时间戳版：
```
function throttle(func, wait) {
    let previous = 0;
    return function() {
        let now = Date.now();
        let context = this;
        let args = arguments;
        if (now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}
```

定时器版:
```
function throttle(func, wait) {
    let timeout;
    return function() {
        let context = this;
        let args = arguments;
        if (!timeout) {
            timeout = setTimeout(() => {
                timeout = null;
                func.apply(context, args)
            }, wait)
        }

    }
}
```