# Vue的生命周期
Vue实例从创建到销毁的过程就是生命周期

开始创建→初始化数据→编译模板→挂载DOM-渲染→更新-渲染→卸载

作用：vue所有的功能实现都是围绕其生命周期进行的，在生命周期的不同阶段调用对应的钩子函数可以实现组件数据管理和DOM渲染两大重要功能。生命周期中有多个事件钩子，在控制整个vue实例的过程时更容易形成好的逻辑。

生命周期分为8个阶段：创建前/后、载入前/后、更新前/后、销毁前/后
第一次页面加载会触发beforeCreate,created, beforeMount,mounted四个钩子函数
DOM渲染在mounted这个周 期就已经完成
![](../Images/生命周期流程图.png)

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

