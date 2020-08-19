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

 