# vue基础

## mvc和mvvm理解

### MVC

MVC即Model View Controller，简单来说就是通过controller的控制去操作model层的数据，并且返回给view层展示。

- View 接受用户交互请求
- View 将请求转交给Controller处理
- Controller 操作Model进行数据更新保存
- 数据更新保存之后，Model会通知View更新
- View 更新变化数据使用户得到反馈

### MVVM

MVVM即Model-View-ViewModel，将其中的 View 的状态和行为抽象化，让我们可以将UI和业务逻辑分开。MVVM的优点是低耦合、可重用性、独立开发。



![mvvm.jpg](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eed86dd3722?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



- View 接收用户交互请求
- View 将请求转交给ViewModel
- ViewModel 操作Model数据更新
- Model 更新完数据，通知ViewModel数据发生变化
- ViewModel 更新View数据

MVVM模式和MVC有些类似，但有以下不同

- ViewModel 替换了 Controller，在UI层之下
- ViewModel 向 View 暴露它所需要的数据和指令对象
- ViewModel 接收来自 Model 的数据

概括起来，MVVM是由MVC发展而来，通过在`Model`之上而在`View`之下增加一个非视觉的组件将来自`Model`的数据映射到`View`中。



















# vue生命周期

## 生命周期函数

- beforeCreate(创建前) vue实例的挂载元素$el和数据对象 data都是undefined, 还未初始化
- created(创建后) 完成了 data数据初始化, el还未初始化
- beforeMount(载入前) vue实例的$el和data都初始化了, 相关的render函数首次被调用
- mounted(载入后) 此过程中进行ajax交互
- beforeUpdate(更新前)
- updated(更新后)
- beforeDestroy(销毁前)
- destroyed(销毁后)

### 1. vue组件有哪些生命周期钩子？

beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestroy、destroyed。
 `keepalived` 有自己独立的钩子函数 activated 和 deactivated。

### 2. Vue 的父组件和子组件生命周期钩子执行顺序是什么？

**渲染过程：**
 父组件挂载完成一定是等子组件都挂载完成后，才算是父组件挂载完，所以父组件的mounted在子组件mouted之后
 父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate -> 子created -> 子beforeMount -> 子mounted -> 父mounted

**子组件更新过程：**

1. 影响到父组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updated
2. 不影响父组件： 子beforeUpdate -> 子updated

**父组件更新过程：**

1. 影响到子组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
2. 不影响子组件： 父beforeUpdate -> 父updated

**销毁过程：**
 父beforeDestroy -> 子beforeDestroy -> 子destroyed -> 父destroyed

 Vue 生命周期

生命周期函数就是组件在初始化或者数据更新时会触发的钩子函数。

在初始化时，会调用以下代码，生命周期就是通过 `callHook` 调用的

```
Vue.prototype._init = function(options) {
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate') // 拿不到 props data
    initInjections(vm) 
    initState(vm)
    initProvide(vm)
    callHook(vm, 'created')
}
复制代码
```

可以发现在以上代码中，`beforeCreate` 调用的时候，是获取不到 props 或者 data 中的数据的，因为这些数据的初始化都在 `initState` 中。

接下来会执行挂载函数

```
export function mountComponent {
    callHook(vm, 'beforeMount')
    // ...
    if (vm.$vnode == null) {
        vm._isMounted = true
        callHook(vm, 'mounted')
    }
}
复制代码
```

`beforeMount` 就是在挂载前执行的，然后开始创建 VDOM 并替换成真实 DOM，最后执行 `mounted` 钩子。这里会有个判断逻辑，如果是外部 `new Vue({})` 的话，不会存在 `$vnode` ，所以直接执行 `mounted` 钩子了。如果有子组件的话，会递归挂载子组件，只有当所有子组件全部挂载完毕，才会执行根组件的挂载钩子。

接下来是数据更新时会调用的钩子函数

```
function flushSchedulerQueue() {
  // ...
  for (index = 0; index < queue.length; index++) {
    watcher = queue[index]
    if (watcher.before) {
      watcher.before() // 调用 beforeUpdate
    }
    id = watcher.id
    has[id] = null
    watcher.run()
    // in dev build, check and stop circular updates.
    if (process.env.NODE_ENV !== 'production' && has[id] != null) {
      circular[id] = (circular[id] || 0) + 1
      if (circular[id] > MAX_UPDATE_COUNT) {
        warn(
          'You may have an infinite update loop ' +
            (watcher.user
              ? `in watcher with expression "${watcher.expression}"`
              : `in a component render function.`),
          watcher.vm
        )
        break
      }
    }
  }
  callUpdatedHooks(updatedQueue)
}

function callUpdatedHooks(queue) {
  let i = queue.length
  while (i--) {
    const watcher = queue[i]
    const vm = watcher.vm
    if (vm._watcher === watcher && vm._isMounted) {
      callHook(vm, 'updated')
    }
  }
}
复制代码
```

上图还有两个生命周期没有说，分别为 `activated` 和 `deactivated` ，这两个钩子函数是 `keep-alive` 组件独有的。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

最后就是销毁组件的钩子函数了

```
Vue.prototype.$destroy = function() {
  // ...
  callHook(vm, 'beforeDestroy')
  vm._isBeingDestroyed = true
  // remove self from parent
  const parent = vm.$parent
  if (parent && !parent._isBeingDestroyed && !vm.$options.abstract) {
    remove(parent.$children, vm)
  }
  // teardown watchers
  if (vm._watcher) {
    vm._watcher.teardown()
  }
  let i = vm._watchers.length
  while (i--) {
    vm._watchers[i].teardown()
  }
  // remove reference from data ob
  // frozen object may not have observer.
  if (vm._data.__ob__) {
    vm._data.__ob__.vmCount--
  }
  // call the last hook...
  vm._isDestroyed = true
  // invoke destroy hooks on current rendered tree
  vm.__patch__(vm._vnode, null)
  // fire destroyed hook
  callHook(vm, 'destroyed')
  // turn off all instance listeners.
  vm.$off()
  // remove __vue__ reference
  if (vm.$el) {
    vm.$el.__vue__ = null
  }
  // release circular reference (#6759)
  if (vm.$vnode) {
    vm.$vnode.parent = null
  }
}
复制代码
```

在执行销毁操作前会调用 `beforeDestroy` 钩子函数，然后进行一系列的销毁操作，如果有子组件的话，也会递归销毁子组件，所有子组件都销毁完毕后才会执行根组件的 `destroyed` 钩子函数。























# vue相关属性

## v-model双向绑定原理

v-model本质上是语法糖，v-model 在内部为不同的输入元素使用不同的属性并抛出不同的事件。

- text 和 textarea 元素使用 value 属性和 input 事件
- checkbox 和 radio 使用 checked 属性和 change 事件
- select 字段将 value 作为 prop 并将 change 作为事件

所以我们可以v-model进行如下改写：

```
<input v-model="sth" />
//  等同于
<input :value="sth" @input="sth = $event.target.value" /> 
```

这个语法糖必须是固定的，也就是说属性必须为value，方法名必须为：input。

知道了v-model的原理，我们可以在自定义组件上实现v-model。

```js
//Parent
<template>
    {{num}}
    <Child v-model="num">
</template>
export default {
    data(){
        return {
            num: 0
        }
    }
}

//Child
<template>
    <div @click="add">Add</div>
</template>
export default {
    props: ['value'],
    methods:{
        add(){
            this.$emit('input', this.value + 1)
        }
    }
} 
```

```js
//parent
<template>
	{{num}}
	<Child v-model="num"/>
</template>
export default {
	data(){
		return {
			num:0
		}
	}
}

//child
<template>
	<input type="text"
		:value="text1"
		@input="$emit('change', $event.target.value)"
	>
</template>
export default {
	model: {
		prop: 'text1'
		event: change
	}
	props: {
	  text1: String
	  default() {
	  	return ''
	  }
	}
}
	
```



## key的作用

1. 让vue精准的追踪到每一个元素，`高效的更新虚拟DOM`。
2. 触发过渡

```
<transition>
  <span :key="text">{{ text }}</span>
</transition> 
```

当text改变时，这个元素的key属性就发生了改变，在渲染更新时，Vue会认为这里新产生了一个元素，而老的元素由于key不存在了，所以会被删除，从而触发了过渡。

## scoped属性作用

> 在Vue文件中的style标签上有一个特殊的属性，scoped。当一个style标签拥有scoped属性时候，它的css样式只能用于当前的Vue组件，可以使组件的样式不相互污染。如果一个项目的所有style标签都加上了scoped属性，相当于实现了样式的模块化。

scoped属性的实现原理是给每一个dom元素添加了一个独一无二的动态属性，给css选择器额外添加一个对应的属性选择器，来选择组件中的dom。

```
<template>
    <div class="box">dom</div>
</template>
<style lang="scss" scoped>
.box{
    background:red;
}
</style> 
```

vue将代码转译成如下：

```
.box[data-v-11c6864c]{//属性选择器
    background:red;
}
<template>
    <div class="box" data-v-11c6864c>dom</div>
</template> 
```

## scoped样式穿透

scoped虽然避免了组件间样式污染，但是很多时候我们需要修改组件中的某个样式，但是又不想去除scoped属性。

1. 使用/deep/

```html
//Parent
<template>
<div class="wrap">
    <Child />
</div>
</template>

<style lang="scss" scoped>
.wrap /deep/ .box{
    background: red;
}
</style>

//Child
<template>
    <div class="box"></div>
</template> 
```

1. 使用两个style标签

```html
//Parent
<template>
<div class="wrap">
    <Child />
</div>
</template>

<style lang="scss" scoped>
//其他样式
</style>
<style lang="scss">
.wrap .box{
    background: red;
}
</style>

//Child
<template>
    <div class="box"></div>
</template> 
```

## ref的作用

1. 获取dom元素`this.$refs.box`
2. 获取子组件中的data`this.$refs.box.msg`
3. 调用子组件中的方法`this.$refs.box.open()`

### 1. v-show 和 v-if 有哪些区别？

`v-if` 会在切换过程中对条件块的事件监听器和子组件进行销毁和重建，如果初始条件是false，则什么都不做，直到条件第一次为true时才开始渲染模块。
 `v-show` 只是基于css进行切换，不管初始条件是什么，都会渲染。
 所以，`v-if` 切换的开销更大，而 `v-show` 初始化渲染开销更大，在需要频繁切换，或者切换的部分dom很复杂时，使用 `v-show` 更合适。渲染后很少切换的则使用 `v-if` 更合适。

### 2. computed 和 watch 有什么区别？

`computed` 计算属性，是依赖其他属性的计算值，并且有缓存，只有当依赖的值变化时才会更新。
`watch` 是在监听的属性发生变化时，在回调中执行一些逻辑。
所以，`computed` 适合在模板渲染中，某个值是依赖了其他的响应式对象甚至是计算属性计算而来，而 `watch` 适合监听某个值的变化去完成一段复杂的业务逻辑。

watch可以获取值类型的oldvalue，不能获取引用类型的oldvalue

- computed 就是简单计算一下，适用于渲染页面。watch 适合做一些复杂业务逻辑
- 前者有依赖两个 watcher，computed watcher 和渲染 watcher。判断计算出的值变化后渲染 watcher 派发更新触发渲染

### 3. computed vs methods

计算属性是基于他们的响应式依赖进行缓存的，只有在依赖发生变化时，才会计算求值，而使用 `methods`，每次都会执行相应的方法。

### 4. keep-alive 的作用是什么？

`keep-alive` 可以在组件切换时，保存其包裹的组件的状态，使其不被销毁，防止多次渲染。
其拥有两个独立的生命周期钩子函数 actived 和 deactived，使用 `keep-alive` 包裹的组件在切换时不会被销毁，而是缓存到内存中并执行 deactived 钩子函数，命中缓存渲染后会执行 actived 钩子函数。

### 5. Vue 中 v-html 会导致什么问题

在网站上动态渲染任意 HTML，很容易导致 XSS 攻击。所以只能在可信内容上使用 v-html，且永远不能用于用户提交的内容上。

### Vue 双向绑定

- 在初始化 data props 时，递归对象，给每一个属性双向绑定，对于数组而言，会拿到原型重写函数，实现手动派发更新。因为函数不能监听到数据的变动，和 proxy 比较一下。
- 除了以上数组函数，通过索引改变数组数据或者给对象添加新属性也不能触发，需要使用自带的set 函数，这个函数内部也是手动派发更新
- 在组件挂载时，会实例化渲染观察者，传入组件更新的回调。在实例化过程中，会对模板中的值对象进行求值，触发依赖收集。在触发依赖之前，会保存当前的渲染观察者，用于组件含有子组件的时候，恢复父组件的观察者。触发依赖收集后，会清理掉不需要的依赖，性能优化，防止不需要的地方去重复渲染。
- 改变值会触发依赖更新，会将收集到的所有依赖全部拿出来，放入 nextTick 中统一执行。执行过程中，会先对观察者进行排序，渲染的最后执行。先执行 beforeupdate 钩子函数，然后执行观察者的回调。在执行回调的过程中，可能 watch 会再次 push 进来，因为存在在回调中再次赋值，判断无限循环。

### v-model原理

- v:model 在模板编译的时候转换代码
- v-model 本质是 :value 和 v-on，但是略微有点区别。在输入控件下，有两个事件监听，输入中文时只有当输出中文才触发数据赋值
- v-model 和:bind 同时使用，前者优先级更高，如果 :value 会出现冲突
- v-model 因为语法糖的原因，还可以用于父子通信 

### 表单

<textarea value={sd}>sds</textarea>










# vue组件通信 

## 组件之间的传值通信

1. 父组件给子组件传值通过props
2. 子组件给父组件传值通过$emit触发回调
3. vuex状态管理
4. 兄弟组件通信，通过实例一个vue实例eventBus作为媒介，要相互通信的兄弟组件之中，都引入eventBus

```js
//main.js
import Vue from 'vue'
export const eventBus = new Vue()

//brother1.vue
import eventBus from '@/main.js'
export default{
	methods: {
	    toBus () {
	        eventBus.$emit('greet', 'hi brother')
	    }
	}
}

//brother2
import eventBus from '@/main.js'
export default{
    mounted(){
        eventBus.$on('greet', (msg)=>{
            this.msg = msg
        })
    }
} 
```

### 1. 组件通信方式有哪些？

**父子组件通信：**

```
props` 和 `event`、`v-model`、 `.sync`、 `ref`、 `$parent` 和 `$children
```

**非父子组件通信：**

```
$attr` 和 `$listeners`、 `provide` 和 `inject`、`eventbus`、通过根实例`$root`访问、`vuex`、`dispatch` 和 `brodcast
```

  

### 2. 子组件为什么不可以修改父组件传递的Prop？

Vue提倡单向数据流,即父级 `props` 的更新会流向子组件,但是反过来则不行。这是为了防止意外的改变父组件状态，使得应用的数据流变得难以理解。如果破坏了单向数据流，当应用复杂时，debug 的成本会非常高。

### 4. Vuex和单纯的全局对象有什么区别？

Vuex和全局对象主要有两大区别：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

### 5. 为什么 Vuex 的 mutation 中不能做异步操作？

Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。 

### Vue 的父子通信

- 使用 `v-model` 实现父传子，子传父。因为 v-model 默认解析成 :value 和 :input
- 父传子 
  - 通过 `props`
  - 通过 `$children` 访问子组件数组，注意该数组乱序
  - 对于多级父传子，可以使用 `v-bind={$attrs}` ，通过对象的方式筛选出父组件中传入但子组件不需要的 props
  - $listens 包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。
- 子传父 
  - 父组件传递函数给子组件，子组件通过 `$emit` 触发
  - 修改父组件的 `props`
  - 通过 `$parent` 访问父组件
  - .sync
- 平行组件 
  - EventBus
- Vuex 解决一切













# vue 路由

### 1. Vue-router 导航守卫有哪些

全局前置/钩子：beforeEach、beforeResolve、afterEach

路由独享的守卫：beforeEnter

组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

完整的导航解析流程如下：

1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

### 2. vue-router hash 模式和 history 模式有什么区别？

区别：

1. url 展示上，hash 模式有“#”，history 模式没有
2. 刷新页面时，hash 模式可以正常加载到 hash 值对应的页面，而 history 没有处理的话，会返回 404，一般需要后端将所有页面都配置重定向到首页路由。
3. 兼容性。hash 可以支持低版本浏览器和 IE。

### vue路由有几种模式

1. hash模式

即地址栏URL中的#符号，它的特点在于：hash 虽然出现URL中，但不会被包含在HTTP请求中，对后端完全没有影响，不需要后台进行配置，因此改变hash不会重新加载页面。

1. history模式

利用了HTML5 History Interface 中新增的pushState() 和replaceState() 方法（需要特定浏览器支持）。history模式改变了路由地址，因为需要后台配置地址。

window.onhashchange 

history.pushState()和window.onpopstate()

### 3. vue-router hash 模式和 history 模式是如何实现的？

**hash 模式：**
 `#`后面 hash 值的变化，不会导致浏览器向服务器发出请求，浏览器不发出请求，就不会刷新页面。同时通过监听 hashchange 事件可以知道 hash 发生了哪些变化，然后根据 hash 变化来实现更新页面部分内容的操作。

**history 模式：**
 history 模式的实现，主要是 HTML5 标准发布的两个 API，`pushState` 和 `replaceState`，这两个 API 可以在改变 url，但是不会发送请求。这样就可以监听 url 变化来实现更新页面部分内容的操作。

路由懒加载

```
routes：[{
		path：'/user',
		component：() => import ('./components/user')
	}
]
```





### axios拦截器怎么配

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
}, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
});
// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
 }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error); 
});
```

### 路由原理

前端路由实现起来其实很简单，本质就是监听 URL 的变化，然后匹配路由规则，显示相应的页面，并且无须刷新。目前单页面使用的路由就只有两种实现方式

- hash 模式
- history 模式

`www.test.com/#/` 就是 Hash URL，当 `#` 后面的哈希值发生变化时，不会向服务器请求数据，可以通过 `hashchange` 事件来监听到 URL 的变化，从而进行跳转页面。



