## body主体标签

* background="设置背景图片" 
* bgproperties="fixed(背景永远跟随鼠标滑动)"
* bgcolor="设置背景底色"
* text="设置字体颜色"
* leftmargin="距离左边距离的值"
* topmargin="距离顶部距离的值"

## 文字标签

```
<font face="字体名称"></font>
<font size="字体大小"></font>
<font color="字体颜色"></font>
<h1-h6>为标题大小
<b>文字加粗</b>
<strong>文字加粗</strong>
<i>斜体</i>
<em>斜体</em>
<dfn>斜体<dfn>
<cite>斜体</cite>
<ins>底线</ins>
<strike>横线</strike>
<kbd>键盘文字</kbd>
<tt>打字体</tt>
<pre>保留与格式化文本</pre>
<hr>水平分割线   noshade(实线)
<big>这是大号字体</big>
<small>这是缩小字体</small>
<sub>下标</sub>
<sup>上标</sup>
<u>下划线</u>
<del>被删除文本</del>
```

## 段落列表标签

```
<br>换行标签
<nobr>不换行</nobr>
<p>段落标签</p>
<ul type="形状">无序列表
	<li>..</li>
</ul>
<ol type="I" start="起始值">(有序列表)
    <li type="circle">列表项</li>        //可以对列表项单个操作类型
    <li value="5">列表项</li>            //改变顺序值，影响后面
</ol>
<menu>菜单列表
	<li>..</li>
</menu>
<dir>目录列表
	<li>..</li>
</dir>
<dl>
    <dt>主目录</dt>
    	<dd>次目录</dd>
</dl>
```

## 链接标签

```
<a href="url"></a>指定链接地址
<a name="锚头"/>
<a href="#跳转到锚头">
<a href="http://www.baidu.com" target="_blank"><img src="img/图片超链接.jpg"/></a>
<a href="mailto:228389787@qq.com">邮箱地址</a>
vlink="未访问超链接的颜色"
alink="超链接点击变化的颜色"
```

## 图片标签

```
<img src="图片地址" width="宽度" height="高度" alt="备注图片" border="图片边框" vspace="垂直边距" hspace="水平边距" align="见下表">
    align常用属性值
    top:文字的中间线在图片上方
    middle:文字的中间线在图片中间
    bottom:文字的中间线在图片底部
    left:图片在文字的左侧
    right:图片在文字的右侧
    absbottom:文字的底线在图片底部
    absmiddle:文字的底线在图片中间
    baseline:英文文字基准线对齐
    texttop:英文文字上边线对齐
```

## 多媒体标签

```
<bgsound src="路径地址" loop="循环次数（true）"></bgsound>      //背景音乐
<embed src="路径地址" loop="true|false" autostart="是否自动播放" width="宽度" height="高度"> 
```

## 滚动标签

```
<marquee>滚动文字</marquee>
    direction="滚动方向" 
    behavior="滚动方式" 
    loop="滚动次数" 
    scrollamount="滚动速度" 
    scrolldelay="滚动间隔" 
    bgcolor="滚动区域背景颜色" 
    width="长度" 
    height="高度"
    hspace="水平范围" 
    vspace="垂直范围"
```

## 表格标签

```
<table align="对齐方式" width="宽度" height="高度" border="边框" bgcolor="背景颜色" background="背景图片" bodercolor="边框颜色" cellspacing="单元格间距值" cellpadding="单元格补白值">
	<caption>标题</caption>
	<thead>表头</thead>
	<tr valign="垂直对齐方式">
		<td colspan="合并列" rowspan="合并行"></td>
	</tr>
</table>
```

## 表单标签

```
<form action="表单的处理程序" method="post/get" name="表单名称" target="目标窗口打开方式">
	<input name="文本字段的名称" type="text" value="文字字段的默认取值" size="文本字段的长度" maxlength="最多字符数"/  required="为空报错">
	<input name="密码域的名称" type="password" value="密码域的默认值" size="密码域的长度" maxlength="最多字符数"/>
	<input  name="单选按钮的名称" type="radio" value="单选按钮的取值" checked="默认项"/>
	<input name="复选框的名称" type="checkbox" value="复选框的取值" checked="默认项"/>
	<input type="submit" name="按钮的名称 " value="按钮的取值" onclick="处理程序"/>
	<input type="reset" name="按钮的名称" value="按钮的取值"/>
	<input name="图像域的名称" type="image" src="图像的路径"/>
	<input name="隐藏域的名称" type="hidden" value="隐藏域的取值"/>
	<input name="文件域的名称" type="file" size="文件域的长度" maxlength="最多字符数"/>
	
	<select name="下拉菜单的名称">
		<option value="选项值">1</option>
	</select>
	
	<select name="选择列表名称" size="4">
		<option value="选项值">1</option>
	</select>
	
	<select name="选择列表名称" size="4" multiple<!--允许选择多条-->
		<option value="选项值">1</option>
	</select>
	
	<textarea name="文本区域的名称" cols="长度" rows="行数"></textarea>
</form>
```

## 框架标签

```
<iframe src="地址" width="宽" height="高" align="对齐方式" marginwidth="水平边距" marginheight="垂直边距" srcolling="是否需要滚动条">
```