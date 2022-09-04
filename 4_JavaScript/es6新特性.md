### 1.let和const

#### let基本总结：

- let指令只在块内有效
- 不存在变量提升（就是说使用只能在声明之后否则报错）
- 暂时性死区（let变量和外面的同变量没有任何的关系）
```
let temp=1;
{
	temp="abc"//报错
	let temp=2;
}
```
- 不允许重复声明（1.let不允许在相同作用域内，重复声明同一个变量。2.不能在函数内部重新声明参数。）
- do 表达式(返回块级作用域的最后一行的表达式的值)
```
let x = do {
  let t = f();
  t * t + 1;
};
上面代码中，变量x会得到整个块级作用域的返回值（t * t + 1）
```



1.在let命令所在的代码块内有效
```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```
for循环的计数器，就很合适使用let命令。
```
for (let i = 0; i < 10; i++) {
  // ...
}
console.log(i);
// ReferenceError: i is not defined
```
下面的代码如果使用var，最后输出的是10。
```
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```
如果使用let，声明的变量仅在块级作用域内有效，最后输出的是 6。
```
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```
另外，for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

2.不存在变量提升
var命令会发生”变量提升“现象，即变量可以在声明之前使用，值为undefined。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。
为了纠正这种现象，let命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。
```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
3.暂时性死区

只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。
```
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。
```
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
```
typeof x; // ReferenceError
let x;
```
上面代码中，变量x使用let命令声明，所以在声明之前，都属于x的“死区”，只要用到该变量就会报错。因此，typeof运行时就会抛出一个ReferenceError。

作为比较，如果一个变量根本没有被声明，使用typeof反而不会报错。

```
typeof undeclared_variable // "undefined"
```

上面代码中，undeclared_variable是一个不存在的变量名，结果返回“undefined”。所以，在没有let之前，typeof运算符是百分之百安全的，永远不会报错。现在这一点不成立了。这样的设计是为了让大家养成良好的编程习惯，变量一定要在声明之后使用，否则就报错。

有些“死区”比较隐蔽，不太容易发现。
```
function bar(x = y, y = 2) {
  return [x, y];
}

bar(); // 报错
```
上面代码中，调用bar函数之所以报错（某些实现可能不报错），是因为参数x默认值等于另一个参数y，而此时y还没有声明，属于”死区“。如果y的默认值是x，就不会报错，因为此时x已经声明了。
```
function bar(x = 2, y = x) {
  return [x, y];
}
bar(); // [2, 2]
```
另外，下面的代码也会报错，与var的行为不同

```
// 不报错
var x = x;

