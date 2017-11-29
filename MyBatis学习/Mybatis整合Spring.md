# Mybatis整合Spring

## 开发流程
- 导包
  - spring03中的所有jar包中除去commonlogging和log4j其余包导入(因为mybatis中已经含有)
  - mybatis中的所有jar包
  - mybatis和spring的整合包
- 设置配置文件
  - 新建名字为config的Source Folder,将所有的配置文件放在此目录下
  - 此包的路径为classpath
- 创建db.properties
```
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql:///bd19
jdbc.user=root
jdbc.password=root
```
- 创建applicationContext文件配置连接池和事务
```html
<!-- 配置c3p0连接池 -->
<context:property-placeholder location="classpath:db.properties"/>
<bean name="dataSoruce" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  <property name="driverClass" value="${jdbc.driverClass}"></property>
  <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>    
  <property name="user" value="${jdbc.user}"></property>
  <property name="password" value="${jdbc.password}"></property>
</bean>
<!-- 配置spring中核心事务管理器, 添加事务依赖的连接池-->
<bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSoruce"></property>
</bean>
<!-- 将通知织入到切点的时候，切点的什么样的方法配置事务的什么属性 -->
<tx:advice transaction-manager="transactionManager" id="tv">
    <tx:attributes>
        <tx:method name="find*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
        <tx:method name="select*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
<!-- 配置切面 -->
<aop:config>
    <aop:pointcut expression="execution(* test.sm.service.impl..*.*(..))" id="pct"/>
    <aop:advisor advice-ref="tv" pointcut-ref="pct"/>
</aop:config>
```
- 创建 mybatis-config.xml , 配置别名
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "mybatis-3-config.dtd">
<configuration>
    <!-- 配置别名 -->
    <typeAliases>
        <package name="test.sm.model"/>
    </typeAliases>
</configuration>
```
- 创建log4j.properties 
```
# Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

## dao层设置

### 传统dao模式开发
- mybatis操作数据库使用的是`sqlSession`对象,`sqlSession`是`sqlSessionFactory`创建出来的,所以需要先创建factory
- 在mybatis­spring整合包中,有一个`SqlSessionFactoryBean`,是用来创建factory的,我们可以将这个类配入spring容器中,他依赖于数据库连接池和`sqlMapConfig.xml`文件

```html
<bean name="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <property name="dataSource" ref="dataSoruce"></property>
</bean>
```

在mybatis­spring整合包中,有一个类叫做SqlSessionDaoSupport,其内部将sqlSession和factory对象进行了封装,使用的时候只需要调用this.getSqlSession()方法进行获取,类似于Spring中的JdbcDaoSupport,我们可以将dao的实现类继承与SqlSessionDaoSupport,在使用的时候调用getSqlSession()方法,在spring中将factory注入给这个类的属性

```html
<bean name="userDao" class="test.sm.dao.impl.UserDaoImpl">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```

### mapper动态代理开发

#### MapperFactoryBean

在mybatis­spring整合包中有一个类叫做 `MapperFactoryBean` ,是用来生成 mapper 的实现类的,我们可以将其配入spring容器中,依赖于`sqlSessionFactory`和`mapper`的接口类

```html
<bean name="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
    <property name="configLocation" value="classpath:SqlMapConfig.xml"></property>
</bean>
<bean name="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="test.sm.mapper.UserMapper"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```

- 此方法生产出来的mapper的实现类放在spring容器中
- 每创建一个实现类，都要去spring容器中进行配置

#### MapperScannerConfigurer

在mybatis­spring整合包中有一个类叫做 `MapperScannerConfigurer` ,它其中有一个 basePackage 的属性用来进行mapper文件扫描

- `MapperScannerConfigurer` 类会自动找到spring中的factory
- 不需要在 sqlMapConfig 中配置扫描 mapper 文件, 所以 sqlMapConfig中只需配置别名即可

```html
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="test.sm.mapper"></property>
</bean>
```

## service层设置

service层配置对应的接口实现类, 对引入的mapper可以使用属性注入和自动装配的方式进行配置

```html
<bean name="userService" class="test.sm.service.impl.UserServiceImpl"> </bean>
```

## Mybatis逆向工程

- model中的会多出来Example文件,是用来执行查询的时候设置条件的
- 使用接口调用方法的时候需要传递的参数类型是example类型
- 不能使用逆向工程连续生成两次,如果生成了多次,会造成mapper文件的重复,会报 resultMap重复
- 必须保证使用逆向工程生成的包的结构和项目的包的结构一模一样,如果不同会造成mapper文件中的类名找不到

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring/applicationContext-*.xml")
public class TestDemo {

	@Autowired
	UserServe us;
	
	@Test
	public void Test() { 
		User u = new User();
		u.setName("test");
		u.setPwd("112");
		us.addUser(u);
	}
	
	@Test
	public void Test01() { 
		List<User> list = us.findAllUser();
		System.out.println(list);
	}
	
	@Test
	public void Test02() { 
		List<User> list = us.findUser();
		System.out.println(list);
	}

}
```

```java
@Autowired
private UserMapper um;

@Override
public void addUser(User u) {
    um.insertSelective(u);
}

@Override
public List<User> findAllUser() {
    List<User> list = um.selectByExample(null);
    return list;
}

@Override
public List<User> findUser() {
    UserExample ue = new UserExample();
    String str = "e";
    ue.createCriteria().andNameLike("%"+str+"%");
    List<User> list = um.selectByExample(ue);
    return list;
}
```

## 配置文件

### config目录

db.properties
```
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql:///bd19
jdbc.user=root
jdbc.password=root
```

log4j.properties
```
# Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

### config/mybatis目录

mybatis-config.xml
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "mybatis-3-config.dtd">

<configuration>
   <!-- 配置别名 -->
   <typeAliases>
      <package name="test.sm.model"/>
   </typeAliases>
</configuration>
```

### config/spring目录

applicationContext-dao.xml
```html
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xmlns:tx="http://www.springframework.org/schema/tx"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd">
  
  <!-- 配置c3p0连接池 -->
  <context:property-placeholder location="classpath:db.properties"/>
  <bean name="dataSoruce" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driverClass}"></property>
    <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>    
    <property name="user" value="${jdbc.user}"></property>
    <property name="password" value="${jdbc.password}"></property>
  </bean>
  
  <!-- 导入mybatis-config.xml中的配置 -->
  <bean name="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"></property>
      <property name="dataSource" ref="dataSoruce"></property>
  </bean>
  
  <!-- 扫描包 会自动寻找SessionFactory -->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="test.sm.mapper"></property>
  </bean>

</beans>
```

applicationContext-service.xml
```html
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xmlns:tx="http://www.springframework.org/schema/tx"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd">

  <!-- 配置扫描 -->
  <context:component-scan base-package="test.sm.service"/>

</beans>
```

applicationContext-tx.xml
```html
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xmlns:tx="http://www.springframework.org/schema/tx"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd">

  <!-- 配置spring中核心事务管理器 添加事务依赖的连接池 -->
  <bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSoruce"></property>
  </bean>
  
  <!-- 将通知织入到切点的时候，切点的什么样的方法配置事务的什么属性 -->
  <tx:advice transaction-manager="transactionManager" id="tv">
      <tx:attributes>
          <tx:method name="find*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
          <tx:method name="select*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
      </tx:attributes>
  </tx:advice>
  
  <!-- 配置切面 -->
  <aop:config>
      <aop:pointcut expression="execution(* test.sm.service.impl..*.*(..))" id="pct"/>
      <aop:advisor advice-ref="tv" pointcut-ref="pct"/>
  </aop:config>

</beans>
```







