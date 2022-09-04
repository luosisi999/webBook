# 一、组件分类
 1. 由 vue-router
    产生的每个页面，它本质上也是一个组件（.vue）。一般不会有prop选项和自定义事件，不会被复用也不会对外提供接口 。
 2. 不包含业务，独立、具体功能的基础组件。这类组件作为项目的基础控件会被大量使用，组件的API进行过高强度的抽象，可以通过不同配置实现不同的功能。独立组件的开发难度要高于第一类组件，因为它的侧重点是API的设计、兼容性、性能、以及复杂的功能。
 3. 业务组件。不像第二类独立组件只包含某个功能，这类组件在业务中被多个页面复用，但只在当前项目会用到，不具有通用性，而且会包含一些业务。业务组件更像是介于第一类和第二类之间，在开发上也与独立组件类似，但寄托于项目，有必要考虑组件的可维护性和复用性。
# 二、prop、event、slot
组件都是由prop、event、slot三部分组成，它们构成了 Vue.js 组件的 API
## 1. 属性prop
prop 定义了这个组件有哪些可配置的属性，组件的核心功能也都是它来确定的。写通用组件时，**props最好用对象的写法，这样可以针对每个属性设置类型、默认值或自定义校验属性的值**。<br>
比如封装一个按钮组件 <i-button>：

```
<template>
<button :class="'i-button-size' + size" :disabled="disabled"></button></template><script>
// 判断参数是否是其中之一
function oneOf (value, validList) {
	for (let i = 0; i < validList.length; i++) {
		if (value === validList[i]) {
			return true;
		}
	}
	return false;
}

export default {
	props:{//定义两个属性：尺寸size和是否禁用disabled
		size: {//size 使用 validator 进行了值的自定义验证
			validator (value) {
				return oneOf(value, ['small', 'large', 'default']);
			},
			default: 'default'
		},
	disabled: {
		type: Boolean,
		default: false
	}
	}
}
</script>
```
使用组件

```
<i-button size="large"></i-button>
<i-button disabled></i-button>
```
**组件里定义的 props，都是修改定义在 data 里单向数据流，也就是只能通过父级修改，组件自己不能修改 props 的值，只能的数据，非要修改，也是通过后面介绍的自定义事件通知父级，由父级来修改。**
## 2.插槽 slot
如果要给上面的按钮组件 <i-button> 添加一些文字内容，就要用到组件的第二个 API：插槽 slot，它可以分发组件的内容，比如在上面的按钮组件中定义一个插槽：<br>
子组件：

```
<template>
	<button :class="'i-button-size' + size" :disabled="disabled">
	<slot></slot>
	</button>
</template>
```
这里的 <slot> 节点就是指定的一个插槽的位置，这样在组件内部就可以扩展内容了：<br>
父组件（<i-button>是调用的子组件）：

```
<i-button>按钮 1</i-button>
<i-button>
	<strong>按钮 2</strong>
</i-button>
```
再举一个例子：

```
//父组件<template>
<div>
我是父组件
<slotOne1>//子组件1
<p style="color:red">我是父组件插槽内容</p>
</slotOne1>
</div></template>
```

```
//子组件slotOne1
<template>
<div class="slotOne1">
<div>我是slotOne1组件</div>
<slot></slot>
</div>
</template>
```
显示为：

```
我是父组件我是slotOne1组件我是父组件插槽内容
```
当需要多个插槽时，会用到具名 slot，比如上面的<i-button>组件我们再增加一个 slot，用于设置另一个图标组件：

```
<template>
<button :class="'i-button-size' + size" :disabled="disabled">
<slot name="icon"></slot>
<slot></slot>
</button></template>
```

```
<i-button>
<i-icon slot="icon" type="checkmark"></i-icon>
按钮 3
</i-button>
```
这样，父级内定义的内容，就会出现在组件对应的 slot 里，没有写名字的，就是默认的 slot。<br>
在组件的 <slot> 里也可以写一些默认的内容，这样在父级没有写任何 slot 时，它们就会出现，比如：

```
<slot>提交</slot>
```
## 自定义事件 event
给组件 <i-button> 加一个点击事件，目前有两种写法，先看自定义事件 event（部分代码省略）：

```
<template>
	<button @click="handleClick">
		<slot></slot>
	</button>
</template>
<script>
export default {
	methods: {
		handleClick (event) {
		this.$emit('on-click', event);
		}
	}
}
</script>
```
通过 $emit，就可以触发自定义的事件 on-click ，在父级通过 @on-click 来监听：

