# 一、CSS

## 盒模型 

`box-sizing: content-box`（W3C盒模型，又名标准盒模型）：元素的宽高大小表现为内容的大小。 `box-sizing: border-box`（IE盒模型 ）：元素的宽高表现为内容 + 内边距 + 边框的大小。背景会延伸到边框的外沿。

2.margin、border、padding、content由外到里 3.几种获得宽高的方式

- dom.style.width/height  这种方式只能取到dom元素内联样式所设置的宽高，也就是说如果该节点的样式是在style标签中或外联的CSS文件中设置的话，通过这种方法是获取不到dom的宽高的。
- dom.currentStyle.width/height  这种方式获取的是在页面渲染完成后的结果，就是说不管是哪种方式设置的样式，都能获取到。但这种方式只有IE浏览器支持。
- window.getComputedStyle(dom).width/height  这种方式的原理和2是一样的，这个可以兼容更多的浏览器，通用性好一些。
- dom.getBoundingClientRect().width/height  这种方式是根据元素在视窗中的绝对位置来获取宽高的
- dom.offsetWidth/offsetHeight  这个就没什么好说的了，最常用的，也是兼容最好的。

4.拓展 各种获得宽高的方式

- 获取屏幕的高度和宽度（屏幕分辨率）： window.screen.height/width
- 获取屏幕工作区域的高度和宽度（去掉状态栏）： window.screen.availHeight/availWidth
- 网页全文的高度和宽度： document.body.scrollHeight/Width
- 滚动条卷上去的高度和向右卷的宽度： document.body.scrollTop/scrollLeft
- 网页可见区域的高度和宽度（不加边线）： document.body.clientHeight/clientWidth
- 网页可见区域的高度和宽度（加边线）： document.body.offsetHeight/offsetWidth

5.边距重叠解决方案(BFC) BFC原理

- 内部的box会在垂直方向，一个接一个的放置 每个元素的margin box的左边，与包含块border box的左边相接触（对于从做往右的格式化，否则相反）
- box垂直方向的距离由margin决定，属于同一个bfc的两个相邻box的margin会发生重叠
- bfc的区域不会与浮动区域的box重叠
- bfc是一个页面上的独立的容器，外面的元素不会影响bfc里的元素，反过来，里面的也不会影响外面的
- 计算bfc高度的时候，浮动元素也会参与计算 创建bfc
- float属性不为none（脱离文档流）
- position为absolute或fixed
- display为inline-block,table-cell,table-caption,flex,inine-flex
- overflow不为visible
- 根元素 demo

```
<section class="top">
	<h1>上</h1>
	这块margin-bottom:30px;
</section>
<!-- 给下面这个块添加一个父元素，在父元素上创建bfc -->
<div style="overflow:hidden">
	<section class="bottom">
	<h1>下</h1>
	这块margin-top:50px;
	</section>
</div> 
```

##### css reset 和 normalize.css 有什么区别

- 两者都是通过重置样式，保持浏览器样式的一致性
- 前者几乎为所有标签添加了样式，后者保持了许多浏览器样式，保持尽可能的一致
- 后者修复了常见的桌面端和移动端浏览器的bug：包含了HTML5元素的显示设置、预格式化文字的font-size问题、在IE9中SVG的溢出、许多出现在各浏览器和操作系统中的与表单相关的bug。
- 前者中含有大段的继承链
- 后者模块化，文档较前者来说丰富 

## CSS3的新特性

- word-wrap 文字换行
- text-overflow 超过指定容器的边界时如何显示
- text-decoration 文字渲染
- text-shadow文字阴影
- gradient渐变效果
- transition过渡效果 transition-duration：过渡的持续时间
- transform拉伸，压缩，旋转，偏移等变换
- animation动画

transition和animation的区别：

  Animation和transition大部分属性是相同的，他们都是随时间改变元素的属性值，他们的主要区别是transition需要触发一个事件才能改变属性，而animation不需要触发任何事件的情况下才会随时间改变属性值，并且transition为2帧，从from .... to，而animation可以一帧一帧的。

## CSS选择器及其优先级

- !important
- 内联样式style=""
- ID选择器#id
- 类选择器/属性选择器/伪类选择器.class.active[href=""]
- 元素选择器/关系选择器/伪元素选择器html+div>span::after
- 通配符选择器*

