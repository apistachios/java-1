 # HTML中的常用标签

- 独站一行的标签称作块级标签
- 不占一行的标签为行内标签
- 标签与标签之间可以组合,
- 通常情况写想要标签独占一行,推荐使用p标签

## h标签
```html
<h1>我是标题1</h1>
<h2>我是标题2</h2>
<h6>我是标题6</h6>
<h7>标题只到6,如果写成7就和普通文字效果相同</h7>
```

## p标签
段落标签,会单独占据一行
```
<p>我是p标签,我单独占一行</p>
```

## hr标签
分割线标签,会在页面上显示一条分割线
- size属性:水平线的高度
```html
<hr size="5px"/>
```

## span标签
显示文本的,不会单独占据一行
```html
<span>span标签</span>
<span>span标签</span>
```

## font标签
设置文本的大小颜色等信息,不会独占一行

- size属性:1-7,默认是3
- color属性:可以是#xxxxxx表示3原色,也可以是red.blue,green等
- 如果红绿蓝2位取值相同,可省略成1位,例如#FF1133简化为#F13

```html
<font size="10px" color="#3284c7">font字体</font>
<font size="10px" >font字体</font>
<font color="red">font字体</font>
```

## b标签
粗体标签,不会独占一行
```html
<b>粗体</b>
```

## i标签
斜体标签,不会独占一行
```html
<i>斜体标签</i>
```

## br标签
换行标签
```html
<b>粗体</b>
<br>
<i>斜体</i>
```

## img标签
显示图片的标签,不会独占一行  

- `src`属性:路径,注意路径问题  
- `alt`属性:图片无法显示的时候显示的文字  
- `width`属性:设置宽,可以使像素值,也可以是百分比  
- `height`属性:设置高,可以使像素值,也可以是百分比  
- `title`属性:鼠标移上去后显示名字  

我们一般会将所有图片都存放在img文件夹下便于管
理
```html
<img alt="图片挂了" src="img/1.png">
<img alt="图片挂了" src="img/1.png" title="标题" width="100%" height="100%">
```

## a标签

超链接标签,不会独占一行  
- `href`属性,指定点击后跳转的路径(url),如果需要点击后
没有反应,需要写为: `javascript:void(0)`
- `target`属性:指定跳转模式,`_blank`表示新建窗口,`_self`表示当前页,默认是`_self`

```html
<a href="http:/baidu.com" target="_blank">百度一下</a>
<!--点击不跳转-->
<a href="javascript:void(0)" >百度一下</a>
```

## ol标签

有序列表,是组合标签,ol内部嵌套li标签  
- `type`属性:取值范围,”A”,”a”,”I”,”i”,”1”

```html
<ol type="A">
    <li>吃得好</li>
    <li>学习好</li>
    <li>身体还得棒</li>
</ol>
```

## ul标签

无序列表,是组合标签,ul内部嵌套li标签  
- `type`属性:取值范围是,`disc`(实心圆),`circle`(空心
圆),`square`(方块)

```html
<ul type="square">
    <li>北京</li>
    <li>上海</li>
    <li>天津</li>
</ul>
```

## iframe标签

`iframe` 元素会创建包含另外一个文档的内联框架(即
行内框架)。  
- `name`属性:指定iframe的名称,如果 a 标签的中的`target` 属性值是其 `name` 属性的时候,点击a标签,对应的链接内容会出现在 `iframe` 中  
- `src`属性:指的是`iframe`中显示的内容的连接  
- `frameborder`属性:取值为 0(不显示边框) 和 1(显示边框)   
- `scrolling`属性:取值为 yes(可以滚动) no(不能滚动)auto(自动)
- `width`属性:表示宽度  
- `height`属性:表示高度

```html
<a target="pageBox" href="http://www.baidu.com">百度</a>
<a target="pageBox" href="http://www.sina.com">新浪</a>
<a target="pageBox" href="http://www.hao123.com">网址大全</a>
<br>
<iframe name="pageBox" src="http://www.baidu.com" height="800px" width="100%" 
     frameborder="0" scrolling="yes"></iframe>
```