```
<i-button @on-click="handleClick"></i-button>
```
上面的 click 事件，是在组件内部的 <button> 元素上声明的，这里还有另一种方法，直接在父级声明，但为了区分原生事件和自定义事件，要用到事件修饰符 .native，所以上面的示例也可以这样写：

```
<i-button @click.native="handleClick"></i-button>
```
如果不写 .native 修饰符，那上面的 @click 就是自定义事件 click，而非原生事件 click，但我们在组件内只触发了 on-click 事件，而不是 click，所以直接写 @click 会监听不到。
# 三、组件的通信
组件一般有以下几种关系： A.vue-->B.vue-->C.vue ;B.vue-->D.vue A 和 B、B 和 C、B 和 D 都是父子关系，C 和 D 是兄弟关系，A 和 C 是隔代关系（可能隔多代）。组件间经常会通信，Vue.js 内置的通信手段一般有两种：

 - ref：给元素或组件注册引用信息； 
 - $parent / $children：访问父 / 子实例。
   这两种都是直接得到组件实例，使用后可以直接调用组件的方法或访问数据，
## 1. ref
比如下面的示例中，用 ref 来访问组件（部分代码省略）：

```
// component-aexport default {
	data () {
		return {
			title: 'Vue.js'
		}
	},
	methods: {
		sayHello () {
			window.alert('Hello');
		}
	}
}
```

```
<template>
	<component-a ref="comA"></component-a>
</template>
<script>
	export default {
		mounted () {
		const comA = this.$refs.comA;
		console.log(comA.title); // Vue.js
		comA.sayHello(); // 弹窗
		}
	}
</script>
```
## 2. $parent / $children
$parent 和 $children 类似，也是基于当前上下文访问父组件或全部子组件的。<br>
$children ： 当前实例的直接子组件。需要注意 $children 并不保证顺序，也不是响应式的。如果你发现自己正在尝试使用 $children 来进行数据绑定，考虑使用一个数组配合 v-for 来生成子组件，并且使用 Array 作为真正的来源。<br>
$parent： 当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。<br>
这两种方法的弊端是，无法在跨级或兄弟间通信<br>
## 3.provide / inject
provide / inject可以解决上述弊端<br>
A.vue-->B.vue,用法：<br>
```
// A.vue
export default {
	provide: {
		name: 'Aresn'
	}
}

// B.vue
export default {
	inject: ['name'],
		mounted () {
			console.log(this.name); // Aresn
		}
}
```
**provide / inject替代 Vuex**
Vuex 做状态管理，它是一个专为 Vue.js 开发的状态管理模式，用于集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。<br>
使用 Vuex，最主要的目的是跨组件通信、全局数据维护、多人协同开发。需求比如有：用户的登录信息维护、通知信息维护等全局的状态和数据。<br>
一般在 webpack 中使用 Vue.js，都会有一个入口文件 main.js，里面通常导入了 Vue、VueRouter、iView 等库，通常也会导入一个入口组件 app.vue 作为根组件。一个简单的 app.vue 可能只有以下代码：

```
<template>
	<div>
		<router-view></router-view>
	</div>
</template>
<script>
	export default {
	}
</script>
```
使用 provide / inject 替代 Vuex，就是在这个 app.vue 文件上做文章。把app.vue 理解为一个最外层的根组件，用来存储所有需要的全局数据和状态，甚至是计算属性（computed）、方法（methods）等。因为你的项目中所有的组件（包含路由），它的父组件（或根组件）都是 app.vue，所以我们把整个 app.vue 实例通过 provide 对外提供。

```
<template>
	<div>
		<router-view></router-view>
	</div>
</template>
<script>
	export default {
		provide () {
			return {
				app: this
			}
	}
}
</script>
```
把整个 app.vue 的实例 this 对外提供，命名为 app（这个名字可以自定义，推荐使用 app，使用这个名字后，子组件不能再使用它作为局部属性）。接下来，任何组件（或路由）只要通过 inject 注入 app.vue 的 app 的话，都可以直接通过 this.app.xxx 来访问 app.vue 的 data、computed、methods 等内容。<br>
**app.vue 是整个项目第一个被渲染的组件，而且只会渲染一次（即使切换路由，app.vue 也不会被再次渲染），利用这个特性，很适合做一次性全局的状态数据管理。**

```
//app.vue
<script>
	export default {
		provide () {
			return {
				app: this
			}
		},
		data () {
			return {
				userInfo: null
			}
		},
		methods: {
			getUserInfo () {
			// 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
				$.ajax('/user/info', (data) => {
					this.userInfo = data;
				});
			}
		},
		mounted () {
			this.getUserInfo();
		}
	}
</script>
```
其它任何界面或组件通过inject注入app 后就可以访问 userInfo 的数据了：

