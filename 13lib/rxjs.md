ReactiveX combines the `Observer pattern` with the `Iterator pattern` and `functional programming` with collections to fill the need for an ideal way of managing sequences of events. ReactiveX

### 什么是函数式编程 ?

`Functional Programming` 是一种编程范式(`programming paradigm`)，就像`Object-oriented Programming(OOP)`一样，就是一种写程式的方法论，这些方法论告诉我们如何思考及解决问题。

函数式编程关心数据的映射，命令式编程关心解决问题的步骤.

这里的映射就是数学上函数的概念——一种东西和另一种东西之间的对应关系, 简单说`Functional Programming` 核心思想就是做运算处理，并用function 来思考问题.

 

### 函数式编程基本要素

#### 函式为一等公民(First Class)

所谓一等公民是指跟其它对象具有同等的地位，也就是说函数能够被赋值给变量，也能够被当作参数传入另一个函数，也可当作一个函数的返回值。

```
// 函数能够被赋值给变量
var hello = function() {}

// 函数当作参数传入另一个函数
fetch('www.google.com')
.then(function(response) {}) // 匿名 function 被傳入 then()

// 当作一个函数的返回值
var a = function(a) {
	return function(b) {
	  return a + b;
	}; 
}
复制代码
```

#### Expression, no Statement

`Functional Programming`都是表达式(`Expression`)不会是语句(Statement)。 基本区分表达式与语句：

- 表达式是一个运算过程，一定会有返回值，例如执行一个`function`, 声明一个变量。
- 语句则是表现某个行为，例如赋值给一个变量， for循环，if判断

> 有时候表达式也可能同时是合法的语句，这里只讲基本的判断方法。如果想更深入了解其中的差异，可以看这篇文章[Expressions versus statements in JavaScript ](http://2ality.com/2012/09/expressions-vs-statements.html)

#### 纯函数（Pure Function）

纯函数是这样一种函数，即相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用(`side effect`)

举个简单的例子，`slice`和`splice`：

```
var arr = [1, 2, 3, 4, 5];

arr.slice(0, 3); // [1, 2, 3]

arr.slice(0, 3); // [1, 2, 3]

arr.slice(0, 3); // [1, 2, 3]
复制代码
```

这里可以看到`slice`不管执行几次，返回值都是相同的，并且除了返回一个值之外并没有做任何事，所以`slice`就是一个`pure function`。

```
var arr = [1, 2, 3, 4, 5];

arr.splice(0, 3); // [1, 2, 3]

arr.splice(0, 3); // [4, 5]

arr.slice(0, 3); // []
复制代码
```

这里我们换成用`splice`，因为`splice`每执行一次就会影响`arr`的值，导致每次结果都不同，这就很明显不是一个`pure function`。

什么是副作用(`side effect`)

副作用指一个`function`做了跟本身运算返回值没有关系的事，比如说修改某个全域变数，或是修改传入参数的值，甚至是执行`console.log`都算是副作用。

`Functional Programming` 强调没有副作用，也就是`function` 要保持纯粹，只做运算并返回一个值，没有其他额外的行为。

这里列举几个常见的副作用：

- 更改文件系统
- 发送一个 http 请求
- 可变数据（random）
- 打印/log
- 获取用户输入
- DOM 查询

概括来讲，只要是跟函数外部环境发生的交互就都是副作用——这一点可能会让你怀疑无副作用编程的可行性。函数式编程的哲学就是假定副作用是造成不正当行为的主要原因, 这并不是说，要禁止使用一切副作用，而是说，要让它们在可控的范围内发生。

### 函数式编程优势

- 可读性高: 通过一系列的函数封装过程，代码变得非常的简洁且可读性极高
- 可维护性高: 因为`Pure function`等特性，执行结果不依赖外部状态，且不会对外部环境有任何操作，这使得单元测试和调试都更容易
- 易于并行处理: 由于不共享外部状态，不会造成资源争用(Race condition)，也就不需要用锁来保护可变状态，也就不会出现死锁，这样可以更好地并发起来。

## 观察者模式（Observer Pattern）

> 观察者模式，即发布-订阅模式，它定义了一个一对多的依赖关系，让一个或多个观察者对象监听一个主题对象。这样一来，当被观察者状态发生改变时，需要通知相应的观察者，使这些观察者对象能够自动更新。

### 关键要素

#### 主题

主题是观察者观察的对象，一个主题必须具备下面三个特征。

- 持有监听的观察者的引用
- 支持增加和删除观察者
- 主题状态改变，主动通知观察者

#### 观察者

当主题发生变化，收到通知后进行具体的处理



![img](https://user-gold-cdn.xitu.io/2018/7/1/16453d1a22437918?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



这里举一个例子来说明，牛奶送奶站就是主题，订奶客户为监听者，客户从送奶站订阅牛奶后，会每天收到牛奶。如果客户不想订阅了，可以取消，以后就不会收到牛奶。

根据上面的说明，我们可以简单实现一个被观察者：

```
class Subject {
  constructor() {
    this.observerCollection = [];
  }

  registerObserver(observer){
    this.observerCollection.push(observer)
  }
  unRegisterObserver(observer){
    this.observerCollection.splice(this.observer.findIndex(observer), 1)
  }

  notifyObservers(message){
    this.observerCollection.forEach(observer => {
      observer.notify(message);
    })
  }
}
复制代码
```

### 松耦合

- 观察者增加或删除无需修改主题的代码，只需调用主题对应的增加或者删除的方法即可。
- 主题只负责通知观察者，但无需了解观察者如何处理通知。举个例子，送奶站只负责送递牛奶，不关心客户是喝掉还是洗脸。
- 观察者只需等待主题通知，无需观察主题相关的细节。还是那个例子，客户只需关心送奶站送到牛奶，不关心牛奶由哪个快递人员，使用何种交通工具送达。

## 迭代器模式（Iterator Pattern）

迭代器模式（Iterator）提供了一种方法顺序访问一个集合对象中各个元素，而又不暴露该对象的内部表示，迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。

`Iterator` 的遍历过程是这样的：

1. 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
2. 第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。
3. 第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。
4. 不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

可参考[ES6系列--7. 可迭代协议和迭代器协议](https://juejin.im/post/6844903630802255885)中关于迭代器的介绍。

`JavaScript` 中像 `Array、Set、Map` 等都属于内置的可迭代类型, 可以通过 `iterator`方法来获取一个迭代对象，调用迭代对象的 `next` 方法将获取一个元素对象，如下示例：

```
var arr = [1, 2, 3];

var iterator = arr[Symbol.iterator]();

iterator.next();
// { value: 1, done: false }
iterator.next();
// { value: 2, done: false }
iterator.next();
// { value: 3, done: false }
iterator.next();
// { value: undefined, done: true }

复制代码
```

遍历迭代器可以使用下面的方法。

```
while(true) {
  let result;
  try {
    result = iterator.next();
  } catch (error) {
    handleError(error); // 错误处理
  }
  if (result.done) {
    handleCompleted(); // 已完成之后的处理
  }
  doSomething(result.value);
}
复制代码
```

上面的代码主要对应三种情况：

- 获取下一个值（next）：调用`next`方法可以将元素一个个的返回，这样就支持了返回多次值。
- 已完成（complete）：当没有更多值时，next返回元素中的`done`为`true`。
- 错误处理（error）：当 `next` 方法执行时报错，则会抛出 `error` 事件，所以可以用 `try catch` 包裹 `next` 方法处理可能出现的错误。

下一篇开始介绍Observable 和 observer。


作者：Escape Plan
链接：https://juejin.im/post/6844903630403813384
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。