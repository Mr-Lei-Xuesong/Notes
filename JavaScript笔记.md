# 输出

+ 使用 window.alert() 写入警告框
+ 使用window.confirm() 确认框
+ 使用window.prompt() 输入框
+ 使用 document.write() 写入 HTML 输出
+ 使用 innerHTML 写入 HTML 元素
+ 使用 console.log() 写入浏览器控制台

# 常见的HTML事件

+ onchange	HTML 元素改变
+ onclick	用户点击 HTML 元素
+ onmouseover	用户在一个HTML元素上移动鼠标
+ onmouseout	用户从一个HTML元素上移开鼠标
+ onkeydown	用户按下键盘按键
+ onload	浏览器已完成页面的加载

# 字符串方法

* length 属性返回字符串的长度：

* indexOf() 方法返回字符串中指定文本**首次**出现的索引（位置）

* lastIndexOf() 方法返回指定文本在字符串中**最后**一次出现的索引

* search() 方法搜索特定值的字符串，并返回匹配的位置

* slice() 提取字符串的某个部分并在新字符串中返回被提取的部分。

  该方法设置两个参数：起始索引（开始位置），终止索引（结束位置）。

* substring() 类似于 slice()。

  不同之处在于 substring() 无法接受负的索引。

* substr() 类似于 slice()。

  不同之处在于第二个参数规定被提取部分的*长度*。

* substr() 类似于 slice()。

  不同之处在于第二个参数规定被提取部分的*长度*。

+ replace() 方法用另一个值替换在字符串中指定的值：
+ 通过 toUpperCase() 把字符串转换为大写
+ 通过 toLowerCase() 把字符串转换为小写
+ concat() 连接两个或多个字符串
+ concat() 方法可用于代替加运算符
+ trim() 方法删除字符串两端的空白符
+ charAt() 方法返回字符串中指定下标（位置）的字符串
+ charCodeAt() 方法返回字符串中指定索引的字符 unicode 编码

# 数值方法

+ toString() 以字符串返回数值。
+ isNaN() 函数用于检查其参数是否是非数字值。如果参数值为 NaN 或字符串、对象、undefined等非数字值则返回 true, 否则返回 false。
+ toExponential() 返回字符串值，它包含已被四舍五入并使用指数计数法的数字。
+ toFixed() 返回字符串值，它包含了指定位数小数的数字
+ toPrecision() 返回字符串值，它包含了指定长度的数字
+ valueOf() 以数值返回数值
+ Number() 可用于把 JavaScript 变量转换为数值
+ Number() 还可以把日期转换为数字：
+ parseInt() 解析一段字符串并返回数值。允许空格。只返回首个数字：
+ parseFloat() 解析一段字符串并返回数值。允许空格。只返回首个数字：
+ MAX_VALUE 返回 JavaScript 中可能的最大数字。
+ MIN_VALUE 返回 JavaScript 中可能的最小数字。

# 数组方法

* toString() 把数组转换为数组值（逗号分隔）的字符串。
* join() 方法也可将所有数组元素结合为一个字符串。
* pop() 方法从数组中删除最后一个元素：
* push() 方法（在数组结尾处）向数组添加一个新的元素：
* shift() 方法会删除首个数组元素，并把所有其他元素“位移”到更低的索引。
* unshift() 方法（在开头）向数组添加新元素，并“反向位移”旧元素 
* concat() 方法通过合并（连接）现有数组来创建一个新数组：
* slice() 方法用数组的某个片段切出新数组。

# 数组排序

+ sort() 方法以字母顺序对数组进行排序：
+ reverse() 方法反转数组中的元素。
+ Math.max.apply 来查找数组中的最高值：
+ Math.min.apply 来查找数组中的最低值

# 数组迭代

* Array.forEach() 方法为每个数组元素调用一次函数（回调函数）

* Array.map()

  ```
  var numbers1 = [45, 4, 9, 16, 25];
  var numbers2 = numbers1.map(myFunction);
  
  document.getElementById("demo").innerHTML = numbers2;
  
  function myFunction(value, index, array) {
    return value;
  }
  ```

