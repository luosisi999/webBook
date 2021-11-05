# Vue的生命周期
Vue实例从创建到销毁的过程就是生命周期

开始创建→初始化数据→编译模板→挂载DOM-渲染→更新-渲染→卸载

作用：vue所有的功能实现都是围绕其生命周期进行的，在生命周期的不同阶段调用对应的钩子函数可以实现组件数据管理和DOM渲染两大重要功能。生命周期中有多个事件钩子，在控制整个vue实例的过程时更容易形成好的逻辑。

生命周期分为8个阶段：创建前/后、载入前/后、更新前/后、销毁前/后

| 钩子函数 | 状态 | 用处 |
| :--- | :----: | :----: |
| beforeCreate | 此阶段为实例初始化之后，this指向创建的实例，此时数据观察事件机制都未形成，不能获得DOM节点 | 初始化非响应式变量 |
| created    | 此阶段实例创建完成，属性已绑定，数据对象data已存在，但DOM未生成，$el未存在      | 初始化完成事件、异步请求    |
| beforeMount |vue实例的$el和data都已初始化，此时为虚拟的DOM节点，data.message未替换 | - |
| mounted | vue实例挂载完成，data.message成功渲染 | 发起后端请求、对挂载的DOM进行操作 |
| beforeUpdate|数据驱动DOM|访问现有DOM |
| updated | 完成虚拟DOM的重新渲染和打补丁 | 对数据统一处理的函数 |
| updabeforeDestoryted | 组件销毁之前调用 | 销毁定时器、解绑全局事件、销毁插件对象删除提示 |
| destroyed | 组件销毁之后调用，对data的改变不会再触发周期函数，vue实例已解除事件监听和DOM绑定，但DOM结构依然存在 | - |

第一次页面加载时触发：beforeCreate、created、beforeMount、mounted
# Vue的每一周期都干了什么？
beforeCreate:
> 1. 父子关系确认(parent/parent/parent/children已经有了)
> 2. 根元素确认($root)
> 3. 初始化自定义事件,如:on/on/on/emit/once/once/once/off
> 4. 初始化插槽(slots),初始化_c函数/slots),初始化\_c函数/slots),初始化_c函数/createElement函数(这两个函数内部都是调用createElement函数来创建虚拟节点)
> 5. 将attrs/attrs/attrs/listeners定义成响应式的属性
> 6. 在实例上初始化一些变量,如：_isMounted,_isDestroyed,_watcher...等
> 7. 此时数据还没有被代理，还不是响应式的
> 8. 调用beforeCreate钩子函数

created:
> 1. 初始化inject，inject用来注入数据，源码内部就是一个while循环，从组件自身向父级查找，一直找到父级组件中有provide属性的，一般写业务代码不推荐用，因为数据来源不清晰，自己封装组件的时候可能会用，可以实现爷/孙通讯，此时数据还不是响应式的
> 2. 初始化props、初始化methods、初始化data、初始化computed、初始化watch，是有先后顺序的
> 3. 在初始化data的时候会进行数据劫持，此时数据分为2种，一种是对象，一种是数组，内部会循环对象,将对象中的每个属性都调用defineReactive函数定义成响应式,defineReactive函数内部是用Object.defineProperty来定义的，如果对象中的值还是对象的话，会递归定义成响应式，如果是数组的话，会劫持数组的7个方法，分别是'push', 'pop','shift','unshift','splice', 'sort', 'reverse',因为他们都会影响原数组，内部会将这7个方法进行重写，用户调用这7个方法时，实际上还是调用数组本身的方法，只不过内部会去通知watcher更新，而且如果用户是用push、unshift、splice这3个方法给数组增加数据时，内部会拿到增加的数据，判断这个数据是不是对象，如果是对象，会再次定义成响应式，被劫持过的属性都有一个__ob__属性
> 4. 初始化provide,如果provide的值为一个函数时，会调用这个函数，并且将内部的this指向当前组件实例
> 5. 调用created钩子函数

beforeMount:
> 1. 看实例中是否提供了render函数，如果没有提供，会调用compileToFunction编译成函数,compileToFunction中主要做了3件事:
1.将模板编译成ast语法树(ast用是来描述语法层面的信息，比如标签是什么，属性是什么等等)，内部会用正则不停的去匹配标签中的内容，匹配一点就删一点，最后匹配结束，模板也被删完了，返回这个ast树
2.将ast语法树编译成字符串的代码,例如:1会先转成{tag:'div',attrs:[{id:'app'}],1},然后编译成字符串代码，如: _c('div',{id:"app"},_v(1)) ,这个代码是字符串的
通过new Function + with语法将字符串代码编译成函数，以上代码变成ƒ anonymous(){with(this){return _c('div',{id:"app"},_v(1)) }}
> 2. 以上编译出来的函数就是render函数
> 3. 调用beforeMount钩子函数

mounted:
> 1. 创建一个渲染Watcher
> 2. 在Watcher中做了已下三件事:1. 调用pushTarget函数将当前watcher存起来 2.调用render函数 3. 调用popTarget函数将当前watcher删掉
> 3. 在调用render函数的时候，会在当前组件实例上取值，取值就会触发Object.defineProperty中的get,内部会收集依赖，进行dep/watcher的双向关联,然后返回真实的值，接着内部会先创建虚拟节点，然后更新页面
> 4. 调用mounted钩子函数

beforeUpdate/updated:
> 1. 当外界更新data中的数据时，会触发set，因为之前渲染的时候收集了依赖，所以我们会通知watcher去更新，然后再次创建虚拟节点，这一次创建的虚拟节点会和之前创建的虚拟节点进行diff对比，然后局部更新页面
> 2. 在局部更新页面之前调用beforeUpdate钩子函数
> 3. 在局部更新页面之后调用updated钩子函数

beforeDestroy/destroyed:
> 1. 当组件被卸载时，会清空组件中绑定的事件，会在当前父组件中的 children中移除自己，会在当前组件中移除children中移除自己，会在当前组件中移除children中移除自己，会在当前组件中移除parent,清空watcher等等...
> 2. 当组件被卸载前调用beforeDestroy钩子函数
> 3. 当组件被卸载后调用destroyed钩子函数
