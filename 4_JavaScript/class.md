在面字节的时候用到了class的写法，当时有点蒙，后来学习了一下class。
# 写一个框架
```

class App{
    constructor(){
        this.arr=[]
    }
    use(fn){
        this.arr.push(fn)
    }
    run(){
        var fn=this.arr.shift()
        while(fn){
            fn()
            fn=this.arr.shift();
        }
    }
}
var app= new App();
app.use(function(){
    console.log(1);
})
app.use(function(){
    console.log(2);
})
app.use(function(){
    console.log(3);
})
app.run();
//当调用run的时候才打印1,2,3

```

# 一、Class的基本语法
**1. 基本写法**
```
class App{
    constructor(){
        this.arr=[]
    }
    use(fn){
        this.arr.push(fn)
    }
    run(){
        var fn=this.arr.shift()
        while(fn){
            fn()
            fn=this.arr.shift();
        }
    }
}

上面的代码App可以看做是构造函数
function App(){
}
//其中use和run都是相当于在app.prototype上添加的。
```

**2. 类的实例**
```
var app= App()//错误
var app =new App()//正确
```

**3. 注意点**
- 类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式
- 类不存在提升
- name 属性
```
class Point {}
Point.name// "Point"
// name属性总是返回紧跟在class关键字后面的类名。
```

- Generator 方法  
某个方法之前加上星号（*），就表示该方法是一个 Generator 函数。

**4. 静态方法**

类相当于实例的原型，所有类中的方法都 可以被实例继承，如果在方法前面加上static则不被实例继承。
```

class Foo {
    static classMethod() {
    return 'hello';
   }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod()// TypeError: foo.classMethod is not a function
```
注意，如果是静态方法中的this指的是类本身，而不是实例。<br>
如果是非静态方法则指的是实例。

**5. 实例属性的新写法**
```
class App{
    constructor(){
        this.arr=[]
    }
    use(fn){
        this.arr.push(fn)
    }
    run(){
        var fn=this.arr.shift()
        while(fn){
            fn()
            fn=this.arr.shift();
        }
    }
}

```
上面的this.arr定义在consructor中，也可以定义在类的最顶层
```
class App{
    arr=[]
    use(fn){
        this.arr.push(fn)
    }
    run(){
        var fn=this.arr.shift()
        while(fn){
            fn()
            fn=this.arr.shift();
        }
    }
}

```
这种新写法的好处是，所有实例对象自身的属性都定义在类的头部，看上去比较整齐，一眼就能看出这个类有哪些实例属性。

**6.静态属性**

静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。
```
//老写法
class Foo {}
Foo.prop = 1;
Foo.prop // 1
//新写法
class Foo{
    static prop = 1;
}
```
新写法是显式声明（declarative），而不是赋值处理，语义更好。

**7. 私有方法和私有属性**

一种做法是在命名上加以区别。
```

class Widget {
    // 公有方法
    foo (baz) {
        this._bar(baz);
    }
    // 私有方法
    _bar(baz) {
        return this.snaf = baz;
    }
// ...
}
```

私有属性的提案

方法是在属性名之前，使用#表示。
```
class IncreasingCounter {
    #count = 0;
    get value() {
       console.log('Getting the current value!');
       return this.#count;
    }
    increment() {
    this.#count++;
    }
}
```
\#count就是私有属性，只能在类的内部使用（this.#count）。如果在类的外部使用，就会报错。

# 二、Class的继承

**1. 简介**

Class 可以通过extends关键字实现继承
```
class Point {
    constructor(x, y) {
    this.x = x;this.y = y;
    }
}
class ColorPoint extends Point {
    constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
}}
```
在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。这是因为子类实例的构建，基于父类实例，只有super方法才能调用父类实例。

**2. Object.getPrototypeOf()**

Object.getPrototypeOf方法可以用来从子类上获取父类。
```
Object.getPrototypeOf(ColorPoint) === Point
// true
```
**3. super 关键字**
super这个关键字，既可以当作函数使用，也可以当作对象使用
**第一种情况**，super作为函数调用时，代表父类的构造函数。
ES6 要求，子类的构造函数必须执行一次super函数。
```
class A {
}
class B extends A {
    constructor() {
    super();
}
}
```
**第二种情况，super作为对象使用。**
super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。
```
class A {
    p() {
        return 2;
        }
    }
class B extends A {
    constructor() {
        super();
        console.log(super.p());// 2
        }
    }
    let b = new B();
```

**4. 类的 prototype 属性和proto属性**

（1）子类的__proto__属性，表示构造函数的继承，总是指向父类。

（2）子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性。

```

class A {
}
class B extends A {}
B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```
**5. 原生构造函数的继承**

**6. Mixin 模式的实现**

Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。它的最简单实现如下。
```
const a = {a: 'a'};
const b = {b: 'b'};
const c = {...a, ...b}; 
// {a: 'a', b: 'b'}
```
上面代码中，c对象是a对象和b对象的合成，具有两者的接口。