![img](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="942" height="493"></svg>)



History 模式是 HTML5 新推出的功能，比之 Hash URL 更加美观



![img](data:image/svg+xml;utf8,<?xml version="1.0"?><svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1244" height="585"></svg>)



























# vue原理

### MVVM

MVVM 由以下三个内容组成

- View：界面
- Model：数据模型
- ViewModel：作为桥梁负责沟通 View 和 Model

在 JQuery 时期，如果需要刷新 UI 时，需要先取到对应的 DOM 再更新 UI，这样数据和业务的逻辑就和页面有强耦合。

在 MVVM 中，UI 是通过数据驱动的，数据一旦改变就会相应的刷新对应的 UI，UI 如果改变，也会改变对应的数据。这种方式就可以在业务处理中只关心数据的流转，而无需直接和页面打交道。ViewModel 只关心数据和业务的处理，不关心 View 如何处理数据，在这种情况下，View 和 Model 都可以独立出来，任何一方改变了也不一定需要改变另一方，并且可以将一些可复用的逻辑放在一个 ViewModel 中，让多个 View 复用这个 ViewModel。

在 MVVM 中，最核心的也就是数据双向绑定，例如 Angluar 的脏数据检测，Vue 中的数据劫持。

#### 脏数据检测

当触发了指定事件后会进入脏数据检测，这时会调用 `$digest` 循环遍历所有的数据观察者，判断当前值是否和先前的值有区别，如果检测到变化的话，会调用 `$watch` 函数，然后再次调用 `$digest` 循环直到发现没有变化。循环至少为二次 ，至多为十次。

脏数据检测虽然存在低效的问题，但是不关心数据是通过什么方式改变的，都可以完成任务，但是这在 Vue 中的双向绑定是存在问题的。并且脏数据检测可以实现批量检测出更新的值，再去统一更新 UI，大大减少了操作 DOM 的次数。所以低效也是相对的，这就仁者见仁智者见智了。

## 响应原理

vue采用数据劫持结合发布者-订阅者模式的方式，通过`Object.defineProperty`劫持data属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。 

## 组件data为什么返回函数

组件中的data写成一个函数，数据以函数返回值形式定义，这样每复用一次组件，就会返回一份新的data。如果单纯的写成对象形式，就使得所有组件实例共用了一份data，造成了数据污染。

## vue给对象新增属性页面没有响应

由于Vue会在初始化实例时对属性执行`getter/setter`转化，所以属性必须在`data`对象上存在才能让Vue将它转换为响应式的。Vue提供了$set方法用来触发视图更新。

```
export default {
    data(){
        return {
            obj: {
                name: 'fei'
            }
        }
    },
    mounted(){
        this.$set(this.obj, 'sex', 'man')
    } 
} 
```

#### 数据劫持

Vue 内部使用了 `Object.defineProperty()` 来实现双向绑定，通过这个函数可以监听到 `set` 和 `get` 的事件。

```js
var data = { 
  name: 'yck' 
  obj: {}
}
observe(data)
let name = data.name // -> get value
data.name = 'yyy' // -> change value

//对数组进行监听要单独处理
const oldArrayProperty = Array.prototype
const arrProto = Object.create(oldArrayProperty)
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
	arrProto[methodName] = function() {
		updateView()
		oldArrayProperty[methodName].call(this, ...arguments)
	} 
})


function observe(obj) {
  // 判断类型
  if (!obj || typeof obj !== 'object') {
    return
  }
  if(Array.isArray(obj)){
    obj.__proto__ = arrProto
  }
  Object.keys(obj).forEach(key => {
    defineReactive(obj, key, obj[key])
  })
}

function defineReactive(obj, key, val) {
  // 递归子属性
  observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      console.log('get value')
      return val
    },
    set: function reactiveSetter(newVal) {
      console.log('change value')
      //对属性进行新增属性，如data.obj.a = 'a'
      observe(newVal)
      val = newVal
    }
  })
} 
```

以上代码简单的实现了如何监听数据的 `set` 和 `get` 的事件，但是仅仅如此是不够的，还需要在适当的时候给属性添加发布订阅

```
<div>
    {{name}}
</div> 
```

在解析如上模板代码时，遇到 `{{name}}` 就会给属性 `name` 添加发布订阅。

```js
// 通过 Dep 解耦
class Dep {
  constructor() {
    this.subs = []
  }
  addSub(sub) {
    // sub 是 Watcher 实例
    this.subs.push(sub)
  }
  notify() {
    this.subs.forEach(sub => {
      sub.update()
    })
  }
}
// 全局属性，通过该属性配置 Watcher
Dep.target = null

function update(value) {
  document.querySelector('div').innerText = value
}

class Watcher {
  constructor(obj, key, cb) {
    // 将 Dep.target 指向自己
    // 然后触发属性的 getter 添加监听
    // 最后将 Dep.target 置空
    Dep.target = this
    this.cb = cb
    this.obj = obj
    this.key = key
    this.value = obj[key]
    Dep.target = null
  }
  update() {
    // 获得新值
    this.value = this.obj[this.key]
    // 调用 update 方法更新 Dom
    this.cb(this.value)
  }
}
var data = { name: 'yck' }
observe(data)
// 模拟解析到 `{{name}}` 触发的操作
new Watcher(data, 'name', update)
// update Dom innerText
data.name = 'yyy'  
```

接下来,对 `defineReactive` 函数进行改造

```
function defineReactive(obj, key, val) {
  // 递归子属性
  observe(val)
  let dp = new Dep()
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      console.log('get value')
      // 将 Watcher 添加到订阅
      if (Dep.target) {
        dp.addSub(Dep.target)
      }
      return val
    },
    set: function reactiveSetter(newVal) {
      console.log('change value')
      val = newVal
      // 执行 watcher 的 update 方法
      dp.notify()
    }
  })
} 
```

以上实现了一个简易的双向绑定，核心思路就是手动触发一次属性的 getter 来实现发布订阅的添加。

 

### 1. Vue 的响应式原理

如果面试被问到这个问题，又描述不清楚，可以直接画出 Vue 官方文档的这个图，对着图来解释效果会更好。 

![img](https://user-gold-cdn.xitu.io/2020/2/20/17062a3aa3acd499?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 Vue 的响应式是通过 `Object.defineProperty Object.defineProperty``observe``getter``setter``watcher``getter``setter  watcher`



### 2. Object.defineProperty有哪些缺点？

这道题目也可以问成 “为什么vue3.0使用proxy实现响应式？” 其实都是对Object.defineProperty 和 proxy实现响应式的对比。

1. `Object.defineProperty` 只能劫持对象的属性，而 `Proxy` 是直接代理对象
   由于 `Object.defineProperty` 只能对属性进行劫持，需要遍历对象的每个属性，计算量大。而 `Proxy` 可以直接代理对象。
2. `Object.defineProperty` 对新增属性需要手动进行 `Observe`， 由于 `Object.defineProperty` 劫持的是对象的属性，所以新增属性时，需要重新遍历对象，对其新增属性再使用 `Object.defineProperty` 进行劫持。 也正是因为这个原因，使用 Vue 给 `data` 中的数组或对象新增属性时，需要使用 `vm.$set` 才能保证新增的属性也是响应式的。Vue.set
3. `Proxy` 支持13种拦截操作，这是 `defineProperty` 所不具有的。
4. 新标准性能红利
   `Proxy` 作为新标准，长远来看，JS引擎会继续优化 `Proxy` ，但 `getter` 和 `setter` 基本不会再有针对性优化。
5. `Proxy` 兼容性差 目前并没有一个完整支持 `Proxy` 所有拦截方法的Polyfill方案

更详细的对比，可以查看我的文章 [为什么Vue3.0不再使用defineProperty实现数据监听？](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247485104&idx=1&sn=648f1850fb59f04c6ca52cdd794013b5&chksm=ce5e34cbf929bddd4325de7f78dc4ad5822cddff69c999d1225f860e917dfd3d6eea43527c56&token=1424393752&lang=zh_CN#rd)

### 3. Vue中如何检测数组变化？

Vue 的 `Observer` 对数组做了单独的处理，对数组的方法进行编译，并赋值给数组属性的 `__proto__` 属性上，因为原型链的机制，找到对应的方法就不会继续往上找了。编译方法中会对一些会增加索引的方法（`push`，`unshift`，`splice`）进行手动 observe。 具体同样可以参考我的这篇文章 [为什么Vue3.0不再使用defineProperty实现数据监听？](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247485104&idx=1&sn=648f1850fb59f04c6ca52cdd794013b5&chksm=ce5e34cbf929bddd4325de7f78dc4ad5822cddff69c999d1225f860e917dfd3d6eea43527c56&token=1424393752&lang=zh_CN#rd)，里面有详细的源码分析。

```
const oldArrayProperty = Array.prototype
const arrObject = Object.create(oldArrayProperty)
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
	arrObject[methodName] = function() {
		updateView()
		oldArrayProperty[methodName].call(this, ...arguments)
	} 
})
```

### 5. nextTick是做什么用的，其原理是什么?

能回答清楚这道问题的前提，是清楚 EventLoop 过程。
 在下次 DOM 更新循环结束后执行延迟回调，在修改数据之后立即使用 `nextTick` 来获取更新后的 DOM。
 `nextTick` 对于 micro task 的实现，会先检测是否支持 `Promise`，不支持的话，直接指向 macro task，而 macro task 的实现，优先检测是否支持 `setImmediate`（高版本IE和Etage支持），不支持的再去检测是否支持 MessageChannel，如果仍不支持，最终降级为 `setTimeout` 0；
 默认的情况，会先以 `micro task` 方式执行，因为 micro task 可以在一次 tick 中全部执行完毕，在一些有重绘和动画的场景有更好的性能。
 但是由于 micro task 优先级较高，在某些情况下，可能会在事件冒泡过程中触发，导致一些问题(可以参考 Vue 这个 issue：[github.com/vuejs/vue/i…](https://github.com/vuejs/vue/issues/6556))，所以有些地方会强制使用 macro task （如 `v-on`）。

> 注意：之所以将 `nextTick` 的回调函数放入到数组中一次性执行，而不是直接在 `nextTick` 中执行回调函数，是为了保证在同一个tick内多次执行了 `nextTcik`，不会开启多个异步任务，而是把这些异步任务都压成一个同步任务，在下一个tick内执行完毕。

### 6. Vue 的模板编译原理

vue模板的编译过程分为3个阶段：

1. 第一步：解析
   将模板字符串解析生成 AST，生成的AST 元素节点总共有 3 种类型，1 为普通元素， 2 为表达式，3为纯文本。
2. 第二步：优化语法树
   Vue 模板中并不是所有数据都是响应式的，有很多数据是首次渲染后就永远不会变化的，那么这部分数据生成的 DOM 也不会变化，我们可以在 patch 的过程跳过对他们的比对。

此阶段会深度遍历生成的 AST 树，检测它的每一颗子树是不是静态节点，如果是静态节点则它们生成 DOM 永远不需要改变，这对运行时对模板的更新起到极大的优化作用。

1. 生成代码

```
const code = generate(ast, options) 
```

通过 `generate` 方法，将ast生成 `render` 函数。 更多关于 AST，Vue 模板编译原理，以及和 AST 相关的 Babel 工作原理等，我在 [掌握了AST，再也不怕被问babel，vue编译，Prettier等原理](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247485010&idx=1&sn=8a5a46a10f7ea706ef41592359a646a4&chksm=ce5e3429f929bd3f48fc6f6c85226aac672dd8236a203ec97977fbc0330a39c7f0883b634cc0&token=1424393752&lang=zh_CN#rd) 中做了详细介绍。

### 7. v-for 中 key 的作用是什么？

清晰回答这道问题，需要先清楚 Vue 的 diff 过程 

`key` 是给每个 `vnode` 指定的唯一 `id`，在同级的 `vnode`  diff 过程中，可以根据 `key` 快速的对比，来判断是否为相同节点

利用 `key` 的唯一性可以生成 `map` 来更快的获取相应的节点。

另外指定 `key` 后，就不再采用“就地复用”策略了，可以保证渲染的准确性。

### 8. 为什么 v-for 和 v-if 不建议用在一起

当 `v-for` 和 `v-if` 处于同一个节点时，`v-for` 的优先级比 `v-if` 更高，这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。如果要遍历的数组很大，而真正要展示的数据很少时，这将造成很大的性能浪费。
 这种场景建议使用 `computed`，先对数据进行过滤。 

## mixin

### 问题

变量来源不明确，不利于阅读。多mixin可能造成命名冲突，生命周期函数不会冲突，数据同名会冲突。mixin和组件可能出现多对多的关系，复杂度高 。



















# react

# 面试官问: 如何理解Virtual DOM？

### 一、vdom是什么？

vdom是虚拟DOM(Virtual DOM)的简称，指的是用JS模拟的DOM结构，将DOM变化的对比放在JS层来做。换而言之，vdom就是JS对象。

如下DOM结构:

```
<ul id="list">
    <li class="item">Item1</li>
    <li class="item">Item2</li>
</ul>
复制代码
```

映射成虚拟DOM就是这样:

```
{
    tag: "ul",
    attrs: {
        id:&emsp;"list"
    },
    children: [
        {
            tag: "li",
            attrs: { className: "item" },
            children: ["Item1"]
        }, {
            tag: "li",
            attrs: { className: "item" },
            children: ["Item2"]
        }
    ]
} 
复制代码
```

### 二、为什么要用vdom?

现在有一个场景，实现以下需求:

```
[
    {
        name: "张三",
        age: "20",
        address: "北京"
    },
    {
        name: "李四",
        age: "21",
        address: "武汉"
    },
    {
        name: "王五",
        age: "22",
        address: "杭州"
    },
]
复制代码
```

将该数据展示成一个表格，并且随便修改一个信息，表格也跟着修改。 用jQuery实现如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>

<body>
  <div id="container"></div>
  <button id="btn-change">改变</button>

  <script src="https://cdn.bootcss.com/jquery/3.2.0/jquery.js"></script>
  <script>
    const data = [{
        name: "张三",
        age: "20",
        address: "北京"
      },
      {
        name: "李四",
        age: "21",
        address: "武汉"
      },
      {
        name: "王五",
        age: "22",
        address: "杭州"
      },
    ];
    //渲染函数
    function render(data) {
      const $container = $('#container');
      $container.html('');
      const $table = $('<table>');
      // 重绘一次
      $table.append($('<tr><td>name</td><td>age</td><td>address</td></tr>'));
      data.forEach(item => {
        //每次进入都重绘
        $table.append($(`<tr><td>${item.name}</td><td>${item.age}</td><td>${item.address}</td></tr>`))
      })
      $container.append($table);
    }
    
    $('#btn-change').click(function () {
      data[1].age = 30;
      data[2].address = '深圳';
      render(data);
    });
  </script>
</body>
</html>

复制代码
```

这样点击按钮，会有相应的视图变化，但是你审查以下元素，每次改动之后，table标签都得重新创建，也就是说table下面的每一个栏目，不管是数据是否和原来一样，都得重新渲染，这并不是理想中的情况，当其中的一栏数据和原来一样，我们希望这一栏不要重新渲染，因为DOM重绘相当消耗浏览器性能。

因此我们采用JS对象模拟的方法，将DOM的比对操作放在JS层，减少浏览器不必要的重绘，提高效率。

当然有人说虚拟DOM并不比真实的DOM快，其实也是有道理的。当上述table中的每一条数据都改变时，显然真实的DOM操作更快，因为虚拟DOM还存在js中diff算法的比对过程。所以，上述性能优势仅仅适用于大量数据的渲染并且改变的数据只是一小部分的情况。

虚拟DOM更加优秀的地方在于:

1、它打开了函数式的UI编程的大门，即UI = f(data)这种构建UI的方式。

2、可以将JS对象渲染到浏览器DOM以外的环境中，也就是支持了跨平台开发，比如ReactNative。

另外大家可以参考尤大的一些回答: [www.zhihu.com/question/31…](https://www.zhihu.com/question/31809713/answer/53544875)

### 三、使用snabbdom实现vdom

snabbdom地址:[github.com/snabbdom/sn…](https://github.com/snabbdom/snabbdom)

这是一个简易的实现vdom功能的库，相比vue、react，对于vdom这块更加简易，适合我们学习vdom。vdom里面有两个核心的api，一个是h函数，一个是patch函数，前者用来生成vdom对象，后者的功能在于做虚拟dom的比对和将vdom挂载到真实DOM上。

简单介绍一下这两个函数的用法:

- h('标签名', {属性}, [子元素])
- h('标签名', {属性}, [文本])
- patch(container, vnode) container为容器DOM元素
- patch(vnode, newVnode)

现在我们就来用snabbdom重写一下刚才的例子:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div id="container"></div>
  <button id="btn-change">改变</button>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom.js"></script>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-class.js"></script>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-props.js"></script>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-style.js"></script>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-eventlisteners.min.js"></script>
  <script src="https://cdn.bootcss.com/snabbdom/0.7.3/h.js"></script>
  <script>
    let snabbdom = window.snabbdom;

    // 定义patch
    let patch = snabbdom.init([
      snabbdom_class,
      snabbdom_props,
      snabbdom_style,
      snabbdom_eventlisteners
    ]);

    //定义h
    let h = snabbdom.h;

    const data = [{
        name: "张三",
        age: "20",
        address: "北京"
      },
      {
        name: "李四",
        age: "21",
        address: "武汉"
      },
      {
        name: "王五",
        age: "22",
        address: "杭州"
      },
    ];
    data.unshift({name: "姓名", age: "年龄", address: "地址"});

    let container = document.getElementById('container');
    let vnode;
    const render = (data) => {
      let newVnode = h('table', {}, data.map(item => { 
        let tds = [];
        for(let i in item) {
          if(item.hasOwnProperty(i)) {
            tds.push(h('td', {}, item[i] + ''));
          }
        }
        return h('tr', {}, tds);
      }));

      if(vnode) {
          patch(vnode, newVnode);
      } else {
          patch(container, newVnode);
      }
      vnode = newVnode;
    }

    render(data);

    let btnChnage = document.getElementById('btn-change');
    btnChnage.addEventListener('click', function() {
      data[1].age = 30;
      data[2].address = "深圳";
      //re-render
      render(data);
    })
  </script>
</body>
</html>
复制代码
```

再进入页面:



![img](https://user-gold-cdn.xitu.io/2019/8/22/16cb6a6528a91c5e?imageslim)

你会发现，只有改变的栏目才闪烁，也就是进行重绘，数据没有改变的栏目还是保持原样，这样就大大节省了浏览器重新渲染的开销。



### 四、diff算法

#### 1、什么是diff算法？

所谓diff算法，就是用来找出两段文本之间的差异的一种算法。

作为一个前端，大家经常会听到diff算法这个词，其实diff并不是前端原创的算法，其实这一个算法早已在linux的diff命令中有所体现，并且大家常用的git diff也是运用的diff算法。

#### 2、vdom为什么用diff算法

DOM操作是非常昂贵的，因此我们需要尽量地减少DOM操作。这就需要找出本次DOM必须更新的节点来更新，其他的不更新，这个找出的过程，就需要应用diff算法。

#### 3、vdom中diff算法的简易实现

以下代码只是帮助大家理解diff算法的原理和流程，不可用于生产环境。

将vdom转化为真实dom：

```
const createElement = (vnode) => {
  let tag = vnode.tag;
  let attrs = vnode.attrs || {};
  let children = vnode.children || [];
  if(!tag) {
    return null;
  }
  //创建元素
  let elem = document.createElement(tag);
  //属性
  let attrName;
  for (attrName in attrs) {
    if(attrs.hasOwnProperty(attrName)) {
      elem.setAttribute(attrName, attrs[attrName]);
    }
  }
  //子元素
  children.forEach(childVnode => {
    //给elem添加子元素
    elem.appendChild(createElement(childVnode));
  })

  //返回真实的dom元素
  return elem;
}
复制代码
```

用简易diff算法做更新操作:

```
function updateChildren(vnode, newVnode) {
  let children = vnode.children || [];
  let newChildren = newVnode.children || [];

  children.forEach((childVnode, index) => {
    let newChildVNode = newChildren[index];
    if(childVnode.tag === newChildVNode.tag) {
      //深层次对比, 递归过程
      updateChildren(childVnode, newChildVNode);
    } else {
      //替换
      replaceNode(childVnode, newChildVNode);
    }
  })
}
```

### 初次渲染过程 

1.解析模板为render函数（或在开发环境已完成，vue-loader，webpack）

2.触发响应式，监听data属性的getter，setter函数

3.执行render函数，生成vnode，patch(elem, vnode)

### 更新过程

1.修改data，触发setter（此前在getter中已经被监听）

2.重新执行render，生成newVnode

3.patch(vnode, newVnode)





# React服务端渲染

# node作中间层及请求代码优化

### 一、为什么要引入node中间层?

其实任何技术都是与它的应用场景息息相关的。这里我们反复谈的SSR，其实不到万不得已我们是用不着它的，SSR所解决的最大的痛点在于SEO，但它同时带来了更昂贵的成本。不仅因为服务端渲染需要更加复杂的处理逻辑，还因为同构的过程需要服务端和客户端都执行一遍代码，这虽然对于客户端并没有什么大碍，但对于服务端却是巨大的压力，因为数量庞大的访问量，对于每一次访问都要另外在服务器端执行一遍代码进行计算和编译，大大地消耗了服务器端的性能，成本随之增加。如果访问量足够大的时候，以前不用SSR的时候一台服务器能够承受的压力现在或许要增加到10台才能抗住。痛点在于SEO，但如果实际上对SEO要求并不高的时候，那使用SSR就大可不必了。

那同样地，为什么要引入node作为中间层呢？它是处在哪两者的中间？又是解决了什么场景下的问题？

在不用中间层的前后端分离开发模式下，前端一般直接请求后端的接口。但真实场景下，后端所给的数据格式并不是前端想要的，但处于性能原因或者其他的因素接口格式不能更改，这时候需要在前端做一些额外的数据处理操作。前端来操作数据本身无可厚非，但是当数据量变得庞大起来，那么在客户端就是产生巨大的性能损耗，甚至影响到用户体验。在这个时候，node中间层的概念便应运而生。

它最终解决的前后端协作的问题。

一般的中间层工作流是这样的:前端每次发送请求都是去请求node层的接口，然后node对于相应的前端请求做转发，用node去请求真正的后端接口获取数据，获取后再由node层做对应的数据计算等处理操作，然后返回给前端。这就相当于让node层替前端接管了对数据的操作。



![img](https://user-gold-cdn.xitu.io/2019/7/5/16bbff86c9c72be5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 二、SSR框架中引入中间层

在之前搭建的SSR框架中，服务端和客户端请求利用的是同一套请求后端接口的代码，但这是不科学的。

对客户端而言，最好通过node中间层。而对于这个SSR项目而言，node开启的服务器本来就是一个中间层的角色，因而对于服务器端执行数据请求而言，就可以直接请求真正的后端接口啦。

```
//actions.js
//参数server表示当前请求是否发生在node服务端
const getUrl = (server) => {
    return server ? 'xxxx(后端接口地址)' : '/api/sanyuan.json(node接口)';
}
//这个server参数是Home组件里面传过来的，
//在componentDidMount中调用这个action时传入false，
//在loadData函数中调用时传入true, 这里就不贴组件代码了
export const getHomeList = (server) => {
  return dispatch => {
    return axios.get(getUrl(server))
      .then((res) => {
        const list = res.data.data;
        dispatch(changeList(list))
      })
  }
}
复制代码
```

在server/index.js应拿到前端的请求做转发，这里是直接用proxy形式来做，也可以用node单独向后端发送一次HTTP请求。

```
//增加如下代码
import proxy from 'express-http-proxy';
//相当于拦截到了前端请求地址中的/api部分，然后换成另一个地址
app.use('/api', proxy('http://xxxxxx(服务端地址)', {
  proxyReqPathResolver: function(req) {
    return '/api'+req.url;
  }
}));
复制代码
```

### 三、请求代码优化

其实请求的代码还是有优化的余地的，仔细想想，上面的server参数其实是不用传递的。

现在我们利用axios的instance和thunk里面的withExtraArgument来做一些封装。

```
//新建server/request.js
import axios from 'axios'

const instance = axios.create({
  baseURL: 'http://xxxxxx(服务端地址)'
})

export default instance


//新建client/request.js
import axios from 'axios'

const instance = axios.create({
  //即当前路径的node服务
  baseURL: '/'
})

export default instance
复制代码
```

然后对全局下store的代码做一个微调:

```
import {createStore, applyMiddleware, combineReducers} from 'redux';
import thunk from 'redux-thunk';
import { reducer as homeReducer } from '../containers/Home/store';
import clientAxios from '../client/request';
import serverAxios from '../server/request';

const reducer = combineReducers({
  home: homeReducer
})

export const getStore = () => {
  //让thunk中间件带上serverAxios
  return createStore(reducer, applyMiddleware(thunk.withExtraArgument(serverAxios)));
}
export const getClientStore = () => {
  const defaultState = window.context ? window.context.state : {};
   //让thunk中间件带上clientAxios
  return createStore(reducer, defaultState, applyMiddleware(thunk.withExtraArgument(clientAxios)));
}

复制代码
```

现在Home组件中请求数据的action无需传参，actions.js中的请求代码如下：

```
export const getHomeList = () => {
  //返回函数中的默认第三个参数是withExtraArgument传进来的axios实例
  return (dispatch, getState, axiosInstance) => {
    return axiosInstance.get('/api/sanyuan.json')
      .then((res) => {
        const list = res.data.data;
        console.log(res)
        dispatch(changeList(list))
      })
  }
}
复制代码
```

至此，代码优化就做的差不多了，这种代码封装的技巧其实可以用在其他的项目当中，其实还是比较优雅的。

## styled-components:前端组件拆分新思路

一直在思考React组件如何拆分的问题，直到接触到styled-components，让我有一种如鱼得水的感觉，今天我就给大家分享一下这个库如何让我们的前端组件开发的更优雅，如何保持更合适的组件拆分粒度从而更容易维护。

### 一、使用方法

styled-components是给React量身定制的一个库，奉行React中all in js的设计理念，并将这个理念进一步发挥到极致，让CSS也能够成为一个个的JS模块。

使用起来也相当方便，首先安装这个库

```
npm install styled-components --save
复制代码
```

然后在style.js中使用(注意这里不是style.css，样式文件全部是JS文件)

```
import styled from 'styled-components';
//styled.xxx表示创建xxx这个h5标签,
//后面紧接的字符串里面写的是CSS代码
export const HeaderWrapper = styled.div`
  z-index: 1;
  position: relative;
  height: 56px;
  border-bottom: 1px solid #f0f0f0;
`;
复制代码
```

之后再React中使用它:

```
import React, {Component} from 'react';
import { HeaderWrapper } from './style.js';

class App extend Component{
    render() {
        return (
            <HeaderWrapper></HeaderWrapper>
        )
    }
}
export default App;
复制代码
```

Ok!这就是它的日常使用方式。如果有兴趣可以去github的相应仓库打开更多使用姿势:)

### 二、使用styled-component解决了哪些痛点

可能你还会有疑惑：这么做有什么好处呢？

#### 1.CSS模块化

尽量降低模块之间的耦合度，利于项目的进一步维护。比起用原生的CSS，这是它首当其冲的优势。

#### 2.支持预处理器嵌套语法

如:

```
export const SearchWrapper = styled.div`
  position: relative;
  float: left;
  .zoom {
    right: 5px;
    &.focused {
      background: #777;
      color: #fff;
    }
  }
`;
复制代码
```

可以采用sass,less的嵌套语法，开发更加流畅。

#### 3.让CSS代码能够处理逻辑

不仅仅是因为里面的模板字符串可以写JS表达式，更重要的是能够拿到组件的上下文信息(state、props)

比如,在React组件中的JSX代码中写了这样一段：

```
<RecommendItem imgUrl={'xxx'}/>
复制代码
```

在相应的style.js中就能够接受相应的参数:

```
export const RecommendItem = styled.div`
  width: 280px;
  height: 50px;
  background: url(${(props) => props.imgUrl});
  background-size: contain;
`;
复制代码
```

CSS能够拿到props中的内容，进行相应的渲染，是不是非常酷炫？

#### 4.语义化

如果以上几点还不能体现它的优势，那这一点就是对于前端开发者的毒药。

现在很多人对标签语义化的概念趋之若鹜，但其实大多数开发者都还是div+class一把撸的模式。难道是因为语义化不好吗？能够让标签更容易理解当然是件好事情，但是对于html5规范推出的标签来说，一方面对于开发者来说略显繁琐，还是div、span、h1之类更加简洁和亲切，另一方面标准毕竟是标准，它并不能代表业务，因此并不具有足够的表达力来描述纷繁的业务，甚至这种语义化有时候是可有可无的。我觉得这两点是开发者更喜欢div+class一把撸的根本原因。

那好，照着这个思路，拿React组件开发而言，如果要想获得更好的表达力，尽可能的语义化，那怎么办？可能你会暗笑：这还用说，拆组件啊！但组件真的是拆的越细越好吗？

有人曾经说过:当你组件拆的越来越细的时候，你会发现每一个组件就是一个标签。但是这会造成一些更加严重的问题。假设我们拆的都是UI组件，当我们为了语义化连一个button都要封装成一个组件的时候，代码会臃肿不堪，因为会出现数量过于庞大的组件，非常不利于维护。

那，有没有一个折中的方案呢？既能提高标签语义化，又能控制JS文件的数量。 没错，这个方案就是styled-components。

以首页的导航为例, 取出逻辑后JSX是这样：

```
<HeaderWrapper>
    <Logo/>
    <Nav>
        <NavItem className='left active'>首页</NavItem>
        <NavItem className='left'>下载App</NavItem>
        <NavItem className='right'>
            <i className="iconfont">&#xe636;</i>
    	</NavItem>
        <SearchWrapper>
            <NavSearch></NavSearch>
            <i className='iconfont'>&#xe614;</i>
        </SearchWrapper>
    </Nav>
    <Addition>
        <Button className='writting'>
    	  <i className="iconfont">&#xe615;</i>
    	  来参加
    	</Button>
    	<Button className='reg'>注册</Button>
    </Addition>
</HeaderWrapper>
复制代码
//style.js
import styled from 'styled-components';

export const HeaderWrapper = styled.div`
    z-index: 1;
    position: relative;
    height: 56px;
    border-bottom: 1px solid #f0f0f0;
`;

export const Logo = styled.div`
    position: absolute;
    top: 0;
    left: 0;
    display: block;
    width: 100px;
    height: 56px;
    background: url(${logoPic});
    background-size: contain;
`;

export const Nav = styled.div`
    width: 960px;
    height: 100%;
    padding-right: 70px;
    box-sizing: border-box;
    margin: 0 auto;
`;
//......
复制代码
```

拆分后的标签基本是在style.js里面导出的变量名，完全自定义，这个时候CSS都成为了一个个JS模块，每一个模块相当于一个标签(如：styled.div已经帮我们创建好了标签)，在模块下面完全可以再写h5标签。这样的开发方式其实是非常灵活的。

### 三、开发过程中遇到的坑以及目前缺点

坑: 以前的injectGlobal已经被弃用，因此对于全局的样式文件需要使用createGlobalStyle来进行引入。

```
//iconfont.js
//全局样式同理
import {createGlobalStyle} from 'styled-components'

export const IconStyle = createGlobalStyle`
@font-face {
  font-family: "iconfont";
  src: url('./iconfont.eot?t=1561883078042'); /* IE9 */
  src: url('./iconfont.eot?t=1561883078042#iefix') format('embedded-opentype'), /* IE6-IE8 
  //...
}

.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
`
复制代码
```

然后在全局的根组件App.js里面:

```
import { IconStyle } from './statics/iconfont/iconfont'
import { GlobalStyle } from  './style'
//import ...

function App() {
  return (
    <Provider store={store}>
      <div>
        {/* 通过标签形式引入这些样式 */}
        <GlobalStyle></GlobalStyle>
        <IconStyle></IconStyle>
        <Header />
        <BrowserRouter>
        <div>
          <Route path='/' exact component={Home}></Route>
          <Route path='/detail' exact component={Detail}></Route>
        </div>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
复制代码
```

对于styled-components缺点而言，我认为目前唯一的不足在于模板字符串里面没有CSS语法，写起来没有自动提示，对于用惯IDE提示的人来说还是有美中不足的。不过也并不是什么太大的问题，如果有相应的插件或工具欢迎在评论区分享。

### 一、什么是Redux的中间件？

所谓中间件，一定是处于两个事物的中间，那么什么是Redux中间件呢？

回顾一下Redux的工作流程:



![img](https://user-gold-cdn.xitu.io/2019/6/19/16b6f71e151db1c4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 Redux的中间件，处于Action和Reducer之间，将中间某个过程拦截一下，进行一些处理再继续正常执行，这就是中间件的功能。



那为什么要用到Redux中间件呢？

对于异步请求的代码，我们最好将它们放到Redux中间件里面。

当项目复杂到一定规模的时候，我们希望让各个模块尽可能的实行单一职责，比如React作为一个视图层的框架就只负责渲染，数据的事情统统交给Redux来处理，此时，相对于把异步请求的Ajax代码放到写到组件的componentDidMount生命周期钩子函数，其实交给Redux是一种更好的选择。另外，交给Redux,一方面能够做到不同组件的接口复用，另一方面方便于测试，单纯去测试一些异步代码比测试一个React组件的生命周期函数会更加容易。 然而Redux里面dispatch的过程中只允许传递对象，而不允许传递函数。于是，redux-thunk便应运而生。

### 二、Redux-thunk实现异步更新state

```
npm install redux-thunk --save
复制代码
```

首先在项目中启用redux-thunk

```
//src/store/index.js
import {createStore, applyMiddleware, compose} from 'redux';
import reducer from './reducer';
import thunk from 'redux-thunk'
import TodoSagas from './sagas'

//需要同时激活redux-devtools的chrome插件，下面是激活代码
//兼容代码在redux-devtools的文档下拿过来照着用即可
const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(applyMiddleware(thunk))
//创建store
const store = createStore(
  reducer,
  enhancer
)

export default store;
复制代码
//src/store/actionCreators.js
//以下为getTodoList部分
export const getTodoList = () => {
  return (dispatch) => {
    axios.get('xxx').then((res) => {
      const data = res.data.list
      //创建action
      const action = initListAction(data)
      //相当于store.dispatch
      //其实redux-thunk给当前返回函数中添加的第一个默认参数就是store的dispatch方法
      dispatch(action)
    })
  }
}
复制代码
  //App.js
  componentDidMount() {
    // axios.get('xxx').then((res) => {
    //   const data = res.data.data
    //   const action = initListAction(data)
    //   store.dispatch(action)
    // })
    //将以上代码放进Redux，效果相同
    const action = getTodoList()
    store.dispatch(action)
  }
复制代码
```

因此，redux-thunk其实是拦截了store的dispatch方法，Redux中store.dispatch原本是不能传一个函数进去的，但是redux-thunk让dispatch拥有的接受函数参数的能力。具体来说，如果dispatch方法中传递的是一个对象，那么直接按照正常的Redux工作流来运行，但如果是一个函数，那么直接执行它，并把store.dispatch这个方法当作第一个参数传进这个函数。

### 三、Redux-saga实现异步更新state

redux-saga也是Redux中处理异步函数的中间件。

```
npm install redux-saga --save
复制代码
```

首先在项目中启动redux-saga:

```
//src/store/index.js
import {createStore, applyMiddleware, compose} from 'redux';
import reducer from './reducer';
import createSagaMiddleware from 'redux-saga'
import TodoSagas from './sagas'
//创建中间件
const sagaMiddleware = createSagaMiddleware()
//需要同时激活redux-devtools的chrome插件，下面是激活代码
const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware))
//创建store
const store = createStore(
  reducer,
  enhancer
)
//启动中间件
sagaMiddleware.run(TodoSagas)

export default store;
复制代码
//sagas.js
import { takeEvery, put } from 'redux-saga/effects'
import { GET_INIT_LIST } from './actionTypes';
import { initListAction } from './actionCreators';
import axios from 'axios'
function* getInitList() {
  try {
    const res = yield axios.get('xxx')
    //拿到Ajax数据后，通过initListAction创建action对象
    //注意: 这是另一个action:INIT_LIST_ACTION，直接改变state中的list的值
    const action = initListAction(res.data.list)
    //put相当于dispatch这个新的action
    yield put(action)
  }catch(e) {
    console.log('网络请求失败')
  }
}
//导出的mySaga需要写成一个Generator函数，异步处理函数getInitList也应是Generator函数
function* mySaga() {
  //拦截GET_INIT_LIST这个action
  yield takeEvery(GET_INIT_LIST, getInitList);
}

export default mySaga
复制代码
  //App.js
  componentDidMount() {
    const action = getInitList()
    store.dispatch(action)
  }
复制代码
```

所以你能够看到，在redux-saga当中的处理思路是: 先抛出一个类似于信号灯的action，redux-saga看到了这个信号灯，拦截下来，然后执行响应的异步函数，在这个异步函数拿到数据后，执行真正要更新state的action。在这个过程中，作为信号灯的action在reducer中并没有具体关于state的逻辑编写，而仅仅是给redux-saga发一个信号而已。 

















# react对比vue

react修改数据会重新创建新的数据进行赋值，

## 1.背景

### vue

Google 前工程师尤雨溪于 2014 年创建了这个框架。Vue是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。

### react

与 Vue 不同，react 库是由 Facebook 创建的。最初是为了 Facebook 广告流量管理创建的。那时 Facebook 遇到了维护和编码方面的问题。它以动态创建和交互式 UI 的能力而闻名。

## 2.核心思想

vue与react都推崇组件式的开发理念，但是在设计的核心思想上有很大差别。

### vue

vue的整体思想仍然是拥抱经典的html(结构)+css(表现)+js(行为)的形式，vue鼓励开发者使用template模板，并提供指令供开发者使用(v-if、v-show、v-for等等)，因此在开发vue应用的时候会有一种在写经典web应用（结构、表现、行为分离）的感觉。另一方面，在针对组件数据上，vue2.0通过`Object.defineProperty`对数据做到了更细致的监听，精准实现组件级别的更新。

### react

react整体上是函数式的思想，组件使用jsx语法，all in js，将html与css全都融入javaScript，jsx语法相对来说更加灵活，我一开始刚转过来也不是很适应，感觉写react应用感觉就像是在写javaScript。当组件调用setState或props变化的时候，组件内部render会重新渲染，子组件也会随之重新渲染，可以通过`shouldComponentUpdate`或者`PureComponent`可以避免不必要的重新渲染（个人感觉这一点上不如vue做的好）。

## 3.组件形式

### vue

vue组件定义使用xx.vue文件来表示，vue组件将html、css、js组合到一起，模板部分使用数据使用`{{}}`，形式如下：

```
// 模板(html)
<template>
  <div>{{name}}</div>
</template>

// 数据管理(js)
<script>
export default {
  name: 'NewComponent',
  data() {
    return {
      name: 'xx'
    }
  }
}
</script>

// 样式(css)
<style scoped>

</style>

```

组件使用：

```
<new-component name="xx" />

```

### react

react推荐使用jsx或者js文件来表示组件，react支持class组件和function组件2种形式，react使用`{}`包裹变量，这点需要注意。

> 注意： 组件名称必须以大写字母开头。React 会将以小写字母开头的组件视为原生 DOM 标签。例如，`` 代表 HTML 的 div 标签，而 `` 则代表一个组件，并且需在作用域内使用 Welcome。

（1）class组件

```
import React from 'react';

class NewComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'xx'
    };
  }
  render() {
    rerurn(<div>{name}</div>);
  }
}

export default NewComponent;

```

（2）function组件

hooks的出现赋予了function组件管理state的能力。

```
import React, { useState } from 'react';

function NewComponent() {
  const [name, setName] = useState('');
  return (<div>{name}</div>);
}

export default NewComponent;

```

## 4.数据管理(props、data vs state)

组件数据管理通常包含2部分，来自父组件的数据props与自身的数据。

vue与react中的props都是单向数据流的，父级prop的更新会向下流动到子组件中，但是反过来则不行。prop可以是数组或对象，用于接收来自父组件的数据。

### vue

#### props

vue中的props支持传递静态或动态props，静态props一般传递字符串。

```
<blog-post title="My journey with Vue"></blog-post>
```

静态prop传递布尔值true可以这样写，传值false仍然需要使用动态prop传值。

```
<blog-post disabled></blog-post>
```

动态赋值使用v-bind，可以简写为:。

```
<blog-post v-bind:title="tile"></blog-post>
// 简写形式
<blog-post :title="tile"></blog-post>
```

动态prop常用来传递对象、数组、布尔值（false值，true值可以直接传属性）等。

```
<blog-post :title="post.title + ' by ' + post.author.name"></blog-post>
```

#### data

vue中使用data来管理组件的数据，vue 将会递归将 data 的属性转换为 getter/setter，从而让 data 的属性能够响应数据变化。对象必须是纯粹的对象 (含有零个或多个的 key/value 对)。一旦观察过，不需要再次在数据对象上添加响应式属性。因此推荐在创建实例之前，就声明所有的根级响应式属性。

当一个组件被定义，data必须声明为返回一个初始数据对象的函数。

```
export default {
  name: 'NewComponent',
  data() {
    return {
      name: 'xxx',
      age: 12
    }
  }
}
```

当需要在组件内部修改数据时，可以直接通过vue实例修改：

```
  methods: {
    changeName() {
      this.name = 'new Name';
    }
  }
```

### react

#### props

react中的props也与vue一样可以传递静态或动态props，静态props一般传递字符串。

函数组件和class组件都可以使用props，函数组件使用props参数获取父组件传下来的props。

函数组件获取props：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;

```

class组件使用`this.props`获取组件`props`：

```
class Welcome extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    const { name } = this.props;
    return <div>{name}</div>;
  }
}

```

动态props：

```
<Welcome name={name} />

```

#### state

react中使用state来管理组件内的数据，hooks的出现使得`函数组件`也具备管理state的能力。

##### class组件state

class组件在构造函数（constructor）中定义组件内数据（state），修改数据必须通过setState修改，不能直接修改state，这点非常重要。

class组件使用state：

```
class Welcome extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'xx'
    };
    this.changeName = this.changeName.bind(this);
  }

  changeName() {
    this.setState({
      name: 'new name'
    });
  }

  render() {
    const { name } = this.state;
    return <div onClick={this.changeName}>{name}</div>;
  }
}

```

关于class组建的setState有以下两点说明：

- 1.setState更新是异步的，但是**在setTimeout和原生事件中是同步的**。
- 2.setState更新的是组件的部分数据，react会自动将数据合并。

当需要使用上一个state值时，可以让 setState() 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：

```
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));

```

##### function组件useState

react 16.0之前函数组件只是纯的渲染组件，hooks的出现赋予了函数组件管理state的能力。

useState返回一个state，以及更新state的函数。如果新的 state 需要通过使用先前的 state 计算得出，那么可以将函数传递给 setState。该函数将接收先前的 state，并返回一个更新后的值。

```
import React, { useState } from 'react';

function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}

```

> 关于setState有以下三点说明：
>
> - 1.与 class 组件中的 setState 方法不同，useState 不会自动合并更新对象。
> - 2.只能在函数最外层调用 Hook。不要在循环、条件判断或者子函数中调用。
> - 3.只能在 React 的函数组件或自定义hook中调用 Hook。不要在其他 JavaScript 函数中调用。

## 5.组件数据交互

组件数据交互是指父子组件、兄弟组件、跨层组件之间传递数据。 兄弟组件之间可以通过事件总线或者通过父组件传递数据，这里就不详细赘述。

### 5.1.父子组件数据交互(props+自定义事件 vs props+回调)

对于父子组件数据交互，vue中使用prop+`$emit`自定义事件实现，react通过props+`回调`实现。

#### vue

vue中父组件通过props传递数据给子组件，子组件使用`$emit`触发自定义事件，父组件中监听子组件的自定义事件获取子组件传递来的数据。

子组件使用`$emit`传递自定义事件`myEvent`：

```
<template>
  <div @click="changeName">{{name}}</div>
</template>

<script>
export default {
  name: 'NewComponent',
  data() {
    return {
      name: 'xxx',
      age: 12
    }
  },
  methods: {
    changeName() {
      this.name = 'new Name';
      this.$emit('myEvent', this.name);
    }
  }
}
</script>


```

父组件使用`@myEvent`监听自定义事件，回调函数参数是子组件传回的数据：

```
<template>
  <div>
    <new-component @myEvent="getName"></new-component>
  </div>
</template>

<script>
import NewComponent from './NewComponent';

export default {
  components: {
    NewComponent
  },
  data() {
    return {}
  },
  methods: {
    getName(name) {
      console.log(name)
    }
  }
}
</script>


```

#### react

react中父组件使用props传递数据和回调函数给子组件，子组件通过props传下来的回调函数返回数据，父组件通过回调函数获取子组件传递上来的数据。

子组件通过props接收父组件传下来的回调事件：

```
import React, { useState } from 'react';

function Children(props) {
  const { myEvent } = props;
  const [name, setName] = useState('xxx');

  const changeName = () => {
    setName('new name');
    myEvent('new name');
  };
  return <div onClick={changeName}>{name}</div>;
} 
```

父组件通过回调事件获取子组件传递的参数：

```
function Parent() {
  const changeName = name => {
    console.log(name);
  };
  return <Children myEvent={changeName}></Children>;
} 
```

### 5.2.跨组件数据交互(provide/inject vs Context)

vue和react都支持跨组件传递数据，vue中主要通过`provide / inject`实现，react中主要通过`Context`实现。

#### vue

vue中通过`provide / inject`在祖先组件向所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

祖先组件中定义provide选项，provide选项应该是一个对象或返回一个对象的函数。

```
<template>
  <div>
    <new-component @myEvent="getName"></new-component>
  </div>
</template>

<script>
import NewComponent from './NewComponent';

export default {
  provide: { // 定义provide选项
    message: 'This is a big news'
  },
  components: {
    NewComponent
  },
  data() {
    return {}
  },
  methods: {
    getName(name) {
      console.log(name)
    }
  }
}
</script> 
```

子组件通过inject选项获取祖先组件的provide选项值，inject选项应该是一个字符串数组或者对象。

```
<template>
  <div>{{message}}</div>
</template>

<script>
export default {
  name: 'Children',
  inject: ['message'],
  data() {
    return {}
  }
}
</script> 
```

> 注意：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

#### react

Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

在父组件创建一个Context对象，通过`Context.provider`的value属性向消费组件传值。

```
import React, { useState } from 'react';

// 创建Context对象
const MyContext = React.createContext({ theme: 'black' });

function Parent() {
  const changeName = name => {
    console.log(name);
  };
  // Context.provider向消费组件传值
  return (<MyContext.Provider value={{ theme: 'white' }}>
    <Children myEvent={changeName}></Children>;
  </MyContext.Provider>); 
} 
```

消费组件获取Context有2种方式：

（1）class组件通过contextType获取最近Context上的那个值。

```
class DeepChildren1 extends React.Component {
  constructor(props) {
    super(props);
  }

  static contextType = MyContext;

  render() {
    return <div>{this.context.theme}123</div>;
  }
} 
```

（2）函数式组件通过`Context.Consumer`订阅到Context的变更。

```
function DeepChildren(props) {
  return (<MyContext.Consumer>
    {
      (value) => (
        <div>{value.theme}</div>
      )
    }
  </MyContext.Consumer>);
} 
```

关于Context需要注意：

> 当Provider的父组件进行重渲染时，consumers组件会重新渲染，并且没有办法避免，应该尽量避免使用Context。

## 6.class与style

关于class与style处理上，vue与react也存在较大差异。

### vue

vue对class与style特意做了增强，可以传字符串、对象、数组。

#### class

（1）给class绑定字符串：

```
<div class="hello"></div> 
```

（2）给class绑定对象：

```
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div> 
```

data如下：

```
data: {
  isActive: true,
  hasError: false
} 
```

HTML 将被渲染为:

```
<div class="static active"></div> 
```

（3）给class绑定数组：

```
<div :class="[activeClass, errorClass]"></div> 
```

data如下：

```
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
} 
```

HTML 将被渲染为:

```
<div class="active text-danger"></div> 
```

（4）class还可以直接绑定到组件上，这一点react并不支持。

声明组件如下：

```
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
}) 
```

在使用它的时候添加一些 class：

```
<my-component class="baz boo"></my-component> 
```

HTML 将被渲染为:

```
<p class="foo bar baz boo">Hi</p> 
```

#### style

style用来绑定内联样式，支持传对象、数组，使用需要添加浏览器引擎前缀的 CSS 属性时，如 transform，Vue.js 会自动侦测并添加相应的前缀。

（1）传对象，CSS 属性名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div> 
```

data如下：

```
data: {
  activeColor: 'red',
  fontSize: 20
} 
```

HTML将被渲染为：

```
<div style="color: red; font-size: 20px;"></div> 
```

（2）传数组将多个样式应用到同一个元素上

```
<div :style="[baseStyles, overridingStyles]"></div> 
```

data如下：

```
baseStyles: {
  fontSize: '20px',
  color: 'blue'
},
overridingStyles: {
  height: '80px'
}  
```

HTML将被渲染为：

```
<div style="font-size: 20px; color: blue; height: 80px;"></div> 
```

### react

react使用className用于指定css的class，react中不能直接为组件指定class。

#### className

react中className一般传值字符串常量或者字符串变量，不能传递数组或者对象语法。

（1）传字符串常量：

```
function NewComponent() {
  return <div className="container" >This is New Component.</div>;
} 
```

（2）传字符串变量：

```
function NewComponent() {
  const newClass = 'conrainer'
  return <div className={newClass}>This is New Component.</div>;
} 
```

（3）传递多个class，可以使用es6的模板字符串实现：

```
function NewComponent() {
  const newClass = 'conrainer'
  return <div className={`${newClass} new-container`}>This is New Component.</div>;
} 
```

当需要传递数组或者对象语法时，可以引入classnames库实现：

```
import classNames from 'classnnames';

function NewComponent() {
  const newClass = 'container';
  return <div className={classNames(newClass, 'newContainer', { bar: true }, ['new-class', { c: true }])}>This is New Component.</div>;
} 
```

html将被渲染为：

```
<div class="container newContainer bar new-class c">This is New Component.</div> 
```

#### style

通常不推荐将 style 属性作为设置元素样式的主要方式。在多数情况下，应使用 className 属性来引用外部 CSS 样式表中定义的 class。style 在 React 应用中多用于在渲染过程中添加动态计算的样式。

style接收一个对象

```
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}


```

注意：样式不会自动补齐前缀。如需支持旧版浏览器，请手动补充对应的样式属性：

```
const divStyle = {
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
};

function ComponentWithTransition() {
  return <div style={divStyle}>This should work cross-browser</div>;
}


```

## 7.生命周期

组件的生命周期一般包含：初始化、挂载、更新、卸载四个大阶段

### vue

vue生命周期图示： 

![vue生命周期](https://user-gold-cdn.xitu.io/2020/1/8/16f82fcbdf6c9d66?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



vue生命周期包含:

- beforeCreate 
  实例组件刚创建，元素DOM和数据都还没有初始化，暂时不能在这个周期里面进行任何操作。
- created 
  数据data已经初始化完成，方法也已经可以调用，但是DOM未渲染。调用后台接口获取数据可以在这个阶段完成。
- beforeMount 
  DOM未完成挂载，数据也初始化完成，但是数据的双向绑定还是显示{{}}，虚拟DOM结构已经生成。
- mounted 
  数据和DOM都完成挂载，在上一个周期占位的数据把值给渲染进去。这个周期适合执行初始化需要操作DOM的方法。
- beforeUpdate 
  页面数据改变了都会触发，在更新前触发，此时的数据还是未更新前的数据，没有数据改变则不执行。
- updated 
  页面数据改变都会触发，在更新完成之后触发，此时的数据是更新后的数据。

> 注意：在这里操作数据很容易引起卡死。

- beforeDestroy 
  组件销毁之前执行，在这个周期里仍然可以访问data和method，多组件间通信需要发布信息时可以在该阶段完成。
- destroyed 
  当离开组件对应页面时组件销毁时触发，主要用于取消一些副作用（取消事件监听、取消定时器、取消不必要的请求等）

### react

react生命周期分为16.0之前和16.0之后：

#### 16.0之前

react16.0之前生命周期如下：



![react 16.0之前生命周期](https://user-gold-cdn.xitu.io/2020/1/8/16f82fdb8d3a2df3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



（1）初始化

- constructor 
  是class组件的默认方法，常用来初始化state或者设置属性等。

```
class Counter extends React.component{
    constructor(props){
        super(props); // 声明constructor时必须调用super方法
        this.state = {
          count: 0
        };
        this.color = 'red';
    }
}


```

（2）挂载阶段

- componentWillMount() 
  组件挂载之前调用，并且只会调用一次。
- render 
  render是一个React组件必须定义的生命周期函数，用来渲染DOM。 并必须 return 一个React元素（描述组件，即UI），不负责组件实际渲染工作，之后由React自身根据此元素去渲染出页面DOM。

> 不要在render里面修改state,会引起死循环导致卡死。

- componentDidMount() 
  组件挂在完成之后调用，在这个阶段可以获取真实dom元素，常用来发起异步请求获取数据。

（3）更新阶段

当通过setState修改state或父组件重新render引起props更新，都会引起子组件的重新render。

- componentWillReceiveProps(nextProps) 
  props发生变化以及父组件重新渲染时都会触发该生命周期函数。在该阶段可以通过参数nextProps获取变化后的props参数， 通过this.props访问之前的props。该生命周期内可以进行setState。
- shouldComponentUpdate(nextProps,nextState) 
  组件每次setState或者父组件重新render都会引起子组件render，可以使用该钩子比较nextProps，nextState及当前组件的this.props，this.state的状态用来判断是否需要重新渲染。默认返回true，需要重新render，返回false则不触发渲染。

> 一般我们通过该钩子来优化性能，避免子组件不必要的渲染。

- componentWillUpdate(nextProps, nextState) 
  当组件收到新的 props 或 state 时，会在渲染之前调用。使用此作为在更新发生之前执行准备更新的机会。初始渲染不会调用此方法。

> 注意：不能在此方法中调用`this.setState`

- componentDidUpdate(prevProps, prevState) 此方法在组件更新后被调用。首次渲染不会执行此方法。当组件更新后，可以在此处对DOM进行操作。

> 注意：可以在`componentDidUpdate()`中直接调用`setState()`，但是它必需被包裹在一个条件语句里，否则会导致死循环。

（4）卸载阶段

- componentWillUnmount() 
  会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在`componentDidMount()` 中创建的订阅等。

> 注意：componentWillUnmount() 中不应调用 setState()，因为该组件将永远不会重新渲染。

#### 16.0之后

react 16.0之后移除的生命周期函数：

- componentWillMount
- componentWillReceiveProps
- componentWillUpdate

但是为了向下兼容，react并未删除这三个生命周期，新增以 `UNSAFE_` 前缀为别名的三个函数 `UNSAFE_componentWillMount()`、`UNSAFE_componentWillReceiveProps()`、`UNSAFE_componentWillUpdate()`。

新增的生命周期函数：

- static getDerivedStateFromProps(nextProps, prevState)
- getSnapshotBeforeUpdate(prevProps, prevState)

生命周期如下：



![react 16.0之后生命周期](https://user-gold-cdn.xitu.io/2020/1/8/16f82fe4ce9f15c1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



> 总结： 
> （1）初始化阶段保持不变 
> （2）挂载阶段： 
>   getDerivedStateFromProps => render => componentDidMount 
> （3）更新阶段 
>   getDerivedStateFromProps => shoudeComponentUpdate => render => getSnapshotBeforeUpdate => componentDidUpdate 
> （4）卸载阶段保持不变

接下来重点介绍getDerivedStateFromProps、getSnapshotBeforeUpdate两个方法。

（1）static getDerivedStateFromProps(props, state)

getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。

> 当state的值在任何时候都取决于props的时候适用该方法。

示例如下：

```
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
  }
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.type !== prevState.type) {
        return {
            type: nextProps.type,
        };
    }
    return null;
  }
  render() {
    return (
      <div>{/* ...contents... */}</div>
    );
  }
}


