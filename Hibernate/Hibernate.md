# Hibernate

## 使用方法

1. 导包
2. 创建model下的类和对应的.hbm.xml文件
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<!-- package无法指定子包 -->
<hibernate-mapping package="test.hib.model">
<class name="User" table="t_user">
    <id name="id" column="u_id">
        <!-- 主键生成策略 -->
        <generator class="native"></generator>
    </id>
    <property name="name" column="u_name"></property>
    <property name="age" column="u_age"></property>
</class>
</hibernate-mapping>
```
3. src下新建核心配置文件hibernate.cfg.xml
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 必选配置 -->
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql:///hibernate</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">root</property> 
        <!-- 配置mysql编码 -->
        <property name="connectio.useUnicode">true</property>  
        <property name="connection.characterEncoding">utf-8</property>      
         <!-- 可选配置 (格式化sql, 自动建表)-->
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.format_sql">true</property>
        <property name="hibernate.hbm2ddl.auto">update</property>
        <!-- 引入orm映射文件 -->
        <mapping resource="test/hib/model/User.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```
4. 进行测试
```java
@Test
public void test() {
    //加载核心配置文件,默认加载src下的hibernate.cfg.xml,名字错误就无法加载
    Configuration conf = new Configuration().configure();
    //构建sessinfactory
    SessionFactory factory = conf.buildSessionFactory();		
    //创建session
    Session session = factory.openSession();
    //开启事务
    Transaction transaction = session.beginTransaction();
    User u = new User();
    u.setAge(112);
    u.setName("张三");
    session.save(u);
    //提交事务
    transaction.commit();
    // 关闭session
    session.close();
    factory.close();
}
```

## orm映射文件

名称使用`类名.hbm.xml` 并且放在和实体相同的包中

###  `hibernate-mapping`标签

- `hibernate-mapping`标签,在此配置表结构和对象的映射
- package属性用于指定所在的包,那么在配置文件中使用完整类名的的时候,就可以不带包名, **但是只会指定到当前包,不会对应其子包** 

### class标签

配置实体与表中的对应关系的

- `name属性`:设置为实体的完整类名,如果已经在`hibernate-mapping` 中设置了`package` 属性那么就可以只写类名
- `table属性`:设置为数据库中对应的表名,此表就是要与对象建立映射关系的那个表
- `dynamic-insert属性`:动态插入 默认值是false,如果设置为true就代表如果字段值为null,不参与insert语句
- `dynamic-update属性`:动态更新 默认值”false”如果设置为true就代表没改动过的属性,将不会生成到update语句中

- id标签: 配置数据库中主键的映射属性的
  - `name属性`:设置实体中的属性名
  - `column属性`:设置对应数据库中的字段名称
  - `length属性`: 设置列的数据长度
  - `unsaved-value属性`: 指定主键为什么值时,当做null来处理(不常用)
  - `access属性`: 如果设置为filed ,那么在操作属性时,会直接操作对应的字段, 而不是get/set方法(不推荐使用)

  - `generator标签`: 主键生成策略
    - increment :数据库自己生成主键. 先从数据库中查询最大的ID值,将ID值加作为新的主键
    - identity :依赖于数据的主键自增功能
    - sequence :序列,依赖于数据中的序列功能(Oracle).
    - hilo : Hibernate自己实现序列的算法,自己生成主键. (hilo算法,纯了解,)
    - native :自动根据数据库判断,三选一. identity|sequence|hilo
    - uuid :生成32位的不重复随机字符串当做主键
    - assigned :自己指定主键值. 表的主键是自然主键时使用.

  - `property标签`: 配置除了主键之外的的普通属性映射
    - `name属性`: 实体中属性名称
    - `column属性`: 表中列的名称
    - `length属性`: 数据长度
    - `precision属性`: 小数点后的精度
    - `scale属性`: 有效位数
    - `insert属性`: 该属性是否加入insert语句.(一般不用)
    - `update属性`: 该属性是否加入update语句.(一般不用)
    - `not-null属性`: 指定属性的约束是否使用 非空
    - `unique属性`: 指定属性的约束是否使用 唯一
    - `type属性`:表达该属性的类型(一般可以不用设置),可以有三种指定属性. `java类型`,如 `java.lang.String`,  数据库类型指定,如`varchar`,   Hibernate类型指定,如`string`

## hibernate.cfg.xml核心配置文件

用于配置hibernate中的数据库连接和映射文件所需的基本信息,Hibernate的配置文件有两种格式

一种是propertiest文件,默认是hibernate.properties

一种是xml格式的配置,默认名字是hibernate.cfg.xml,在实际开发过程中大部分时候使用的都是xml文件进行配置.一般将此文件放在src下

### 必选配置

- 数据库驱动`hibernate.connection.driver_class`
- 数据库url `hibernate.connection.url`
- 数据库连接用户名`hibernate.connection.username`
- 数据库连接密码`hibernate.connection.password`
- 数据库方言`hibernate.dialect `因为hibernate需要生成sql语句,在不同的数据库中sql语法略有差别,指定方言就是设置hibernate在生成sql语句的时候针对哪个库生成的sql
  - `org.hibernate.dialect.MySQLDialect` 通用存储引擎(我们一般设置为通用存储引擎,无论对应的哪个存储引擎的数据库都能进行操作)
  - `org.hibernate.dialect.MySQLInnoDBDialect` InnoDB存储引擎
  - `org.hibernate.dialect.MySQLMyISAMDialect` MyISAM存储引擎

