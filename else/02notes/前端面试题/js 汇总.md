# JS数据

## `JS`数据类型

JS基本有5种简单数据类型：String，Number，Boolean，Null，Undefined。引用数据类型：Object，Array，Function。

## 判断数据类型的方法

在写业务逻辑的时候，经常要用到JS数据类型的判断，面试常见的案例深浅拷贝也要用到数据类型的判断。

### typeof

```
console.log(typeof 2);               // number
console.log(typeof true);            // boolean
console.log(typeof 'str');           // string
console.log(typeof undefined);       // undefined
console.log(typeof []);              // object 
console.log(typeof {});              // object
console.log(typeof function(){});    // function
console.log(typeof null);            // object 
```

优点：能够快速区分基本数据类型 缺点：不能将Object、Array和Null区分，都返回object

### instanceof

```
console.log(2 instanceof Number);                    // false
console.log(true instanceof Boolean);                // false 
console.log('str' instanceof String);                // false  
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true 
```

优点：能够区分Array、Object和Function，适合用于判断自定义的类实例对象 缺点：Number，Boolean，String基本数据类型不能判断

### Object.prototype.toString.call()

```
var toString = Object.prototype.toString;
 
console.log(toString.call(2));                      //[object Number]
console.log(toString.call(true));                   //[object Boolean]
console.log(toString.call('str'));                  //[object String]
console.log(toString.call([]));                     //[object Array]
console.log(toString.call(function(){}));           //[object Function]
console.log(toString.call({}));                     //[object Object]
console.log(toString.call(undefined));              //[object Undefined]
console.log(toString.call(null));                   //[object Null] 
```

优点：精准判断数据类型 缺点：写法繁琐不容易记，推荐进行封装后使用

## null和undefined区别

Undefined类型只有一个值，即undefined。当声明的变量还未被初始化时，变量的默认值为undefined。用法：

- 变量被声明了，但没有赋值时，就等于undefined。
- 调用函数时，应该提供的参数没有提供，该参数等于undefined。
- 对象没有赋值的属性，该属性的值为undefined。
- 函数没有返回值时，默认返回undefined。

Null类型也只有一个值，即null。null用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。用法

- 作为函数的参数，表示该函数的参数不是对象。
- 作为对象原型链的终点。

## ==和===区别

- ==， 两边值类型不同的时候，要先进行类型转换，再比较
- ===，不做类型转换，类型不同的一定不等。

==类型转换过程：

1. 如果类型不同，进行类型转换
2. 判断比较的是否是 null 或者是 undefined, 如果是, 返回 true .
3. 判断两者类型是否为 string 和 number, 如果是, 将字符串转换成 number
4. 判断其中一方是否为 boolean, 如果是, 将 boolean 转为 number 再进行判断
5. 判断其中一方是否为 object 且另一方为 string、number 或者 symbol , 如果是, 将 object 转为原始类型再进行判断

### 经典面试题：[] == ![] 为什么是true

转化步骤：

1. !运算符优先级最高，`![]`会被转为为false，因此表达式变成了：`[] == false`
2. 根据上面第(4)条规则，如果有一方是boolean，就把boolean转为number，因此表达式变成了：`[] == 0`
3. 根据上面第(5)条规则，把数组转为原始类型，调用数组的toString()方法，`[]`转为空字符串，因此表达式变成了：`'' == 0`
4. 根据上面第(3)条规则，两边数据类型为string和number，把空字符串转为0，因此表达式变成了：`0 == 0`
5. 两边数据类型相同，0==0为true

# JS作用域 

> JS中的作用域分为两种：全局作用域和函数作用域。函数作用域中定义的变量，只能在函数中调用，外界无法访问。没有块级作用域导致了if或for这样的逻辑语句中定义的变量可以被外界访问，因此ES6中新增了let和const命令来进行块级作用域的声明。 

## 请解释变量声明提升

通过var声明的变量会被提升至作用域的顶端。不仅仅是变量，函数声明也一样会被提升。当同一作用域内同时出现变量和函数声明提升时，变量仍然在函数前面。  

## 闭包 

> 简单来说闭包就是在函数里面声明函数，本质上说就是在函数内部和函数外部搭建起一座桥梁，使得子函数可以访问父函数中所有的局部变量，但是反之不可以，这只是闭包的作用之一，另一个作用，则是保护变量不受外界污染，使其一直存在内存中，在工作中我们还是少使用闭包的好，因为闭包太消耗内存，不到万不得已的时候尽量不使用。 

## var,let,const的区别

`let` 为 `ES6` 新添加申明变量的命令，它类似于 `var`，但是有以下不同：

- `var` 声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象
- `let` 声明的变量，其作用域为该语句所在的代码块内，不存在变量提升
- `const`声明的变量不允许修改

## 定义函数的方法

1. 函数声明

```
//ES5
function getSum(){}
function (){}//匿名函数
//ES6
()=>{} 
```

1. 函数表达式

```
//ES5
var getSum=function(){}
//ES6
let getSum=()=>{} 
```

1. 构造函数

```
const getSum = new Function('a', 'b' , 'return a + b') 
```

## This的指向

第一准则是：this永远指向函数运行时所在的对象，而不是函数被创建时所在的对象。

- 普通的函数调用，函数被谁调用，this就是谁。
- 构造函数的话，如果不用new操作符而直接调用，那即this指向window。用new操作符生成对象实例后，this就指向了新生成的对象。
- 匿名函数或不处于任何对象中的函数指向window 。
- 如果是call，apply等，指定的this是谁，就是谁。 

### 箭头函数的特点

```
function a() {
    return () => {
        return () => {
        	console.log(this)
        }
    }
}
console.log(a()()())
复制代码
```

箭头函数其实是没有 `this` 的，这个函数中的 `this` 只取决于他外面的第一个不是箭头函数的函数的 `this`。在这个例子中，因为调用 `a` 符合前面代码中的第一个情况，所以 `this` 是 `window`。并且 `this` 一旦绑定了上下文，就不会被任何代码改变。

### This

`this` 是很多人会混淆的概念，但是其实他一点都不难，你只需要记住几个规则就可以了。

```
function foo() {
	console.log(this.a)
}
var a = 1
foo()

var obj = {
	a: 2,
	foo: foo
}
obj.foo()

// 以上两者情况 `this` 只依赖于调用函数前的对象，优先级是第二个情况大于第一个情况

// 以下情况是优先级最高的，`this` 只会绑定在 `c` 上，不会被任何方式修改 `this` 指向
var c = new foo()
c.a = 3
console.log(c.a)

// 还有种就是利用 call，apply，bind 改变 this，这个优先级仅次于 new
```

 

## call,apply和bind区别

三个函数的作用都是将函数绑定到上下文中，用来改变函数中this的指向；三者的不同点在于语法的不同。

```
fun.call(thisArg[, arg1[, arg2[, ...]]])
fun.apply(thisArg, [argsArray]) 
```

所以`apply`和`call`的区别是`call`方法接受的是若干个参数列表，而`apply`接收的是一个包含多个参数的数组。

而bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

```
var bindFn = fun.bind(thisArg[, arg1[, arg2[, ...]]])
bindFn() 

```

Demos：

```
var name = 'window';
var sayName = function (param) {
    console.log('my name is:' + this.name + ',my param is ' + param)
};
//my name is:window,my param is window param
sayName('window param')

var callObj = {
    name: 'call'
};
//my name is:call,my param is call param
sayName.call(callObj, 'call param');


var applyObj = {
    name: 'apply'
};
//my name is:apply,my param is apply param
sayName.apply(applyObj, ['apply param']);

var bindObj = {
    name: 'bind'
}
var bindFn = sayName.bind(bindObj, 'bind param')
//my name is:bind,my param is bind param
bindFn(); 

```

### 如何实现一个 bind 函数

对于实现以下几个函数，可以从几个方面思考

- 不传入第一个参数，那么默认为 `window`
- 改变了 this 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？

```
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
复制代码

```

### 如何实现一个 call 函数

```
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}
复制代码

```

### 如何实现一个 apply 函数

```
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this

  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }

  delete context.fn
  return result
}

```

 

# JS数组 

## 数组去重

```
let arr = [1,'1',2,'2',1,2,'x','y','f','x','y','f'];
function unique1(arr){
	let result = [arr[0]];
	for (let i = 1; i < arr.length; i++) {
		let item = arr[i];
		if(result.indexOf(item) == -1){
			result.push(item);
		}
	}
	return result;
}
console.log(unique1(arr)); 
```

### 数组降维

```
[1, [2], 3].flatMap((v) => v + 1)
// -> [2, 3, 4]
复制代码
```

如果想将一个多维数组彻底的降维，可以这样实现

```
const flattenDeep = (arr) => Array.isArray(arr)
  ? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , [])
  : [arr]

flattenDeep([1, [[2], [3, [4]], 5]])
```

 

## 数组方法

##### 1 ["1", "2", "3"].map(parseInt)

```
首先, map接受两个参数, 一个回调函数 callback, 一个回调函数的this值

其中回调函数接受三个参数 currentValue, index, arrary;

而题目中, map只传入了回调函数--parseInt.

其次, parseInt 只接受两个两个参数 string, radix(基数).  
本题理解来说也就是key与 index  
parseInt('1', 0); //1
parseInt('2', 1); //NaN
parseInt('3', 2); //NaN 

parseInt(string, radix)
string	必需。要被解析的字符串。
radix 可选。表示要解析的数字的基数。该值介于 2 ~ 36 之间。
如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。 

```