## BFC

BFC（Block Formatting Context）格式化上下文，是Web页面中盒模型布局的CSS渲染模式，指一个独立的渲染区域或者说是一个隔离的独立容器。 

### BFC应用

- 防止margin重叠
- 清除内部浮动
- 自适应两（多）栏布局
- 防止字体环绕

### 触发BFC条件

- 根元素
- float的值不为none
- overflow的值不为visible
- display的值为inline-block、table-cell、table-caption
- position的值为absolute、fixed

### BFC的特性

- 内部的Box会在垂直方向上一个接一个的放置。
- 垂直方向上的距离由margin决定
- bfc的区域不会与float的元素区域重叠。
- 计算bfc的高度时，浮动元素也参与计算
- bfc就是页面上的一个独立容器，容器里面的子元素不会影响外面元素。

## div水平居中

1. 行内元素

```
.parent {
    text-align: center;
} 
```

1. 块级元素

```
.son {
    margin: 0 auto;
} 
```

1. flex布局

```
.parent {
    display: flex;
    justify-content: center;
} 
```

1. 绝对定位定宽

```
.son {
    position: absolute;
    width: 宽度;
    left: 50%;
    margin-left: -0.5*宽度
} 
```

1. 绝对定位不定宽

```
.son {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 0);
} 
```

1. left/right: 0

```
.son {
    position: absolute;
    width: 宽度;
    left: 0;
    right: 0;
    margin: 0 auto;
} 
```

## div垂直居中

1. 行内元素

```
.parent {
    height: 高度;
}
.son {
    line-height: 高度;
} 
```

1. table

```
.parent {
  display: table;
}
.son {
  display: table-cell;
  vertical-align: middle;
} 
```

1. flex

```
.parent {
    display: flex;
    align-items: center;
} 
```

1. 绝对定位定高

```
.son {
    position: absolute;
    top: 50%;
    height: 高度;
    margin-top: -0.5高度;
} 
```

1. 绝对定位不定高

```
.son {
    position: absolute;
    top: 50%;
    transform: translate( 0, -50%);
} 
```

1. top/bottom: 0;

```
.son {
    position: absolute;
    height: 高度;
    top: 0;
    bottom: 0;
    margin: auto 0;
} 
```

## 绝对定位和相对定位

- `absolute` 绝对定位 相对于最近的已定位的祖先元素, 有已定位(指position不是static的元素)祖先元素, 以最近的祖先元素为参考标准。如果无已定位祖先元素, 以body元素为偏移参照基准, 完全脱离了标准文档流。
- `fixed` 固定定位的元素会相对于视窗来定位,这意味着即便页面滚动，它还是会停留在相同的位置。一个固定定位元素不会保留它原本在页面应有的空隙。

共同点：改变行内元素的呈现方式，都脱离了文档流；不同点：absolute的”根元素“是可以设置的，fixed的“根元素”固定为浏览器窗口

## flex布局

采用 Flex 布局的元素，称为 `Flex 容器`（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 `Flex 项目`（flex item），简称“项目”。