```
<template>
	<div>
	{{ app.userInfo }}
	</div>
</template>
<script>
	export default {
	inject: ['app']
	}
</script>
```
在其他页面导致userInfo更新，需要重新获取时：
```
<template>
	<div>{{ app.userInfo }}</div>
</template>
<script>
	export default {
		inject: ['app'],
		methods: {
			changeUserInfo () {
				// 这里修改完用户数据后，通知 app.vue 更新，以下为伪代码
				$.ajax('/user/update', () => {
				// 直接通过 this.app 就可以调用 app.vue 里的方法
				this.app.getUserInfo();
				})
			}
		}
	}
</script>
```

mixins
例如上面的例子中将用户信息放到混合中：

```
//user.js
export default {
	data () {
		return {
			userInfo: null
		}
	},
	methods: {
		getUserInfo () {
		// 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
			$.ajax('/user/info', (data) => {
				this.userInfo = data;
			});
		}
	},
	mounted () {
	this.getUserInfo();
	}
}
```
在app.vue中混合：

```
<script>
import mixins_user from '../mixins/user.js';

	export default {
		mixins: [mixins_user],
			data () {
				return {
				
				}
			}
	}
</script>
```
app.vue可以直接使用user.js的数据和方法，这样比较容易维护<br>
只要一个组件使用了 provide 向下提供数据，那其下所有的子组件都可以通过 inject 来注入，不管中间隔了多少代，而且可以注入多个来自不同父级提供的数据。需要注意的是，一旦注入了某个数据，比如上面示例中的 app，那这个组件中就不能再声明 app 这个数据了，因为它已经被父级占有。
## 4. on/emit实现父子通信
$emit 会在当前组件实例上触发自定义事件，并传递一些参数给监听器的回调，一般来说，都是在父级调用这个组件时，使用 @on 的方式来监听自定义事件的，比如在子组件中触发事件：

```
// child.vue，部分代码省略
export default {
	methods: {
		handleEmitEvent () {
			this.$emit('test', 'Hello Vue.js');
		}
	}
}
```
在父组件中监听由 child.vue 触发的自定义事件 test：

```
<!-- parent.vue，部分代码省略-->
<template>
	<child-component @test="handleEvent">
</template>
<script>
	export default {
		methods: {
			handleEvent (text) {
				console.log(text); // Hello Vue.js
			}
		}
	}
</script>
```

这里看似是在父组件 parent.vue 中绑定的自定义事件 test 的处理句柄，然而事件 test 并不是在父组件上触发的，而是在子组件 child.vue 里触发的，只是通过 v-on 在父组件中监听。既然是子组件自己触发的，那它自己也可以监听到，这就要使用 $on 来监听实例上的事件，换言之，组件使用 $emit 在自己实例上触发事件，并用 $on 监听它。

```
<template>
	<div>
		<button @click="handleEmitEvent">触发自定义事件</button>
	</div>
</template>
<script>
	export default {
		methods: {
			handleEmitEvent () {
			// 在当前组件上触发自定义事件 test，并传值
			this.$emit('test', 'Hello Vue.js')
			}
		},
		mounted () {
		// 监听自定义事件 test
			this.$on('test', (text) => {
				window.alert(text);
				});
			}
	}
</script>

```
**$on 监听了自己触发的自定义事件 test，**因为有时不确定何时会触发事件，一般会在 mounted 或 created 钩子中来监听。
# 四、Vue 的构造器——extend 与手动挂载——$mount
## 1.使用场景
在写vue.js时不论是用 CDN 的方式还是在 Webpack 里用 npm 引入的 Vue.js，都会有一个根节点，并且创建一个根实例：

```
<body
	<div id="app"></div>
</body>
<script>
	const app = new Vue({
	el: '#app'
	});
</script>
```
Webpack一般在入口文件 main.js 里，最后会创建一个实例：

```
import Vue from 'vue';
import App from './app.vue';
new Vue({
	el: '#app',
	render: h => h(App)
});
```
因为用 Webpack 基本都是前端路由的，它的 html 里一般都只有一个根节点<br>。组件只需注册即可使用，有一下几个特点：
- 所有的内容，都是在 #app 节点内渲染的；
- 组件的模板，是事先定义好的；
- 由于组件的特性，注册的组件只能在当前位置渲染。


常规的组件无法解决以下问题：

 - 组件的模板是通过调用接口从服务端获取的，需要动态渲染组件； 
 - 实现类似原生 window.alert() 的提示框组件，它的位置是在
   <body> 下，而非 ，并且不会通过常规的组件自定义标签的形式使用，而是像 JS 调用函数一样使用
