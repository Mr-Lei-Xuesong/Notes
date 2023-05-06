## canvas元素

```html
用于图形的绘制，通过脚本 (通常是JavaScript)来完成.
<canvas> 标签只是图形容器，您必须使用脚本来绘制图形。
getContext("2d"); 创建对象"2d"
```

* 矩形
  + fillRect(x, y, width, height)：绘制一个填充的矩形。
  + strokeRect(x, y, width, height)：绘制一个矩形的边框。
  + clearRect(x, y, widh, height)：清除指定的矩形区域，然后这块区域会变的完全透明。

* 路径
  + beginPath() 新建一条路径，路径一旦创建成功，图形绘制命令被指向到路径上生成路径
  + moveTo(x, y) 把画笔移动到指定的坐标(x, y)。相当于设置路径的起始点坐标。
  + closePath() 闭合路径之后，图形绘制命令又重新指向到上下文中
  + fill() 通过填充路径的内容区域生成实心的图形
  + stroke() 方法会实际地绘制出通过 moveTo() 和 lineTo() 方法定义的路径。默认颜色是黑色。

* 画圆
  + arc(x,y,r,start,stop)
    + 画布的左上角坐标为0,0
    + x：圆心在x轴上的坐标
    + y：圆心在y轴上的坐标
    + r：半径长度
    + start：起始角度，以弧度表示，圆心平行的右端为0度
    + stop：结束角度，以弧度表示
    + 注意：Math.PI表示180°，画圆的方向是顺时针

* 文本
  + font - 定义字体
  + fillText(text,x,y) - 在 canvas 上绘制实心的文本
  + strokeText(text,x,y) - 在 canvas 上绘制空心的文本

* 渐变

```
渐变可以填充在矩形, 圆形, 线条, 文本等等, 各种形状可以自己定义不同的颜色。
以下有两种不同的方式来设置Canvas渐变：
	createLinearGradient(x,y,x1,y1) - 创建线条渐变
	createRadialGradient(x,y,r,x1,y1,r1) - 创建一个径向/圆渐变
当我们使用渐变对象，必须使用两种或两种以上的停止颜色。
	addColorStop()方法指定颜色停止，参数使用坐标来描述，可以是0至1.
使用渐变，设置fillStyle或strokeStyle的值为 渐变，然后绘制形状，如矩形，文本，或一条线。
```

* 图像
  + drawImage(image,x,y)

## 新多媒体元素

```html
<audio>	定义音频内容
<video>	定义视频（video 或者 movie）
	<source>	定义多媒体资源 <video> 和 <audio>
<embed>	定义嵌入的内容，比如插件。
<track>	为诸如 <video> 和 <audio> 元素之类的媒介规定外部文本轨道。
```

## Input类型

```html
你喜欢的颜色<input type="color">
日期<input type="date">
时间<input type="time">
日期和时间<input type="datetime"> <input type="datetime-local">
邮箱<input type="email">
年月<input type="month">
年周<input type="week">
数字1-5之间<input type="number" min="1" max="5">
滑块<input type="range" min="1" max="10">
搜索<input type="search">
电话<input type="tel">
url<input type="url">
```

## 表单元素

```html
<datalist>	<input>标签定义选项列表。请与 input 元素配合使用该元素，来定义 input 可能的值。

<keygen>	<keygen> 标签规定用于表单的密钥对生成器字段。

<output>	<output> 标签定义不同类型的输出，比如脚本的输出。<datalist> 元素规定输入域的选项列表
```

## 表单属性

```
<form>/<input> autocomplete 属性
规定 form 或 input 域应该拥有自动完成功能,当用户在自动完成域中开始输入时，浏览器应该在该域中显示填写的选项。

<form> novalidate 属性 
novalidate 属性是一个 boolean(布尔) 属性.novalidate 属性规定在提交表单时不应该验证 form 或 input 域。

<input> autofocus 属性 
autofocus 属性是一个 boolean 属性.autofocus 属性规定在页面加载时，域自动地获得焦点

<input> formaction 属性
用于描述表单提交的URL地址.

<input> formenctype 属性 
formenctype 属性描述了表单提交到服务器的数据编码 (只对form表单中 method="post" 表单)

<input> formmethod 属性 
formmethod 属性定义了表单提交的方式。formmethod 属性覆盖了 <form> 元素的 method 属性。

<input> formnovalidate 属性 
novalidate 属性是一个 boolean 属性.novalidate属性描述了 <input> 元素在表单提交时无需被验证。formnovalidate 属性会覆盖 <form> 元素的novalidate属性

<input> formtarget 属性 
formtarget 属性指定一个名称或一个关键字来指明表单提交数据接收后的展示。The formtarget 属性覆盖 <form>元素的target属性.

<input> height 和 width 属性 
height 和 width 属性规定用于 image 类型的 <input> 标签的图像高度和宽度。

<input> list 属性 
list 属性规定输入域的 datalist。datalist 是输入域的选项列表

<input> min 和 max 属性 
min、max 和 step 属性用于为包含数字或日期的 input 类型规定限定（约束）。
注意: min、max 和 step 属性适用于以下类型的 <input> 标签：date pickers、number 以及 range。

<input> multiple 属性 
multiple 属性是一个 boolean 属性.multiple 属性规定<input> 元素中可选择多个值。

<input> pattern 属性 
pattern 属性描述了一个正则表达式用于验证 <input> 元素的值。

<input> placeholder 属性 
placeholder 属性提供一种提示（hint），描述输入域所期待的值。

<input> required 属性 
required 属性是一个 boolean 属性.required 属性规定必须在提交之前填写输入域（不能为空）。
```

## 语义元素

* section元素

```
<section> 标签定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分。
```

*  article元素 

```
<article> 标签定义独立的内容。
```

*  nav元素 

```
<nav> 标签定义导航链接的部分。
```

*  aside元素

```
<aside> 标签定义页面主区域内容之外的内容（比如侧边栏）。
```

*  header元素 

```
<header>元素描述了文档的头部区域
<header>元素主要用于定义内容的介绍展示区域.
在页面中你可以使用多个<header> 元素.
```

*  footer元素

```
<footer> 元素描述了文档的底部区域.
<footer> 元素应该包含它的包含元素
一个页脚通常包含文档的作者，著作权信息，链接的使用条款，联系信息等
```

*  figure元素和figcaption元素

```
<figure>标签规定独立的流内容（图像、图表、照片、代码等等）。
<figure> 元素的内容应该与主内容相关，但如果被删除，则不应对文档流产生影响。
<figcaption> 标签定义 <figure> 元素的标题.
<figcaption>元素应该被置于 "figure" 元素的第一个或最后一个子元素的位置。
```

