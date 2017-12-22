# Struts2

## 使用方法

1. 导包
2. 创建一个类`HelloWorldAction`, 写一个方法, 返回值为String类型
```java
public class HelloWorldAction {
    public String hehe(){
        System.out.println(11111);
        return "success";
    }
}
```
- 在src根目录下创建一个名字为`struts.xml` 的文件
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
  "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
  "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <package name="hehe" namespace="/hehe" extends="struts-default">
        <action name="heheAction" class="test.web.action.HelloWorldAction" method="hehe">
            <result name="success">/hehe.jsp</result>
        </action>
</package>
```
3. 在webcontent目录下,创建一个名为hehe的jsp文件
4. web.xml中配置struts的核心过滤器
```html
<filter>
    <filter-name>StrutsPrepareAndExecuteFilter</filter-name>
    <filter-class> org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>StrutsPrepareAndExecuteFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
5. 访问 http://localhost:8080/Struts2Test/hehe/heheAction

## structs.xml文件配置

### package标签

`<package>` 配置web应用的不同模块,一般在一个功能模块下配置一个package,在当前的package下配置这个模块的多个action

- `name属性标签`: 就是识别作用,为了区分不同的package 
- `namespace属性标签`: 一般用于设置项目的功能模块, 可以不设置, 注意区分根命名空间(/) 和默认命名空间的区别
  - 按照请求路径会先去匹配`namespace `,如果有对应的`namespace` 接着会去`action` 的`name` ,如果没有对应`action` 的`name` ,就回去默认的命名空间下找对应的`action` ,如果没有这个`action` ,就会报`404`
  - 按照请求路径会先去匹配`namespace` ,如果没有这个`namespace` 就会去根命名空间下查找对应的`action` 如果没有对应的`action` 就回去找默认的`action` 如果还没有就`404`
- `extends属性标签`: 集成, struts中的所有package都必须继承struts-default
- `abstract属性标签`: 标识符,用以表示这个包是用来被继承的,被继承的包中往往是没有action的 

### action标签

`action` 用来配置一个请求

- `name`属性标签: 决定我们的请求路径的映射 
- `class`属性标签: 决定请求接受时候是Struts2去反射哪个类, 需配置全类名
- `method`属性标签: 决定请求接收时候调用的方法

### result标签

`result` 结果配置,用于设置不同的方法返回值,可以配置不同的返回值对应不同的视图

- `name`属性: 通过返回值的具体字符串, 来跳转页面 
- `type`属性: 决定以什么样是方式跳转页面, 默认是内部转发
- 标签体表示相对路径,相对于web应用开始

### include标签

用来表示包含其他文件, 加载其他配置文件

## 常量配置

默认的常量配置在structs核心包中的`default.properties`中

### 修改常量配置方式

常量配置加载顺序
1. 默认
2. `struts.xml`中在`structs`的根标签下,书写`constant`标签进行配置,在项目中主要使用这种方式
3. 在`src`下创建`struts.properties`文件
4. 在`web.xml`文件中,配置`context-param`

常用常量设置

```html
<!-- 配置编码格式,默认便为 UTF-8-->
<constant name="struts.i18n.encoding" value="UTF-8"></constant>
<!-- 指定访问action的路径的后缀名-->
<constant name="struts.action.extension" value="do,action,"></constant>
<!-- 打开开发模式, 以支持热部署-->
<constant name="struts.devMode" value="true"></constant>
```

## 动态方法调用

如果一个业务模块有多个方法,我们可以使用动态方法调用省略action的配置,设置动态方法调用有两种方法

### 方法一 配置常用变量

- 开启动态方法调用
```html
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
```
- 配置action的时候不写method
- 在访问的时候输入网址: `http://localhost:8080/Struts2Test/命名空间/action的name!方法名`

### 方法二 通配符方式
- 关闭动态方法调用
- 对于方法名可以使用一个`*` 通配符,在后面的class和method可以使用`{索引}` 来读取前面的内容

```html
<package name="xx" extends="struts-default" namespace="/user">
    <action name="hello_*" class="test.struts.web.action.HelloAction" method="{1}">
        <result name="success">/a.jsp</result>
    </action>
</package>
```

## structs2中的默认配置

- `method` 的默认值`execute`
- `result` 的默认值是`success`
- `result` 的type的默认值是`dispatcher`
- `class` 的默认值是`ActionSupport` 
  - 其中有`execute `方法返回值`是success`
  - 一般可用于通过action跳转到指定界面

- `default-action-ref` 配置`package`下的默认的`action`,当访问当前包下,如果找不到指定`action`,就会自动寻找默认的`action`
```html
<package name="xx" extends="struts-default" namespace="/user">
    <default-action-ref name="demoAction"></default-action-ref>
    <action name="demoAction" class="test.struts.web.action.HelloAction">
        <result name="success">/a.jsp</result>
    </action>
</package>
```

## Action类

Action类建立的方式一共有3种类型

1. 普通的pojo,不需要实现接口,不需要继承任何父类,这种类的好处就是书写自由,框架代码侵入性低,但是缺点就是对于很多Struts2提供的很多功能我们都需要自己去实现,实际开发中并不常用
2. 实现Action接口,抽象方法 `execute()` 需要去实现,并且内置了一些字符串供我们使用,实际开发中并不常用,主要是起到一个规范的作用
3. 继承ActionSupport父类,在ActionSupport中提供很多丰富的功能,并且实现了`Action, Validateable, ValidationAware, TextProvider,LocaleProvider` 这些接口为我们提供了更多的功能,并且ActionSupport内部也有很多丰富的功能,这些功能包括获取国际化信息,数据校验,默认处理用户请求等功能,实际开发过程中大多使用这种方法来创建Action类

- 内部方法的特征为
  1. 修饰符 public
  2. 返回值为String
  3. 方法名随意
  4. 不能有参数
  5. 可以抛异常