对于这两种场景可以使用Vue.extend 和 vm.$mount语法解决。
## 2.用法
创建一个 Vue 实例时，都会有一个选项 el，来指定实例的根节点，如果不写 el 选项，那组件就处于未挂载状态。Vue.extend 的作用，就是基于 Vue 构造器，创建一个“子类”，它的参数跟 new Vue 的基本一样，但 data 要跟组件一样，是个函数，再配合 $mount ，就可以让组件渲染，并且挂载到任意指定的节点上，比如 body。

```
import Vue from 'vue';

const AlertComponent = Vue.extend({
	template: '<div>{{ message }}</div>',
	data () {
		return {
			message: 'Hello, Aresn'
		};
	},
});
```
这一步创建了一个构造器，这个过程就可以解决异步获取 template 模板的问题，下面要手动渲染组件，并把它挂载到 body 下：

```
const component = new AlertComponent().$mount();
```
这一步调用了 $mount 方法对组件进行了手动渲染，但它仅仅是被渲染好了，并没有挂载到节点上，也就显示不了组件。此时的 component 已经是一个标准的 Vue 组件实例，因此它的 $el 属性也可以被访问：

```
document.body.appendChild(component.$el);
```
除了 body还可以挂载到其它节点上。<br>
$mount 也有一些快捷的挂载方式，以下两种都是可以的：

```
// 在 $mount 里写参数来指定挂载的节点new AlertComponent().$mount('#app');
// 不用 $mount，直接在创建实例时指定 el 选项new AlertComponent({ el: '#app' });
```
实现同样的效果，除了用 extend 外，也可以直接创建 Vue 实例，并且用一个 Render 函数来渲染一个 .vue 文件：

```
import Vue from 'vue';
import Notification from './notification.vue';

const props = {}; // 这里可以传入一些组件的 props 选项

const Instance = new Vue({
	render (h) {
		return h(Notification, {
		props: props
		});
	}
});

const component = Instance.$mount();
document.body.appendChild(component.$el);
```
渲染后，如果想操作 Render 的 Notification 实例：

```
const notification = Instance.$children[0];
```
因为 Instance 下只 Render 了 Notification 一个子组件，所以可以用 $children[0] 访问到。
需要注意的是，采用 $mount 手动渲染的组件，如果要销毁，也要用 $destroy 来手动销毁实例，必要时，也可以用 removeChild 把节点从 DOM 中移除。
# 四、动态渲染 .vue 文件的组件—— Display
动态渲染的核心技术就是extend 和 $mount。
## 1.接口设计
一个常规的 .vue 文件一般都会包含 3 个部分：

```
- <template>：组件的模板；
- <script>：组件的选项，不包含 el；
- <style>：CSS 样式。
```
Display 是一个功能型的组件，没有交互和事件，只需要一个 prop：code 将 .vue 的内容传递过来，其余工作都是在组件内完成的，这对使用者很友好。当然，你也可以设计成三个 props，分别对应 html、js、css，那分割的工作就要使用者来完成。但第一个方案更方便。
## 2.实现
在 src/components 目录下创建 display 目录，并新建 display.vue 文件，基本结构如下：

```
<!-- display.vue -->
<template>
	<div ref="display"></div>
</template>
<script>
export default {
	props: {
		code: {
			type: String,
			default: ''
		}
	},
	data () {
		return {
			html: '',
			js: '',
			css: ''
		}
	},
}
</script>
```
父级传递 code 后，将其分割，并保存在 data 的 html、js、css 中，后续使用。
使用正则，基于 <> 和 </> 的特性进行分割：

```
// display.vue，部分代码省略
export default {
	methods: {
		getSource (source, type) {
			const regex = new RegExp(`<${type}[^>]*>`);
			let openingTag = source.match(regex);
			
			if (!openingTag) return '';
				else openingTag = openingTag[0];
			
			return source.slice(source.indexOf(openingTag) + openingTag.length, 		source.lastIndexOf(`</${type}>`));
		},
	splitCode () {
		const script = this.getSource(this.code, 'script').replace(/export default/, 'return ');
		const style = this.getSource(this.code, 'style');
		const template = '<div id="app">' + this.getSource(this.code, 'template') + '</div>';
		this.js = script;
		this.css = style;
		this.html = template;
	},
	}
}
```
getSource 方法接收两个参数：
- source：.vue 文件代码，即 props: code；
- type：分割的部分，也就是 template、script、style。

