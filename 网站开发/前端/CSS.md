# CSS 基础
CSS：Cascading Style Sheets ，层叠样式表 ，实现网页的排版布局与样式美化

## 使用方式

*   行内样式/内联样式：HTML标签内

  ```html
<标签名 style="属性:值;属性:值;">
      
  属性值：中文必须有引号，英文可以没有
  ```
*   内嵌样式：HTML文件内，借助于style标签，实现CSS样式与HTML标签之间的分离，借助于CSS选择器匹配元素并应用样式

  ```html
  <style>
  	选择器{
  		属性:值;
        	属性:值;
       }
  </style>
  ```

*   外链样式：保存为.css文件，在html文件内通过head中使用<link>标签引用，方便修改和复用，内部写法和内嵌样式相同

  ```html
   <link rel="stylesheet" href="URL" type="text/css">
  ```

## 特征

*   层叠性：多组CSS样式共同作用于一个元素
*   继承性：后代元素可以继承祖先元素中的某些样式，大部分的文本属性都可以被继承(font-size,color等)

## 样式优先级

距离对象越近优先级越高

*   行内样式的优先级最高
*   文档内嵌与外链样式表，优先级一致，看代码和对象距离，越近优先级越高
*   浏览器默认样式和继承样式优先级较低

# 选择器 {}

通过标签名或者某些属性值到页面中选取所有相应的标签元素，为其应用样式
*   标签选择器

```css
标签名{属性:值;}
```
*   id选择器：id具有唯一性

```css
#id属性值{属性:值;}
<标签 id = '属性值'>
```
*   class选择器

```css
.class属性值 {属性:值;}
<标签 class= '属性值'>
```
1.  类选择器与其他选择器结合使用可以缩小范围，找到指定标签，例 :a.c1{ }
2.  class属性值可以写多个,共同应用类选择器的样式，例 :\<p class="c1 c2"></p>

*   群组选择器：多个选择器并列，同时设置相同属性

```css
selector1,selector2,selector3{属性:值;}
* {}表示所有标签
```
*   后代选择器

```css
selector1 selector2{属性:值;}
```
*   子代选择器

```css
selector1>selector2{属性:值;}
```
*   伪类选择器：匹配元素状态

```
:link 	 超链接访问前的状态
:visited 超链接访问后的状态
:hover	 鼠标滑过时的状态
:active  鼠标点按不抬起时的状态(激活)
:focus	 焦点状态(文本框被编辑时就称为获取焦点)
注意：多个状态同时设置，需要按顺序
```
## 选择器优先级

| 选择器       | 权重 |
| ------------ | ---- |
| 标签选择器   | 1    |
| (伪)类选择器 | 10   |
| id选择器     | 100  |

浏览器解释速度越快权重越大

选择器的权重越大优先级越高

复杂选择器最终的权重为各个选择器权重值之和

群组选择器权重以每个选择器单独的权重为准，不进行相加计算，因为互不影响						

# 盒模型

对于尺寸进行设置

## 尺寸单位

元素尺寸

-   px 像素单位(固定像素格式在不同设备屏幕可能变形)
-   % 百分比，参照父元素对应属性的值进行计算

字体尺寸

-   em 参照父元素的字体大小计算
-   rem 参照根元素(对于页面来说是body)的字体大小计算，默认1rem=16px(调整页面字体只需要调整根)

## 内容尺寸 width/height

-   一般情况下，为元素设置width/height，指定的是内容框的大小，不是实际的大小，外边还有边框或边距

-   内容溢出：内容超出元素的尺寸范围，称为溢出。默认情况下溢出部分仍然可见，可以使用overflow调整溢出部分的显示,取值如下：

    | 取值    | 作用                           |
    | ------- | ------------------------------ |
    | visible | 默认值，溢出部分可见           |
    | hidden  | 溢出部分隐藏                   |
    | scroll  | 强制在水平和垂直方向添加滚动条 |
    | auto    | 自动在溢出方向添加可用滚动条   |

## 边框 border

```css
border:width style color;

单边框设置：
border-top/bottom/left/right:width style color;
```

边框样式style：

| 样式取值 | 含义       |
| -------- | ---------- |
| solid    | 实线边框   |
| dotted   | 点线边框   |
| dashed   | 虚线边框   |
| double   | 双线边框   |
| groove   | 槽线式边框 |
| ridge    | 脊线式边框 |
| inset    | 内嵌边框   |
| outset   | 突起边框   |

三角标制作

1.  元素宽高为0，边框宽度随意
2.  三个方向透明边框，某个方向边框可见色(边框实际以梯形显示)

圆角边框

```css
border-radius:50%

border-radius圆角半径，取值为像素值或占宽高的百分比(最多是50%，此时是圆形)
一个值 	统一设置
两个值 	分别设置左上(右下)，右上(左下)
三个值 	分别设置左上，右上(左下)，右下
四个值 	分别设置左上，右上，右下，左下
从左上开始顺时针赋值，没有的和对角相同
```