* Array.filter()

  ```
  var numbers = [45, 4, 9, 16, 25];
  var over18 = numbers.filter(myFunction);
  
  document.getElementById("demo").innerHTML = over18;
  
  function myFunction(value, index, array) {
    return value > 18;
  }
  ```

* Array.reduce()

  ```
  var numbers = [45, 4, 9, 16, 25];
  var sum = numbers.reduce(myFunction);
  
  document.getElementById("demo").innerHTML = "总和是：" + sum;
  
  function myFunction(total, value, index, array) {
    return total + value;
  }
  ```

* Array.every() 方法检查所有数组值是否通过测试
* Array.some() 方法检查某些数组值是否通过了测试
* Array.indexOf() 方法在数组中搜索元素值并返回其位置
* Array.lastIndexOf() 与 Array.indexOf() 类似，但是从数组结尾开始搜索
* Array.find() 方法返回通过测试函数的第一个数组元素的值
* Array.findIndex() 方法返回通过测试函数的第一个数组元素的索引

# 日期

* new Date() 用当前日期和时间创建新的日期对象

* new Date(year, month, day, hours, minutes, seconds, milliseconds) 用指定日期和时间创建新的日期对象。

  ```
  7个数字分别指定年、月、日、小时、分钟、秒和毫秒（按此顺序）
  6个数字指定年、月、日、小时、分钟、秒
  5个数字指定年、月、日、小时和分钟
  4个数字指定年、月、日和小时
  3 个数字指定年、月和日
  2个数字指定年份和月份
  
  ```

* new Date(milliseconds)  创建一个零时加毫秒的新日期对象

* new Date(date string) 从日期字符串创建一个新的日期对象

  * toUTCString() 方法将日期转换为 UTC 字符串（一种日期显示标准）
  * toDateString() 方法将日期转换为更易读的格式

# 获取日期方法

* getDate() 以数值返回天（1-31）
* getDay() 以数值获取周名（0-6）
* getFullYear() 获取四位的年（yyyy）
* getHours() 获取小时（0-23）
* getMilliseconds() 获取毫秒（0-999） 
* getMinutes()  获取分（0-59） 
  * getMonth() 获取月（0-11）
* getSeconds() 获取秒（0-59） 
* getTime() 获取时间（从 1970 年 1 月 1 日至今）

# 设置日期方法

* setDate() 以数值（1-31）设置日
* setFullYear()设置年（可选月和日）
*  setHours() 设置小时（0-23）
* setMilliseconds() 设置毫秒（0-999）
* setMinutes() 设置分（0-59）
* setMonth() 设置月（0-11）      
*  setSeconds() 设置秒（0-59）                            
*  setTime()  设置时间（从 1970 年 1 月 1 日至今的毫秒数）

# 数学Math

* Math.round(x) 的返回值是 x 四舍五入为最接近的整数：
* Math.pow(x, y) 的返回值是 x 的 y 次幂：
* Math.sqrt(x) 返回 x 的平方根：
* Math.abs(x) 返回 x 的绝对（正）值：
* Math.ceil(x) 的返回值是 x *上舍入*最接近的整数：
* Math.floor(x) 的返回值是 x *下舍入*最接近的整数
* Math.sin(x) 返回角 x（以弧度计）的正弦（介于 -1 与 1 之间的值）
* Math.cos(x) 返回角 x（以弧度计）的余弦（介于 -1 与 1 之间的值）
* Math.min() 和 Math.max() 可用于查找参数列表中的最低或最高值
* Math.random() 返回介于 0（包括） 与 1（不包括） 之间的随机数

```
Math.E          // 返回欧拉指数（Euler's number）
Math.PI         // 返回圆周率（PI）
Math.SQRT2      // 返回 2 的平方根
Math.SQRT1_2    // 返回 1/2 的平方根
Math.LN2        // 返回 2 的自然对数
Math.LN10       // 返回 10 的自然对数
Math.LOG2E      // 返回以 2 为底的 e 的对数（约等于 1.414）
Math.LOG10E     // 返回以 10 为底的 e 的对数（约等于0.434）
```