// 报错
let x = x;
// ReferenceError: x is not defined
```
上面代码报错，也是因为暂时性死区。使用let声明变量时，只要变量在还没有声明完成前使用，就会报错。上面这行就属于这个情况，在变量x的声明语句还没有执行完成前，就去取x的值，导致报错”x 未定义“。

ES6 规定暂时性死区和let、const语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。

总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

4.不允许重复声明

let不允许在相同作用域内，重复声明同一个变量。

```
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}
```
因此，不能在函数内部重新声明参数。

6.块级作用域

为什么需要块级作用域？
ES5 只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景。
```
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined
```
第二种场景，用来计数的循环变量泄露为全局变量。

var s = 'hello';

for (var i = 0; i < s.length; i++) {
  console.log(s[i]);
}

console.log(i); // 5
上面代码中，变量i只用来控制循环，但是循环结束后，它并没有消失，泄露成了全局变量。

ES6 的块级作用域
let实际上为 JavaScript 新增了块级作用域。

```
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```
上面的函数有两个代码块，都声明了变量n，运行后输出 5。这表示外层代码块不受内层代码块的影响。如果两次都使用var定义变量n，最后输出的值才是 10。<br>
内层作用域可以定义外层作用域的同名变量。
```
{
  {let insane = 'Hello World'}
  console.log(insane); // 报错
}
```

```
{
  let insane = 'Hello World';
  {let insane = 'Hello World'}
}
```
块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。
```
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());
```
```
// 块级作用域写法
{
  let tmp = ...;
  ...
}
```
7.块级作用域与函数声明

函数能不能在块级作用域之中声明？这是一个相当令人混淆的问题。

ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。

// 情况一
if (true) {
  function f() {}
}

// 情况二
try {
  function f() {}
} catch(e) {
  // ...
}
上面两种函数声明，根据 ES5 的规定都是非法的。

但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，因此上面两种情况实际都能运行，不会报错。

ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。

```
function f() { console.log('I am outside!'); }
(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```

#### const
1.const声明一个只读的常量。一旦声明，常量的值就不能改变。
```
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.
```
2.声明常量的时候必须赋值
```
const foo;
// SyntaxError: Missing initializer in const declaration
```
3.const 和let同样是块级作用域
```
if (true) {
  const MAX = 5;
}

MAX // Uncaught ReferenceError: MAX is not defined
```
4.const同样存在暂时性死区，只能在声明的位置后面使
```
if (true) {
  console.log(MAX); // ReferenceError
  const MAX = 5;
}
```
6.const和let一样在相同的作用域不能重复声明
```
var message = "Hello!";
let age = 25;

// 以下两行都会报错
const message = "Goodbye!";
const age = 30;

```
7.const的本质其实并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。

- 对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。
- 于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。
```
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
```
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```



 ### es6中变量赋值的方式
1.方式1-直接赋值
```
let a = 1;
let b = 2;
let c = 3;
```
2.模式匹配”左右结构相等赋值
```
let [a, b, c] = [1, 2, 3];
//a=1;
//b=2;
//c=3
```
```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
3.如果解构不成功，变量的值就等于undefined。
```
let [foo] = [];
let [bar, foo] = [1];
//foo=undefined;
```
4.结构不完全相等，依然能够解析成功
```
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```
6.如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。
```
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
7.赋值带有默认值
```
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```
```
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```
### 对象的解析
1.基本解析
```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
2.对象的解析和位置没有关系，和键名有关系
```
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```
3.变量名和属性名不一样
```
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
```
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
```
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
上面代码中，foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo。
```
3.嵌套赋值
```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```
4.对象的解析模式也可以设置默认值
```
var {x, y = 5} = {x: 1};
x // 1
y // 5
```
5.解析不成功的时候
```
默认值生效的条件是，对象的属性值严格等于undefined。
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```
```
let {foo} = {bar: 'baz'};
foo // undefined
```
6.如果子对象所在的父对象是undefined那么报错，
```
// 报错
let {foo: {bar}} = {baz: 'baz'};
```
上面代码中，等号左边对象的foo属性，对应一个子对象。该子对象的bar属性，解构时会报错。原因很简单，因为foo这时等于undefined，再取子属性就会报错，请看下面的代码

7.给一个已经声明的变量再次声明时，要注意。
```
// 错误的写法
let x;
{x} = {x: 1};
// SyntaxError: syntax error
上面代码的写法会报错，因为 JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。

// 正确的写法
let x;
({x} = {x: 1});
```
8.特殊的解析
```
({} = [true, false]);
({} = 'abc');
({} = []);
上面的表达式虽然毫无意义，但是语法是合法的，可以执行。
```
### 3.箭头函数
- 箭头函数没有this指向，箭头的函数内部的this的值，只能通过查找当前的作用域链
- 箭头函数没有arguments
- 箭头函数不能使用new来实例化对象

```
let Person = () => {
}
let p = new Person()//报错
```
### 4.对象的扩展功能
属性的简洁表示法

```
const name = '张三';
const age = 19;
const person = {
    name, //等同于name:name
    age,
    // 方法也可以简写
    sayName() {
        console.log(this.name);
    }
}
person.sayName();
```

这种写法用于函数的返回值，将会非常方便。

```
function getPoint() {
  const x = 1;
  const y = 10;
  return {x, y};
}

getPoint()
// {x:1, y:10}
```

对象扩展运算符

```
const [a, ...b] = [1, 2, 3];//剩余参数
a // 1
b // [2, 3]
```
### 5.Symbol
Symbol 声明的值是独一无二的(使用相对较少)
用途：用来定义对象的私有变量

```
const name = Symbol('name');
const name2 = Symbol('name');
consle.log(name === namee2)//false
let s1 = Symbol('name');
let obj = {
	[s1]:'小马哥'
}
//如果使用Symbol定义的对象的变量，取值时候一定要是使用[]的形式。
```
注意：for循环不能获取到Symbol声明得属性名
Object.getOwnPropertySymbols(obj)可以获取Symbol定义的属性名
### Set
定义：是一个集合，表示无重复的有序列表

```
let set = new Set()
console.log(set)
//添加元素
set.add(1)
//set.delete(a)
//set.has(1)//true
//set.forEach()
forEach箭头函数的value个key是一样的，所用经常不适用
let set =new Set()
set.add(111)
set.add(222)
set.add(["dd","ff","Ddd"])
set.forEach((value,key) => {
    console.log(value)
    console.log(key)
})
```
将set对象转换成数组

```
let set =new Set([1,2,3,4,5,6])
let arr = [...set]
```
### Map类型
定义：类型是键值对的有序列表，建和值是任意类型的。

```
let map = new Map()
map.set('name','张三')
//map.get('name')
//map.has('name')
//map.delete(‘name’)
//map.clear()
```

### 数组的方法扩展
- form
将伪数组转成数组

```
function (){
let arr = Array.from(arguments)
//let arr1 = [...arguments]扩展运算符也能将伪数组转成数组
}
let arr = Array.from(arguments，elem=>elem)
//form方法可以传入第二函数参数
```

- of
定义：将一组任意的数据类型的值转换成数组

```
Array.of(1,2,3,4,5,'20')
```
- copywithin()复制
```
[1,2,3,4,5,'20'].copywithin(0,3)//将索引3及以后的元素替换索引0的位置后的三个元素
//[4,5,'20',4,5,'20']
```
- find()
定义：找出第一个符合条件的数组成员的索引
```
let numberIndex=[7,2,3,4,5,'20'].find(value=>n<3)
// numberIndex=1
```
- for of
定义：循环遍历器
```
for (const index of ['a','b'].keys()) {
    console.log(index);
}
for (const elem of ['a','b'].values()) {
    console.log(elem);
}
for (const [index,elem] of ['a','b'].entries()) {
    console.log(index,elem);
}
```

- includes
定义：表示数据是否包含某个数组，返回true或者false

```
[1,2,3,4].includes(4)
//true
//替换indexOf
```
### ES6的迭代器-Iterator
定义：是一种新的遍历器,两个核心
1.迭代器是一个接口，能快捷的访问数据。
通过Symbol.iterator创建迭代器
2.迭代器是遍历数据结构的一个指针
```
const arr = ['one','two','three']
const aa = arr[Symbol.iterator]()
console.log(aa)
console.log(aa.next())
console.log(aa.next())
console.log(aa.next())
console.log(aa.next())
//Object [Array Iterator] {}
//{ value: 'one', done: false }
//{ value: 'two', done: false }
//{ value: 'three', done: false }
//{ value: undefined, done: true }
```

### ES6的生成器-generator
//generator函数，可以通过关键字，yield关键字，将函数挂起<br>
和普通函数的区别：
1.function后面有个*。
2.yield只能在函数内部使用，让函数挂起	
	

```
function* fun(){

    yield 2;
    yield 3;
}
let aa =fun()//返回遍历器对象
console.log(aa)//Object [Generator] {}
console.log(aa.next())//yield的值通过对象返回{ value: 2, done: false }
console.log(aa.next())//{ value: 3, done: false }
console.log(aa.next())//{ value: undefined, done: true }
```
总结：generator函数是分段执行的，yield是暂停执行，next()函数是继续执行
generator函数是解决异步编程的一种方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/75b17e5ce7624fc28d08319c438a7f28.png)
#### generator的应用
**异步编程**
异步函数同步化
![在这里插入图片描述](https://img-blog.csdnimg.cn/d97bd840897444f39b70d9603f7a5065.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/eb49491a7ffb46a6b0e7968fb7c29889.png)
**load案例(异步编程)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/e4ac9bd841a147308b85a2784fdc9e39.png)![在这里插入图片描述](https://img-blog.csdnimg.cn/1f09e415c714449da8b57c7c810de431.png)
### 17 promise对象
#### promise的基本使用
三个状态：pedding（进行）、Resolved（成功）、rejected（失败）<br>
pedding（进行）-》Resolved（成功）<br>
pedding（进行）-》rejected（失败）<br>

```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```
#### Promise.prototype.then()
- 两个参数
    - 第一个参数是resolved状态的回调函数，第二个参数是rejected状态的回调函数，它们都是可选的。
    - 可以链式编程，每次都返回一个新的promise对象，若改状态为resoved的时候，执行下一个then方法
```
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function (comments) {
  console.log("resolved: ", comments);
}, function (err){
  console.log("rejected: ", err);
});
```

#### Promise.prototype.catch() 
romise.prototype.catch()方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。
```
getJSON('/posts.json').then(function(posts) {
  // ...
}).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});
```
#### Promise.resolve() 
作用：将现有任何对象转为 Promise 对象

```
const jsPromise = Promise.resolve($.ajax('/whatever.json'));
```

```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

