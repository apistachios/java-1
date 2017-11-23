# Filter+Listener

## Filter过滤器

filter是对客户端访问资源的过滤，符合条件放行，不符合条件不放行，并且可以对目 标资源访问前后进行逻辑处理

### 设置步骤

- 创建类实现Filter接口
- 在doFilter方法中编写放行和拦截的代码
- 在web.xml文件中进行配置
```html
<filter>
  <display-name>MyFilter</display-name>
  <filter-name>MyFilter</filter-name>
  <filter-class>web.filter.MyFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>MyFilter</filter-name>
  <servlet-name>MyServlet</servlet-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### filter-mapping配置
- 对于 `<url-pattern>/*</url-pattern>` 将路径配置为`/*` 就是拦截所有的请求
- 还可以配置成`<servlet-name>MyServlet01</servlet-name>` 表示指拦截指定的servlet
- `url-pattern` 和`servlet-name` 可以同时配置多个
```html
<filter-mapping>
    <filter-name>UserFilter</filter-name>
    <servlet-name>MyServlet01</servlet-name>
    <servlet-name>MyServlet02</servlet-name>
    <url-pattern>*.do</url-pattern>
    <url-pattern>*.action</url-pattern>
</filter-mapping>
```
- `urlpattern`1`可以设置多种方式进行配置
  - 完全匹配 `/sertvle1` ,可以使用`<servlet-name>sertvle1</servlet-name>` 代替
  - 目录匹配 `/aaa/bbb/*` 
  - 扩展名匹配 `*.abc` `*.jsp` `*.action`
  - `/*` 拦截所有,包括静态资源和jsp页面
  - 目录匹配和扩展名匹配不能同时使用,会造成服务器无法启动
  - 如果有多个`filter`,按照`web.xml`中的 `<filter-mapping>` 标签的前后顺序进行过滤,并不依照`<filter>` 的前后顺序

### dispatcher：访问的方式

- `REQUEST`：默认值，代表直接访问某个资源时执行filter
- `FORWARD`：转发时才执行filter
- `INCLUDE`: 包含资源时执行filter
- `ERROR`：发生错误时 进行跳转是执行filter

### 生命周期
- 服务器启动的时候init方法调用,filter被创建
- 服务器关闭的时候destory方法调用,filter被销毁
- 当有请求过来并符合拦截规则的时候doFilter方法执行.

### init和destory方法

jdk1.8中支持接口中定义非抽象方法,使用修饰符`default`进行修饰,`init`和`destory`都是`default`修饰,所以实现接口的时候可以不用实现这两个方法

- init方法:  `filter`对象创建时执行
- destory方法: `filter`对象销毁时执行
- doFilter方法
  - ServletRequest/ServletResponse：每次在执行doFilter方法时 web容器负责创建一个request和一个response对象作为doFilter的参数传递进来。该request个该response就是在访问目标资源的service方法时的request和response。
  - FilterChain：过滤器链对象，通过该对象的doFilter方法可以放行该请求

```java
public void init(FilterConfig fConfig) {
	System.out.println("init");
}
public void destroy() {
	System.out.println("destory");
}
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

	request.setCharacterEncoding("UTF-8");
	response.setContentType("text/html; charset=UTF-8");

	System.out.println("filter");
    //放行
	chain.doFilter(request, response);
}
```

### 全局编码
设置request和response的编码为UTF8
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
	request.setCharacterEncoding("UTF-8");
	response.setContentType("text/html; charset=UTF-8");
}
```
tomcat内置编码过滤器设置

```html
<filter>
  <filter-name>setCharacterEncodingFilter</filter-name>
  <filter-class>org.apache.catalina.filters.SetCharacterEncodingFilter</filter-class>
  <init-param>
    <param-name>encoding</param-name>
    <param-value>UTF-8</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>setCharacterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```


## Listener监听器

监听器就是监听某个对象的的状态变化的组件,其主要是监听三个域对象(ServletContext域,HttpSession域,ServletRequest域)的创建和销毁以及域对象内的属性的改变


||ServletContext域| HttpSession域| ServletRequest域|
|:-:|:-:|:-:|:-:|
|域对象的创建和销毁| ServletContextListener| HttpSessionListener| ServletRequestListener|
|域对象内的属性的改变| ServletContextAttributeListener| HttpSessionAttributeListener| ServletRequestAttributeListener|

### ServletContextListener

监听ServletContext域的创建和销毁

#### 设置步骤
- 写一个监听器类去实现监听器接口
- 覆盖接口方法
- 在web.xml中进行配置
```java
public class MyServletContextListener implements ServletContextListener {
    //创建ServletContext对象的时候调用contextInitialized方法
    @Override
    public void contextInitialized(ServletContextEvent sce)  { 
    	System.out.println("Initialize");
    }
    //当ServletContext销毁的时候调用contextInitialized方法
    @Override
    public void contextDestroyed(ServletContextEvent sce)  { 
    	System.out.println("Destroye");
        String text = sce.getServletContext().getInitParameter("name");
    	System.out.println(text);
    }
}
```
```html
<listener>
    <listener-class>listenerServletContextListenerTest</listener-class>
</listener>
```

### ServletContextAttributeListener
```java
getServletContext().setAttribute("name", "123");
getServletContext().setAttribute("name", "1");
getServletContext().removeAttribute("name");
```
```java
public class MyServletContextArrtribudeListener implements ServletContextAttributeListener {

    @Override
    public void attributeAdded(ServletContextAttributeEvent scae)  { 

    	System.out.println("向域中放入数据");
    	System.out.println(scae.getName()+"---"+scae.getValue());
    }

    @Override
    public void attributeRemoved(ServletContextAttributeEvent scae)  { 
    	System.out.println("从域中删除数据");
    }

    @Override
    public void attributeReplaced(ServletContextAttributeEvent scae)  { 
    	System.out.println("替换数据");
    }
	
}
```
```html
<listener>
    <listener-class>web.listener.MyServletContextArrtribudeListener</listener-class>
</listener>
```

### session中的绑定的对象相关的监听器

对象放在session中一共有四种状态
1. 绑定状态：就一个对象被放到session域中
2. 解绑状态：就是这个对象从session域中移除了
3. 钝化状态：是将session内存中的对象持久化（序列化）到磁盘
4. 活化状态：就是将磁盘上的对象再次恢复到session内存中

#### 绑定与解绑的监听器HttpSessionBindingListener

此接口是需要对象去实现,并且不需要去注册

```java
public class User implements HttpSessionBindingListener{
	
    private int age;
    private String name;
    Get...
    Set...

    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        HttpSessionBindingListener.super.valueBound(event);
        
        this.setAge(28);
        System.out.println("绑定");
    }

    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        HttpSessionBindingListener.super.valueUnbound(event);
        
        this.setAge(11);
        System.out.println("解绑");
    }
}
```

#### 钝化与活化的监听器HttpSessionActivationListener

实质上就是session中对象的序列化和反序列化,可用于服务器的优化,所以需要将放在session中的对象实现此接口,如果需要对象存在磁盘中,需要当前类实现`Serializable`接口

```java
public class User implements HttpSessionBindingListener,Serializable{
	
    private int age;
    private String name;
    Get...
    Set...

    @Override
    public void sessionWillPassivate(HttpSessionEvent se) {
        HttpSessionActivationListener.super.sessionWillPassivate(se);
        System.out.println("session钝化");
    }

    @Override
    public void sessionDidActivate(HttpSessionEvent se) {
        HttpSessionActivationListener.super.sessionDidActivate(se);

System.out.println("session活化");
	}
```

- 通过`在META-INF`中创建一个名字为`context.xml`文件配置session中的对象多久被钝化

```html
<?xml version="1.0" encoding="UTF-8"?>

<Context>
	<!-- maxIdleSwap:session中的对象多长时间不使用就钝化单位是分钟 -->
	<!-- directory:钝化后的对象的文件写到磁盘的哪个目录下  配置钝化的对象文件在work/catalina/localhost/钝化文件 -->
	<!--org.apache.catalina.session.PersistentManager的钝化引擎-->
	<!--org.apache.catalina.session.FileStore 用于存储文件-->
	<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
	<Store className="org.apache.catalina.session.FileStore" directory="bigdata19" />
	</Manager>
</Context>
```