```

（2）getSnapshotBeforeUpdate(prevProps, prevState)

getSnapshotBeforeUpdate() 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 `componentDidUpdate()`。此用法并不常见，但它可能出现在 UI 处理中，如需要以特殊方式处理滚动位置的聊天线程等。应返回 snapshot 的值（或 null）。

示例如下：

```
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}


```

## 8.事件处理(@Click vs onClick)

vue与react在针对事件处理上的用法上也有较大不同。

### vue

vue中使用 v-on 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。通常使用v-on接收一个需要调用的方法名称。

（1）直接绑定方法，不传任何参数，回调函数参数是浏览器事件event对象。

event是原生 MouseEvent

示例：

```
<div  @click="greet">Greet</div>


```

method：

```
  methods: {
    greet(event) {
      console.log(event);
    }
  }


```

（2）内联调用方法

示例：

```
<div  @click="greet('hello')">Greet</div>


```

method:

```
methods: {
  greet(message) {
    this.message = message;
  }
}


```

有时也需要在method中访问原生DOM事件，可以将$event显式传入method中。

```
<div  @click="greet('hello', $event)">Greet</div>


```

method:

```
methods: {
  greet(message, event) {
    this.message = message;
  }
}


```

（3）事件修饰符和按键修饰符

Vue.js为事件添加了事件修饰符和按键修饰符（个人感觉这个是vue做的很好的点，让用户更加聚焦数据逻辑，无需花费更多的精力处理DOM事件细节）。

Ⅰ. 事件修饰符

在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。为了解决这个问题，Vue.js 为 v-on 提供了事件修饰符，修饰符是由点开头的指令后缀来表示的。

- .stop：阻止事件继续传播
- .prevent：阻止事件默认行为
- .capture：添加事件监听器时使用事件捕获模式
- .self：当前元素触发时才触发事件处理函数
- .once：事件只触发一次
- .passive：告诉浏览器你不想阻止事件的默认行为，不能和.prevent一起使用。

示例：

```
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 --
<div v-on:scroll.passive="onScroll">...</div>


