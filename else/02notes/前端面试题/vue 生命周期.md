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
 `` 有自己独立的钩子函数 activated 和 deactivated。

### 2. Vue 的父组件和子组件生命周期钩子执行顺序是什么？

**渲染过程：**
 父组件挂载完成一定是等子组件都挂载完成后，才算是父组件挂载完，所以父组件的mounted在子组件mouted之后
 父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate -> 子created -> 子beforeMount -> 子mounted -> 父mounted

**子组件更新过程：**

1. 影响到父组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
2. 不影响父组件： 子beforeUpdate -> 子updated

**父组件更新过程：**

1. 影响到子组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
2. 不影响子组件： 父beforeUpdate -> 父updated

**销毁过程：**
 父beforeDestroy -> 子beforeDestroy -> 子destroyed -> 父destroyed

 


