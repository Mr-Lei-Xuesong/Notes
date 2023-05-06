## 文本字体

+ font-family:设置字体
+ font-size:字体大小
+ font-style:斜体(normal正常，italic斜体，oblique偏斜体)
+ font-weight: 加粗(normal正常，bold粗体，bolder特粗，lighter特细体)
+ font-variant:字体变体
+ letter-spacing:字母间距
+ word-spacing:单词间距
+ text-decoration: 文字修饰(underline下划线，overline上划线，line-through删除线)
+ text-align:文字对齐方式(left左，right右，center居中，justify两端对齐)
+ text-indent:段落缩进
+ line-height:调整行高
+ text-transform:转换英文大小写(lowercase所有小写，uppercase所有大写，capitalize首个大字母大写)
+ vertical-align:文字垂直对齐方式(sub文字的下标，super文字的上标，top垂直靠上对齐....)

## 图片样式

+ border-(left/right/top/bottom)-style: 边框样式
+ border-(left/right/top/bottom)-color: 边框颜色
+ border-(left/right/top/bottom)-width: 边框粗细
+ float:none|left|right(浮动)
+ img{margin-(left.right.bottom.top):值}间距

## 网页背景

+ background-color:背景颜色
+ background-image:url(图像地址)
+ background-repeat:repet-x水平方向上铺,repet-y垂直方向上铺，no-repeat不重复
+ background-attachment: fixed(不随滚动条滚动),scroll(随着滚动条滚动);
+ background-position:背景定位

## 链接