```

Ⅱ. 按键修饰符

在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 v-on 在监听键盘事件时添加按键修饰符。 你可以直接将 KeyboardEvent.key 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。

① 按键码 
 Vue 提供了绝大多数常用的按键码的别名

- .enter
- .tab
- .delete (捕获“删除”和“退格”键)
- .esc
- .space
- .up
- .down
- .left
- .right

使用 keyCode 特性也是允许的：

```
<input v-on:keyup.13="submit">


```

② 系统修饰键 
 可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。

- .ctrl
- .alt
- .shift
- .meta

③ .exact 修饰符 
 .exact 修饰符允许你控制由精确的系统修饰符组合触发的事件。

```
<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>


```

④ 鼠标按钮修饰符 
 这些修饰符会限制处理函数仅响应特定的鼠标按钮。

- .left
- .right
- .middle

> 关于v-on处理事件的好处： 
> 1.扫一眼 HTML 模板便能轻松定位在 JavaScript 代码里对应的方法。 
> 2.因为你无须在 JavaScript 里手动绑定事件，你的 ViewModel 代码可以是非常纯粹的逻辑，和 DOM 完全解耦，更易于测试。 
> 3.当一个 ViewModel 被销毁时，所有的事件处理器都会自动被删除。你无须担心如何清理它们。

### react

React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同:

- React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

（1）事件处理程序不传参数

在class组件中使用回调函数，需要显式绑定this或者使用箭头函数。

不传参数时，默认参数是e，这是一个合成事件。React 根据 W3C 规范来定义这些合成事件，所以你不需要担心跨浏览器的兼容性问题。

> 在React中不能通过返回false的方式阻止默认行为，你必须显式的使用preventDefault。

显示绑定this：

```
class NewComponent extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this); // 显示绑定this.handleClick
  }

  handleClick(e) {
    e.preventDefault();
    console.log(e.target);
  }

  render() {
    return <div onClick={this.handleClick}>Click me</div>;
  }
}