![flex.jpeg](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eecacd1b3fc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



1. 父元素属性

| 属性名          |                        属性值                         |                             备注                             |
| --------------- | :---------------------------------------------------: | :----------------------------------------------------------: |
| display         |                         flex                          |     定义了一个flex容器，它的直接子元素会接受这个flex环境     |
| flex-direction  |         row,row-reverse,column,column-reverse         |                        决定主轴的方向                        |
| flex-wrap       |               nowrap,wrap,wrap-reverse                |                 如果一条轴线排不下，如何换行                 |
| flex-flow       |            [flex-direction] , [flex-wrap]             | 是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap` |
| justify-content | flex-start,flex-end,center,space-between,space-around |     设置或检索弹性盒子元素在主轴（横轴）方向上的对齐方式     |
| align-items     |      flex-start,flex-end,center,baseline,stretch      |     设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式     |

1. 子元素属性

| 属性名      |                      属性值                      |                             备注                             |
| ----------- | :----------------------------------------------: | :----------------------------------------------------------: |
| order       |                      [int]                       | 默认情况下flex order会按照书写顺训呈现，可以通过order属性改变，数值小的在前面，还可以是负数。 |
| flex-grow   |                     [number]                     | 设置或检索弹性盒的扩展比率,根据弹性盒子元素所设置的扩展因子作为比率来分配剩余空间 |
| flex-shrink |                     [number]                     | 设置或检索弹性盒的收缩比率,根据弹性盒子元素所设置的收缩因子作为比率来收缩空间 |
| flex-basis  |                  [length], auto                  |                  设置或检索弹性盒伸缩基准值                  |
| align-self  | auto,flex-start,flex-end,center,baseline,stretch | 设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式，可以覆盖父容器align-items的设置 |

## 让元素消失

visibility:hidden、display:none、z-index=-1、opacity：0

1. opacity：0,该元素隐藏起来了，但不会改变页面布局，并且，如果该元素已经绑定了一些事件，如click事件也能触发
2. visibility:hidden,该元素隐藏起来了，但不会改变页面布局，但是不会触发该元素已经绑定的事件
3. display:none, 把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删掉
4. z-index=-1置于其他元素下面

## 清除浮动

1. 在浮动元素后面添加 `clear:both` 的空 div 元素，

```
<div class="container">
    <div class="left"></div>
    <div class="right"></div>
    <div style="clear:both"></div>
</div> 
```

1. 给父元素添加 `overflow:hidden` 或者 auto 样式，触发BFC。

```
<div class="container">
    <div class="left"></div>
    <div class="right"></div>
</div> 
.container{
    width: 300px;
    background-color: #aaa;
    overflow:hidden;
    zoom:1;   /*IE6*/
} 
```

1. 使用伪元素，也是在元素末尾添加一个点并带有 clear: both 属性的元素实现的。

```
<div class="container clearfix">
    <div class="left"></div>
    <div class="right"></div>
</div> 
.clearfix{
    zoom: 1; /*IE6*/
}
.clearfix:after{
    content: ".";
    height: 0;
    clear: both;
    display: block;
    visibility: hidden;
} 
```

**推荐**使用第三种方法，不会在页面新增div，文档结构更加清晰。

## calc函数

calc函数是css3新增的功能，可以使用calc()计算border、margin、padding、font-size和width等属性设置动态值。

```css
#div1 {
    position: absolute;
    left: 50px;
    width: calc( 100% / (100px * 2) );
    //兼容写法
    width: -moz-calc( 100% / (100px * 2) );
    width: -webkit-calc( 100% / (100px * 2) );
    border: 1px solid black;
} 
```

注意点：

- 需要注意的是，运算符前后都需要保留一个空格，例如：width: calc(100% - 10px);
- calc()函数支持 "+", "-", "*", "/" 运算;
- 对于不支持 calc() 的浏览器，整个属性值表达式将被忽略。不过我们可以对那些不支持 calc()的浏览器，使用一个固定值作为回退。

## 移动端rem

rem官方定义『The font size of the root element』，即根元素的字体大小。rem是一个相对的CSS单位，1rem等于html元素上font-size的大小。所以，我们只要设置html上font-size的大小，就可以改变1rem所代表的大小。

```
(function () {
    var html = document.documentElement;
    function onWindowResize() {
        html.style.fontSize = html.getBoundingClientRect().width / 20 + 'px';
    }
    window.addEventListener('resize', onWindowResize);
    onWindowResize();
})(); 
```

## 移动端1px

一般来说，在PC端浏览器中，设备像素比（dpr）等于1，1个css像素就代表1个物理像素；但是在retina屏幕中，dpr普遍是2或3，1个css像素不再等于1个物理像素，因此比实际设计稿看起来粗不少。

1. 伪元素+scale

```
<style>
    .box{
        width: 100%;
        height: 1px;
        margin: 20px 0;
        position: relative;
    }
    .box::after{
        content: '';
        position: absolute;
        bottom: 0;
        width: 100%;
        height: 1px;
        transform: scaleY(0.5);
        transform-origin: 0 0; 
        background: red;
    }
</style>