+ a:link {color:#000000;}      /* 未访问链接*/
+ a:visited {color:#00FF00;}  /* 已访问链接 */
+ a:hover {color:#FF00FF;}  /* 鼠标移动到链接上 */
+ a:active {color:#0000FF;}  /* 鼠标点击时 */

## 列表属性

+ list-style	简写属性。用于把所有用于列表的属性设置于一个声明中
+ list-style-image	将图象设置为列表项标志。
+ list-style-position	设置列表中列表项标志的位置。
+ list-style-type	设置列表项标志的类型。

## 盒子模型

+ margin-(top,right,bottom,left)(外边距) - 清除边框外的区域，外边距是透明的。
+ border-(top,right,bottom,left)(边框) - 围绕在内边距和内容外的边框。
+ padding-(top,right,bottom,left)(内边距) - 清除内容周围的区域，内边距是透明的。
+ content(内容) - 盒子的内容，显示文本和图像。

## 边框属性

+ border-(top,right,bottom,left)-style:  边框样式
  + none: 默认无边框
  + dotted: 定义一个点线边框
  + dashed: 定义一个虚线边框
  + solid: 定义实线边框
  + double: 定义两个边框。 两个边框的宽度和 border-width 的值相同
  + groove: 定义3D沟槽边框。效果取决于边框的颜色值
  + ridge: 定义3D脊边框。效果取决于边框的颜色值
  + inset:定义一个3D的嵌入边框。效果取决于边框的颜色值
  + outset: 定义一个3D突出边框。 效果取决于边框的颜色值

+ border-(top,right,bottom,left)-width:   边框宽度
+ border-(top,right,bottom,left)-color:   边框颜色

## 外边框

+ outline-color:	颜色
+ outline-style:	轮廓样式
+ outline-width:	宽度

## 隐藏元素

+ display:none		隐藏的元素不会占用任何空间
+ visibility:hidden	隐藏的元素仍需占用与未隐藏之前一样的空间

## 定位

+ postion:
  + absolute(绝对定位)，其位置相对于最近已定位的父元素，如果元素没有已定位的父元素那么它的位置相对于
  + static(默认的静态定位)，即没有定位，遵循正常的文档流对象，静态定位的元素不受top、left、right、bottom影响。
  + relative(相对定位)，其位置相对其正常时的位置。相对定位元素经常被用来作为绝对定位元素的容器块。
  + fixed，元素的位置相对于浏览器窗口，是固定位置。即使窗口是滚动的它也不会移动。
  + sticky(粘性定位)，基于用户滚动位置来定位，在未滚动出目标区域时，它的行为就像position:relative;它的表现就像 position:fixed;，它会固定在目标位置。元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。
  + z-index属性指定了一个元素的堆叠顺序（哪个元素应该放在前面，或后面）

## Overflow

+ visible	默认值。内容不会被修剪，会呈现在元素框之外。
+ hidden	内容会被修剪，并且其余内容是不可见的。
+ scroll	内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。
+ auto	如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。
+ inherit	规定应该从父元素继承 overflow 属性的值。

## 浮动

+ clear 指定不允许元素周围有浮动元素。	
  + left
  + right
  + both
  + none

+ float 指定一个盒子（元素）是否可以浮动。	
  + left
  + right
  + none 

## 布局-水平&垂直对齐

+ 要水平居中对齐一个元素(如 <div>), 可以使用 margin: auto;
+ 文本在元素内居中对齐，可以使用 text-align: center;

## 导航栏

```html
<html>
<head>
<meta charset="utf-8">
<title></title>
<style>
ul {
    list-style-type: none;
    margin: 0;
    padding: 0;
    overflow: hidden;
    background-color: #333;
}

li {
    float: left;
    border-right:1px solid #bbb;
}

li:last-child {
    border-right: none;
}

li a {
    display: block;
    color: white;
    text-align: center;
    padding: 14px 16px;
    text-decoration: none;
}

li a:hover:not(.active) {
    background-color: #111;
}

.active {
    background-color: #4CAF50;
}
</style>
</head>
<body>

<ul>
  <li><a class="active" href="#home">主页</a></li>
  <li><a href="#news">新闻</a></li>
  <li><a href="#contact">联系</a></li>
  <li style="float:right"><a href="#about">关于</a></li>
</ul>

</body>
</html>
```

## 下拉菜单

```html
<html>
<head>
<title>下拉菜单实例</title>
<meta charset="utf-8">
<style>
.dropbtn {
    background-color: #4CAF50;
    color: white;
    padding: 16px;
    font-size: 16px;
    border: none;
    cursor: pointer;
}

.dropdown {
    position: relative;
    display: inline-block;
}

.dropdown-content {
    display: none;
    position: absolute;
    background-color: #f9f9f9;
    min-width: 160px;
    box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
}

.dropdown-content a {
    color: black;
    padding: 12px 16px;
    text-decoration: none;
    display: block;
}

.dropdown-content a:hover {background-color: #f1f1f1}

.dropdown:hover .dropdown-content {
    display: block;
}

.dropdown:hover .dropbtn {
    background-color: #3e8e41;
}
</style>
</head>
<body>

<h2>下拉菜单</h2>
<p>鼠标移动到按钮上打开下拉菜单。</p>

<div class="dropdown">
  <button class="dropbtn">下拉菜单</button>
  <div class="dropdown-content">
    <a href="//www.runoob.com">菜鸟教程 1</a>
    <a href="//www.runoob.com">菜鸟教程 2</a>
    <a href="//www.runoob.com">菜鸟教程 3</a>
  </div>
</div>

</body>
</html>
```

## 提示信息

```html
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<style>
.tooltip {
    position: relative;
    display: inline-block;
    border-bottom: 1px dotted black;
}

.tooltip .tooltiptext {
    visibility: hidden;
    width: 120px;
    background-color: black;
    color: #fff;
    text-align: center;
    border-radius: 6px;
    padding: 5px 0;

    /* 定位 */
    position: absolute;
    z-index: 1;
}

.tooltip:hover .tooltiptext {
    visibility: visible;
}
</style>
<body style="text-align:center;">

<div class="tooltip">鼠标移动到这
  <span class="tooltiptext">提示文本</span>
</div>

</body>
</html>
```

## 图片廓

```html
<html>
<head>
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title>  
<style>
div.img {
    margin: 5px;
    border: 1px solid #ccc;
    float: left;
    width: 180px;
}

div.img:hover {
    border: 1px solid #777;
}

div.img img {
    width: 100%;
    height: auto;
}

div.desc {
    padding: 15px;
    text-align: center;
}
</style>
</head>
<body>
<div class="responsive">
  <div class="img">
    <a target="_blank" href="//static.runoob.com/images/demo/demo1.jpg">
      <img src="//static.runoob.com/images/demo/demo1.jpg" alt="图片文本描述" width="300" height="200">
    </a>
    <div class="desc">这里添加图片文本描述</div>
  </div>
</div>
</body>
</html>
```

## 图像透明

+ opacity来设定图片透明度，opacity 属性能够设置的值从 0.0 到 1.0  
+ img:hover鼠标控制

## 使用css来渲染表单

```html
<html>
<head>
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<style>
input[type=text], select {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}
input[type=submit] {
  width: 100%;
  background-color: #4CAF50;
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
input[type=submit]:hover {
  background-color: #45a049;
}
div {
  border-radius: 5px;
  background-color: #f2f2f2;
  padding: 20px;
}
</style>
<body>
<h3>使用 CSS 来渲染 HTML 的表单元素</h3>
<div>
  <form action="/action_page.php">
    <label for="fname">First Name</label>
    <input type="text" id="fname" name="firstname" placeholder="Your name..">
    <label for="lname">Last Name</label>
    <input type="text" id="lname" name="lastname" placeholder="Your last name..">
    <label for="country">Country</label>
    <select id="country" name="country">
      <option value="australia">Australia</option>
      <option value="canada">Canada</option>
      <option value="usa">USA</option>
    </select>
  
    <input type="submit" value="Submit">
  </form>
</div>
</body>
</html>
```