```

箭头函数：

```
class NewComponent extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick = (e) => {
    e.preventDefault();
    console.log(e.target);
  };

  render() {
    return <div onClick={this.handleClick}>Click me</div>;
  }
}


```

（2）事件处理程序传递参数

通常我们会为事件处理函数传递额外的参数，有两种方式向事件处理函数传递参数：

Ⅰ. 箭头函数传递参数 
通过箭头函数的方式，事件对象e必需显示的进行传递。

```
class NewComponent extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick(e, message) {
    e.preventDefault();
    console.log(message);
  };

  render() {
    return <div onClick={(e) => this.handleClick(e, 'hello')}>Click me</div>;
  }
}


```

Ⅱ. 通过bind形式传递参数 
 e作为第二个参数传递，事件对象以及更多的参数将会被隐式的传递。

```
class NewComponent extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick(message, e) { // e作为第二个参数
    e.preventDefault();
    console.log(message);
  };

  render() {
    return <div onClick={this.handleClick.bind(this, 'hello')}>Click me</div>;
  }
}

```


 Proxy 与 Object.defineProperty 对比

`Object.defineProperty` 虽然已经能够实现双向绑定了，但是他还是有缺陷的。

1. 只能对属性进行数据劫持，所以需要深度遍历整个对象
2. 对于数组不能监听到数据的变化

虽然 Vue 中确实能检测到数组数据的变化，但是其实是使用了 hack 的办法，并且也是有缺陷的。

```
const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)
// hack 以下几个函数
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
methodsToPatch.forEach(function (method) {
  // 获得原生函数
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator (...args) {
    // 调用原生函数
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // 触发更新
    ob.dep.notify()
    return result
  })
})
复制代码
```

反观 Proxy 就没以上的问题，原生支持监听数组变化，并且可以直接对整个对象进行拦截，所以 Vue 也将在下个大版本中使用 Proxy 替换 Object.defineProperty

```
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver);
    },
    set(target, property, value, receiver) {
      setBind(value);
      return Reflect.set(target, property, value);
    }
  };
  return new Proxy(obj, handler);
};

