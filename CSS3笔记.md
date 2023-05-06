# 边框

+ border-image	设置所有边框图像的速记属性。	
  + -border-image-source	用于指定要用于绘制边框的图像的位置
  + -border-image-sli ce	图像边界向内偏移
  + -border-image-width	图像边界的宽度
  + -border-image-outset	用于指定在边框外部绘制 border-image-area 的量
  + -border-image-repeat	用于设置图像边界是否应重复（repeat）、拉伸（stretch）或铺满（round）。
+ border-radius	一个用于设置所有四个边框- *-半径属性的速记属性
  + 四个值: 第一个值为左上角，第二个值为右上角，第三个值为右下角，第四个值为左下角。
  + 三个值: 第一个值为左上角, 第二个值为右上角和左下角，第三个值为右下角
  + 两个值: 第一个值为左上角与右下角，第二个值为右上角与左下角
  + 一个值： 四个圆角值相同
+ box-shadow	附加一个或多个下拉框的阴影 (box-shadow: h-shadow v-shadow blur spread color inset)
  + -h-shadow	必需的。水平阴影的位置。允许负值
  + -v-shadow	必需的。垂直阴影的位置。允许负值
  + -blur	可选。模糊距离
  + -spread	可选。阴影的大小
  + -color	可选。阴影的颜色。在CSS颜色值寻找颜色值的完整列表
  + -inset	可选。从外层的阴影（开始时）改变阴影内侧阴影

# 背景

+ background-image	属性添加背景图片。
+ background-size		规定背景图片的尺寸。
+ background-origin	规定背景图片的定位区域。
  + (content-box, padding-box,和 border-box区域内可以放置背景图像。)

+ background-clip		规定背景的绘制区域。

# 渐变

+ 线性渐变：
  + background-image: linear-gradient(direction, color-stop1, color-stop2, ...);
    	direction(to bottom、to top、to right、to left、to bottom right，等等)

+ 径向渐变：
  + background-image: radial-gradient(shape size at position, start-color, ..., last-color);

# 文本效果

+ text-shadow		属性适用于文本阴影。
+ box-shadow 		属性适用于盒子阴影	
+ text-overflow	规定当文本溢出包含元素时发生的事情。

# 字体

* @font-face
+ font-family	name	必需。规定字体的名称。
  + src	URL				必需。定义字体文件的 URL。

# 2D转换

* transform:
+ translate(X,Y)方法，根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。
  + rotate()方法，在一个给定度数顺时针旋转的元素。负值是允许的，这样是元素逆时针旋转。
  + scale(X,Y)方法，该元素增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数
  + skew() 方法，包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。
  + matrix 方法有六个参数，包含旋转，缩放，移动（平移）和倾斜功能。
* -webkit-transform:/* Safari and Chrome */

# 3D转换

* 转换属性

  + transform	向元素应用 2D 或 3D 转换。	
  + transform-origin	允许你改变被转换元素的位置。
  + transform-style	规定被嵌套元素如何在 3D 空间中显示。
  + perspective	规定 3D 元素的透视效果。
  + perspective-origin	规定 3D 元素的底部位置。
  + backface-visibility	定义元素在不面对屏幕时是否可见。		
  
* 3d转换方法

  + matrix3d(n,n,n,n,n,n,n,n,n,n,n,n,n,n,n,n)	定义 3D 转换，使用 16 个值的 4x4 矩阵。
  + translate3d(x,y,z)	定义 3D 转化。
  + translateX(x)	定义 3D 转化，仅使用用于 X 轴的值。
  + translateY(y)	定义 3D 转化，仅使用用于 Y 轴的值。
  + translateZ(z)	定义 3D 转化，仅使用用于 Z 轴的值。
  + scale3d(x,y,z)	定义 3D 缩放转换。
  + scaleX(x)	定义 3D 缩放转换，通过给定一个 X 轴的值。
  + scaleY(y)	定义 3D 缩放转换，通过给定一个 Y 轴的值。
  + scaleZ(z)	定义 3D 缩放转换，通过给定一个 Z 轴的值。
  + rotate3d(x,y,z,angle)	定义 3D 旋转。
  
  + rotateX(angle)	定义沿 X 轴的 3D 旋转。
  + rotateY(angle)	定义沿 Y 轴的 3D 旋转。
  + rotateZ(angle)	定义沿 Z 轴的 3D 旋转。
  + perspective(n)	定义 3D 转换元素的透视视图。

# 过渡