#### Promise.prototype.finally() 
finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作
```
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```
上面代码中，不管promise最后的状态，在执行完then或catch指定的回调函数以后，都会执行finally方法指定的回调函数。
#### Promise.all()
Promise.all()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。
```
const p = Promise.all([p1, p2, p3]);
```
（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

#### Promise.race()
```
const p = Promise.race([p1, p2, p3]);
```
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

```
const p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
]);

p.then(console.log)
.catch(console.error);
```
上面代码中，如果 5 秒之内fetch方法无法返回结果，变量p的状态就会变为rejected，从而触发catch方法指定的回调函数。

#### Promise.allSettled()
Promise.allSettled()方法接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的<>br Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是fulfilled还是rejected），返回的 Promise 对象才会发生状态变更。
#### Promise.any()
只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。
```
Promise.any([
  fetch('https://v8.dev/').then(() => 'home'),
  fetch('https://v8.dev/blog').then(() => 'blog'),
  fetch('https://v8.dev/docs').then(() => 'docs')
]).then((first) => {  // 只要有一个 fetch() 请求成功
  console.log(first);
}).catch((error) => { // 所有三个 fetch() 全部请求失败
  console.log(error);
});
```
**Promise.any()跟Promise.race()方法很像，只有一点不同，就是Promise.any()不会因为某个 Promise 变成rejected状态而结束，必须等到所有参数 Promise 变成rejected状态才会结束。**
#### Promise.resolve()
有时需要将现有对象转为 Promise 对象，Promise.resolve()方法就起到这个作用。
```
const jsPromise = Promise.resolve($.ajax('/whatever.json'));
```
Promise.resolve()等价于下面的写法。
```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```
- 参数的四种情况：
    - 参数是一个 Promise 实例，原封不动的返回这个实例
    - 参数是一个thenable对象，会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then()方法。
    - 参数不是具有then()方法的对象，或根本就不是对象（如果参数是一个原始值，或者是一个不具有then()方法的对象，则Promise.resolve()方法返回一个新的 Promise 对象，状态为resolved。）
    - 不带有任何参数（Promise.resolve()方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。）


