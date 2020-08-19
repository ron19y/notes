## mvc和mvvm理解

### MVC

MVC即Model View Controller，简单来说就是通过controller的控制去操作model层的数据，并且返回给view层展示。



![mvc.png](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eed617bfe79?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



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

 


