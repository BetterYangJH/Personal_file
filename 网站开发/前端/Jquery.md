# 使用 

## 引入

```javascript
<script src="js/jquery-1.11.3.min.js"></script>
```

## 获取元素

形如：$(选择器).选择器/属性操作/方法操作，支持链式

工厂函数 \$() : 用于获取元素节点，创建元素节点或将原生JS对象转换为jquery对象,返回 jQuery 对象，jQuery 对象实际是一个类数组对象。原生JS对象与jQuery对象的属性和方法不能混用(不能调用对方的方法)。可以互相转换 :

*   原生JS-->jQuery对象
    $(原生对象)，返回 jQuery 对象

*   jQuery对象-->原生JS

    \$("")\[index]

    \$("").get(index);

### 选择器

基础选择器

```text
标签选择器：$("div")
ID 选择器：$("#d1")
类选择器：$(".c1")
群组选择器：$("body,p,h1")
```
层级选择器

```text
后代选择器： $("div .c1")
子代选择器： $("div>span")
相邻兄弟选择器： $("h1+p")  1后第一个兄弟元素,必须是2,不是找不到
通用兄弟选择器： $("h1~h2") 1后所有满足2的兄弟元素
```
过滤/子代/层级选择器$("选择器:过滤选项")

```text
第一个元素:first 
最后一个元素:last 
下标等于index的元素:eq(index)
下标小于index的元素:lt(index)
下标大于index的元素:gt(index)

奇数下标对应的元素(偶数行):odd
偶数下标对应的元素(奇数行):even
否定筛选,除()中选择器外,其他元素:not(选择器)

第一个子元素:first-child
最后一个子元素:last-child
第n个子元素(从1开始):nth-child(n)

父元素parent()
祖先元素/满足选择器的祖先元素parents()/parents('')
子元素/满足选择器的子元素children()/children("")
满足选择器的后代元素find("")
上/下一个兄弟元素prev/next()/prev/next("")
所有的兄弟元素/满足选择器的所有兄弟元素siblings()/siblings("")
```
属性选择器 [ ]

```text
包含指定属性的元素 [attrName]
属性等于指定值的元素[attrName=value]
属性不等于指定值/没有该属性的元素[attrName!=value]
 
属性值以指定字符开头的元素[attrName ^= value]
属性值以指定字符结尾的元素[attrName $= value]
属性值包含指定字符的元素[attrName *= value]
```
## 操作元素

### 操作元素内容

```javascript
设置或读取标签内容 html() //等价于原生innerHTML,操作文字和标签
设置或读取标签内容 text() //等价于innerText,操作文字
设置或读取表单元素的值 val() //等价于原生value属性
```
### 操作标签属性

设置或读取标签属性：表单元素(按钮):check用prop,其他可以用attr

attr("attrName","value") / prop("attrName","value")

attr("attrName") / prop("attrName" )

移除指定属性

removeAttr("attrName")

### 操作标签样式

操作class属性值

```javascript
addClass("className")	// 添加指定的类名
removeClass("className")// 移除指定的类名,如果参数省略,表示清空class属性值
toggleClass("className")// 如果当前元素存在指定类名,则移除;不存在则添加
```
操作行内样式

```javascript
css("属性名","属性值")  //设置行内样式
css(JSON)			 //设置一组CSS样式
```
### 操作元素

创建

```javascript
var div = $("<div></div>");	//创建元素
div.html("动态创建").attr("id","d1").css("color","red"); // 链式设置属性
// 或直接创建标签
var div = $("<h1 id='d1'>一级标题</h1>");
```
添加至页面 

```javascript
$obj.append(newObj);	// 添加为$obj最后一个子元素
$obj.prepend(newObj);	// 添加为$obj第一个子元素
$obj.after(newObj);		// 添加为$obj后面的兄弟元素
$obj.before(newObj);	// 添加为$obj前面的兄弟元素
```
移除元素 
```javascript
$obj.remove();
```
## jQuery事件处理

*   文档加载完毕：

```javascript
// 语法一 
$(document).ready(function (){})
// 语法二 
$().ready(function (){})
// 语法三 
$(function(){})
```
对应原生window.onload

onload重写会覆盖，只执行最后的

jquery重写，会依次执行

*   事件绑定方式

```js
// 语法一 
$(选择器).on("click"，function)
// 语法二
$(选择器).bind("click",function)
// 语法三
$(选择器).click(function(){})

点击事件 click
获取焦点 focus
失去焦点 blur
值改变事件 input 实时触发,记录字符改变数量
值改变事件 change 光标移出input触发,记录修改次数
```
xxx对应原生onxxx

*   this 表示事件的触发对象

    this 为原生对象只能使用原生的属性和方法，可以使用$(this)转换为jquery对象