## table标签

表格标签,是组合标签,内置有很多子标签

### table标签的组成  
 - `<table>` 父标签,相当于表格容器
 - `<caption>` 表格的标题,写在 `<table>` 内的第一行用于指定表格的标题,会显示的表格正上方
 - `<tr>` 表示表格一行
 - `<th>` 表格每一列的标题,写在 `<tr>` 内
 - `<td>` 表格的每一个单元格,写在 `<tr>` 内
```html
    <table>
        <caption>
            <h2>普通表格</h2></caption>
        <tr>
            <th>标题1</th>
            <th>标题2</th>
            <th>标题3</th>
        </tr>
        <tr>
            <td>1.1</td>
            <td>1.2</td>
            <td>1.3</td>
        </tr>
        <tr>
            <td>2.1</td>
            <td>3.2</td>
            <td>2.3</td>
        </tr>
    </table>
```

### table标签的属性  

 - `border`:表格边框的宽度
 - `width`:宽度,可以是像素也可以是百分比
 - `height`:高度,可以是像素也可以是百分比
 - `align`:水平对齐方式 常用`left` `center` `right`
 - `valign`:垂直对齐方式 常用`top` `middle` `bottom`
 - `cellspacing`:外边距,单元格与单元格之间的距离
 - `cellpadding`:内边距,单元格内容与单元格之间的距离
 - bgcolor:背景颜色

### table属性注意点
 1.  宽度和高度可以设置`table`标签和`td`标签  
	1. `table`设置`width`和`height`设置表格宽度和高度 
	2. `td`设置`width`和`height`,只会影响当前单元格,不会影响表格的宽高
  2.  水平对齐  
	1. 水平对齐可以设置`table` `tr` `td `
	2. `table`设置`align`,可以控制表格在水平方向的水平对齐方式  
	3. `tr`设置`align`,可以控制当前行所有单元格内容的水平对齐方式  
	4. `td`设置`align`,设置之前按照`tr`的对齐方式,设置后是控制当前单元格内容在水平方向的对齐方式
 3.  垂直对齐  
	1. 垂直对齐可以设置`tr` `td  `
	2. `tr`设置`valign`,可以控制当前行所有单元格内容的垂直对齐方式  
	3. `td`设置`valign`,设置之前按照`tr`的对齐方式,设置后是控制当前单元格内容在垂直方向的对齐方式
 4. 单元格和单元格之间的距离叫外边距  
	1. 外边距`cellspacing`只能给`table`设置,默认情况下外边距是2px
 5. 单元格内容和单元格之间的距离叫做内边距  
	1. 内边距`cellpadding`只能给`table`设置,默认是1px
 6.  背景颜色  
	1. `table` `tr` `td` 都可以设置  
	2. `table`设置整个表格背景颜色,`tr`设置当前行,`td`设置单元格  
	3. 如果都进行设置,就近原则
 7. 单元格的合并,对于`td`而言  
	1. 水平方向上占据的列数 `colspan`
	2. 垂直方向上占据的行数 `owspan`

### 细线表格  
- 去掉边框
- 设置表格背景颜色为black
- 设置单元格背景颜色为white
- 设置外边距为1px

```html
<table bgcolor="black" width="200px" height="300" cellpadding="1px" cellspacing="1px">
	<caption><h2>细线表格</h2></caption>
    <tr bgcolor="White">
        <td colspan="2">row 1, cell 1</td>
        <td>row 1, cell 2</td>
    </tr>
    <tr bgcolor="White">
        <td>2.1</td>
        <td>row 2, cell 2</td>
        <td rowspan="2">2.3</td>
    </tr>
    <tr bgcolor="White">
        <td>2.1</td>
        <td>row 2, cell 2</td>
    </tr>
</table>
```

## div标签

普通的标签,独自使用没有任何效果,主要用于网页中
区域的划分,会单独占据一行.
```html
<div>hello world</div>
```

## 标签的嵌套
```html
<b> <font size="10px" color="#3284c7">font字体</font></b>
```