### 轮廓线 outline

```css
outline:width style color
```

1.  style类型同border
2.  边框占位，轮廓不占位(可能压在其他元素之上)
3.  特殊：一般取none以取消文本输入框默认轮廓线
4.  大的会被压住，如边框5，轮廓7，border总7，边框显示5,轮廓显示2

### 阴影 shadow

#### 盒阴影

```css
box-shadow:offsetX offsetY blur (spread) color;

以浏览器窗口或元素自身左上角为原点建立坐标系
offsetX 	像素值，阴影的水平偏移距离，左负右正
offsetY 	像素值，阴影的垂直偏移距离，上负下正
blur 		像素值，表示阴影的模糊程度，值越大越模糊
spread 		选填，像素值，阴影是否需要延伸
color 		设置阴影颜色,默认为黑色
```

#### 文字阴影

```css
text-shadow:offsetX offsetY blur color;
取值使用类似盒阴影，无延伸值
```

## 内边距padding

```css
padding:width
不能取负值

20px;					一个值表示统一设置上右下左
20px 30px;				两个值表示分别设置(上下) (左右)
20px 30px 40px;			三个值表示分别设置上右下，左右保持一致
20px 30px 40px 50px;	表示分别设置上右下左
从上开始顺时针赋值，没有的和对边相同

单内边距设置：
padding-top/right/bottom/left：width
```

padding的大小计算在元素内，padding增加的必须在元素尺寸的width和height中减去，保持元素大小不变

## 外边距margin

```css
margin:width
可以取负值
margin:0; 取消默认外边距
margin:0 auto;左右自动外边距，元素在父元素内水平居中

单外边距设置：
margin-top/right/bottom/left：width
```

一般用于调整元素与元素之间的距离(各元素外边距会重叠)

### 外边距合并

相邻元素垂直方向的外边距取其中较大的值，会造成压盖

外边距溢出：子元素的margin-top作用于父元素上(外边距溢出)，因为父元素没盖子无法确定位置，子元素便以body为参照而不是父元素

解决：

```css
<body>
    <div id='div1'>
		<div></div>
	</div>
</body>

方法一：调整外层内边距
#div1{
    width：150px;
    height:150px;
    padding-left: 50px;
    padding-top:50px;
}

方法二：调整内层外边距，并设置父元素防止外边距溢出
#div1{
     width: 200px;
     height: 200px;
     background-color: blue;
     border-top: 1px solid transparent;或padding-top: 1px;
}
#div1>div{
     width: 100px;
     height: 100px;
     background-color: red;
     margin-left: 50px;
     margin-top: 50px;
}

方法三：增加没内容的表格，表格自带样式
<body>
    <div id='div1'>
		<table></table>
		<div></div>
	</div>
</body>

方法四：父元素添加空内容
#div::before{
    content:'';
    display:table;
}
```

-   块元素

    对盒模型相关属性完全支持;

    块元素垂直方向上的外边距会重叠

-   行内元素对盒模型相关属性不完全支持

    不支持width/height,不支持上下边距
    行内元素水平方向上的外边距不重叠，会累加显示

## 元素最终尺寸

标准盒模型：

```css
box-sizing:content-box;
```

*   各个属性值累加得到最终尺寸
*   元素设置width/height指定的是内容框的大小

*   最终尺寸 = width/height+padding+border+margin

特殊盒模型：

```css
box-sizing:border-box;
```

*   元素设置width/height指定的是包含边框在内的区域大小

*   width/height = width/height + padding\*2 + border\*2

*   width/height+margin

# 布局方式

*   标准流/静态流/文档流：默认布局方式，按照代码书写顺序及标签类型从上到下,从左到右依次显示

*   浮动布局：在父元素范围内浮动，一般用于水平排列

    ```css
    float:left/right
    ```

    *   设置浮动会从原始位置脱流，向左或向右依次停靠在其他元素边缘，在文档中不再占位(右后非浮动元素会补位)
    *   设置浮动，就具有块元素的特征，可以手动调整宽高，内外边距
    *   同一列：当前元素到当前行上层，下行元素向上补齐到当前行下层，会压盖
    *   同一行：同一层依次水平排列
    *   浮动元素会遮挡正常元素的位置，无法遮挡正常内容的显示，内容围绕在浮动元素周围显示
    *   子元素全部设置浮动,，会导致父元素没有内容支撑，高度为0，影响父元素背景色和背景图片展示，影响页面布局
        *   对于内容固定的元素，如果子元素都浮动，可以给父元素固定高度(例:导航栏)
        *   为父元素设置overflow:hidden;隐藏溢出内容，或显示原本元素大小
        *   在父元素的末尾添加空的块元素。设置clear:both;清除浮动(clear取值：left，right，both，需要和子元素浮动一致)

