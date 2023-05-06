# JQuery语法

* 基础语法是：*$(selector).action()*

  * 美元符号定义 jQuery
  * 选择符（selector）“查询”和“查找” HTML 元素

  * jQuery 的 action() 执行对元素的操作

    * 示例

      ```
      $(this).hide() - 隐藏当前元素
      $("p").hide() - 隐藏所有段落
      $(".test").hide() - 隐藏所有 class="test" 的所有元素
      $("#test").hide() - 隐藏所有 id="test" 的元素
      ```

# JQuery选择器

| 语法                     | 描述                                                    |
| :----------------------- | :------------------------------------------------------ |
| $("*")                   | 选取所有元素                                            |
| $(this)                  | 选取当前 HTML 元素                                      |
| $("p.intro")             | 选取 class 为 intro 的 <p> 元素                         |
| $("p:first")             | 选取第一个 <p> 元素                                     |
| $("ul li:first")         | 选取第一个 <ul> 元素的第一个 <li> 元素                  |
| $("ul li:first-child")   | 选取每个 <ul> 元素的第一个 <li> 元素                    |
| $("[href]")              | 选取带有 href 属性的元素                                |
| $("a[target='_blank']")  | 选取所有 target 属性值等于 "_blank" 的 <a> 元素         |
| $("a[target!='_blank']") | 选取所有 target 属性值不等于 "_blank" 的 <a> 元素       |
| $(":button")             | 选取所有 type="button" 的 <input> 元素 和 <button> 元素 |
| $("tr:even")             | 选取偶数位置的 <tr> 元素                                |
| $("tr:odd")              | 选取奇数位置的 <tr> 元素                                |

# JQuery事件

* click() 点击事件
* dblclick() 当双击元素时，会发生 dblclick 事件
* mouseenter() 当鼠标指针穿过元素时，会发生 mouseenter 事件
* mouseleave() 当鼠标指针离开元素时，会发生 mouseleave 事件
* mousedown() 当鼠标指针移动到元素上方，并按下鼠标按键时，会发生 mousedown 事件
* mouseup() 当在元素上松开鼠标按钮时，会发生 mouseup 事件
* hover() 方法用于模拟光标悬停事件
* focus() 当元素获得焦点时，发生 focus 事件
* blur() 当元素失去焦点时，发生 blur 事件

# JQuery 效果

## 隐藏/显示

* hide(*speed,callback*)

* show(*speed,callback*)

  ```
  可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒。
  可选的 callback 参数是隐藏或显示完成后所执行的函数名称
  ```

* toggle(speed,callback) 切换 hide() 和 show() 方法

## 淡入淡出

* fadeIn(*speed,callback*) 用于淡入已隐藏的元素
* fadeOut(*speed,callback*) 方法用于淡出可见元素

* fadeToggle() 方法可以在 fadeIn() 与 fadeOut() 方法之间进行切换

* fadeTo(*speed,opacity,callback*) 方法允许渐变为给定的不透明度（值介于 0 与 1 之间）

  ```
  必需的 speed 参数规定效果的时长。它可以取以下值："slow"、"fast" 或毫秒
  fadeTo() 方法中必需的 opacity 参数将淡入淡出效果设置为给定的不透明度（值介于 0 与 1 之间）
  可选的 callback 参数是该函数完成后所执行的函数名称
  ```

## 滑动

* slideDown() 方法用于向下滑动元素
* slideUp() 方法用于向上滑动元素
* slideToggle() 方法可以在 slideDown() 与 slideUp() 方法之间进行切换

## 动画

* animate({*params*}*,speed,callback*) 方法用于创建自定义动画

  ```
  必需的 params 参数定义形成动画的 CSS 属性
  可选的 speed 参数规定效果的时长。它可以取以下值："slow"、"fast" 或毫秒
  可选的 callback 参数是动画完成后所执行的函数名称
  ```

* 操作多个属性

  ```
  $("button").click(function(){
    $("div").animate({
      left:'250px',
      opacity:'0.5',
      height:'150px',
      width:'150px'
    });
  });
  ```

* 使用相对值

  ```
  $("button").click(function(){
    $("div").animate({
      left:'250px',
      height:'+=150px',
      width:'+=150px'
    });
  });
  ```

* 使用预定义值

  ```
  $("button").click(function(){
    $("div").animate({
      height:'toggle'
    });
  });
  ```

* 使用队列功能

  ```
  $("button").click(function(){
    var div=$("div");
    div.animate({height:'300px',opacity:'0.4'},"slow");
    div.animate({width:'300px',opacity:'0.8'},"slow");
    div.animate({height:'100px',opacity:'0.4'},"slow");
    div.animate({width:'100px',opacity:'0.8'},"slow");
  });
  ```

## 停止动画