<div class="box"></div> 
```

1. border-image

```
div{
    border-width: 1px 0px;
    -webkit-border-image: url(border.png) 2 0 stretch;
    border-image: url(border.png) 2 0 stretch;
} 
```

## 两边宽度固定中间自适应的三栏布局

圣杯布局和双飞翼布局是前端工程师需要日常掌握的重要布局方式。两者的功能相同，都是为了实现一个两侧宽度固定，中间宽度自适应的三栏布局。

### 圣杯布局

```
<style>
body{
    min-width: 550px;
}
#container{
    padding-left: 200px;
    padding-right: 150px;
}
#container .column{
    float: left;
}
#center{
    width: 100%;
}
#left{
    width: 200px;
    margin-left: -100%;
    position: relative;
    right: 200px;
}
#right{
    width: 150px;
    margin-right: -150px;
}
</style>
<div id="container">
    <div id="center" class="column">center</div>
    <div id="left" class="column">left</div>
    <div id="right" class="column">right</div>
</div> 
```



![Layout.gif](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eecace7c294?imageslim)



### 双飞翼布局

```
<style>
body {
    min-width: 500px;
}
#container {
    width: 100%;
}
.column {
    float: left;
}
#center {
    margin-left: 200px;
    margin-right: 150px;
}
#left {
    width: 200px;
    margin-left: -100%;
}
#right {
    width: 150px;
    margin-left: -150px;
}
</style>
<div id="container" class="column">
    <div id="center">center</div>
</div>
<div id="left" class="column">left</div>
<div id="right" class="column">right</div> 
```

## 伪类和伪元素

css引入伪类和伪元素概念是为了格式化文档树以外的信息。也就是说，伪类和伪元素都是用来修饰不在文档树中的部分。



![before-after.jpg](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eecad345c02?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 伪类

伪类存在的意义是为了通过选择器找到那些不存在DOM树中的信息以及不能被常规CSS选择器获取到的信息。

1. 获取不存在与DOM树中的信息。比如a标签的:link、visited等，这些信息不存在与DOM树结构中，只能通过CSS选择器来获取；
2. 获取不能被常规CSS选择器获取的信息。比如：要获取第一个子元素，我们无法用常规的CSS选择器获取，但可以通过 :first-child 来获取到。



![weilei.png](https://user-gold-cdn.xitu.io/2019/12/12/16ef8eecad4f1adb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 伪元素

伪元素用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过:before来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。常见的伪元素有：`::before`，`::after`，`::first-line`，`::first-letter`，`::selection`、`::placeholder`等

> 因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。

### ::after和:after的区别

在实际的开发工作中，我们会看到有人把伪元素写成`:after`，这实际是 CSS2 与 CSS3新旧标准的规定不同而导致的。

CSS2 中的伪元素使用1个冒号，在 CSS3 中，为了区分伪类和伪元素，规定伪元素使用2个冒号。所以，对于 CSS2 标准的老伪元素，比如`:first-line`，`:first-letter`，`:before`，`:after`，写一个冒号浏览器也能识别，但对于 CSS3 标准的新伪元素，比如::selection，就必须写2个冒号了。

## CSS画圆半圆扇形三角梯形

```
div{
    margin: 50px;
    width: 100px;
    height: 100px;
    background: red;
}
/* 半圆 */
.half-circle{
    height: 50px;
    border-radius: 50px 50px 0 0;
}
/* 扇形 */
.sector{
    border-radius: 100px 0 0;
}
/* 三角 */
.triangle{
    width: 0px;
    height: 0px;
    background: none;
    border: 50px solid red;
    border-color: red transparent transparent transparent;
}
/* 梯形 */
.ladder{
    width: 50px;
    height: 0px;
    background: none;
    border: 50px solid red;
    border-color: red transparent transparent transparent;
}
```

##### link @import导入css

1. link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务；@import属于CSS范畴，只能加载CSS。
2. link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
3. link无兼容问题；@import是在CSS2.1提出的，低版本的浏览器不支持。
4. link支持使用Javascript控制DOM去改变样式；而@import不支持。

##### animation



![img](https://user-gold-cdn.xitu.io/2018/9/9/165bd6dede39a8b8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



##### 长宽比方案

1. 使用padding方式结合calc实现
2. 长宽一项设置百分比另一项aspect-ratio实现（需借助插件实现）

##### display相关

1. block:div等容器类型
2. inline:img span等行内类型
3. table系列：将样式变成table类型
4. flex:重点把握，非常强大
5. grid:同上
6. inline-block:可设置宽度，两者间有一点间隙
7. inherit:继承父级 