##### 2 [[3,2,1].reduce(Math.pow), [].reduce(Math.pow)]

```
arr.reduce(callback[, initialValue])
reduce接受两个参数, 一个回调, 一个初始值.
回调函数接受四个参数 previousValue, currentValue, currentIndex, array
需要注意的是 If the array is empty and no initialValue was provided, TypeError would be thrown.
所以第二个表达式会报异常. 第一个表达式等价于 Math.pow(3, 2) => 9; Math.pow(9, 1) =>9 

```

#####  3 ary.filter(function(x) { return x === undefined;})

```
var ary = [0,1,2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined;});
我们看到在迭代这个数组的时候, 首先检查了这个索引值是不是数组的一个属性, 那么我们测试一下.

0 in ary; => true
3 in ary; => false
10 in ary; => true
也就是说 从 3 - 9 都是没有初始化的bug !, 这些索引并不存在与数组中. 在 array 的函数调用的时候是会跳过这些坑的.

```

### 

# JS对象

### 通过new创建一个对象的时候，构造函数内部有哪些改变？

```
function Person(){}
Person.prototype.friend = [];
Person.prototype.name = '';
var a = new Person();
a.friend[0] = '王琦';
var b = new Person();
console.log(b.friend);//Array [ "王琦" ] 

```

- 创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
- 属性和方法被加入到 this 引用的对象中。
- 新创建的对象由 this 所引用，并且最后隐式的返回 this 。

## JS实现继承

首先创建一个父类

```js
// 定义一个动物类
function Animal(name, color) {
    // 属性
    this.name = name || 'Animal';
    this.color = color || ['black'];
    // 实例方法
    this.sleep = function () {
        console.log(this.name + '正在睡觉！');
    }
}
// 原型方法
Animal.prototype.eat = function (food) {
    console.log(this.name + '正在吃：' + food);
}; 
```

### 原型链继承

new了一个空对象，这个空对象指向Animal并且Cat.prototype指向了这个空对象，这种就是基于原型链的继承。

```js
function Cat(name) {
    this.name = name || 'tom'
}
Cat.prototype = new Animal()

var cat = new Cat()
cat.color.push('red')
cat.sleep() //tom正在睡觉！
cat.eat('fish') //tom正在吃：fish
console.log(cat.color) //["black", "red"]
console.log(cat instanceof Animal) //true
console.log(cat instanceof Cat) //true
var new_cat = new Cat()
console.log(new_cat.color) //["black", "red"]  
```

- 特点：基于原型链，既是父类的实例，也是子类的实例。
- 缺点：1.无法实现多继承；2.所有新实例都会共享父类实例的属性。

### 构造继承

```js
function Dog(name) {
    Animal.call(this)
    this.name = name || 'mica'
}
var dog = new Dog()
dog.color.push('blue')
dog.sleep() // mica正在睡觉！
dog.eat('bone') //Uncaught TypeError: dog.eat is not a function
console.log(dog.color) //["black", "blue"]
console.log(dog instanceof Animal) //false
console.log(dog instanceof Dog) //true
var new_dog = new Dog()
console.log(new_dog.color) //["black"] 
```

- 特点：可以实现多继承（call多个），解决了所有实例共享父类实例属性的问题。
- 缺点：1.只能继承父类实例的属性和方法；2.不能继承原型上的属性和方法。

### 组合继承

```js
function Mouse(name){
    Animal.call(this)
    this.name = name || 'jerry'
}
Mouse.prototype = new Animal()
Mouse.prototype.constructor = Mouse

var mouse = new Mouse()
mouse.color.push('yellow)
mouse.sleep() //jerry正在睡觉！
mouse.eat('carrot') //jerry正在吃：carrot
console.log(mouse instanceof Animal)//true
console.log(mouse instanceof Mouse)//true
var new_mouse = new Mouse()
console.log(new_mouse.color) //["black"] 
```

- 特点：可以继承实例属性/方法，也可以继承原型属性/方法
- 缺点：调用了两次父类构造函数，生成了两份实例

## 深拷贝和浅拷贝

浅拷贝

```
function simpleClone(obj) {
    var result = {};
    for (var i in obj) {
        result[i] = obj[i];
    }
    return result;
} 

```

深拷贝，遍历对象中的每一个属性

```
function deepClone(obj) {
    let result;
    if (typeof obj == 'object') {
        result = isArray(obj) ? [] : {}
        for (let i in obj) {
            //isObject(obj[i]) ? deepClone(obj[i]) : obj[i]
            //多谢"朝歌在掘金"指出，多维数组会有问题
            result[i] = isObject(obj[i])||isArray(obj[i])?deepClone(obj[i]):obj[i]
        }
    } else {
        result = obj
    }
    return result
}
function isObject(obj) {
    return Object.prototype.toString.call(obj) == "[object Object]"
}
function isArray(obj) {
    return Object.prototype.toString.call(obj) == "[object Array]"
} 

```

## 0.1+0.2!=0.3怎么处理

把需要计算的数字升级（乘以10的n次幂）成计算机能够精确识别的整数，等计算完成后再进行降级（除以10的n次幂），即：

```
(0.1*10 + 0.2*10)/10 == 0.3 //true 
```

## 5.原生对象和宿主对象

**原生对象**是ECMAScript规定的对象，所有内置对象都是原生对象，比如Array、Date、RegExp等；

**宿主对象**是宿主环境比如浏览器规定的对象，用于完善是ECMAScript的执行环境，比如Document、Location、Navigator等。 

**基本数据类型**指的是简单的数据段，有5种，包括null、undefined、string、boolean、number；

**引用数据类型**指的是有多个值构成的对象，包括object、array、date、regexp、function等。

**主要区别：**

- 声明变量时不同的内存分配：前者由于占据的空间大小固定且较小，会被存储在栈当中，也就是变量访问的位置；后者则存储在堆当中，变量访问的其实是一个指针，它指向存储对象的内存地址。
- 也正是因为内存分配不同，在复制变量时也不一样。前者复制后2个变量是独立的，因为是把值拷贝了一份；后者则是复制了一个指针，2个变量指向的值是该指针所指向的内容，一旦一方修改，另一方也会受到影响。
- 参数传递不同：虽然函数的参数都是按值传递的，但是引用值传递的值是一个内存地址，实参和形参指向的是同一个对象，所以函数内部对这个参数的修改会体现在外部。原始值只是把变量里的值传递给参数，之后参数和这个变量互不影响。  

# JS函数

## 全局函数eval()有什么作用？

eval()只有一个参数，如果传入的参数不是字符串，它直接返回这个参数。如果参数是字符串，它会把字符串当成javascript代码进行编译。如果编译失败则抛出一个语法错误(syntaxError)异常。如果编译成功，则开始执行这段代码，并返回字符串中的最后一个表达式或语句的值，如果最后一个表达式或语句没有值，则最终返回undefined。如果字符串抛出一个异常，这个异常将把该调用传递给eval()。



## 9.解释下为什么接下来这段代码不是IIFE(立即调用的函数表达式):

```
function foo(){
    //code
}() 

```

以function关键字开头的语句会被解析为函数声明，而函数声明是不允许直接运行的。 只有当解析器把这句话解析为函数表达式，才能够直接运行，怎么办呢？以运算符开头就可以了。

```
(function foo(){
    // code..
})() 

```

## 16.JS里arguments究竟是什么？

Javascrip中国每个函数都会有一个Arguments对象实例arguments，它引用着函数的实参，可以用数组下标的方式"[]"引用arguments的元素。arguments.length为函数实参个数，arguments.callee引用函数自身。

在函数代码中，使用特殊对象arguments，开发者无需明确指出参数名，通过使用下标就可以访问相应的参数。

```js
function test() {
        var s = "";
        for (var i = 0; i < arguments.length; i++) {
            alert(arguments[i]);
            s += arguments[i] + ",";
        }
        return s;
    }
    test("name", "age");//name,age  
```

arguments虽然有一些数组的性质，但其并非真正的数组，只是一个类数组对象。其并没有数组的很多方法，不能像真正的数组那样调用.jion(),.concat(),.pop()等方法。

### 17.什么是"use strict";?使用它的好处和坏处分别是什么？

在代码中出现表达式-"use strict"; 意味着代码按照严格模式解析，这种模式使得Javascript在更严格的条件下运行。

***好处：***

- 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
- 消除代码运行的一些不安全之处，保证代码运行的安全；
- 提高编译器效率，增加运行速度；
- 为未来新版本的Javascript做好铺垫。

***坏处：***

- 同样的代码，在"严格模式"中，可能会有不一样的运行结果；
- 一些在"正常模式"下可以运行的语句，在"严格模式"下将不能运行。 

 

# JS原型链

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

















# JS事件机制

### 1. 什么是事件代理/事件委托？

事件代理/事件委托是利用事件冒泡的特性，将本应该绑定在多个元素上的事件绑定在他们的祖先元素上，尤其在动态添加子元素的时候，可以非常方便的提高程序性能，减小内存空间。

### 2.什么是事件冒泡？什么是事件捕获？

冒泡型事件：事件按照从最特定的事件目标到最不特定的事件目标(document对象)的顺序触发。

捕获型事件：事件从最不精确的对象(document 对象)开始触发，然后到最精确(也可以在窗口级别捕获事件，不过必须由开发人员特别指定)。