+ transition	简写属性，用于在一个属性中设置四个过渡属性。
+ transition-property	规定应用过渡的 CSS 属性的名称。	
+ transition-duration	定义过渡效果花费的时间。默认是 0。	
+ transition-timing-function	规定过渡效果的时间曲线。默认是 "ease"。	
+ transition-delay	规定过渡效果何时开始。默认是 0。	

# 动画

* @keyframes 规则是创建动画。

* @keyframes 规则内指定一个 CSS 样式和动画将逐步从目前的样式更改为新的样式。

  ```
  请用百分比来规定变化发生的时间，或用关键词 "from" 和 "to"，等同于 0% 和 100%。
  0% 是动画的开始，100% 是动画的完成。
  ```

* animation: name duration timing-function delay iteration-count direction fill-mode play-state

  + animation-name	指定要绑定到选择器的关键帧的名称
  + animation-duration	动画指定需要多少秒或毫秒完成
  + animation-timing-function	设置动画将如何完成一个周期
  + animation-delay	设置动画在启动前的延迟间隔。
  + animation-iteration-count	定义动画的播放次数。
  + animation-direction	指定是否应该轮流反向播放动画。
  + animation-fill-mode	规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式
  + animation-play-state	指定动画是否正在运行或已暂停

# 多列

+ column-count 属性指定了需要分割的列数。
+ column-gap 属性指定了列与列间的间隙。
+ column-rule-style 属性指定了列与列间的边框样式：
+ column-rule-width 属性指定了两列的边框厚度:
+ column-rule-color 属性指定了两列的边框颜色：
+ column-rule 属性是 column-rule-* 所有属性的简写。
+ column-span 指定元素跨列多少
+ column-width 属性指定了列的宽度

# 用户界面

+ appearance	允许您使一个元素的外观像一个标准的用户界面元素	
+ box-sizing	允许你以适应区域而用某种方式定义某些元素
+ icon	为创作者提供了将元素设置为图标等价物的能力。
+ nav-down	指定在何处使用箭头向下导航键时进行导航	
+ nav-index	指定一个元素的Tab的顺序	
+ nav-left	指定在何处使用左侧的箭头导航键进行导航	
+ nav-right	指定在何处使用右侧的箭头导航键进行导航
+ nav-up	指定在何处使用箭头向上导航键时进行导航	
+ outline-offset	外轮廓修饰并绘制超出边框的边缘	
+ resize	指定一个元素是否是由用户调整大小

# 弹性盒子内容

* `flex-direction` 属性指定了弹性子元素在父容器中的位置。
+ flex-direction: row | row-reverse | column | column-reverse
    + row：横向从左到右排列（左对齐），默认的排列方式。
    + row-reverse：反转横向排列（右对齐，从后往前排，最后一项排在最前面。
    + column：纵向排列。
    + column-reverse：反转纵向排列，从后往前排，最后一项排在最上面。
* justify-content 属性应用在弹性容器上，把弹性项沿着弹性容器的主轴线（main axis）对齐。
  + justify-content: flex-start | flex-end | center | space-between | space-around
    + flex-start：弹性项目向行头紧挨着填充。这个是默认值。第一个弹性项的main-start外边距边线被放置在该行的main-start边线，而后续弹性项依次平齐摆放。
    + flex-end：弹性项目向行尾紧挨着填充。第一个弹性项的main-end外边距边线被放置在该行的main-end边线，而后续弹性项依次平齐摆放。
    + center：弹性项目居中紧挨着填充。（如果剩余的自由空间是负的，则弹性项目将在两个方向上同时溢出）。
    + space-between：弹性项目平均分布在该行上。如果剩余空间为负或者只有一个弹性项，则该值等同于flex-start。否则，第1个弹性项的外边距和行的main-start边线对齐，而最后1个弹性项的外边距和行的main-end边线对齐，然后剩余的弹性项分布在该行上，相邻项目的间隔相等。
    + space-around：弹性项目平均分布在该行上，两边留有一半的间隔空间。如果剩余空间为负或者只有一个弹性项，则该值等同于center。否则，弹性项目沿该行分布，且彼此间隔相等（比如是20px），同时首尾两边和弹性容器之间留有一半的间隔（1/2*20px=10px）。
* `align-items` 设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方+ 
  + align-items: flex-start | flex-end | center | baseline | stretch
    + flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
    + flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
    + center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
    + baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
    + stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。	
* **flex-wrap** 属性用于指定弹性盒子的子元素换行方式
+ flex-wrap: nowrap|wrap|wrap-reverse|initial|inherit;
    + nowrap - 默认， 弹性容器为单行。该情况下弹性子项可能会溢出容器。
    + wrap - 弹性容器为多行。该情况下弹性子项溢出的部分会被放置到新行，子项内部会发生断行
    + wrap-reverse -反转 wrap 排列。