*   定位布局：结合偏移属性调整元素的显示位置

    ```css
    postion:relative/absolute/fixed/static默认值
    
    先position定位，再left,right,top,bottom修改偏移量
    relative 相对定位
    absolute 绝对定位
    fixed    固定定位
    top   	 距参照物的顶部
    right	 距参照物的右侧
    bottom	 距参照物的底部
    left	 距参照物的左侧
    ```
    *   relative：参照元素在文档中的原始位置进行偏移
        *   不会脱离文档流
        *   参照元素原始位置不变
        *   会被压盖
        *   通常用于元素微调
    *   absolute：参照离他最近的已经定位的祖先元素进行偏移，如果没有,则参照窗口进行偏移
        *   会脱离文档流
        *   在文档中不占位
        *   可以手动设置宽高
        *   用于辅助relative："父相子绝" : 父元素设置相对定位,子元素绝对定位，参照已定位的父元素偏移.
    *   fixed：参照窗口进行定位,不跟随网页滚动而滚动
        *   脱离文档流
        *   用于弹窗，广告

## 堆叠次序 

定位元素与文档中正常元素发生堆叠，已定位元素在上

同为已定位元素发生堆叠，按照 HTML 代码的书写顺序，后来者居上

可以使用 z-index(层数)属性调整已定位元素的显示位置，无单位的数值，值越大元素越靠上

# 颜色属性

英文单词：red，green，blue

三原色表示：RGB(r,g,b) ，每种颜色取值0~255

三原色+透明度表示：rgba(r,g,b,alpha) ，三原色每种取值0-255，alpha取值0(透明)-1(不透明)

十六进制表示：以#为前缀，分为长十六进制和短十六进制。

-   长十六进制：每两位为一组，代表一种三原色，例：#ff0000
-   短十六进制：由3位组成，每一位代表一种三原色，浏览器会自动对每一位进行重复扩充，例：#f00等价于#ff0000
-   配合opacity(0 ~ 1)调节不透明度

# 背景属性

## 背景颜色 color

```css
background-color: red;
```

## 背景图片

### 设置背景图片 url

```css
background-image : url("路径");
```

### 背景图片显示位置 position

默认显示在元素左上角

```css
background-position:x y;
```

取值方式 ：

*   像素值：背景图片在元素坐标系中的起点坐标
*   方位值：水平 left/center/right，垂直 top/center/bottom，如果只设置某一个方向的方位值，另外一个方向默认为center
*   百分比：横坐标 = (元素宽度 - 背景图片宽度）* x%，纵坐标 = (元素高度 - 背景图片高度) * y %
    *   左上角：0% 0% 
    *   右下角：100% 100%
    *   居中：50% 50%

### 背景图片尺寸 size

```css
background-size:width height;
```

取值方式 ：

```text
1. 像素值
	1. 500px 500px; 同时指定宽高
	2. 500px;  指定宽度，高度自适应
2. 百分比
	百分比参照元素的尺寸进行计算
	1. 50% 50%; 根据元素宽高,分别计算图片的宽高
	2. 50%; 根据元素宽度计算图片宽高,图片高度等比例缩放
```

### 背景图片重复方式 repeat

如果元素尺寸大于图片尺寸，会自动重复平铺，直至铺满整个元素

如果元素尺寸小于图片尺寸，图片默认从元素左上角开始显示，超出部分不可见

```css
background-repeat:repeat/repeat-x/repeat-y/no-repeat

repeat  默认值，沿水平和垂直方向重复平铺
repeat-x 沿X轴重复平铺
repeat-y 沿Y轴重复平铺
no-repeat 不重复平铺
```

## 简写

```css
background:color url("") repeat position;
```

严格按照顺序书写

background-size 需要单独设置

# 文本属性

## 字体

类型：可以指定多个字体名称作为备选字体，使用逗号隔开

```css
font-family:Arial,"黑体"; 
```

大小

```css
font-size:20px;
```

粗细

```css
font-weight:normal/400;bold/700
一般浏览器只能解析变粗，特殊字体才能体现出小于400的
```

斜体

```css
font-style:italic;
```

### 简写

```css
font : style weight size family;
```

严格按照顺序书写

## 文本样式

颜色

```css
color:red;
```

装饰线：可以多个，使用空格隔开

```css
text-decoration:none;

underline		下划线
overline		上划线
line-through 	 删除线
none			取消装饰线（一般用于取消超链接下划线）
```

水平位置

```css
text-align:center;

left(默认值)	左对齐
center		  居中对齐
right		  右对齐
justify		  两端对齐
```

行高：可以采用无单位的数值,代表当前字体大小的倍数

```css
line-height:30px;
```

*   可以借助行高和元素高度，调整文本垂直位置
    *   line-height = height 设置一行文本在元素中垂直居中
    *   line-height > height 文本下移显示
    *   line-height < height 文本靠上显示

### 简写

```css
font : size/line-height family;
```

# Tips

*   精灵图技术 ：为了减少网络请求，可以将所有的小图标拼接在一张图片上，一次网络请求全部得到；再借助于background-position进行背景图片位置的调整，结合大小固定的div，实现显示不同的图标


​		




​			
​			