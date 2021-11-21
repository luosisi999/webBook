# Vue中的$nextTick
**一、NextTick是什么**
**定义**：在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即自动执行该函数，获取更新后的 DOM
**通俗理解**:Vue 在更新 DOM 时是异步执行的。当数据发生变化，Vue将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新
**为什么要有nexttick**
举个例子
```
{{num}}
for(let i=0; i<100000; i++){
    num = i
}
```
如果没有 nextTick 更新机制，那么 num 每次更新值都会触发视图更新(上面这段代码也就是会更新10万次视图)，有了nextTick机制，只需要更新一次，所以nextTick本质是一种优化策略

**二、使用场景**
如果想要在修改数据后立刻得到更新后的DOM结构，可以使用Vue.nextTick()

第一个参数为：回调函数（可以获取最近的DOM结构）

第二个参数为：执行函数上下文
```
Vue.nextTick
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

[Vue.$nextTick()的理解和用法](https://www.jianshu.com/p/4a6d0fc67251)