* `align-content` 属性用于修改 `flex-wrap` 属性的行为。类似于 `align-items`, 但它不是设置弹性子元素的对齐，而是设置各个行的对齐。
+ align-content: flex-start | flex-end | center | space-between | space-around | stretch
    + stretch - 默认。各行将会伸展以占用剩余的空间。
    + flex-start - 各行向弹性盒容器的起始位置堆叠。
    + flex-end - 各行向弹性盒容器的结束位置堆叠
    + center -各行向弹性盒容器的中间位置堆叠。
    + space-between -各行在弹性盒容器中平均分布。
    + space-around - 各行在弹性盒容器中平均分布，两端保留子元素与子元素之间间距大小的一半。
* `align-self` 属性用于设置弹性元素自身在侧轴（纵轴）方向上的对齐方式。
+ align-self: auto | flex-start | flex-end | center | baseline | stretch
    + auto：如果'align-self'的值为'auto'，则其计算值为元素的父元素的'align-items'值，如果其没有父元素，则计算值为'stretch'。
    + flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
    + flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
    + center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
    + baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
    + stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

# 多媒体查询

```
@media not|only mediatype and (expressions) {
    CSS 代码...;
}

not: not是用来排除掉某些特定的设备的，比如 @media not print（非打印设备）。

only: 用来定某种特别的媒体类型。对于支持Media Queries的移动设备来说，如果存在only关键字，移动设备的Web浏览器会忽略only关键字并直接根据后面的表达式应用样式文件。对于不支持Media Queries的设备但能够读取Media Type类型的Web浏览器，遇到only关键字时会忽略这个样式文件。

all: 所有设备，这个应该经常看到。
```

* 对媒体类型

  + all	用于所有多媒体类型设备
  + print	用于打印机
  + screen	用于电脑屏幕，平板，智能手机等。
  + speech	用于屏幕阅读器
  
* 多媒体功能

  + aspect-ratio	定义输出设备中的页面可见区域宽度与高度的比率
  + color	定义输出设备每一组彩色原件的个数。如果不是彩色设备，则值等于0
  + color-index	定义在输出设备的彩色查询表中的条目数。如果没有使用彩色查询表，则值等于0
  + device-aspect-ratio	定义输出设备的屏幕可见宽度与高度的比率。
  + device-height	定义输出设备的屏幕可见高度。
  + device-width	定义输出设备的屏幕可见宽度。
  + grid	用来查询输出设备是否使用栅格或点阵。
  + height	定义输出设备中的页面可见区域高度。
  + max-aspect-ratio	定义输出设备的屏幕可见宽度与高度的最大比率。
  + max-color	定义输出设备每一组彩色原件的最大个数。
  + max-color-index	定义在输出设备的彩色查询表中的最大条目数。
  + max-device-aspect-ratio	定义输出设备的屏幕可见宽度与高度的最大比率。
  + max-device-height	定义输出设备的屏幕可见的最大高度。
  + max-device-width	定义输出设备的屏幕最大可见宽度。
  + max-height	定义输出设备中的页面最大可见区域高度。
  + max-monochrome	定义在一个单色框架缓冲区中每像素包含的最大单色原件个数。
  + max-resolution	定义设备的最大分辨率。
  + max-width	定义输出设备中的页面最大可见区域宽度。
  + min-aspect-ratio	定义输出设备中的页面可见区域宽度与高度的最小比率。
  + min-color	定义输出设备每一组彩色原件的最小个数。
  + min-color-index	定义在输出设备的彩色查询表中的最小条目数。
  + min-device-aspect-ratio	定义输出设备的屏幕可见宽度与高度的最小比率。
  + min-device-width	定义输出设备的屏幕最小可见宽度。
  + min-device-height	定义输出设备的屏幕的最小可见高度。
  + min-height	定义输出设备中的页面最小可见区域高度。
  + min-monochrome	定义在一个单色框架缓冲区中每像素包含的最小单色原件个数
  + min-resolution	定义设备的最小分辨率。
  + min-width	定义输出设备中的页面最小可见区域宽度。
  + monochrome	定义在一个单色框架缓冲区中每像素包含的单色原件个数。如果不是单色设备，则值等于0
  + orientation	定义输出设备中的页面可见区域高度是否大于或等于宽度。
  + resolution	定义设备的分辨率。如：96dpi, 300dpi, 118dpcm
  + scan	定义电视类设备的扫描工序。
  + width	定义输出设备中的页面可见区域宽度。
  
  