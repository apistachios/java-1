# 注解配置Spring

- 导包4+2+1(spring包中的aop包)
- 配置Context的schema约束,
- 在application.xml文件中添加一句话`<context:component-scan base-package="test.annotation"></context:component-scan>` 会扫描package中设置的包和子包,并且包含其子包中的所有配置注解的**类**,会将其注入到spring容器中
- 在包下的类中把需要spring容器进行管理的类,添加注解

## 配置到容器中的注解

在要配置到容器中的类头上添加注解,spring通过扫描就能将其放在spring的容器中,四种注解的功能都是一样的,推荐使用后三个

- `@Component`: 一个普通的spring Bean类。
- `@Service`: 用以区分将此类是service层
- `@Controller`: 用以区分将此类是web层
- `@Repository`: 用以区分此类是dao层

```java
@Component("user")
    public class User {
        private String name;
        private int age;
        private Car car;
        set...
        get...
    }
}
```

- 通过注解的形式去配置类的时候如果没有写名字,默认名字为首字母小写
- 通过配置文件的形式向容器中注入类的时候,如果没有写名字,没有默认名字

### @scope配置创建对象模式

- `@Scope(scopeName=”singleton”)`指定单例模式创建对象,默认是单例
- `@Scope(scopeName=”prototype”)`指定多例模式创建对象

```java
@Repository("user")
@Scope(scopeName="singleton")
public class User {
}
```

### 生命周期方法

需要设置容器的`close``((ClassPathXmlApplicationContext)ac).close();`才能看到销毁操作,并且只支持`singleton`的对象

- `@PostConstruct`放在初始化方法上面
 - `@PreDestroy`放在销毁的方法上面

```java
@PostConstruct
public void init(){
    System.out.println("构造方法调用后调用");
}
@PreDestroy
public void destory(){
    System.out.println("容器关闭前销毁");
}
```

###　属性注入的注解

#### 基本数据类型和String

`@value`注入属性值(对于基本数据类型)

使用此注解是注入的类型是基本类型包括字符串,可以将其放在成员变量上,也可以将其放在set方法上,格式为 `@value("xxx")`

```java
@Value("张三")
private String name;
```

#### 引用类型

- `@Autowired`和`@Qualifier`会自动找spring容器中的相同类型的值进行匹配,如果容器中有多个符合类型的那么得再添加一个`@Qualifier("bean的名称")` 注解
```java
@Autowired
@Qualifier("car2")
private Car car;
public User() {
    super();
}
```
- `@Resource`: 对于引用类型直接指定注入哪个名称的对象
```java
@Resource(name="car")
private Car car;
public User() {
    super();
}
```

## Spring中的JUnit4测试

- 导入spring包中的test包
- 在测试的类上方添加注解`@RunWith(SpringJUnit4ClassRunner.class)` 表示让`SpringJUnit4ClassRunner`这个类帮我们创建容器
- 然后在类上添加`@ContextConfiguration("classpath:applicationContext.xml")`配置相关配置文件的路径
- 在需要获取容器中的对象的时候设置为成员变量,使用`Resource`或者`Autowired`进行获取
- 使用test进行测试
```java
//使用这个类帮助我们去创建容器
@RunWith(SpringJUnit4ClassRunner.class)
//配置配置文件的路径,必须要写classpath
@ContextConfiguration("classpath:applicationContext.xml")
public class TestDemo {
    @Resource(name="user")
    private User u;
    @Test
    public void test01(){
        System.out.println(u);
    }
}
```



