#### Promise.reject()
Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。
```
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```

### async
作用：使得异步方法更加方便。
async 函数是 Generator 函数的语法糖。
**基本用法**
sync函数返回一个 Promise 对象，使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

```
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```
### class

```
class Person {
    // constructor方法 是类的默认方法,通过new命令生成对象实例时,自动调用该方法,一个类必须有constructor方法,如果没有定义,会被默认添加
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    //等同于Person.prototype = function sayName(){}
    sayName(){
        return this.name;
    }
}
console.log(Person===Person.prototype.constructor)

```
上面代码定义了一个“类”，可以看到里面有一个constructor()方法，这就是构造方法，而this关键字则代表实例对象。
- 继承
![在这里插入图片描述](https://img-blog.csdnimg.cn/b5e2601f041f4e6eadc01e771a412c0b.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/edbea9d4eaf24dfea42b684b02f8427b.png)super(name,age); // 调用父类的constructor(name, age)

### Module 模块化
模块功能主要由两个命令构成：export和import。export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量

```
//module/index.js
export const name = 'zhangsan ';
export const age = 18;
export const color = 'red ';
export const sayName = function() {
    console.log(fristName);
}

//也可以这样
const name = 'zhangsan ';
const age = 18;
const color = 'red ';
const sayName = function() {
    console.log(fristName);
}
export {name,age,color,sayName}
```

- import命令

使用export命令定义了模块的对外接口以后，其他 JS 文件就可以通过import命令加载这个模块。

```
//main.js
import {name,age,color,sayName,fn} from './modules/index.js';
```
如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名

```
import * as obj from './modules/index.js';
console.log(obj);
```
- export default 命令
使用export default命令为模块指定默认输出

```
//export-default.js
export default function(){
    console.log('foo');
}

//或者写成
function foo() {
  console.log('foo');
}

export default foo;
```
在其它模块加载该模块时，import命令可以为该匿名函数指定任意名字

```
//import-default.js
import customName from './export-default.js'
customNmae();//foo
```
