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

 