let obj = { a: 1 }
let value
let p = onWatch(obj, (v) => {
  value = v
}, (target, property) => {
  console.log(`Get '${property}' = ${target[property]}`);
})
p.a = 2 // bind `value` to `2`
p.a // -> Get 'a' = 2
复制代码
```

### 虚拟 DOM

虚拟 DOM 涉及的内容很多，具体可以参考我之前 [写的文章](https://juejin.im/post/5b10dd36e51d4506e04cf802)

### 路由鉴权

- 登录页和其他页面分开，登录以后实例化 Vue 并且初始化需要的路由
- 动态路由，通过 addRoute 实现

### Vue 和 React 区别

- Vue 表单支持双向绑定开发更方便
- 改变数据方式不同，setState 有使用坑
- props Vue 可变，React 不可变
- 判断是否需要更新 React 可以通过钩子函数判断，Vue 使用依赖追踪，修改了什么才渲染什么
- React 16以后 有些钩子函数会执行多次
- React 需要使用 JSX，需要 Babel 编译。Vue 虽然可以使用模板，但是也可以通过直接编写 render 函数不需要编译就能运行。
- 生态 React 相对较好

 React 生命周期

在 V16 版本中引入了 Fiber 机制。这个机制一定程度上的影响了部分生命周期的调用，并且也引入了新的 2 个 API 来解决问题。

在之前的版本中，如果你拥有一个很复杂的复合组件，然后改动了最上层组件的 `state`，那么调用栈可能会很长



![img](https://user-gold-cdn.xitu.io/2018/6/25/164358b0310f476c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



调用栈过长，再加上中间进行了复杂的操作，就可能导致长时间阻塞主线程，带来不好的用户体验。Fiber 就是为了解决该问题而生。

Fiber 本质上是一个虚拟的堆栈帧，新的调度器会按照优先级自由调度这些帧，从而将之前的同步渲染改成了异步渲染，在不影响体验的情况下去分段计算更新。



![img](https://user-gold-cdn.xitu.io/2018/6/25/164358f89595d56f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



对于如何区别优先级，React 有自己的一套逻辑。对于动画这种实时性很高的东西，也就是 16 ms 必须渲染一次保证不卡顿的情况下，React 会每 16 ms（以内） 暂停一下更新，返回来继续渲染动画。

对于异步渲染，现在渲染有两个阶段：`reconciliation` 和 `commit` 。前者过程是可以打断的，后者不能暂停，会一直更新界面直到完成。

**Reconciliation** 阶段

- `componentWillMount`
- `componentWillReceiveProps`
- `shouldComponentUpdate`
- `componentWillUpdate`

**Commit** 阶段

- `componentDidMount`
- `componentDidUpdate`
- `componentWillUnmount`

因为 `reconciliation` 阶段是可以被打断的，所以 `reconciliation` 阶段会执行的生命周期函数就可能会出现调用多次的情况，从而引起 Bug。所以对于 `reconciliation` 阶段调用的几个函数，除了 `shouldComponentUpdate` 以外，其他都应该避免去使用，并且 V16 中也引入了新的 API 来解决这个问题。

`getDerivedStateFromProps` 用于替换 `componentWillReceiveProps` ，该函数会在初始化和 `update` 时被调用

```
class ExampleComponent extends React.Component {
  // Initialize state in constructor,
  // Or with a property initializer.
  state = {};

