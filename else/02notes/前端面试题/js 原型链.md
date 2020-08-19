### 简单说下原型链？



![prototype](https://user-gold-cdn.xitu.io/2018/9/19/165f189f736f19fd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



每个函数都有 `prototype` 属性，除了 `Function.prototype.bind()`，该属性指向原型。

每个对象都有 `__proto__` 属性，指向了创建该对象的构造函数的原型。其实这个属性指向了 `[[prototype]]`，但是 `[[prototype]]` 是内部属性，我们并不能访问到，所以使用 `_proto_` 来访问。

对象可以通过 `__proto__` 来寻找不属于该对象的属性，`__proto__` 将对象连接起来组成了原型链。

如果你想更进一步的了解原型，可以仔细阅读 [深度解析原型中的各个难点](https://github.com/KieSun/Blog/issues/2)。

### 怎么判断对象类型？

- 可以通过 `Object.prototype.toString.call(xx)`。这样我们就可以获得类似 `[object Type]` 的字符串。
- `instanceof` 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 `prototype`。





## 什么是原型?



![img](https://user-gold-cdn.xitu.io/2019/4/16/16a26ab5adf73d04?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 原型链：简单来讲就是原型组成的链，比如函数的原型是Function，Function的原型是Object，Object的原型仍然是Object，一直追溯到最终的原型对象。 

函数通过prototype来追溯原型对象，对象通过_proto_来追溯原型对象。

通过一个构造函数创建出来的多个实例，如果都要添加一个方法，给每个实例去添加并不是一个明智的选择。这时就该用上原型了。

在实例的原型上添加一个方法，这个原型的所有实例便都有了这个方法。

原型链继承：

```
function Show(){
this.name="run";
}

function Run(){
this.age="20"; //Run继承了Show,通过原型，形成链条
}
Run.prototype=new Show();
var show=new Run();
alert(show.name)//结果：run 
```

