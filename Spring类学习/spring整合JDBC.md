# spring整合JDBC

## JDBCTemplate(JDBC模版对象)
### 开发流程

- 导包 aop, test, JDBC驱动, c3p0连接池, spring­jdbc(spring包中), spring­tx(spring包中)
- 书写Dao层接口
- 书写Dao的实现类
- 填写增删改查方法
```java
@Repository
public class UserDaoImpl implements UserDao {
    @Autowired
    private JdbcTemplate jt;
    @Override
    public void addUser(User u) {
        jt.update("insert into user values(null,?,?)", u.getName(),u.getPwd());
    }
}
```
- 配置文件
```html
<!-- jar包中的类也可以通过spring进行配置 -->
<bean name="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql:///bd19"></property>
    <property name="user" value="root"></property>
    <property name="password" value="root"></property>
</bean>
<bean name="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="datasource"></property>
</bean>
<!-- 配置扫描路径 -->
<context:component-scan base-package="test.spring.dao"></context:component-scan>
```

### JdbcDaoSupport

`dbcDaoSupport`类内部封装了一个`JDBCTemplate`模版,在需要的时候调用`this.getJdbcTemplate()`即可,所以可以使`daoImpl`继承`JDBCDaoSupport`,在需要的时候调用`this.getJDBCTemplate`

```html
<bean name="userDao" class="test.spring.dao.impl.UserDaoImpl">
    <property name="dataSource" ref="datasource"></property>
</bean>
```

```java
public class UserDaoImpl2 extends JdbcDaoSupport implements UserDao{
    @Override
    public void addUser(User u) {
        this.getJdbcTemplate().update("insert into user values(null,?,?)", u.getName(),u.getPwd());
    }
}
```

### properyies配置jdbc连接信息

创建db.properties配置文件，配置中不能有空格
```
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql:///bd19
jdbc.user=root
jdbc.password=root
``` 

```html
<context:property-placeholder location="classpath:db.properties"/>
<bean name="dataSoruce" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  <property name="driverClass" value="${jdbc.driverClass}"></property>
  <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>    
  <property name="user" value="${jdbc.user}"></property>
  <property name="password" value="${jdbc.password}"></property>
</bean>
```

## 事务

### 事务的特性
- `原子性(Atomicity)` 原子性是指事务是一个不可分割的工作单位,事务中的操作 要么都发生,要么都不发生。
- `一致性(Consistency)` 一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态,也就是说一个事务执行之前和执行之后都必须处于一致性状态.拿转账来说,假设用户A和用户B两者的钱加起来一共是5000,那么不管A和B之间如何转账,转几次账,事务结束后两个用户的钱相加起来应该还得是5000,这就是事务的一致性
- `隔离性(Isolation)` 隔离性是当多个用户并发访问数据库时,比如操作同一张表时,数据库为每一个用户开启的事务,不能被其他事务的操作所干扰,多个并发事务之间要相互隔离.即要达到这么一种效果:对于任意两个并发的事务T1和T2,在事务T1看来,T2要么在T1开始之前就已经结束,要么在T1结束之后才开始,这样每个事务都感觉不到有其他事务在并发地执行
- `持久性(Durability)` 持久性是指一个事务一旦被提交了,那么对数据库中的数据的改变就是永久性的,即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

### 事务并发引起的问题

- `脏读` B事务读取到了A事务尚未提交的数据 ------ 要求B事务要读取A事 务提交的数据
- `不可重复读` 不可重复读是指在对于数据库中的某个数据,一个事务范围内多次查询却返回了不同的数据值,这是由于在查询间隔,被另一个事务修改并提交了
- `幻读(虚读)` 幻读是事务非独立执行时发生的一种现象。例如事务T1对一个表中所有的行的某个数据项做了从“1”修改为“2”的操作,这时事务T2又对这个表中插入了一行数据项,而这个数据项的数值还是为“1”并且提交给数据库。而操作事务T1的用户如果再查看刚刚修改的数据,会发现还有一行没有修改,其实这行是从事务T2中添加的,就好像产生幻觉一样,这就是发生了幻读

### 事务隔离级别

- `read uncommitted` : 读取尚未提交的数据 :哪个问题都不能解决
- `read committed` : 读取已经提交的数据 :可以解决脏读 **oracle默认**
- `repeatable read` : 重读读取:可以解决脏读 和 不可重复读 **mysql默认的**
- `serializable` : 串行化:可以解决 脏读 不可重复读 和虚读 **相当于锁表**

- 查看mysql数据库默认的隔离级别: `select @@tx_isolation`
- 设置mysql的隔离级别 : `set session transaction isolation level 设置事务隔离级别`

