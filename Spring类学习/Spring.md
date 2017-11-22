# Spring

Spring 的核心是**控制反转（IOC）**和**面向切面（AOP）**

- web层:可以使用Spring提供的SpringMVC框架进行请求的处理
- service层:可以使用Spring的IOC进行对象的创建
- dao层:可以使用Spring提供的JDBC模板进行处理

spring不但能够自身完成web应用的开发,并且能够完美兼容其他开源框架,因为spring是一个容器

## 使用方法

- 导入jar包
- 创建普通的类,让Spring帮我们创建
```java
public class People {
    private String name;
    private int age;
    get...
    set...
    toString...
}
```
- 创建xml的配置文件,将写的类配置到对应的文件中,建议放在src下,建议命名为`applicationContext.xml`,可以认为是spring的规范,如果不按照此规范也不会出错
- 设置`schema`约束,需要按照对应的命名空间来决定标签中的顺序的,也可以认为配置后书写标签能够有提示在配置文件写`<bean name="people" class="com.zhiyou100.demo01.People"></bean>`
- 创建一个测试类,写一个测试方法
```java
@Test
public void test() {
    //创建spring容器对象,加载src下的配置文件
    ApplicationContext context = new ClassPathXmlApplicationContext ("applicationContext.xml");
    //通过spring容器获取对象
    People pe = (People) ac.getBean("people")
    //调用对象的方法
    pe.eat();
}
```

## schema约束

配置约束是为了在xml文件中进行配置的时候,能够按照约束进行标签的校验

- 配置一个xml文件最多只能有一个匿名的命名空间,所以我们将bean这个命名空间配置为匿名,如果将其配置为xx,那么当使用bean中的标签的时候需要加`<xx:bean></xx:bean>` ,在配置spring的时候,我们习惯于将bean标签配置为匿名的,可以认为是一种规范
- 因为要使用`schemaLocation`来进行配置键值对匹配,所以我们先配置了别名为xsi的`xmlns:xsi="http://www.w3.org/2001/XMLSchemainstance"`命名空间
- 又配置了一个匿名的命名空间`xmlns="http://www.springframework.org/schema/beans"`
- 通过xsi的schemaLocation,将键值对进行对应`xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd "`

## IOC控制反转

**IOC控制反转(Inversion of Control)** 将对象的创建权交给了Spring, 我们通过Spring 就不用每次去创建对象, 只需要通过Spring容器调用`getBean(“name属性或id属性”)`获取对应的对象

- `ApplicationContext`实现类会在创建的时候,将内部配置的所有对象加载到容器中
- `ClassPathXmlApplicationContext`是A`pplicationContext`的接口实现类,是从classpath的目录下加载配置文件

### bean元素的配置

凡是交给spring容器管理的对象,都是使用bean元素进行配置

- `name`属性给管理的对象起名字,根据该名称能获取对象
- `class`属性是获取该对象的完整类名
- `id`属性跟name属性的功能和用法一模一样,和name属性相比,id属性唯一不重复,并且不能含有特殊字符,如果只设置name属性,那么name属性在容器中就必须是唯一的

### bean元素的创建

默认调用空参构造方法进行创建对象,如果没有空参构造方法就会报错

**容器一旦创建,容器中的对象就会跟着创建,默认通过类的无参构造创建**

- 空参构造方法创建
```html
<bean name="people1" class="test.People"></bean>
```
```java
@Test
public void test01() {
    People pe = (People) ac.getBean("people1");
}
```
- 静态工厂方式创建
```html
<bean name="people2" class=".create.PeopleFactory" factory-method="createPeople"></bean>
```
```java
@Test
public void test02() {
    People createPeople = BeanFactory.createPeople();			
}
```
- 实例工厂方式创建
```html
<bean name="factoryBean" class="create.PeopleFactory"></bean>
<bean name="people3" factory-bean="factoryBean" factory-method="CreatePeople2"></bean>
```
```java
@Test
public void test03() {
    BeanFactory bf = new BeanFactory();
    bf.createPeople2();
}
```
- scope创建
  - singleton单例模式创建,当前对象在容器中只会创建一个对象
  ```html
  <bean name="people4" class="test.People" scope="singleton"></bean>
  ```
  - prototype多例模式创建,当前对象在容器中每次调用getBean返回的是新的对象
  ```html
  <bean name="people4" class="test.People" scope="prototype"></bean>
  ```
- 生命周期方法创建
  - `init-method`初始化方法
  - `destroy-method`销毁方法
  - 不能和`prototype`一起使用,如果是多例的话,spring容器将对象创建完成后就不再负责管理这个对象了,只有是`singleton`对象容器才会负责去管理它
  ```html
  <bean name="people5" class="test.People" init-method="init" destroy-method="destroy" scope="singleton"></bean>
  ```


## DI依赖注入

依赖注入(Dependency Injection),需要有 IOC 的环境,Spring 创建这个类的过程中,Spring 将类的依赖的属性设置进去.

### 注入方式

如果是基本类型使用value,如果是引用类型就需要使用ref

- set方法注入, 类中必须要有set方法,否则属性无法正常注入
```html
<bean name="u1" class="di.User">
    <property name="name" value="lao"></property>
    <property name="age" value="11"></property>
    <property name="zuo" ref="c1"></property>
</bean>
<bean name="c1" class="di.Car">
    <property name="name" value="car"></property>
    <property name="price" value="1111"></property>
</bean>
```
- 构造方法注入, 通过构造方法创建对象,并传递相应的参数给具体的属性
```html
<!-- index表示参数的索引, type表示参数的类型 -->
<bean name="u2" class="di.User">
    <constructor-arg name="name" value="lao" index="1" type="java.lang.String"></></constructor-arg>
    <constructor-arg name="age" value="111"></constructor-arg>
    <constructor-arg name="zuo" ref="c1"></constructor-arg>
</bean>
```
- p名称空间注入
```html
<bean name="u3" class="di.User" p:name="kkk" p:age="11" p:zuo-ref="c1">
</bean>
```
- SPEL表达式注入
```html
<bean name="u5" class="di.User">
    <property name="age" value="#{c1.price > 300000 ? 35 : 20}"></property>
    <property name="name" value="#{'zhangSan'.toUpperCase()}"></property>
    <property name="zuo" ref="c1"></property>
</bean>
```
- 复杂类型注入
```html
<bean name="ct" class="di.ComplexTypeObject">
<property name="arr">
<array>
  <value>休息</value>
  <value>开会</value>
  <ref bean="user4"/>
</array>
</property>
<property name="list">
<list>
  <value>北京</value>
  <value>上海</value>
  <ref bean="car"/>
</list>
</property>
<property name="map">
<map>
  <entry key="driverClass" value="com.mysql.jdbc.Driver"/>
  <entry key="car" value-ref="car"/>
  <entry key-ref="user4" value-ref="car"/>
</map>
</property>
<property name="listCar">
<list>
  <!--也可以直接这样进行配置-->
  <bean class=".spring.di.Car" p:name="123" p:price="123"/>
  <bean class=".spring.di.Car" p:name="456" p:price="456"/>
  <bean class=".spring.di.Car" p:name="789" p:price="789"/>
</list>
</property>
</bean>
```

## Spring 的分模块配置文件的开发

将配置文件按照不同的功能模块分成多个xml文件,在进行引入的时候可以使用标签
`<import resource="相对路径">`进行引入,注意是 **相对路径** , 相对于当前引入到的xml文件的路径,不建议使用 " / " 开头