# Math对象方法

+ abs(x)	返回 x 的绝对值
+ acos(x)	返回 x 的反余弦值，以弧度计
+ asin(x)	返回 x 的反正弦值，以弧度计
+ atan(x)	以介于 -PI/2 与 PI/2 弧度之间的数值来返回 x 的反正切值。
+ atan2(y,x)	返回从 x 轴到点 (x,y) 的角度
+ ceil(x)	对 x 进行上舍入
+ cos(x)	返回 x 的余弦
+ exp(x)	返回 Ex 的值
+ floor(x)	对 x 进行下舍入
+ log(x)	返回 x 的自然对数（底为e）
+ max(x,y,z,...,n)	返回最高值
+ min(x,y,z,...,n)	返回最低值
+ pow(x,y)	返回 x 的 y 次幂
+ random()	返回 0 ~ 1 之间的随机数
+ round(x)	把 x 四舍五入为最接近的整数
+ sin(x)	返回 x（x 以角度计）的正弦
+ sqrt(x)	返回 x 的平方根
+ tan(x)	返回角的正切

# 随机

* Math.random() 返回 0（包括） 至 1（不包括） 之间的随机数

* Math.random() 与 Math.floor() 一起使用用于返回随机整数

  ```
  Math.floor(Math.random() * 10);		// 返回 0 至 9 之间的数
  ```

# 正则表达式

* 语法

  ```
  /正则表达式主体/修饰符(可选)
  var patt = /runoob/i
  
  实例解析：
  /runoob/i  是一个正则表达式。
  
  runoob  是一个正则表达式主体 (用于检索)。
  
  i	执行对大小写不敏感的匹配。
  g	执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。
  m	执行多行匹配。
  ```

* 方括号用于查找某个范围内的字符：

  ```
  [abc]	查找方括号之间的任何字符。
  [0-9]	查找任何从 0 至 9 的数字。
  (x|y)	查找任何以 | 分隔的选项。
  ```

* 元字符是拥有特殊含义的字符：

  ```
  \d	查找数字。
  \s	查找空白字符。
  \b	匹配单词边界。
  \w	匹配单个字符
  \uxxxx	查找以十六进制数 xxxx 规定的 Unicode 字符。
  ```

* 量词

  ```
  n+	匹配任何包含至少一个 n 的字符串。
  n*	匹配任何包含零个或多个 n 的字符串。
  n?	匹配任何包含零个或一个 n 的字符串。
  ```

# RegExp对象

* test() 方法用于检测一个字符串是否匹配某个模式，如果字符串中含有匹配的文本，则返回 true，否则返回 false。

* exec() 方法用于检索字符串中的正则表达式的匹配。

  该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null。

# 严格模式

* "use strict"; 定义 JavaScript 代码应该以“严格模式”执行

# 函数call()

```
var person = {
  fullName: function() {
    return this.firstName + " " + this.lastName;
  }
}
var person1 = {
  firstName:"Bill",
  lastName: "Gates"
}
var person2 = {
  firstName:"Steve",
  lastName: "Jobs"
}
var x = person.fullName.call(person1); 
document.getElementById("demo").innerHTML = x; 
```

# HTML DOM

* 查找HTML元素

  ```
  document.getElementById(id)	通过元素 id 来查找元素
  document.getElementsByTagName(name)	通过标签名来查找元素
  document.getElementsByClassName(name)	通过类名来查找元素
  ```

* 改变HTML元素

  ```
  element.innerHTML = new html content	改变元素的 inner HTML
  element.attribute = new value	改变 HTML 元素的属性值
  element.setAttribute(attribute, value)	改变 HTML 元素的属性值
  element.style.property = new style	改变 HTML 元素的样式
  ```

