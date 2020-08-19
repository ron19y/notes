## 组件之间的传值通信

1. 父组件给子组件传值通过props
2. 子组件给父组件传值通过$emit触发回调
3. 兄弟组件通信，通过实例一个vue实例eventBus作为媒介，要相互通信的兄弟组件之中，都引入eventBus

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

### 3. v-model是如何实现双向绑定的？

`v-model` 是用来在表单控件或者组件上创建双向绑定的，他的本质是 `v-bind` 和 `v-on` 的语法糖，在一个组件上使用 `v-model`，默认会为组件绑定名为 `value` 的 `prop` 和名为 `input` 的事件。
 文章—— [vue 组件通信看这篇就够了](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247484468&idx=1&sn=3c309945992fe4f0c6276d91d7cb67b8&chksm=ce5e364ff929bf59ae686e3fa2412632348d6e190054e0b83bed11b5fd851ebbe8d326b5caf0&token=1424393752&lang=zh_CN#rd) 中也有其详细介绍

### 4. Vuex和单纯的全局对象有什么区别？

Vuex和全局对象主要有两大区别：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

### 5. 为什么 Vuex 的 mutation 中不能做异步操作？

Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。 