# SpringMVC

Spring框架的web层的框架

## 开发流程
- 导包
  - beans+context+core+expression
  - aop+web+webmvc+jstl+commons.logging
- 设置springmvc的约束文件
- 创建配置文件springmvc.xml,配置扫描controller.web包
```html
<context:component-scan base-package="test.springmvc.web"/>
```
- 配置`web.xml`文件
  - 配置前端控制器和 `servlet` 的初始化参数为 `springmvc` 配置文件的路径
  - 和Spring相同 `param-name` 为 `contextConfigLocation` ,这个初始化参数指的是servlet的初始化参数(如果不配置路径,springmvc会默认去找 `/WEB-INF/servlet名字-servlet.xml` 此文件,如果没有此文件就会报异常)
```html
<servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- SpringMVC的配置文件的默认路径是/WEB-INF/servlet名字-servlet.xml -->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <url-pattern>*.action</url-pattern>
</servlet-mapping>
```
- 创建一个包叫做 `controller` 就是web层
  - 创建类,用来处理请求,使用注解的形式将其配置到spring容器中,在其中创建方法处理请求
```java
@Controller
public class RoleController {
	@RequestMapping("/role/roleList.action")
	public ModelAndView roleList(){
		ModelAndView mv = new ModelAndView();
		//向域中放数据
		//内部原理就是将数据放入request域
		mv.addObject("a", "123");
		mv.setViewName("/a.jsp");
		return mv;
	}
}
 ```

## springmvc架构

![springmvc架构图](img/1.png)

1. 用户向服务器发送请求,请求被Spring 前端控制`Servelt DispatcherServlet`捕获
2. `DispatcherServlet`对请求URL进行解析,得到请求资源标识符(URI)。然后根据该URI,调用`HandlerMapping`获得该`Handler`配置的所有相关的对象(包括Handler对象以及Handler对象对应的拦截器),最后以`HandlerExecutionChain`对象的形式返回
3. `DispatcherServlet `根据获得的`Handler`,选择一个合适的`HandlerAdapter`。(附注:如果成功获得`HandlerAdapter`后,此时将开始执行拦截器的`preHandler(...)`方法)
4. 提取`Request`中的模型数据,填充`Handler`入参,开始执行`Handler(Controller)`。 在填充`Handler`的入参过程中,根据你的配置,`Spring`将帮你做一些额外的工作:
    1. `HttpMessageConveter`: 将请求消息(如Json、xml等数据)转换成一个对象,将对象转换为指定的响应信息
    2. 数据转换:对请求消息进行数据转换。如String转换成Integer、Double
    3. 数据根式化:对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
    4. 数据验证: 验证数据的有效性(长度、格式等),验证结果存储到BindingResult或Error中
5. `Handler`执行完成后,向`DispatcherServlet` 返回一个`ModelAndView`对象, 其中view是视图名称，不是真正的视图对象
6. 根据返回的`ModelAndView`,选择一个适合的`ViewResolver`(必须是已经注册到Spring容器中的ViewResolver)进行视图的解析
7. `ViewReslover`解析后返回真正的视图对象 `View）`
8. `DispatcherServlet`对View进行渲染视图(即将模型数据填充至视图中)
9. `DispatcherServlet`响应用户

## springmvc组件介绍及配置

### DispatcherServlet:前端控制器

- 用户请求到达前端控制器,它就相当于mvc模式中的c
- dispatcherServlet是整个流程控制的中心,由它调用其它组件处理用户的请求
- dispatcherServlet的存在降低了组件之间的耦合性。

### HandlerMapping:处理器映射器

- HandlerMapping负责根据用户请求url找到Handler即处理器
- springmvc提供了不同的映射器实现不同的映射方式,例如:配置文件方式,实现接口方式,注解方式等
- 在controller中对应的方法使用的@RequestMapping 就是注解的方式配置,是目前使用最多的配置方式.

### Handler:处理器

- Handler 是继DispatcherServlet前端控制器的后端控制器,在DispatcherServlet的控制下Handler对具体的用户请求进行处理。
- 由于Handler涉及到具体的用户业务请求,所以一般情况需要程序员根据业务需求开发Handler。

### HandlAdapter:处理器适配器

通过HandlerAdapter对处理器进行执行,这是适配器模式的应用,通过扩展适配器可以对更多类型的处理器进行执行。

### ViewResolver:视图解析器

View Resolver负责将处理结果生成View视图,View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址,再生成View视图对象,最后对View进行渲染将处理结果通过页面展示给用户。

### View:视图
springmvc框架提供了很多的View视图类型的支持,包括:jstlView、freemarkerView、pdfView等。我们最常用的视图就是jsp。

### 说明

在springmvc的各个组件中,处理器映射器、处理器适配器、视图解析器称为springmvc的三大组件。

可以认为是 **一个中心三个基本点** ,需要用户开发的组件有handler、view

- 配置相关组件
  - 通过观察发现默认的处理器适配器和处理器映射器,在3.2后就推荐使用新的配置,所以我们要重新去配置新的处理器映射器和处理器适配器
  - 在springmvc的配置文件中需要将新的处理器映射器和处理器适配器配置如容器中
```html
<!-- 配置处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping" />
<!-- 配置处理器适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter" />
```
- 注解驱动
 - SpringMVC使用 `<mvc:annotation-driven>`自动加载`RequestMappingHandlerMapping`和`RequestMappingHandlerAdapter`
```html
<!-- 自动加载处理器映射器和处理器适配器 -->
<mvc:annotation-driven />
```

#### 视图解析器

通过配置视图解析器的前缀和后缀在前端控制器将`modelAndView`交给视图解析器的时候,自动会视图的路径添加前缀和后缀

```html
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/view/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

如果前缀存在两个/的话会删去一个
















