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

## JS作用域 

> JS中的作用域分为两种：全局作用域和函数作用域。函数作用域中定义的变量，只能在函数中调用，外界无法访问。没有块级作用域导致了if或for这样的逻辑语句中定义的变量可以被外界访问，因此ES6中新增了let和const命令来进行块级作用域的声明。 

## 闭包 

> 简单来说闭包就是在函数里面声明函数，本质上说就是在函数内部和函数外部搭建起一座桥梁，使得子函数可以访问父函数中所有的局部变量，但是反之不可以，这只是闭包的作用之一，另一个作用，则是保护变量不受外界污染，使其一直存在内存中，在工作中我们还是少使用闭包的好，因为闭包太消耗内存，不到万不得已的时候尽量不使用。 

## 数组去重

```js
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

```js
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

```js
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

## 全局函数eval()有什么作用？

eval()只有一个参数，如果传入的参数不是字符串，它直接返回这个参数。如果参数是字符串，它会把字符串当成javascript代码进行编译。如果编译失败则抛出一个语法错误(syntaxError)异常。如果编译成功，则开始执行这段代码，并返回字符串中的最后一个表达式或语句的值，如果最后一个表达式或语句没有值，则最终返回undefined。如果字符串抛出一个异常，这个异常将把该调用传递给eval()。

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

 

## 5.原生对象和宿主对象

**原生对象**是ECMAScript规定的对象，所有内置对象都是原生对象，比如Array、Date、RegExp等；

**宿主对象**是宿主环境比如浏览器规定的对象，用于完善是ECMAScript的执行环境，比如Document、Location、Navigator等。 

**基本数据类型**指的是简单的数据段，有5种，包括null、undefined、string、boolean、number；

**引用数据类型**指的是有多个值构成的对象，包括object、array、date、regexp、function等。

**主要区别：**

- 声明变量时不同的内存分配：前者由于占据的空间大小固定且较小，会被存储在栈当中，也就是变量访问的位置；后者则存储在堆当中，变量访问的其实是一个指针，它指向存储对象的内存地址。
- 也正是因为内存分配不同，在复制变量时也不一样。前者复制后2个变量是独立的，因为是把值拷贝了一份；后者则是复制了一个指针，2个变量指向的值是该指针所指向的内容，一旦一方修改，另一方也会受到影响。
- 参数传递不同：虽然函数的参数都是按值传递的，但是引用值传递的值是一个内存地址，实参和形参指向的是同一个对象，所以函数内部对这个参数的修改会体现在外部。原始值只是把变量里的值传递给参数，之后参数和这个变量互不影响。  

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

## 12.请解释变量声明提升

通过var声明的变量会被提升至作用域的顶端。不仅仅是变量，函数声明也一样会被提升。当同一作用域内同时出现变量和函数声明提升时，变量仍然在函数前面。  

### 16.JavaScript里arguments究竟是什么？

Javascrip中国每个函数都会有一个Arguments对象实例arguments，它引用着函数的实参，可以用数组下标的方式"[]"引用arguments的元素。arguments.length为函数实参个数，arguments.callee引用函数自身。

在函数代码中，使用特殊对象arguments，开发者无需明确指出参数名，通过使用下标就可以访问相应的参数。

```
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

### 20.通过new创建一个对象的时候，构造函数内部有哪些改变？

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

   