- mysql的事务控制:
  - 开启事务:`start transaction;`
  - 提交:`commit;`
  - 回滚:`rollback;`
- JDBC事务控制:
  - 开启事务: `conn.setAutoCommit(false);`
  - 提交:`conn.commit();`
  - 回滚:`conn.rollback();`

- 隔离级别的性能: `read uncommitted>read committed>repeatable read>serialazable`
- 安全性: `read uncommitted<read committed<repeatable read<serialazable`

## spring中的事务

不同平台操作事务的方式不同,如
- jdbc是使用connection
- Hibernate操作事务是使用
- transition,mybatis操作事务是使用session操作

但是不管是哪种框架,都要实现spring的
`PlatformTransactionManager`接口

### spring管理事务的属性

- **隔离级别**
  - 读未提交
  - 读已提交
  - 可重复读
  - 串行化
- **是否只读** 表示操作事务的时候,当前事务是否是只读,比如查询操作就应该只读,其他方法牵扯到修改就是非只读
  - true表示只读(不允许做修改数据库的操作)
  - false表示非只读

- **事务的传播行为** 在业务方法平行调用的时候,事务应该如何处理的问题
  - 保证同一事务中
    - `PROPAGATION_REQUIRED` 支持当前事务,如果不存在 就新建一个(默认)
    - `PROPAGATION_SUPPORTS` 支持当前事务,如果不存在,就不使用事务
    - `PROPAGATION_MANDATORY` 支持当前事务,如果不存在,抛出异常
  - 保证不在同一事务中
    - `PROPAGATION_REQUIRES_NEW` 如果有事务存在,挂起当前事务,创建一个新的事务
    - `PROPAGATION_NOT_SUPPORTED` 以非事务方式运行,如果有事务存在,挂起当前事务
    - `PROPAGATION_NEVER` 以非事务方式运行,如果有事务存在,抛出异常
    - `PROPAGATION_NESTED` 如果当前事务存在,则嵌套事务执行

## 转帐例子

### applicationContext.xml
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

<context:property-placeholder location="classpath:db.properties"/>
<!-- jar包中的类也可以通过spring进行配置 -->
<bean name="dataSoruce" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  <property name="driverClass" value="${jdbc.driverClass}"></property>
  <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>    
  <property name="user" value="${jdbc.user}"></property>
  <property name="password" value="${jdbc.password}"></property>
</bean>

<context:component-scan base-package="test.spring"></context:component-scan>

<bean name="jdbcTep" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSoruce"></property>
</bean>

<bean name="userDao" class="test.spring.dao.impl.UserDaoImpl" p:dataSource-ref="dataSoruce"></bean>
<bean name="accountDao" class="test.spring.dao.impl.AccountDaoImpl" p:dataSource-ref="dataSoruce"></bean>

<!-- 配置spring中核心事务管理器, 添加事务依赖的连接池-->
<bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSoruce"></property>
</bean>

<!-- 将通知织入到切点的时候，切点的什么样的方法配置事务的什么属性 -->
<tx:advice transaction-manager="transactionManager" id="tv">
    <tx:attributes>
        <tx:method name="find*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
        <tx:method name="select*" isolation="REPEATABLE_READ" read-only="true" propagation="REQUIRED"/>
        <tx:method name="add*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="insert*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="delete*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="remove*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="update*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="modify*" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
        <tx:method name="transfer" isolation="REPEATABLE_READ" read-only="false" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>

<!-- 配置切面 -->
<aop:config>
    <aop:pointcut expression="execution(* test.spring.service.impl..*.*(..))" id="pct"/>
    <aop:advisor advice-ref="tv" pointcut-ref="pct"/>
</aop:config>

</beans>
```

### db.properties
```
# properties配置要求配置中不能有空格
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql:///bd19
jdbc.user=root
jdbc.password=root
```

### AccountDaoImpl.java
```java
public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {

	@Override
	public void addMoney(int id, int money) {
		getJdbcTemplate().update("update account set money = money+? where id=?",money,id);
	}

	@Override
	public void reduceMoney(int id, int money) {
		getJdbcTemplate().update("update account set money = money-? where id=?",money,id);
	}

}
```

### AccountServiceImpl.java
```java
@Service
public class AccountServiceImpl implements AccountService {

	@Autowired
	private AccountDao ad;
	@Override
	public void transfer(int fromId, int toId, int money) {
		ad.reduceMoney(fromId, money);
        //检测出现异常是否会回滚
		//int i = 1/0;
		ad.addMoney(toId, money);
	}

}
```

### TestDemo.java
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class TestDemo {
	
	@Autowired
	private AccountService as;
	
	@Test
	public void test1(){
		as.transfer(1, 2, 50);
	}
}
```