* 添加和删除元素

  ```
  document.createElement(element)	创建 HTML 元素
  document.removeChild(element)	删除 HTML 元素
  document.appendChild(element)	添加 HTML 元素
  document.replaceChild(element)	替换 HTML 元素
  document.write(text)	写入 HTML 输出流
  ```

* 添加事件处理程序

  ```
  document.getElementById(id).onclick = function(){code}	向 onclick 事件添加事件处理程序
  ```

# DOM事件

* onclick	点击
* onload    用户进入页面时被触发
* onunload   用户离开页面时被触发
* onchange   常结合对输入字段的验证来使用
* onmouseover   用户的鼠标移至元素上方时触发函数
* onmouseout     用户的鼠标移出元素时触发函数
* onmousedown  用户鼠标点击
* onmouseup        用户鼠标释放

# DOM事件监听器

* addEventListener() 方法为指定元素指定事件处理程序，附加事件处理程序而不会覆盖已有的事件处理程序

  * 语法

    ```
    element.addEventListener(event, function, useCapture);
    
    第一个参数是事件的类型（比如 "click" 或 "mousedown"）。
    
    第二个参数是当事件发生时我们需要调用的函数。
    
    第三个参数是布尔值，指定使用事件冒泡还是事件捕获。此参数是可选的。
    
    注意：请勿对事件使用 "on" 前缀；请使用 "click" 代替 "onclick"。
    ```

* removeEventListener() 方法会删除已通过 addEventListener() 方法附加的事件处理程序

# Timing事件

* setTimeout(*function*, *milliseconds*)
  * 在等待指定的毫秒数后执行函数。

* clearTimeout() 方法停止执行 setTimeout() 中规定的函数

* setInterval(*function*, *milliseconds*)
  * 等同于 setTimeout()，但持续重复执行该函数。

# AJAX

```
1. 网页中发生一个事件（页面加载、按钮点击）
2. 由 JavaScript 创建 XMLHttpRequest 对象
3. XMLHttpRequest 对象向 web 服务器发送请求
4. 服务器处理该请求
5. 服务器将响应发送回网页
6. 由 JavaScript 读取响应
7. 由 JavaScript 执行正确的动作（比如更新页面）
```

* XMLHttpRequest对象方法

  | new XMLHttpRequest()                          | 创建新的 XMLHttpRequest 对象                                 |
  | --------------------------------------------- | ------------------------------------------------------------ |
  | abort()                                       | 取消当前请求                                                 |
  | getAllResponseHeaders()                       | 返回头部信息                                                 |
  | getResponseHeader()                           | 返回特定的头部信息                                           |
  | open(*method*, *url*, *async*, *user*, *psw*) | 规定请求method：请求类型 GET 或 POSTurl：文件位置async：true（异步）或 false（同步）user：可选的用户名称psw：可选的密码 |
  | send()                                        | 将请求发送到服务器，用于 GET 请求                            |
  | send(*string*)                                | 将请求发送到服务器，用于 POST 请求                           |
  | setRequestHeader()                            | 向要发送的报头添加标签/值对                                  |

* XMLHttpRequest对象属性

  | onreadystatechange | 定义当 readyState 属性发生变化时被调用的函数                 |
  | ------------------ | ------------------------------------------------------------ |
  | readyState         | 保存 XMLHttpRequest 的状态。0：请求未初始化1：服务器连接已建立2：请求已收到3：正在处理请求4：请求已完成且响应已就绪 |
  | responseText       | 以字符串返回响应数据                                         |
  | responseXML        | 以 XML 数据返回响应数据                                      |
  | status             | 返回请求的状态号200: "OK"403: "Forbidden"404: "Not Found"    |
  | statusText         | 返回状态文本（比如 "OK" 或 "Not Found"）                     |

# JSON

* **在从 web 服务器接收数据时，数据永远是字符串**
  
* JSON.parse()  通过 JSON.parse() 解析数据，这些数据会成为 JavaScript 对象
  
* **在向 web 服务器发送数据时，数据必须是字符串**

  * JSON.stringify() 把 JavaScript 对象转换为字符串

  