分割后，返回的内容不再包含 <template> 等标签，直接是对应的内容，在 splitCode 方法中，把分割好的代码分别赋值给 data 中声明的 html、js、css。有两个细节需要注意：<br>


1. vue 的 `<script>` 部分一般都是以 export default 开始的，可以看到在 splitCode 方法中将它替换为了 return，这个在后文会做解释，当前只要注意，我们分割完的代码，仍然是字符串；
2.  在分割的 `<template>` 外层套了一个，这是为了容错，有时使用者传递的 code 可能会忘记在外层包一个节点，没有根节点的组件，是会报错的。

准备好这些基础工作后，就可以用 extend 渲染组件了，由于当前的 this.js 是字符串，而 extend 接收的选项可不是字符串，而是一个对象类型，那就要先把 this.js 转为一个对象。 new Function语法可以做到，rg1, arg2, ... argN 是被函数使用的参数名称，functionBody 是一个含有包括函数定义的 JavaScript 语句的字符串：

```
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```
例如：

```
const sum = new Function('a', 'b', 'return a + b');

console.log(sum(2, 6)); // 8
```
用 extend 渲染组件：

```
<!-- display.vue，部分代码省略 -->
<template>
	<div ref="display"></div>
</template>
<script>
import Vue from 'vue';
export default {
	data () {
		return {
			component: null
		}
	},
	methods: {
		renderCode () {
			this.splitCode();
			if (this.html !== '' && this.js !== '') {
				const parseStrToFunc = new Function(this.js)();
				parseStrToFunc.template = this.html;
				const Component = Vue.extend( parseStrToFunc );
				this.component = new Component().$mount();
				this.$refs.display.appendChild(this.component.$el);
			}
		}
	},
	mounted () {
		this.renderCode();
	}
}
</script>
```
extend 构造的实例通过 $mount 渲染后，挂载到了组件唯一的一个节点上。 加载 css 可以创建一个 `<style>` 标签，然后把 css 写进去，再插入到页面的 `<head>` 中，这样 css 就被浏览器解析了。为了便于后面在 this.code 变化或组件销毁时移除动态创建的 `<style>` 标签，给每个 style 标签加一个随机 id 用于标识。在 src/utils 目录下新建 random_str.js 文件，并写入以下内容：

```
// 从指定的 a-zA-Z0-9 中随机生成 32 位的字符串
export default function (len = 32) {
	const $chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890';
	const maxPos = $chars.length;
	let str = '';
	for (let i = 0; i < len; i++) {
		str += $chars.charAt(Math.floor(Math.random() * maxPos));
	}
	return str;
}
```
补全 renderCode 方法：

```
// display.vue，部分代码省略import randomStr from '../../utils/random_str.js';

export default {
	data () {
		return {
			id: randomStr()
		}
	},
	methods: {
		renderCode () {
			if (this.html !== '' && this.js !== '') {
				// ...
				if (this.css !== '') {
					const style = document.createElement('style');
					style.type = 'text/css';
					style.id = this.id;
					style.innerHTML = this.css;
					document.getElementsByTagName('head')[0].appendChild(style);
				}
			}
		}
	}
	}
	当 Display 组件销毁时，也要手动销毁 extend 创建的实例以及上面的 css：
	
	// display.vue，部分代码省略
	export default {
		methods: {
			destroyCode () {
				const $target = document.getElementById(this.id);
				if ($target) $target.parentNode.removeChild($target);
					if (this.component) {
						this.$refs.display.removeChild(this.component.$el);
						this.component.$destroy();
						this.component = null;
					}
			}
		},
		beforeDestroy () {
		this.destroyCode();
		}
}
```
当 this.code 更新时，整个过程要重新来一次，所以要对 code 进行 watch 监听：

```
// display.vue，部分代码省略
export default {
	watch: {
		code () {
			this.destroyCode();
			this.renderCode();
		}
	}
}
```
## 4.使用
新建一条路由，并在 src/views 下新建页面 display.vue 来使用 Display 组件：

```
<!-- src/views/display.vue -->
<template>
	<div>
	<h3>动态渲染 .vue 文件的组件—— Display</h3>
	<i-display :code="code"></i-display>
	</div>
</template>
<script>
import iDisplay from '../components/display/display.vue';
import defaultCode from './default-code.js';
export default {
	components: { iDisplay },
	data () {
		return {
		code: defaultCode
		}
	}
}
</script>
// src/views/default-code.js
const code =`<template><div><input v-model="message">{{ message }}</div></template><script>
export default {
	data () {
		return {
		message: ''
		}
	}
}
</script>`;
export default code;
```
