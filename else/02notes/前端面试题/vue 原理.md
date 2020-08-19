### 1. Vue 的响应式原理

如果面试被问到这个问题，又描述不清楚，可以直接画出 Vue 官方文档的这个图，对着图来解释效果会更好。 

![img](https://user-gold-cdn.xitu.io/2020/2/20/17062a3aa3acd499?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 Vue 的响应式是通过 `Object.defineProperty``Object.defineProperty``observe``getter``setter``watcher``getter``setter``watcher`



### 2. Object.defineProperty有哪些缺点？

这道题目也可以问成 “为什么vue3.0使用proxy实现响应式？” 其实都是对Object.defineProperty 和 proxy实现响应式的对比。

1. `Object.defineProperty` 只能劫持对象的属性，而 `Proxy` 是直接代理对象
    由于 `Object.defineProperty` 只能对属性进行劫持，需要遍历对象的每个属性。而 `Proxy` 可以直接代理对象。
2. `Object.defineProperty` 对新增属性需要手动进行 `Observe`， 由于 `Object.defineProperty` 劫持的是对象的属性，所以新增属性时，需要重新遍历对象，对其新增属性再使用 `Object.defineProperty` 进行劫持。 也正是因为这个原因，使用 Vue 给 `data` 中的数组或对象新增属性时，需要使用 `vm.$set` 才能保证新增的属性也是响应式的。
3. `Proxy` 支持13种拦截操作，这是 `defineProperty` 所不具有的。
4. 新标准性能红利
    `Proxy` 作为新标准，长远来看，JS引擎会继续优化 `Proxy` ，但 `getter` 和 `setter` 基本不会再有针对性优化。
5. `Proxy` 兼容性差 目前并没有一个完整支持 `Proxy` 所有拦截方法的Polyfill方案

更详细的对比，可以查看我的文章 [为什么Vue3.0不再使用defineProperty实现数据监听？](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247485104&idx=1&sn=648f1850fb59f04c6ca52cdd794013b5&chksm=ce5e34cbf929bddd4325de7f78dc4ad5822cddff69c999d1225f860e917dfd3d6eea43527c56&token=1424393752&lang=zh_CN#rd)

### 3. Vue中如何检测数组变化？

Vue 的 `Observer` 对数组做了单独的处理，对数组的方法进行编译，并赋值给数组属性的 `__proto__` 属性上，因为原型链的机制，找到对应的方法就不会继续往上找了。编译方法中会对一些会增加索引的方法（`push`，`unshift`，`splice`）进行手动 observe。 具体同样可以参考我的这篇文章 [为什么Vue3.0不再使用defineProperty实现数据监听？](https://mp.weixin.qq.com/s?__biz=Mzg2NTA4NTIwNA==&mid=2247485104&idx=1&sn=648f1850fb59f04c6ca52cdd794013b5&chksm=ce5e34cbf929bddd4325de7f78dc4ad5822cddff69c999d1225f860e917dfd3d6eea43527c56&token=1424393752&lang=zh_CN#rd)，里面有详细的源码分析。

### 4. 组件的 data 为什么要写成函数形式？

Vue 的组件都是可复用的，一个组件创建好后，可以在多个地方复用，而不管复用多少次，组件内的 `data` 都应该是相互隔离，互不影响的，所以组件每复用一次，`data` 就应该复用一次，每一处复用组件的 `data` 改变应该对其他复用组件的数据不影响。
 为了实现这样的效果，`data` 就不能是单纯的对象，而是以一个函数返回值的形式，所以每个组件实例可以维护独立的数据拷贝，不会相互影响。

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

`key` 是给每个 `vnode` 指定的唯一 `id`，在同级的 `vnode`  diff 过程中，可以根据 `key` 快速的对比，来判断是否为相同节点，并且利用 `key` 的唯一性可以生成 `map` 来更快的获取相应的节点。
 另外指定 `key` 后，就不再采用“就地复用”策略了，可以保证渲染的准确性。

### 8. 为什么 v-for 和 v-if 不建议用在一起

当 `v-for` 和 `v-if` 处于同一个节点时，`v-for` 的优先级比 `v-if` 更高，这意味着 `v-if` 将分别重复运行于每个 `v-for` 循环中。如果要遍历的数组很大，而真正要展示的数据很少时，这将造成很大的性能浪费。
 这种场景建议使用 `computed`，先对数据进行过滤。 