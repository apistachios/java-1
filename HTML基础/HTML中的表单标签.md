# HTML中的表单标签

- 要进行发送的数据必须放在`form`中才能发送个服务器
- 把需要提交的标签设置`name`属性
- 提交的方式分为`GET`提交和`POST`提交
 - `GET`提交格式: 默认格式,`服务器地址?name属性值=value属性值&name属性值=value属性值`
 - POET提交格式在地址栏中看不见: `name属性值=value属性值&name属性值=value属性值`


- POST提交的优势
 - GET请求是将请求的数据放在URL后面的,会明文出现在地址栏中,不安全
 - URL的长度不能超过256个字节
 - POST请求是将请求是数据放在http的请求体中,安全,长度没有任何限制

## form标签

- `action`属性表示请求路径,表单提交到服务器的具体url  
- `method`属性表示请求方式一般取值为POST和GET  

```html
<form action="" method="get">
</form>
```

### input标签
用来获取用户输入信息的标签

- `type`属性有很多中,并且不同的属性显示的样式不同  
 - `text`:文本框,输入文本
 -` password`:密码框
 - `radio`:互斥的单选按钮checkbox:复选框
 - `submit`:提交按钮,将表单数据提交到服务器
 - `file`:上传文件组件
 - `image`:图片提交按钮,通过src设置图片
 - `reset`:重置按钮,恢复表单
 - `hidden`:隐藏字段,数据会发送给服务器,浏览器页面中不会显示,一般可用于发送用户的id
 - `button`:普通按钮,一般和js结合使用
- `name`属性,表单数据提交到服务器必须提供name属性
- `value`属性,设置默认值
- `checeked`属性:单选按钮或者复选框被选中
- `readonly`:是否只读
- `disabled`:是否可用
- `size`:大小,一般用于设置文本框的大小
- `maxlength`:允许输入的最大长度,一般用于显示文本框中文本内容的长度
- `placeholder`:占位符属性,常用于设置文本框的占位符

```html
<form action="http://baidu.com" method="POST">
    <input type="text" name="test" value="ickey" size="50px">
    <br>
    <input type="password" name="password" maxlength="10">
    <br>
    <input type="radio" name="sex" value="male" checked>Male
    <br>
    <input type="radio" name="sex" value="female">Female
    <br>
    <input type="checkbox" name="checkbox">
    <br>
    <input type="checkbox" name="checkbox">
    <br>
    <input type="checkbox" name="checkbox">
    <br>
    <input type="file" name="file">
    <br>
    <input type="image" src="img/1.png" name="image">
    <br>
    <input type="submit" name="submit">
    <br>
    <input type="reset" name="1">
    <br>
    <input type="hidden" name="1">
    <br>
    <input type="button" name="111" value="button">
    <br>
</form>
```

### select标签

下拉列表常用于单选和多选,是组合标签,需要子标签`<option>` ,不会独占一行

- `name`属性,发送给服务器的名称  
- `multiple`属性,不写默认单选,取值为multiple表示多选,一般我们只使用单选  
- `size`属性,可见选项的数目

```html
<form action="/demo/demo_form.asp">
    <select name="cars" size="5">
        <option value="volvo">Volvo</option>
        <option value="saab">Saab</option>
        <option value="fiat">Fiat</option>
        <option value="audi">Audi</option>
    </select>
    <input type="submit">
</form>
```

### option标签

- selected属性,表示勾选当前选项  
- value属性,发送给服务器的选项值

- 注意:
 - 如果使用多选,那么选择的时候需要按住ctrl键进行选择.
 - `size`属性我们一般情况下不去设置
 - `selected`属性如果不设置的时候默认是列表中的第一个被选中
 - `value`属性如果不设置的时候,发送给服务器的时候value值默认是 `<option>文本值</option>`文本值,如果设置了就是value属性值,一般情况下都需要进行设置.

```html
<select name="select" multiple size="2">
    <option value="bj">北京</option>
    <option value="sh">上海</option>
    <option value="tj">天津</option>
    <option value="wh">武汉</option>
    <option value="zz">郑州</option>
    <option value="gz" selected>广 州</option>
</select>
```

### textarea标签
文本域,用于多行输入文本  

- `cols`属性,文本域的列数  
- `rows`属性,文本域的行数  
- `name`属性,发送给服务器的名称  
- `value`属性,`textarea`的内容

```html
<textarea rows="10" cols="40" name="textarea"></textarea>
```

### button标签(不常用)

按钮标签,根据不同的type属性不同,功能会不同,完全可以使用`input`进行替代,所以一般不用`button` 
- `type`属性可以有三个值`|button` `submit` `reset`

`button`相当于`input`的`button`需要使用js进行绑定事件,`submit`相当于`input`的`submit`能,`reset`相当于`input`的`reset`功能

```html
<button type="button">按钮</button>
```

## 关于表单标签总结
1. 需要提交到服务器中的数据,必须都要放在`form`表单中,否则是提交不过去的
2. 最后提交的形式就是`name=value&name=value&name=value`
3. `form`中不需要的提交的内容,不设置`name`属性,如按钮本身就是作为提交功能的触发,所以就不需要设置`name`属性