支持W3C标准的浏览器在添加事件时用addEventListener(event,fn,useCapture)方法，基中第3个参数useCapture是一个Boolean值，用来设置事件是在事件捕获时执行，还是事件冒泡时执行。而不兼容W3C的浏览器(IE)用attachEvent()方法，此方法没有相关设置，不过IE的事件模型默认是在事件冒泡时执行的，也就是在useCapture等于false的时候执行，所以把在处理事件时把useCapture设置为false是比较安全，也实现兼容浏览器的效果。

### 3.如何阻止冒泡？

w3c的方法是e.stopPropagation()，IE则是使用e.cancelBubble = true。例如： `window.event? window.event.cancelBubble = true : e.stopPropagation();`

return false也可以阻止冒泡。

### 4.如何阻止默认事件？

w3c的方法是e.preventDefault()，IE则是使用e.returnValue = false，比如：

```
function stopDefault( e ) { 
    //阻止默认浏览器动作(W3C) 
    if ( e && e.preventDefault ) 
        e.preventDefault(); 
    //IE中阻止函数器默认动作的方式 
    else 
        window.event.returnValue = false; 
} 
```

return false也能阻止默认行为。

## 防抖和节流



![debounce_throttle.png](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eecace52a44?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



防抖

```
function debounce(fn, delay) {
  let timer = null;
  return function () {
    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, delay);
  }
} 
```

节流

```
function throttle(fn, cycle) {
  let start = Date.now();
  let now;
  let timer;
  return function () {
    now = Date.now();
    clearTimeout(timer);
    if (now - start >= cycle) {
      fn.apply(this, arguments);
      start = now;
    } else {
      timer = setTimeout(() => {
        fn.apply(this, arguments);
      }, cycle);
    }
  }
} 
```

### 定时器 

#### 1.如果用户持续点击一个按钮，如何只提交一次请求，且不影响后续使用？ 

**何为节流** 触发函数事件后，短时间间隔内无法连续调用，只有上一次函数执行后，过了规定的时间间隔，才能进行下一次的函数调用，一般用于http请求。

**解决原理** 对处理函数进行延时操作，若设定的延时到来之前，再次触发事件，则清除上一次的延时操作定时器，重新定时。

```js
function conso(){
    console.log('is run');
}
var btnUse=true;
$("#btn").click(function(){
    if(btnUse){
        conso();
        btnUse=false;
    }
    setTimeout(function(){
        btnUse=true;
    },1500) //点击后相隔多长时间可执行
}) 
```

#### 2.如何防抖？ 

**何为防抖** 多次触发事件后，事件处理函数只执行一次，并且是在触发操作结束时执行，一般用于scroll事件。

**解决原理** 对处理函数进行延时操作，若设定的延时到来之前再次触发事件，则清除上一次的延时操作定时器，重新定时。

```js
let timer;
window.onscroll  = function () {
    if(timer){
        clearTimeout(timer)
    }
    timer = setTimeout(function () {
        //滚动条位置
        let scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
        console.log('滚动条位置：' + scrollTop);
        timer = undefined;
    },200)
} 
```

或者是这样：

```js
function debounce(fn, wait) {
    var timeout = null;
    return function() {  
        if(timeout !== null)   clearTimeout(timeout);        
        timeout = setTimeout(fn, wait);    
    }
}
// 处理函数
function handle() {    
    console.log(Math.random()); 
}
// 滚动事件
window.addEventListener('scroll', debounce(handle, 1000)); 
```

### 2.猜猜如下题目的结果？

```
function Timer() {
  this.s1 = 0;
  this.s2 = 0;
  setInterval(() => this.s1++, 1000);
  setInterval(function () {
    this.s2++;
  }, 1000);
}

var timer = new Timer();
setTimeout(() => console.log('s1: ', timer.s1), 3100);
setTimeout(() => console.log('s2: ', timer.s2), 3100); 
```

答案是：

```
s1: 3
s2: 0
```

### async、await 优缺点

`async 和 await` 相比直接使用 `Promise` 来说，优势在于处理 `then` 的调用链，能够更清晰准确的写出代码。缺点在于滥用 `await` 可能会导致性能问题，因为 `await` 会阻塞代码，也许之后的异步代码并不依赖于前者，但仍然需要等待前者完成，导致代码失去了并发性。

下面来看一个使用 `await` 的代码。

```
var a = 0
var b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
  a = (await 10) + a
  console.log('3', a) // -> '3' 20
}
b()
a++
console.log('1', a) // -> '1' 1
复制代码
```

对于以上代码你可能会有疑惑，这里说明下原理

- 首先函数 `b` 先执行，在执行到 `await 10` 之前变量 `a` 还是 0，因为在 `await` 内部实现了 `generators` ，`generators` 会保留堆栈中东西，所以这时候 `a = 0` 被保存了下来
- 因为 `await` 是异步操作，遇到`await`就会立即返回一个`pending`状态的`Promise`对象，暂时返回执行代码的控制权，使得函数外的代码得以继续执行，所以会先执行 `console.log('1', a)`
- 这时候同步代码执行完毕，开始执行异步代码，将保存下来的值拿出来使用，这时候 `a = 10`
- 然后后面就是常规执行代码了

### setTimeout 倒计时误差

JS 是单线程的，所以 `setTimeout` 的误差其实是无法被完全解决的，原因有很多，可能是回调中的，有可能是浏览器中的各种事件导致。这也是为什么页面开久了，定时器会不准的原因，当然我们可以通过一定的办法去减少这个误差。

以下是一个相对准备的倒计时实现

```
var period = 60 * 1000 * 60 * 2
var startTime = new Date().getTime();
var count = 0
var end = new Date().getTime() + period
var interval = 1000
var currentInterval = interval

function loop() {
  count++
  var offset = new Date().getTime() - (startTime + count * interval); // 代码执行所消耗的时间
  var diff = end - new Date().getTime()
  var h = Math.floor(diff / (60 * 1000 * 60))
  var hdiff = diff % (60 * 1000 * 60)
  var m = Math.floor(hdiff / (60 * 1000))
  var mdiff = hdiff % (60 * 1000)
  var s = mdiff / (1000)
  var sCeil = Math.ceil(s)
  var sFloor = Math.floor(s)
  currentInterval = interval - offset // 得到下一次循环所消耗的时间
  console.log('时：'+h, '分：'+m, '毫秒：'+s, '秒向上取整：'+sCeil, '代码执行时间：'+offset, '下次循环间隔'+currentInterval) // 打印 时 分 秒 代码执行时间 下次循环间隔

  setTimeout(loop, currentInterval)
}

setTimeout(loop, currentInterval)
```

 

### generator 原理

Generator 是 ES6 中新增的语法，和 Promise 一样，都可以用来异步编程

```
// 使用 * 表示这是一个 Generator 函数
// 内部可以通过 yield 暂停代码
// 通过调用 next 恢复执行
function* test() {
  let a = 1 + 2;
  yield 2;
  yield 3;
}
let b = test();
console.log(b.next()); // >  { value: 2, done: false }
console.log(b.next()); // >  { value: 3, done: false }
console.log(b.next()); // >  { value: undefined, done: true }
复制代码
```

从以上代码可以发现，加上 `*` 的函数执行后拥有了 `next` 函数，也就是说函数执行后返回了一个对象。每次调用 `next` 函数可以继续执行被暂停的代码。以下是 Generator 函数的简单实现

```
// cb 也就是编译过的 test 函数
function generator(cb) {
  return (function() {
    var object = {
      next: 0,
      stop: function() {}
    };

    return {
      next: function() {
        var ret = cb(object);
        if (ret === undefined) return { value: undefined, done: true };
        return {
          value: ret,
          done: false
        };
      }
    };
  })();
}
// 如果你使用 babel 编译后可以发现 test 函数变成了这样
function test() {
  var a;
  return generator(function(_context) {
    while (1) {
      switch ((_context.prev = _context.next)) {
        // 可以发现通过 yield 将代码分割成几块
        // 每次执行 next 函数就执行一块代码
        // 并且表明下次需要执行哪块代码
        case 0:
          a = 1 + 2;
          _context.next = 4;
          return 2;
        case 4:
          _context.next = 6;
          return 3;
		// 执行完毕
        case 6:
        case "end":
          return _context.stop();
      }
    }
  });
} 
```

### Promise

Promise 是 ES6 新增的语法，解决了回调地狱的问题。

可以把 Promise 看成一个状态机。初始是 `pending` 状态，可以通过函数 `resolve` 和 `reject` ，将状态转变为 `resolved` 或者 `rejected` 状态，状态一旦改变就不能再次变化。

`then` 函数会返回一个 Promise 实例，并且该返回值是一个新的实例而不是之前的实例。因为 Promise 规范规定除了 `pending` 状态，其他状态是不可以改变的，如果返回的是一个相同实例的话，多个 `then` 调用就失去意义了。

对于 `then` 来说，本质上可以把它看成是 `flatMap`

### 如何实现一个 Promise

```
// 三种状态
const PENDING = "pending";
const RESOLVED = "resolved";
const REJECTED = "rejected";
// promise 接收一个函数参数，该函数会立即执行
function MyPromise(fn) {
  let _this = this;
  _this.currentState = PENDING;
  _this.value = undefined;
  // 用于保存 then 中的回调，只有当 promise
  // 状态为 pending 时才会缓存，并且每个实例至多缓存一个
  _this.resolvedCallbacks = [];
  _this.rejectedCallbacks = [];

  _this.resolve = function (value) {
    if (value instanceof MyPromise) {
      // 如果 value 是个 Promise，递归执行
      return value.then(_this.resolve, _this.reject)
    }
    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = RESOLVED;
        _this.value = value;
        _this.resolvedCallbacks.forEach(cb => cb());
      }
    })
  };

  _this.reject = function (reason) {
    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = REJECTED;
        _this.value = reason;
        _this.rejectedCallbacks.forEach(cb => cb());
      }
    })
  }
  // 用于解决以下问题
  // new Promise(() => throw Error('error))
  try {
    fn(_this.resolve, _this.reject);
  } catch (e) {
    _this.reject(e);
  }
}

MyPromise.prototype.then = function (onResolved, onRejected) {
  var self = this;
  // 规范 2.2.7，then 必须返回一个新的 promise
  var promise2;
  // 规范 2.2.onResolved 和 onRejected 都为可选参数
  // 如果类型不是函数需要忽略，同时也实现了透传
  // Promise.resolve(4).then().then((value) => console.log(value))
  onResolved = typeof onResolved === 'function' ? onResolved : v => v;
  onRejected = typeof onRejected === 'function' ? onRejected : r => throw r;

  if (self.currentState === RESOLVED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      // 规范 2.2.4，保证 onFulfilled，onRjected 异步执行
      // 所以用了 setTimeout 包裹下
      setTimeout(function () {
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }

  if (self.currentState === REJECTED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      setTimeout(function () {
        // 异步执行onRejected
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }

  if (self.currentState === PENDING) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      self.resolvedCallbacks.push(function () {
        // 考虑到可能会有报错，所以使用 try/catch 包裹
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });

      self.rejectedCallbacks.push(function () {
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });
    }));
  }
};
// 规范 2.3
function resolutionProcedure(promise2, x, resolve, reject) {
  // 规范 2.3.1，x 不能和 promise2 相同，避免循环引用
  if (promise2 === x) {
    return reject(new TypeError("Error"));
  }
  // 规范 2.3.2
  // 如果 x 为 Promise，状态为 pending 需要继续等待否则执行
  if (x instanceof MyPromise) {
    if (x.currentState === PENDING) {
      x.then(function (value) {
        // 再次调用该函数是为了确认 x resolve 的
        // 参数是什么类型，如果是基本类型就再次 resolve
        // 把值传给下个 then
        resolutionProcedure(promise2, value, resolve, reject);
      }, reject);
    } else {
      x.then(resolve, reject);
    }
    return;
  }
  // 规范 2.3.3.3.3
  // reject 或者 resolve 其中一个执行过得话，忽略其他的
  let called = false;
  // 规范 2.3.3，判断 x 是否为对象或者函数
  if (x !== null && (typeof x === "object" || typeof x === "function")) {
    // 规范 2.3.3.2，如果不能取出 then，就 reject
    try {
      // 规范 2.3.3.1
      let then = x.then;
      // 如果 then 是函数，调用 x.then
      if (typeof then === "function") {
        // 规范 2.3.3.3
        then.call(
          x,
          y => {
            if (called) return;
            called = true;
            // 规范 2.3.3.3.1
            resolutionProcedure(promise2, y, resolve, reject);
          },
          e => {
            if (called) return;
            called = true;
            reject(e);
          }
        );
      } else {
        // 规范 2.3.3.4
        resolve(x);
      }
    } catch (e) {
      if (called) return;
      called = true;
      reject(e);
    }
  } else {
    // 规范 2.3.4，x 为基本类型
    resolve(x);
  }
}
```

### 垃圾回收

V8 实现了准确式 GC，GC 算法采用了分代式垃圾回收机制。因此，V8 将内存（堆）分为新生代和老生代两部分。

#### 新生代算法

新生代中的对象一般存活时间较短，使用 Scavenge GC 算法。

在新生代空间中，内存空间分为两部分，分别为 From 空间和 To 空间。在这两个空间中，必定有一个空间是使用的，另一个空间是空闲的。新分配的对象会被放入 From 空间中，当 From 空间被占满时，新生代 GC 就会启动了。算法会检查 From 空间中存活的对象并复制到 To 空间中，如果有失活的对象就会销毁。当复制完成后将 From 空间和 To 空间互换，这样 GC 就结束了。

#### 老生代算法

老生代中的对象一般存活时间较长且数量也多，使用了两个算法，分别是标记清除算法和标记压缩算法。

在讲算法前，先来说下什么情况下对象会出现在老生代空间中：

- 新生代中的对象是否已经经历过一次 Scavenge 算法，如果经历过的话，会将对象从新生代空间移到老生代空间中。
- To 空间的对象占比大小超过 25 %。在这种情况下，为了不影响到内存分配，会将对象从新生代空间移到老生代空间中。

老生代中的空间很复杂，有如下几个空间

```
enum AllocationSpace {
  // TODO(v8:7464): Actually map this space's memory as read-only.
  RO_SPACE,    // 不变的对象空间
  NEW_SPACE,   // 新生代用于 GC 复制算法的空间
  OLD_SPACE,   // 老生代常驻对象空间
  CODE_SPACE,  // 老生代代码对象空间
  MAP_SPACE,   // 老生代 map 对象
  LO_SPACE,    // 老生代大空间对象
  NEW_LO_SPACE,  // 新生代大空间对象

  FIRST_SPACE = RO_SPACE,
  LAST_SPACE = NEW_LO_SPACE,
  FIRST_GROWABLE_PAGED_SPACE = OLD_SPACE,
  LAST_GROWABLE_PAGED_SPACE = MAP_SPACE
}; 
```

在老生代中，以下情况会先启动标记清除算法：

- 某一个空间没有分块的时候
- 空间中被对象超过一定限制
- 空间不能保证新生代中的对象移动到老生代中

在这个阶段中，会遍历堆中所有的对象，然后标记活的对象，在标记完成后，销毁所有没有被标记的对象。在标记大型对内存时，可能需要几百毫秒才能完成一次标记。这就会导致一些性能上的问题。为了解决这个问题，2011 年，V8 从 stop-the-world 标记切换到增量标志。在增量标记期间，GC 将标记工作分解为更小的模块，可以让 JS 应用逻辑在模块间隙执行一会，从而不至于让应用出现停顿情况。但在 2018 年，GC 技术又有了一个重大突破，这项技术名为并发标记。该技术可以让 GC 扫描和标记对象时，同时允许 JS 运行，你可以点击 [该博客](https://v8project.blogspot.com/2018/06/concurrent-marking.html) 详细阅读。

清除对象后会造成堆内存出现碎片的情况，当碎片超过一定限制后会启动压缩算法。在压缩过程中，将活的对象像一端移动，直到所有对象都移动完成然后清理掉不需要的内存。 

### 浏览器 Eventloop 和 Node 中的有什么区别

众所周知 JS 是门非阻塞单线程语言，因为在最初 JS 就是为了和浏览器交互而诞生的。如果 JS 是门多线程的语言话，我们在多个线程中处理 DOM 就可能会发生问题（一个线程中新加节点，另一个线程中删除节点），当然可以引入读写锁解决这个问题。

JS 在执行的过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 Task（有多种 task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说 JS 中的异步还是同步行为。

```
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

console.log('script end');
复制代码
```

以上代码虽然 `setTimeout` 延时为 0，其实还是异步。这是因为 HTML5 标准规定这个函数第二个参数不得小于 4 毫秒，不足会自动增加。所以 `setTimeout` 还是会在 `script end` 之后打印。

不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（microtask） 和 宏任务（macrotask）。在 ES6 规范中，microtask 称为 `jobs`，macrotask 称为 `task`。

```
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

new Promise((resolve) => {
    console.log('Promise')
    resolve()
}).then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
// script start => Promise => script end => promise1 => promise2 => setTimeout
复制代码
```

以上代码虽然 `setTimeout` 写在 `Promise` 之前，但是因为 `Promise` 属于微任务而 `setTimeout` 属于宏任务，所以会有以上的打印。

微任务包括 `process.nextTick` ，`promise` ，`Object.observe` ，`MutationObserver`

宏任务包括 `script` ， `setTimeout` ，`setInterval` ，`setImmediate` ，`I/O` ，`UI rendering`

很多人有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了 `script` ，浏览器会先执行一个宏任务，接下来有异步代码的话就先执行微任务。

所以正确的一次 Event loop 顺序是这样的

1. 执行同步代码，这属于宏任务
2. 执行栈为空，查询是否有微任务需要执行
3. 执行所有微任务
4. 必要的话渲染 UI
5. 然后开始下一轮 Event loop，执行宏任务中的异步代码

通过上述的  Event loop 顺序可知，如果宏任务中的异步代码有大量的计算并且需要操作 DOM 的话，为了更快的 界面响应，我们可以把操作 DOM 放入微任务中。

#### Node 中的 Event loop

Node 中的 Event loop 和浏览器中的不相同。

Node 的 Event loop 分为6个阶段，它们会按照顺序反复运行

```
┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<──connections───     │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
复制代码
```

**timer**

timers 阶段会执行 `setTimeout` 和 `setInterval`

一个 `timer` 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟。

下限的时间有一个范围：`[1, 2147483647]` ，如果设定的时间不在这个范围，将被设置为1。

**I/O **

I/O 阶段会执行除了 close 事件，定时器和 `setImmediate` 的回调

**idle, prepare**

idle, prepare 阶段内部实现

**poll**

poll 阶段很重要，这一阶段中，系统会做两件事情

1. 执行到点的定时器
2. 执行 poll 队列中的事件

并且当 poll 中没有定时器的情况下，会发现以下两件事情

- 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
- 如果 poll 队列为空，会有两件事发生 
  - 如果有 `setImmediate` 需要执行，poll 阶段会停止并且进入到 check 阶段执行 `setImmediate`
  - 如果没有 `setImmediate` 需要执行，会等待回调被加入到队列中并立即执行回调

如果有别的定时器需要被执行，会回到 timer 阶段执行回调。

**check**

check 阶段执行 `setImmediate`

**close callbacks**

close callbacks 阶段执行 close 事件

并且在 Node 中，有些情况下的定时器执行顺序是随机的

```
setTimeout(() => {
    console.log('setTimeout');
}, 0);
setImmediate(() => {
    console.log('setImmediate');
})
// 这里可能会输出 setTimeout，setImmediate
// 可能也会相反的输出，这取决于性能
// 因为可能进入 event loop 用了不到 1 毫秒，这时候会执行 setImmediate
// 否则会执行 setTimeout
复制代码
```

当然在这种情况下，执行顺序是相同的

```
var fs = require('fs')

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0);
    setImmediate(() => {
        console.log('immediate');
    });
});
// 因为 readFile 的回调在 poll 中执行
// 发现有 setImmediate ，所以会立即跳到 check 阶段执行回调
// 再去 timer 阶段执行 setTimeout
// 所以以上输出一定是 setImmediate，setTimeout
复制代码
```

上面介绍的都是 macrotask 的执行情况，microtask 会在以上每个阶段完成后立即执行。

```
setTimeout(()=>{
    console.log('timer1')

    Promise.resolve().then(function() {
        console.log('promise1')
    })
}, 0)

setTimeout(()=>{
    console.log('timer2')

    Promise.resolve().then(function() {
        console.log('promise2')
    })
}, 0)

// 以上代码在浏览器和 node 中打印情况是不同的
// 浏览器中一定打印 timer1, promise1, timer2, promise2
// node 中可能打印 timer1, timer2, promise1, promise2
// 也可能打印 timer1, promise1, timer2, promise2
复制代码
```

Node 中的 `process.nextTick` 会先于其他 microtask 执行。

```
setTimeout(() => {
  console.log("timer1");

  Promise.resolve().then(function() {
    console.log("promise1");
  });
}, 0);

process.nextTick(() => {
  console.log("nextTick");
});
// nextTick, timer1, promise1
```

 























# JS通信

### 10.请尽可能详尽的解释AJAX的工作原理

ajax简单来说是通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。

#### ajax的优点

- 最大的一点是页面无刷新，在页面内与服务器通信，给用户的体验非常好。
- 使用异步方式与服务器通信，不需要打断用户的操作，具有更加迅速的响应能力。
- 可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本，ajax的原则是“按需取数据”，可以最大程度的减少冗余请求。
- 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序。

#### ajax的缺点

- ajax对浏览器后退机制造成了破坏，也就是说用户无法通过浏览器的后退按钮回到前一次操作的页面。虽然有些浏览器解决了这个问题，比如Gmail，但它也并不能改变ajax的机制，它所带来的开发成本是非常高的，和ajax框架所要求的快速开发是相背离的。这是ajax所带来的一个非常严重的问题。
- 安全问题。技术同时也对IT企业带来了新的安全威胁，ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。
- 对搜索引擎的支持比较弱。
- 破坏了程序的异常机制。至少从目前看来，像ajax.dll，ajaxpro.dll这些ajax框架是会破坏程序的异常机制的。关于这个问题，我曾经在开发过程中遇到过，但是查了一下网上几乎没有相关的介绍。后来我自己做了一次试验，分别采用ajax和传统的form提交的模式来删除一条数据……给我们的调试带来了很大的困难。
- 另外，像其他方面的一些问题，比如说违背了url和资源定位的初衷。例如，我给你一个url地址，如果采用了ajax技术，也许你在该url地址下面看到的和我在这个url地址下看到的内容是不同的。这个和资源定位的初衷是相背离的。
- 一些手持设备（如手机、PDA等）现在还不能很好的支持ajax，比如说我们在手机的浏览器上打开采用ajax技术的网站时，它目前是不支持的，当然，这个问题和我们没太多关系。

### es6 promise ajax

```js
定义
const myHttpClient = url => {
  return new Promise((resolve, reject) => {
    let client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();
    function handler() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    }
  });
};
使用
myHttpClient('https://www.baidu.com').then(res => {
  console.log(res);
}).catch(error => {
  console.log(error);
});
```






### 13.请指出document.onload和document.ready两个事件的区别

页面加载完成有两种事件，一是ready，表示文档结构已经加载完成（不包含图片等非文字媒体文件），二是onload，指示页面包含图片等文件在内的所有元素都加载完成。

### 14.如何从浏览器的URL中获取查询字符串参数？

```
getUrlParam : function(name){
        //baidu.com/product/list?keyword=XXX&page=1
        var reg     = new RegExp('(^|&)' + name + '=([^&]*)(&|$)');
        var result  = window.location.search.substr(1).match(reg);
        return result ? decodeURIComponent(result[2]) : null;
 } 
```



![img](https://user-gold-cdn.xitu.io/2019/3/22/169a36d0cef8612b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



- 首先设置一个函数，给这个函数传递一个参数，也就是url的search部分的key值；
- 设置一个正则表达式，以&开头或没有，中间是参数，后面以#或&结尾或没有；
- 通过window.location.search.substr(1).match()匹配,返回一个数组
- 如果数组不为空，返回数组的第3个值，也就是正则表达式的第二个子串

### 18.请解释一下JavaScript的同源策略

同源策略，即拥有相同的协议（protocol），端口（如果指定），主机（域名）的两个页面是属于同一个源。 然而在IE中比较特殊，IE中没有将端口号加入同源的条件中，因此上图中端口不同那一项，在IE中是算同源的。 ` <img> <iframe>`中的src，href都可以任意链接网络资源，是不遵循通源策略的。

### 19.请解释JSONP的工作原理，以及它为什么不是真正的AJAX。

JSONP (JSON with Padding)是一个简单高效的跨域方式，HTML中的script标签可以加载并执行其他域的javascript，于是我们可以通过script标记来动态加载其他域的资源。例如我要从域A的页面pageA加载域B的数据，那么在域B的页面pageB中我以JavaScript的形式声明pageA需要的数据，然后在 pageA中用script标签把pageB加载进来，那么pageB中的脚本就会得以执行。JSONP在此基础上加入了回调函数，pageB加载完之后会执行pageA中定义的函数，所需要的数据会以参数的形式传递给该函数。JSONP易于实现，但是也会存在一些安全隐患，如果第三方的脚本随意地执行，那么它就可以篡改页面内容，截获敏感数据。但是在受信任的双方传递数据，JSONP是非常合适的选择。

AJAX是不跨域的，而JSONP是一个是跨域的，还有就是二者接收参数形式不一样！

### 22.什么是跨域？有什么方法解决跨域带来的问题？

##### 跨域、JSONP 、CORS、postMessage

跨域概念解释：当前发起请求的域与该请求指向的资源所在的域不一样。这里的域指的是这样的一个概念：我们认为若协议 + 域名 + 端口号均相同，那么就是同域。 如下表 

![图](https://user-gold-cdn.xitu.io/2018/9/9/165bd6dede8a98d1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



jsonp实现

```
原生
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';
 
    // 传参并指定回调执行函数为onBack
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=onBack';
    document.head.appendChild(script);
 
    // 回调执行函数
    function onBack(res) {
        alert(JSON.stringify(res));
    }
 </script>
 
jquery
$.ajax({
    url: 'http://www.domain2.com:8080/login',
    type: 'get',
    dataType: 'jsonp',  // 请求方式为jsonp
    jsonpCallback: "onBack",    // 自定义回调函数名
    data: {}
});

vue
this.$http.jsonp('http://www.domain2.com:8080/login', {
    params: {},
    jsonp: 'onBack'
}).then((res) => {
    console.log(res); 
})

配合的后端node实现,其他服务器语言也可以
const querystring = require('querystring');
const http = require('http');
const server = http.createServer();
server.on('request', function(req, res) {
    var params = qs.parse(req.url.split('?')[1]);
    var fn = params.callback;
 
    // jsonp返回设置
    res.writeHead(200, { 'Content-Type': 'text/javascript' });
    res.write(fn + '(' + JSON.stringify(params) + ')');
 
    res.end();
});
server.listen('8080');

jsoup缺点只能实现get请求 
```

CORS：跨源资源共享 Cross-Origin Resource Sharing(CORS)，通常服务器设置，若带cookie请求，则前后端都需要设置 后端常见设置 response.setHeader("Access-Control-Allow-Origin", "[www.domain1.com](http://www.domain1.com)");  // 若有端口需写全（协议+域名+端口），允许那些外源请求 response.setHeader("Access-Control-Allow-Credentials", "true"); //是否需要验证

前端示例

```js
原生 
var xhr = new XMLHttpRequest(); // IE8/9需用window.XDomainRequest兼容
// 前端设置是否带cookie
xhr.withCredentials = true;
xhr.open('post', 'http://www.domain2.com:8080/login', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('user=admin');
 
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        alert(xhr.responseText);
    }

jquery
$.ajax({
    ...
   xhrFields: {
       withCredentials: true    // 前端设置是否带cookie
   },
   crossDomain: true,   // 会让请求头中包含跨域的额外信息，但不会含cookie
    ...
});


postMessage(data,origin)方法接受两个参数
demo

a.html
<iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none;"></iframe>
<script>       
    var iframe = document.getElementById('iframe');
    iframe.onload = function() {
        var data = {
            name: 'aym'
        };
        // 向domain2传送跨域数据
        iframe.contentWindow.postMessage(JSON.stringify(data), 'http://www.domain2.com');
    };
 
    // 接受domain2返回数据
    window.addEventListener('message', function(e) {
        alert('data from domain2 ---> ' + e.data);
    }, false);
</script>

b.html  与a.html不同源

<script>
    // 接收domain1的数据
    window.addEventListener('message', function(e) {
        alert('data from domain1 ---> ' + e.data);
 
        var data = JSON.parse(e.data);
        if (data) {
            data.number = 16;
 
            // 处理后再发回domain1
            window.parent.postMessage(JSON.stringify(data), 'http://www.domain1.com');
        }
    }, false);
</script>
```

 

























# JS算法

### 常见排序

以下两个函数是排序中会用到的通用函数，就不一一写了

```
function checkArray(array) {
    if (!array || array.length <= 2) return
}
function swap(array, left, right) {
    let rightValue = array[right]
    array[right] = array[left]
    array[left] = rightValue
}
复制代码
```

#### 冒泡排序

冒泡排序的原理如下，从第一个元素开始，把当前元素和下一个索引元素进行比较。如果当前元素大，那么就交换位置，重复操作直到比较到最后一个元素，那么此时最后一个元素就是该数组中最大的数。下一轮重复以上操作，但是此时最后一个元素已经是最大数了，所以不需要再比较最后一个元素，只需要比较到 `length - 1` 的位置。

 ![img](https://user-gold-cdn.xitu.io/2018/4/12/162b895b452b306c?imageslim) 

以下是实现该算法的代码

```
function bubble(array) {
  checkArray(array);
  for (let i = array.length - 1; i > 0; i--) {
    // 从 0 到 `length - 1` 遍历
    for (let j = 0; j < i; j++) {
      if (array[j] > array[j + 1]) swap(array, j, j + 1)
    }
  }
  return array;
}
复制代码
```

该算法的操作次数是一个等差数列 `n + (n - 1) + (n - 2) + 1` ，去掉常数项以后得出时间复杂度是 O(n * n)

#### 插入排序

插入排序的原理如下。第一个元素默认是已排序元素，取出下一个元素和当前元素比较，如果当前元素大就交换位置。那么此时第一个元素就是当前的最小数，所以下次取出操作从第三个元素开始，向前对比，重复之前的操作。

![img](https://user-gold-cdn.xitu.io/2018/4/12/162b895c7e59dcd1?imageslim)

以下是实现该算法的代码

```
function insertion(array) {
  checkArray(array);
  for (let i = 1; i < array.length; i++) {
    for (let j = i - 1; j >= 0 && array[j] > array[j + 1]; j--)
      swap(array, j, j + 1);
  }
  return array;
}
复制代码
```

该算法的操作次数是一个等差数列 `n + (n - 1) + (n - 2) + 1` ，去掉常数项以后得出时间复杂度是 O(n * n)

#### 选择排序

选择排序的原理如下。遍历数组，设置最小值的索引为 0，如果取出的值比当前最小值小，就替换最小值索引，遍历完成后，将第一个元素和最小值索引上的值交换。如上操作后，第一个元素就是数组中的最小值，下次遍历就可以从索引 1 开始重复上述操作。

![img](https://user-gold-cdn.xitu.io/2018/4/13/162bc8ea14567e2e?imageslim)

以下是实现该算法的代码

```
function selection(array) {
  checkArray(array);
  for (let i = 0; i < array.length - 1; i++) {
    let minIndex = i;
    for (let j = i + 1; j < array.length; j++) {
      minIndex = array[j] < array[minIndex] ? j : minIndex;
    }
    swap(array, i, minIndex);
  }
  return array;
}
复制代码
```

该算法的操作次数是一个等差数列 `n + (n - 1) + (n - 2) + 1` ，去掉常数项以后得出时间复杂度是 O(n * n)

#### 归并排序

归并排序的原理如下。递归的将数组两两分开直到最多包含两个元素，然后将数组排序合并，最终合并为排序好的数组。假设我有一组数组 `[3, 1, 2, 8, 9, 7, 6]`，中间数索引是 3，先排序数组 `[3, 1, 2, 8]` 。在这个左边数组上，继续拆分直到变成数组包含两个元素（如果数组长度是奇数的话，会有一个拆分数组只包含一个元素）。然后排序数组 `[3, 1]` 和 `[2, 8]` ，然后再排序数组 `[1, 3, 2, 8]` ，这样左边数组就排序完成，然后按照以上思路排序右边数组，最后将数组 `[1, 2, 3, 8]` 和 `[6, 7, 9]` 排序。

![img](https://user-gold-cdn.xitu.io/2018/4/13/162be13c7e30bd86?imageslim)

以下是实现该算法的代码

```
function sort(array) {
  checkArray(array);
  mergeSort(array, 0, array.length - 1);
  return array;
}

function mergeSort(array, left, right) {
  // 左右索引相同说明已经只有一个数
  if (left === right) return;
  // 等同于 `left + (right - left) / 2`
  // 相比 `(left + right) / 2` 来说更加安全，不会溢出
  // 使用位运算是因为位运算比四则运算快
  let mid = parseInt(left + ((right - left) >> 1));
  mergeSort(array, left, mid);
  mergeSort(array, mid + 1, right);

  let help = [];
  let i = 0;
  let p1 = left;
  let p2 = mid + 1;
  while (p1 <= mid && p2 <= right) {
    help[i++] = array[p1] < array[p2] ? array[p1++] : array[p2++];
  }
  while (p1 <= mid) {
    help[i++] = array[p1++];
  }
  while (p2 <= right) {
    help[i++] = array[p2++];
  }
  for (let i = 0; i < help.length; i++) {
    array[left + i] = help[i];
  }
  return array;
}
复制代码
```

以上算法使用了递归的思想。递归的本质就是压栈，每递归执行一次函数，就将该函数的信息（比如参数，内部的变量，执行到的行数）压栈，直到遇到终止条件，然后出栈并继续执行函数。对于以上递归函数的调用轨迹如下

```
mergeSort(data, 0, 6) // mid = 3
  mergeSort(data, 0, 3) // mid = 1
    mergeSort(data, 0, 1) // mid = 0
      mergeSort(data, 0, 0) // 遇到终止，回退到上一步
    mergeSort(data, 1, 1) // 遇到终止，回退到上一步
    // 排序 p1 = 0, p2 = mid + 1 = 1
    // 回退到 `mergeSort(data, 0, 3)` 执行下一个递归
  mergeSort(2, 3) // mid = 2
    mergeSort(3, 3) // 遇到终止，回退到上一步
  // 排序 p1 = 2, p2 = mid + 1 = 3
  // 回退到 `mergeSort(data, 0, 3)` 执行合并逻辑
  // 排序 p1 = 0, p2 = mid + 1 = 2
  // 执行完毕回退
  // 左边数组排序完毕，右边也是如上轨迹
复制代码
```

该算法的操作次数是可以这样计算：递归了两次，每次数据量是数组的一半，并且最后把整个数组迭代了一次，所以得出表达式 `2T(N / 2) + T(N)` （T 代表时间，N 代表数据量）。根据该表达式可以套用 [该公式](https://www.wikiwand.com/zh-hans/主定理) 得出时间复杂度为 `O(N * logN)`

#### 快排

快排的原理如下。随机选取一个数组中的值作为基准值，从左至右取值与基准值对比大小。比基准值小的放数组左边，大的放右边，对比完成后将基准值和第一个比基准值大的值交换位置。然后将数组以基准值的位置分为两部分，继续递归以上操作。

![img](https://user-gold-cdn.xitu.io/2018/4/16/162cd23e69ca9ea3?imageslim)

以下是实现该算法的代码

```
function sort(array) {
  checkArray(array);
  quickSort(array, 0, array.length - 1);
  return array;
}

function quickSort(array, left, right) {
  if (left < right) {
    swap(array, , right)
    // 随机取值，然后和末尾交换，这样做比固定取一个位置的复杂度略低
    let indexs = part(array, parseInt(Math.random() * (right - left + 1)) + left, right);
    quickSort(array, left, indexs[0]);
    quickSort(array, indexs[1] + 1, right);
  }
}
function part(array, left, right) {
  let less = left - 1;
  let more = right;
  while (left < more) {
    if (array[left] < array[right]) {
      // 当前值比基准值小，`less` 和 `left` 都加一
	   ++less;
       ++left;
    } else if (array[left] > array[right]) {
      // 当前值比基准值大，将当前值和右边的值交换
      // 并且不改变 `left`，因为当前换过来的值还没有判断过大小
      swap(array, --more, left);
    } else {
      // 和基准值相同，只移动下标
      left++;
    }
  }
  // 将基准值和比基准值大的第一个值交换位置
  // 这样数组就变成 `[比基准值小, 基准值, 比基准值大]`
  swap(array, right, more);
  return [less, more];
}
复制代码
```

该算法的复杂度和归并排序是相同的，但是额外空间复杂度比归并排序少，只需 O(logN)，并且相比归并排序来说，所需的常数时间也更少。

##### 面试题

**Sort Colors**：该题目来自 [LeetCode](https://leetcode.com/problems/sort-colors/description/)，题目需要我们将 `[2,0,2,1,1,0]` 排序成 `[0,0,1,1,2,2]` ，这个问题就可以使用三路快排的思想。

以下是代码实现

```
var sortColors = function(nums) {
  let left = -1;
  let right = nums.length;
  let i = 0;
  // 下标如果遇到 right，说明已经排序完成
  while (i < right) {
    if (nums[i] == 0) {
      swap(nums, i++, ++left);
    } else if (nums[i] == 1) {
      i++;
    } else {
      swap(nums, i, --right);
    }
  }
};
复制代码
```

**Kth Largest Element in an Array**：该题目来自 [LeetCode](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)，题目需要找出数组中第 K 大的元素，这问题也可以使用快排的思路。并且因为是找出第 K 大元素，所以在分离数组的过程中，可以找出需要的元素在哪边，然后只需要排序相应的一边数组就好。

以下是代码实现

```
var findKthLargest = function(nums, k) {
  let l = 0
  let r = nums.length - 1
  // 得出第 K 大元素的索引位置
  k = nums.length - k
  while (l < r) {
    // 分离数组后获得比基准树大的第一个元素索引
    let index = part(nums, l, r)
    // 判断该索引和 k 的大小
    if (index < k) {
      l = index + 1
    } else if (index > k) {
      r = index - 1
    } else {
      break
    }
  }
  return nums[k]
};
function part(array, left, right) {
  let less = left - 1;
  let more = right;
  while (left < more) {
    if (array[left] < array[right]) {
	   ++less;
       ++left;
    } else if (array[left] > array[right]) {
      swap(array, --more, left);
    } else {
      left++;
    }
  }
  swap(array, right, more);
  return more;
}
复制代码
```

#### 堆排序

堆排序利用了二叉堆的特性来做，二叉堆通常用数组表示，并且二叉堆是一颗完全二叉树（所有叶节点（最底层的节点）都是从左往右顺序排序，并且其他层的节点都是满的）。二叉堆又分为大根堆与小根堆。

- 大根堆是某个节点的所有子节点的值都比他小
- 小根堆是某个节点的所有子节点的值都比他大

堆排序的原理就是组成一个大根堆或者小根堆。以小根堆为例，某个节点的左边子节点索引是 `i * 2 + 1`，右边是 `i * 2 + 2`，父节点是 `(i - 1) /2`。

1. 首先遍历数组，判断该节点的父节点是否比他小，如果小就交换位置并继续判断，直到他的父节点比他大
2. 重新以上操作 1，直到数组首位是最大值
3. 然后将首位和末尾交换位置并将数组长度减一，表示数组末尾已是最大值，不需要再比较大小
4. 对比左右节点哪个大，然后记住大的节点的索引并且和父节点对比大小，如果子节点大就交换位置
5. 重复以上操作 3 - 4 直到整个数组都是大根堆。

![img](https://user-gold-cdn.xitu.io/2018/4/17/162d2a9ff258dfe1?imageslim)

以下是实现该算法的代码

```
function heap(array) {
  checkArray(array);
  // 将最大值交换到首位
  for (let i = 0; i < array.length; i++) {
    heapInsert(array, i);
  }
  let size = array.length;
  // 交换首位和末尾
  swap(array, 0, --size);
  while (size > 0) {
    heapify(array, 0, size);
    swap(array, 0, --size);
  }
  return array;
}

function heapInsert(array, index) {
  // 如果当前节点比父节点大，就交换
  while (array[index] > array[parseInt((index - 1) / 2)]) {
    swap(array, index, parseInt((index - 1) / 2));
    // 将索引变成父节点
    index = parseInt((index - 1) / 2);
  }
}
function heapify(array, index, size) {
  let left = index * 2 + 1;
  while (left < size) {
    // 判断左右节点大小
    let largest =
      left + 1 < size && array[left] < array[left + 1] ? left + 1 : left;
    // 判断子节点和父节点大小
    largest = array[index] < array[largest] ? largest : index;
    if (largest === index) break;
    swap(array, index, largest);
    index = largest;
    left = index * 2 + 1;
  }
}
复制代码
```

以上代码实现了小根堆，如果需要实现大根堆，只需要把节点对比反一下就好。

该算法的复杂度是 O(logN)

#### 系统自带排序实现

每个语言的排序内部实现都是不同的。

对于 JS 来说，数组长度大于 10 会采用快排，否则使用插入排序 [源码实现](https://github.com/v8/v8/blob/ad82a40509c5b5b4680d4299c8f08d6c6d31af3c/src/js/array.js#L760:7) 。选择插入排序是因为虽然时间复杂度很差，但是在数据量很小的情况下和 `O(N * logN)`相差无几，然而插入排序需要的常数时间很小，所以相对别的排序来说更快。

对于 Java 来说，还会考虑内部的元素的类型。对于存储对象的数组来说，会采用稳定性好的算法。稳定性的意思就是对于相同值来说，相对顺序不能改变。

![img](https://user-gold-cdn.xitu.io/2018/4/18/162d7df247dcda00?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


 算法相关

##### 各种排序实现

相关数据 

![表格](https://user-gold-cdn.xitu.io/2018/9/9/165bd6dedf755d33?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



```
// 冒泡排序: 比较两个相邻的项，如果第一个大于第二个则交换他们的位置,元素项向上移动至正确的顺序，就好像气泡往上冒一样
冒泡demo:
function bubbleSort(arr) {
    let len = arr.length;
    for (let i = 0; i < len; i++) {
        for (let j = 0; j < len - 1 - i; j++) {
            if (arr[j] > arr[j+1]) {        //相邻元素两两对比
                [arr[j + 1], arr[j]] = [arr[j], arr[j + 1]];
            }
        }
    }
    return arr;
}
// 1) 首先，在数组中选择一个中间项作为主元
// 2) 创建两个指针，左边的指向数组第一个项，右边的指向最后一个项，移动左指针，直到找到一个比主元大的项，接着，移动右边的指针，直到找到一个比主元小的项，然后交换它们。重复这个过程，直到
// 左侧的指针超过了右侧的指针。这个使比主元小的都在左侧，比主元大的都在右侧。这一步叫划分操作
// 3) 接着，算法对划分后的小数组（较主元小的值组成的的小数组， 以及较主元大的值组成的小数组）重复之前的两个步骤，直到排序完成
快排demo:
function quickSort(arr, left, right) {
    let len = arr.length;
    let partitionIndex;
    left = typeof left !== 'number' ? 0 : left;
    right = typeof right !== 'number' ? len - 1 : right;
    if (left < right) {
        partitionIndex = partition(arr, left, right);
        quickSort(arr, left, partitionIndex - 1);
        quickSort(arr, partitionIndex + 1, right);
    }
    return arr;
}

function partition(arr, left, right) {     //分区操作
    let pivot = left;                      //设定基准值（pivot）
    let index = pivot + 1;
    for (let i = index; i <= right; i++) {
        if (arr[i] < arr[pivot]) {
            [arr[i], arr[index]] = [arr[index], arr[i]];
            index++;
        }
    }
    [arr[pivot], arr[index - 1]] = [arr[index - 1], arr[pivot]];
    return index - 1;
}
// 选择排序：大概思路是找到最小的放在第一位，找到第二小的放在第二位，以此类推 算法复杂度O(n^2)
选择demo:
function selectionSort(arr) {
	let len = arr.length;
	let minIndex;
	for (let i = 0; i < len - 1; i++) {
		minIndex = i;
		for (let j = i + 1; j < len; j++) {
			if (arr[j] < arr[minIndex]) {     //寻找最小的数
			    minIndex = j;                 //将最小数的索引保存
		    }
		}
		[arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
	}
return arr;
}
// 插入排序：每次排一个数组项，假设数组的第一项已经排序，接着，把第二项与第一项进行对比，第二项是该插入到第一项之前还是之后，第三项是该插入到第一项之前还是第一项之后还是第三项
插入demo:
function insertionSort(arr) {
	let len = arr.length;
	let preIndex, current;
	for (let i = 1; i < len; i++) {
	    preIndex = i - 1;
	    current = arr[i];
	    while (preIndex >= 0 && arr[preIndex] > current) {
		    arr[preIndex + 1] = arr[preIndex];
		    preIndex--;
	    }
	    arr[preIndex + 1] = current;
	}
	return arr;
}
// 归并排序：Mozilla Firefox 使用归并排序作为Array.prototype.sort的实现，而chrome使用快速排序的一个变体实现的,前面三种算法性能不好，但归并排序性能不错 算法复杂度O(nlog^n)
// 归并排序是一种分治算法。本质上就是把一个原始数组切分成较小的数组，直到每个小数组只有一个位置，接着把小数组归并成较大的数组，在归并过程中也会完成排序，直到最后只有一个排序完毕的大数组
归并demo:
function mergeSort(arr) {  //采用自上而下的递归方法
    let len = arr.length;
    if(len < 2) {
        return arr;
    }
    let middle = Math.floor(len / 2),
    left = arr.slice(0, middle),
    right = arr.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right){
    let result = [];
    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }
    result.push(...left);
    result.push(...right);
    return result;
}
//堆排序：堆排序把数组当中二叉树来排序而得名。
// 1）索引0是树的根节点；2）除根节点为，任意节点N的父节点是N/2；3）节点L的左子节点是2*L；4）节点R的右子节点为2*R + 1
// 本质上就是先构建二叉树，然后把根节点与最后一个进行交换，然后对剩下对元素进行二叉树构建，进行交换，直到剩下最后一个
堆demo:
var len;    //因为声明的多个函数都需要数据长度，所以把len设置成为全局变量

function buildMaxHeap(arr) {   //建立大顶堆
    len = arr.length;
    for (let i = Math.floor(len / 2); i >= 0; i--) {
        heapify(arr, i);
    }
}

function heapify(arr, i) {     //堆调整
    let left = 2 * i + 1;
    let right = 2 * i + 2;
    let largest = i;
    if (left < len && arr[left] > arr[largest]) {
        largest = left;
    }
    if (right < len && arr[right] > arr[largest]) {
        largest = right;
    }
    if (largest !== i) {
        [arr[i], arr[largest]] = [arr[largest], arr[i]];
        heapify(arr, largest);
    }
}

function heapSort(arr) {
    buildMaxHeap(arr);
    for (let i = arr.length - 1; i > 0; i--) {
        [arr[0],arr[i]]=[arr[i],arr[0]];
        len--;
        heapify(arr, 0);
    }
    return arr;
}
复制代码
```

##### 二分查找

思路 （1）首先，从有序数组的中间的元素开始搜索，如果该元素正好是目标元素（即要查找的元素），则搜索过程结束，否则进行下一步。 （2）如果目标元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半区域查找，然后重复第一步的操作。 （3）如果某一步数组为空，则表示找不到目标元素。

```
// 非递归算法
function binary_search(arr, key) {
    let low = 0;
    let high = arr.length - 1;
    while(low <= high){
        let mid = parseInt((high + low) / 2);
        if(key === arr[mid]){
            return  mid;
        }else if(key > arr[mid]){
            low = mid + 1;
        }else if(key < arr[mid]){
            high = mid -1;
        }else{
            return -1;
        }
    }
}
      

// 递归算法
function binary_search(arr,low, high, key) {
    if (low > high){
        return -1;
    }
    let mid = parseInt((high + low) / 2);
    if(arr[mid] === key){
        return mid;
    }else if (arr[mid] > key){
        high = mid - 1;
        return binary_search(arr, low, high, key);
    }else if (arr[mid] < key){
        low = mid + 1;
        return binary_search(arr, low, high, key);
    }
};
复制代码
```

##### 二叉树相关

```
创建
function Node(data,left,right){
	this.data = data;//数值
	this.left = left;//左节点
	this.right = right;//右节点
};
插入二叉树
function insert(node,data){
	//创建一个新的节点
	let newNode  = new Node(data,null,null);
	//判断是否存在根节点，没有将新节点存入
	if(node == null){
		node = newNode;
	}else{
		//获取根节点
		let current = node;
		let parent;
		while(true){
			//将当前节点保存为父节点
			parent = current;
			//将小的数据放在左节点
			if(data < current.data){
				//获取当前节点的左节点
				//判断当前节点下的左节点是否有数据
				current = current.left;
				if(current == null){
					//如果没有数据将新节点存入当前节点下的左节点
					parent.left = newNode;
					break;
				}
			}else{
				current = current.right;
				if(current == null){
					parent.right = newNode;
					break;
				}
			}
		}    
	}
}
翻转二叉树
function invertTree(node) {
	if (node !== null) {
		node.left, node.right = node.left, node.right;
		invertTree(node.left);
		invertTree(node.right);
	}
	return node;
} 
查找链表中倒数第k个结点
2个思路
1：先遍历出长度，然后查找长度-k+1的值
2：2个指针，一个指针先走k-1，然后两个一起走到底部，后者就是结果
```

 





















# JS设计模式



### 介绍下设计模式

#### 工厂模式

工厂模式分为好几种，这里就不一一讲解了，以下是一个简单工厂模式的例子

```
class Man {
  constructor(name) {
    this.name = name
  }
  alertName() {
    alert(this.name)
  }
}

class Factory {
  static create(name) {
    return new Man(name)
  }
}

Factory.create('yck').alertName() 
```

当然工厂模式并不仅仅是用来 new 出**实例**。

可以想象一个场景。假设有一份很复杂的代码需要用户去调用，但是用户并不关心这些复杂的代码，只需要你提供给我一个接口去调用，用户只负责传递需要的参数，至于这些参数怎么使用，内部有什么逻辑是不关心的，只需要你最后返回我一个实例。这个构造过程就是工厂。

工厂起到的作用就是隐藏了创建实例的复杂度，只需要提供一个接口，简单清晰。

在 Vue 源码中，你也可以看到工厂模式的使用，比如创建异步组件

```
export function createComponent (
  Ctor: Class<Component> | Function | Object | void,
  data: ?VNodeData,
  context: Component,
  children: ?Array<VNode>,
  tag?: string
): VNode | Array<VNode> | void {
    
    // 逻辑处理...
  
  const vnode = new VNode(
    `vue-component-${Ctor.cid}${name ? `-${name}` : ''}`,
    data, undefined, undefined, undefined, context,
    { Ctor, propsData, listeners, tag, children },
    asyncFactory
  )

  return vnode
} 
```

在上述代码中，我们可以看到我们只需要调用 `createComponent` 传入参数就能创建一个组件实例，但是创建这个实例是很复杂的一个过程，工厂帮助我们隐藏了这个复杂的过程，只需要一句代码调用就能实现功能。

#### 单例模式

单例模式很常用，比如全局缓存、全局状态管理等等这些只需要一个对象，就可以使用单例模式。

单例模式的核心就是保证全局只有一个对象可以访问。因为 JS 是门无类的语言，所以别的语言实现单例的方式并不能套入 JS 中，我们只需要用一个变量确保实例只创建一次就行，以下是如何实现单例模式的例子

```
class Singleton {
  constructor() {}
}

Singleton.getInstance = (function() {
  let instance
  return function() {
    if (!instance) {
      instance = new Singleton()
    }
    return instance
  }
})()

let s1 = Singleton.getInstance()
let s2 = Singleton.getInstance()
console.log(s1 === s2) // true 
```

在 Vuex 源码中，你也可以看到单例模式的使用，虽然它的实现方式不大一样，通过一个外部变量来控制只安装一次 Vuex

```
let Vue // bind on install

export function install (_Vue) {
  if (Vue && _Vue === Vue) {
    // ...
    return
  }
  Vue = _Vue
  applyMixin(Vue)
} 
```

#### 适配器模式

适配器用来解决两个接口不兼容的情况，不需要改变已有的接口，通过包装一层的方式实现两个接口的正常协作。

以下是如何实现适配器模式的例子

```
class Plug {
  getName() {
    return '港版插头'
  }
}

class Target {
  constructor() {
    this.plug = new Plug()
  }
  getName() {
    return this.plug.getName() + ' 适配器转二脚插头'
  }
}

let target = new Target()
target.getName() // 港版插头 适配器转二脚插头 
```

在 Vue 中，我们其实经常使用到适配器模式。比如父组件传递给子组件一个时间戳属性，组件内部需要将时间戳转为正常的日期显示，一般会使用 `computed` 来做转换这件事情，这个过程就使用到了适配器模式。

#### 装饰模式

装饰模式不需要改变已有的接口，作用是给对象添加功能。就像我们经常需要给手机戴个保护套防摔一样，不改变手机自身，给手机添加了保护套提供防摔功能。

以下是如何实现装饰模式的例子，使用了 ES7 中的装饰器语法

```
function readonly(target, key, descriptor) {
  descriptor.writable = false
  return descriptor
}

class Test {
  @readonly
  name = 'yck'
}

let t = new Test()

t.yck = '111' // 不可修改 
```

在 React 中，装饰模式其实随处可见

```
import { connect } from 'react-redux'
class MyComponent extends React.Component {
    // ...
}
export default connect(mapStateToProps)(MyComponent) 
```

#### 代理模式

代理是为了控制对对象的访问，不让外部直接访问到对象。在现实生活中，也有很多代理的场景。比如你需要买一件国外的产品，这时候你可以通过代购来购买产品。

在实际代码中其实代理的场景很多，也就不举框架中的例子了，比如事件代理就用到了代理模式。

```
<ul id="ul">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>
<script>
    let ul = document.querySelector('#ul')
    ul.addEventListener('click', (event) => {
        console.log(event.target);
    })
</script> 
```

因为存在太多的 `li`，不可能每个都去绑定事件。这时候可以通过给父节点绑定一个事件，让父节点作为代理去拿到真实点击的节点。

#### 发布-订阅模式

发布-订阅模式也叫做观察者模式。通过一对一或者一对多的依赖关系，当对象发生改变时，订阅方都会收到通知。在现实生活中，也有很多类似场景，比如我需要在购物网站上购买一个产品，但是发现该产品目前处于缺货状态，这时候我可以点击有货通知的按钮，让网站在产品有货的时候通过短信通知我。

在实际代码中其实发布-订阅模式也很常见，比如我们点击一个按钮触发了点击事件就是使用了该模式

```
<ul id="ul"></ul>
<script>
    let ul = document.querySelector('#ul')
    ul.addEventListener('click', (event) => {
        console.log(event.target);
    })
</script> 
```

在 Vue 中，如何实现响应式也是使用了该模式。对于需要实现响应式的对象来说，在 `get` 的时候会进行依赖收集，当改变了对象的属性时，就会触发派发更新。