### 可选配置

如果hibernate版本较新,需要自动建表,需要设置方言mysql的方言为org.hibernate.dialect.MySQL5Dialect

- `hibernate.show_sql` 将生成的sql语句打印到控制台
- `hibernate.format_sql` 进行格式化sql语句,如果为false,sql语句就会打印到一行
- `hibernate.hbm2ddl.auto` 自动导出表结构,通过设置此项,hibernate可以通过实体自动创建表结构其有4个设置
  - `create` 自动建表,每次hibernate运行都会创建新表,之前的内容都会被覆盖(用于测试环境,不常用)
  - `create-drop` 自动建表,每次运行都会创建表,运行结束后删除表
  - `update` 自动建表,如果有就不再自动生成,如果没有创建新表,数据变动不会影响原来的表的内容(推荐使用),比如重新修改了表的映射关系,不会影响原有的数据
  - `validate` 不自动建表,如果没有表就会抛出异常
- `mapping`

## API

### Configuration

加载核心配置文件类,默认会加载src下的hibernate.cfg.xml

```java
//使用空参创建配置对象
Configuration conf = new Configuration();
//加载src中的hibernate.cfg.xml
conf.configure();
```

### SessionFactory

通过conf调用buildSessionFactory() 创建factory,因为核心配置文件中的根标签是session-factory ,所以创建出来的factory就是我们在配置文件中配置的那个factory, 可以通过这个对象创建session对象

因为factory创建消耗资源比较大,并且一个factory可以创建多个session,所以在实际开发过程中SessionFactory只需要创建一个即可

```java
SessionFactory factory = conf.buildSessionFactory();
```

### Session

是操作数据库的核心对象,通过SessionFactory 进行创建,可以进行增删改查

### Transaction

hibernate中处理事务的类

- 在核心配置文件中可以设置事务的隔离级别为1,2,4,8表示读取未提交,读取已提交,可重复读,串行化
```html
<property name="hibernate.connection.isolation">4</property>
```
- 开启事务`Transaction tx = session.beginTransaction();`
- 提交事务`tx.commit();`
- 回滚事务`tx.rollback();`

## 封装工具类

SessionFactory具有如下特点

1. 线程安全,可以保证在并发操作的时候,多个线程使用一个`SessionFactory`对象
2. 在创建的时候,会读取所有`hibernate.cfg.xml`和所有的映射文件,并且会创建数据库中的表,所以比较耗费资源,不能轻易的创建和销毁, 在实际开发过程中我们可以使用一个工具类来创建一个SessionFactory对象,通过调用方法来创建操作数据库的session对象

```java
public class HibernateUtil {
    private static final Configuration CONF;
    private static final SessionFactory FACTORY;
    static {
        CONF = new Configuration().configure();
        FACTORY = CONF.buildSessionFactory();
    }
    public static Session openSession (){
        return FACTORY.openSession();
    }
}
```

## 增删改查

- 查询数据: `session.get(类名.class,id索引)`
- 增加数据: `session.save(对象)`
- 删除数据: `session.delete(对象)`
- 修改数据: `session.update(对象)`

## 实体类
- 无参构造方法,内部通过反射的形式创建对象调用的无参方法
- 成员变量私有提供set和get方法,通过set个get设置和查询对象的属性
- 对于基本类型尽量使用包装类,可以多表示一个null状态
- 需要提供一个唯一标识的OID来与表中的主键进行对应,因为hibernate需要通过这个OID来区分内存中的对象是否是同一个对象
- 不要使用final进行修饰,因为hibernate使用的CGLib技术

## 对象的状态

hibernate中的对象有3种状态,分别为瞬时态,持久态,托管态

- `瞬时态(transient)`,无OID, new 对象还没有交给session管理
- `持久态(persistent)`,有OID,加入到session中,且session并没有关闭,在数据库中有对应的记录
- `托管态(detached)`,也叫游离态,有OID但是和session没有关联,session关闭后

### 持久状态
**hibernate操作数据库的本质就是将瞬时态和托管态转换为持久态,我们今后操作数据库的本质也是需要将对象编程持久态,持久状态的特点是能够自动更新数据库**

当进行查询的时候返回的对象就是持久化对象,此时只需要修改对象的值,不用调用update方法,只要事务进行了提交以后,hibernate就会将其改变的值同步到数据库中

### 状态的转变
- 瞬时态转换为持久态调用`save()`或者`saveOrUpdate()`
- 瞬时态转换为游离态设置OID属性值
- 持久态转换为瞬时态调用`delete()`
- 持久态转换为游离态session调用`close()`
- 游离态转换为瞬时态将OID设置为null
- 游离态转换为持久态调用`update()`或者`saveOrUpdate()`
- 无论对象是瞬时还有游离只要调用`saveOrUpdate()`都会变成持久态