  static getDerivedStateFromProps(nextProps, prevState) {
    if (prevState.someMirroredValue !== nextProps.someValue) {
      return {
        derivedData: computeDerivedState(nextProps),
        someMirroredValue: nextProps.someValue
      };
    }

    // Return null to indicate no change to state.
    return null;
  }
}
复制代码
```

`getSnapshotBeforeUpdate` 用于替换 `componentWillUpdate` ，该函数会在 `update` 后 DOM 更新前被调用，用于读取最新的 DOM 数据。

#### V16 生命周期函数用法建议

```
class ExampleComponent extends React.Component {
  // 用于初始化 state
  constructor() {}
  // 用于替换 `componentWillReceiveProps` ，该函数会在初始化和 `update` 时被调用
  // 因为该函数是静态函数，所以取不到 `this`
  // 如果需要对比 `prevProps` 需要单独在 `state` 中维护
  static getDerivedStateFromProps(nextProps, prevState) {}
  // 判断是否需要更新组件，多用于组件性能优化
  shouldComponentUpdate(nextProps, nextState) {}
  // 组件挂载后调用
  // 可以在该函数中进行请求或者订阅
  componentDidMount() {}
  // 用于获得最新的 DOM 数据
  getSnapshotBeforeUpdate() {}
  // 组件即将销毁
  // 可以在此处移除订阅，定时器等等
  componentWillUnmount() {}
  // 组件销毁后调用
  componentDidUnMount() {}
  // 组件更新后调用
  componentDidUpdate() {}
  // 渲染组件函数
  render() {}
  // 以下函数不建议使用
  UNSAFE_componentWillMount() {}
  UNSAFE_componentWillUpdate(nextProps, nextState) {}
  UNSAFE_componentWillReceiveProps(nextProps) {}
}
复制代码
```

### setState

`setState` 在 React 中是经常使用的一个 API，但是它存在一些问题，可能会导致犯错，核心原因就是因为这个 API 是异步的。

首先 `setState` 的调用并不会马上引起 `state` 的改变，并且如果你一次调用了多个 `setState` ，那么结果可能并不如你期待的一样。

```
handle() {
  // 初始化 `count` 为 0
  console.log(this.state.count) // -> 0
  this.setState({ count: this.state.count + 1 })
  this.setState({ count: this.state.count + 1 })
  this.setState({ count: this.state.count + 1 })
  console.log(this.state.count) // -> 0
}
复制代码
```

第一，两次的打印都为 0，因为 `setState` 是个异步 API，只有同步代码运行完毕才会执行。`setState` 异步的原因我认为在于，`setState` 可能会导致 DOM 的重绘，如果调用一次就马上去进行重绘，那么调用多次就会造成不必要的性能损失。设计成异步的话，就可以将多次调用放入一个队列中，在恰当的时候统一进行更新过程。

第二，虽然调用了三次 `setState` ，但是 `count` 的值还是为 1。因为多次调用会合并为一次，只有当更新结束后 `state` 才会改变，三次调用等同于如下代码

```
Object.assign(  
  {},
  { count: this.state.count + 1 },
  { count: this.state.count + 1 },
  { count: this.state.count + 1 },
)
复制代码
```

当然你也可以通过以下方式来实现调用三次 `setState` 使得 `count` 为 3

```
handle() {
  this.setState((prevState) => ({ count: prevState.count + 1 }))
  this.setState((prevState) => ({ count: prevState.count + 1 }))
  this.setState((prevState) => ({ count: prevState.count + 1 }))
}
复制代码
```

如果你想在每次调用 `setState` 后获得正确的 `state` ，可以通过如下代码实现`回调`

```
handle() {
    this.setState((prevState) => ({ count: prevState.count + 1 }), () => {
        console.log(this.state)
    })
}
复制代码
```

### Vue的 nextTick 原理

`nextTick` 可以让我们在下次 DOM 更新循环结束之后执行延迟回调，用于获得更新后的 DOM。

在 Vue 2.4 之前都是使用的 microtasks，但是 microtasks 的优先级过高，在某些情况下可能会出现比事件冒泡更快的情况，但如果都使用 macrotasks 又可能会出现渲染的性能问题。所以在新版本中，会默认使用 microtasks，但在特殊情况下会使用 macrotasks，比如 v-on。 

对于实现 macrotasks ，会先判断是否能使用 `setImmediate` ，不能的话降级为 `MessageChannel` ，以上都不行的话就使用 `setTimeout`

```
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else if (
  typeof MessageChannel !== 'undefined' &&
  (isNative(MessageChannel) ||
    // PhantomJS
    MessageChannel.toString() === '[object MessageChannelConstructor]')
) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} else {
  /* istanbul ignore next */
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
复制代码
```

`nextTick` 同时也支持 Promise 的使用，会判断是否实现了 Promise

```
export function nextTick(cb?: Function, ctx?: Object) {
  let _resolve
  // 将回调函数整合进一个数组中
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      microTimerFunc()
    }
  }
  // 判断是否可以使用 Promise 
  // 可以的话给 _resolve 赋值
  // 这样回调函数就能以 promise 的方式调用
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
复制代码
```