* stop(*stopAll,goToEnd*) 方法用于停止动画或效果，在它们完成之前

  ```
  可选的 stopAll 参数规定是否应该清除动画队列。默认是 false，即仅停止活动的动画，允许任何排入队列的动画向后执行。
  可选的 goToEnd 参数规定是否立即完成当前动画。默认是 false
  ```

# JQuery HTML

## 捕获

- text() - 设置或返回所选元素的文本内容
- html() - 设置或返回所选元素的内容（包括 HTML 标记）
- val() - 设置或返回表单字段的值
- attr() 方法用于获取/设置属性值
- removeAttr() 删除属性

## 设置

```
$("#btn1").click(function(){
    $("#test1").text("Hello world!");
});
$("#btn2").click(function(){
    $("#test2").html("<b>Hello world!</b>");
});
$("#btn3").click(function(){
    $("#test3").val("RUNOOB");
});
$("button").click(function(){
  $("#runoob").attr("href","http://www.runoob.com/jquery");
});
```

## 添加元素

- append() - 在被选元素的结尾插入内容
- prepend() - 在被选元素的开头插入内容
- after() - 在被选元素之后插入内容
- before() - 在被选元素之前插入内容

## 删除元素

- remove() - 删除被选元素（及其子元素）
- empty() - 从被选元素中删除子元素

## CSS类

- addClass() - 向被选元素添加一个或多个类
- removeClass() - 从被选元素删除一个或多个类
- toggleClass() - 对被选元素进行添加/删除类的切换操作
- css() - 设置或返回样式属性

## CSS方法

* 返回CSS属性

  ```
  $("p").css("background-color");
  ```

* 设置CSS属性

  ```
  $("p").css("background-color","yellow");
  ```

## 尺寸

- width() 方法设置或返回元素的宽度（不包括内边距、边框或外边距）
- height() 方法设置或返回元素的高度（不包括内边距、边框或外边距
- innerWidth() 方法返回元素的宽度（包括内边距）
- innerHeight() 方法返回元素的高度（包括内边距）
- outerWidth() 方法返回元素的宽度（包括内边距和边框）
- outerHeight() 方法返回元素的高度（包括内边距和边框

# JQuery遍历

## 祖先

* parent() 方法返回被选元素的直接父元素
* parents() 方法返回被选元素的所有祖先元素，它一路向上直到文档的根元素 (<html>)
* parentsUntil() 方法返回介于两个给定元素之间的所有祖先元素

## 后代

* children() 方法返回被选元素的所有直接子元素
* find() 方法返回被选元素的后代元素，一路向下直到最后一个后代

## 同胞

* siblings() 方法返回被选元素的所有同胞元素
* next() 方法返回被选元素的下一个同胞元素
* nextAll() 方法返回被选元素的所有跟随的同胞元素
* nextUntil() 方法返回介于两个给定参数之间的所有跟随的同胞元素

## 过滤

* first() 方法返回被选元素的首个元素
* last() 方法返回被选元素的最后一个元素
* eq() 方法返回被选元素中带有指定索引号的元素
* filter() 方法允许您规定一个标准。不匹配这个标准的元素会被从集合中删除，匹配的元素会被返回
* not() 方法返回不匹配标准的所有元素

# AJAX

## load方法

* load() 方法从服务器加载数据，并把返回的数据放入被选元素中

  * 语法

    ```
    $(selector).load(URL,data,callback);
    
    必需的 URL 参数规定您希望加载的 URL。
    
    可选的 data 参数规定与请求一同发送的查询字符串键/值对集合。
    
    可选的 callback 参数是 load() 方法完成后所执行的函数名称
    	responseTxt - 包含调用成功时的结果内容
    	statusTXT - 包含调用的状态
    	xhr - 包含 XMLHttpRequest 对象
    ```

    ```
    $("button").click(function(){
      $("#div1").load("demo_test.txt",function(responseTxt,statusTxt,xhr){
        if(statusTxt=="success")
          alert("外部内容加载成功!");
        if(statusTxt=="error")
          alert("Error: "+xhr.status+": "+xhr.statusText);
      });
    });
    ```

## get()方法与post()方法

* $.get() 方法通过 HTTP GET 请求从服务器上请求数据

  * 语法

    ```
    $.get(URL,callback);
    
    必需的 URL 参数规定您希望请求的 URL。
    
    可选的 callback 参数是请求成功后所执行的函数名。
    ```

* $.post() 方法通过 HTTP POST 请求向服务器提交数据

  * 语法

    ```
    $.post(URL,data,callback);
    
    必需的 URL 参数规定您希望请求的 URL。
    
    可选的 data 参数规定连同请求发送的数据。
    
    可选的 callback 参数是请求成功后所执行的函数名。
    ```

# 其他

* noConflict() 方法会释放对 $ 标识符的控制，这样其他脚本就可以使用它了

  ```
  $.noConflict();
  jQuery(document).ready(function(){
    jQuery("button").click(function(){
      jQuery("p").text("jQuery 仍然在工作!");
    });
  });
  ```

  







