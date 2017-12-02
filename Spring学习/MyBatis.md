# MyBatis

MyBatis使开发者只需要关注 SQL 本身,而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。

传统的JDBC存在如下问题
- 数据库连接创建、释放频繁造成系统资源浪费,从而影响系统性能。如果使用数据库连接池可解决此问题。
- Sql语句在代码中硬编码,造成代码不易维护,实际应用中sql变化的可能较大,sql变动需要改变java代码。
- 使用preparedStatement向占有位符号传参数存在硬编码,因为sql语句的where条件不一定,可能多也可能少,修改sql还要修改代码,系统不易维护。
- 对结果集解析存在硬编码(查询列名),sql变化导致解析代码变化,系统不易维护,如果能将数据库记录封装成pojo对象解析比较方便

## Mybatis使用步骤

- 下载Mybatis的jar包https://github.com/mybatis/mybatis­3/releases
- 添加jar包,mybatis核心包 mybatis依赖包 数据库驱动包
- 添加xml文件约束dtd,在核心包 `/org/apache/ibatis/builder/xml/mybatis-3-config.dtd`和 `/org/apache/ibatis/builder/xml/mybatis-3-mapper.dtd` 对应的两个key值分别是 `-//mybatis.org//DTD Config 3.0//EN` 和 `-//mybatis.org//DTD Mapper 3.0//EN` RootElements分别是 `configuration` 和 `mapper`
- 配置mybatis-config.xml
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "mybatis-3-config.dtd">
<properties resource="db.properties"></properties>
<environments default="development">
<environment id="development">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
        <property name="driver" value="${jdbc.driverClass}"/>
        <property name="url" value="${jdbc.jdbcUrl}"/>
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </dataSource>
    </environment>
</environments>
```
- 配置日志文件,创建log4j.properties
```
# Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```
- 配置mapper文件 UserMapper.xml
```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "mybatis-3-mapper.dtd">
<mapper namespace="test.mybatis.mapper.UserMapper">
    <insert id="addUser" parameterType="test.mybatis.model.User">
        insert into user values (null,#{name},#{age})
    </insert>
    <select id="findAllUser" resultType="test.mybatis.model.User">
        select * from user
    </select>
</mapper>
```
- 写测试类
    1. 创建sqlSessionFactory
    2. 通过文件创建sqlSession
    3. 通过openSession建立连接
    4. 通过sqlSession的增删改查
```java
@Test
public void test() throws IOException {
	//输入流InputStream， 输出流OutputStream
    //加载核心配置文件
    InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
    //创建sessionFactory
    SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
    SqlSessionFactory factory = builder.build(in);
    //通过factory创建session
    SqlSession session = factory.openSession();
    //通过session执行sql， 通过session提交事务
    Account account = (Account) session.selectOne("test.findAccountById", 1);
    System.out.println(account);
    //提交session
    session.commit();
}
```

## MyBatis注意事项

- namespace,必须要进行设置,如果不设置,文件无法加载，可以表示当前mapper中的增删改查数据哪个命名空间,用以区别其他的sql中的内容
- `#{任意写}` 和 `${value}` 的区别相当于? 占位符，拼成的sql语句如果是varchar类型会自动添加''
  - `${value}` 当于sql拼接, 不会添加任何符号
- 核心配置文件中的 `<mappers>` 标签用来在核心配置文件中引入对应的对应的mapper文件
- mapper配置文件是用来书写sql语句的,一个配置文件中只能有一个mapper标签,mapper标签内部有增删改查标签,用来书写增删改查操作
- 增删改查标签中的`parameterType`属性为设置执行sql语句的时候传过来的值的类型
- 增删改查标签中的`resultType`属性为设置执行sql语句的时候返回值的类型
- 如果返回值是list,返回值的类型同样还是写成对应集合中存取对象的类型

## 将mybatis应用到dao层
```java
public class UserDaoImp implements UserDao {
    //通过spring容器注入进来
    private SqlSessionFactory factory;
    public void setFactory(SqlSessionFactoryfactory) {
        this.factory = factory;
    }
    @Override
    public void addUser(User u) {
        factory.openSession().insert("user.addUser", u);
    }
}
```

## mapper动态代理开发

创建接口写接口方法确保4个一致
- 方法名称和文件中的id一致
- 方法的返回值和对应标签内的resultType一致
- 方法的参数和parameterType一致
- namespace和接口的全名称一致

```java
private SqlSession session;
@Before
public void Before() throws IOException {
    session = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis-config.xml")).openSession();;
}
@After
public void After() {
    session.commit();
}
@Test
public void test01() {
    User u = new User();
    u.setName("qqq");
    u.setAge(333);
    UserMapper mapper = session.getMapper(UserMapper.class);
    mapper.addUser(u);
}

@Test
public void test02() {
    UserMapper mapper = session.getMapper(UserMapper.class);
    List<User> li = mapper.findAllUser();
    System.out.println(li);
}
```

### mybatis多参数

UserMapper.java
```	java
User checkUser(String name, int age);
User fuzzyFindUser( String name, String content);
```

### 按参数序号设置

- 在mapper的配置文件中不写`parameterType`
- 在使用参数的地方按照 `#{0}`递增,0代表第一个参数
```html
<select id="checkUser" resultType="test.mybatis.model.User">
    select * from user where name=#{0} and pwd=#{1}
</select>
```
- 调用的时候正常调用

此方法只用于设置 sql 中的字段的值,只能用`#{序号}` 不能使用`${序号}`

### 通过@Param注解设置参数

- 在接口中给参数添加注解
```java
User checkUser(@Param("name") String name, @Param("age") int age);
User fuzzyFindUser(@Param("name") String name, String content);
```
- 在mapper的配置文件中不写`parameterType`
- 在使用参数的地方按照`#{别名}`或者`${别名}`
```html
<select id="checkUser" resultType="User">
    select * from user where name=#{name} and age=#{age}
</select>
```
- 调用的时候正常调用

通过注解形式给参数指定**别名**,供mapper文件中的sql语句使用,如果是占位符使用`#{别名}` 如果是字符串拼接使用`${别名}`

## mybatis­config.xml配置文件

### properties
引入外部的properties文件,可以用于引入外部的数据库连接文件,使用的时候使用el表达式进行引入

- 路径是src路径 ,其内部有标签 `<property>` 
- 如果两个都进行设置, 会先加载内部的`property`再加载**外部**的`properties`的`resource`属性

```html
<properties resource="db.properties"></properties>
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.driverClass}"/>
            <property name="url" value="${jdbc.jdbcUrl}"/>
            <property name="username" value="${jdbc.user}"/>
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>
```

### typeAliases别名
在mapper文件中,如果使用参数类型或者返回值类型的时候需要指定类的全名,可以使用别名来进行配置使用其内部的标签 

- `<typeAliastype="test.mybatis.model.User" alias="User"/>` 将对应的类的别名设置为User或者使用 
- `<package name="test.mybatis.model"/>`会自动扫描当前包和其子包将别名配置为`类名首字母大写(User)`或`类名小写(user)`, 推荐使用这种

```html
<mappers>

    <!-- 配置mapper文件位置 -->
    <mapper resource="sqlMap/Account.xml"/>
    <mapper resource="sqlMap/UserMapper.xml"/>

    <!-- 
    通过接口找文件 
        文件名和接口名称一致，
        文件和接口同包
    -->
    <mapper class="test.mybatis.mapper.UserMapper"/> 

    <!-- 
    package内部原理就是通过接口找文件
    会扫描指定包下及其子包中的所有的mapper文件
        文件名和接口名称一致，
        文件和接口同包
     -->
    <package name="test.mybatis.mapper"/>

</mappers>
```

mybatis内部配置的别名, 在mapper中写哪个都可以

|别名| 映射的类型| 别名| 映射的类型|
|:-:|:-:|:-:|:-:|
|_byte| byte| long| Long|
|_long| long| short| Short
|_short| short| int| Integer|
|_int| int| integer| Integer|
|_integer| int| double| Double|
|_double| double| float| Float|
|_float| float| boolean| Boolean|
|_boolean| boolean| date| Date|
|string| String| decimal| BigDecimal|
|byte| Byte| bigdecimal| BigDecimal|
|map| Map| hashmap| HashMap|
|list| List| arraylist| ArrayList|
|collection| Collection| iterator| Iterator|

## 输入映射(parameterType)与输出映射(resultType)

POJO(Plain Ordinary Java Object)简单的Java对象,实际就是普通JavaBeans

### 输入映射

- 简单类型
  - 整型
  - String类型
- 复杂类型
  - POJO
  - POJO的包装类(内部包含一个普通的类,可以进行多条件查询)
  - 应用范围:查询条件可能是综合的查询条件,不仅包括用户查询条件还包括其它的查询条件(比如查询用户信息的时候,将用户购买商品信息也作为查询条件),这时可以使用包装对象传递输入参数。

### 输出映射
- 简单类型
  - 整型
  - 字符串
```html
<select id="findUserCount" resultType="Integer">
    select count(*) from user
</select>
<select id="findUserName" resultType="String" parameterType="Integer">
    select username from user where id = #{v}
</select>
```
- 复杂类型
  - POJO
  - POJO列表
resultMap
如果sql查询字段名和pojo的属性名不一致,可以通过
resultMap将字段名和属性名作一个对应关系 ,
resultMap实质上还需要将查询结果映射到pojo对象
中。

### resultMap

如果sql查询字段名和pojo的属性名不一致,可以通过resultMap将字段名和属性名作一个对应关系 ,resultMap实质上还需要将查询结果映射到pojo对象中。

#### 同表

字段和属性名称相同的可以不建立映射，会自动映射

- column为数据库中名称
- property为类中名称
- javaType类里面数据类型，一般不设置
- jdbcTyp数据库的数据类型，一般不设置
```html
<resultMap type="User" id="myResultMap">
    <id column="id" property="id"/>
    <result column="name" property="u_name" javaType="String" jdbcType="VARCHAR"/>
</resultMap>
```

#### 多表

字段和属性名称相同的也必须建立手动映射，不会自动映射

- 一对一, 使用association标签， 指定javaType
```html
<association property="department" javaType="department" >
```
- 一对多, 使用collection标签， 指定ofType
```html
<collection property="department" ofType="department" >
```

## 动态sql

### if标签

可用于字符串的非空判断

```html
<select id="findUserByUser111" parameterType="User" resultType="User">
    select * from user where 1=1 
    <if test="name != null and name != '' ">
        and name = like '%' #{name} '%' 
    </if>
    <if test="pwd != null and pwd != '' ">
        and pwd like '%' #{pwd} '%'
    </if>
</select>
```
使用 `where 1=1`  防止为空的时候`and`在最前面

### where标签

- 如果where中没内容，where单词就不会添加
- 会去掉第一个前and单词(紧挨着where的and)

```html
<where>
  <if test="name != null and name != '' ">
    and name like '%' #{name} '%'
  </if>
  <if test="pwd != null and pwd != '' ">
    and pwd like '%' #{pwd} '%'
  </if>
</where>
```

### sql片段

把多次出现的sql封装成一个片段, 在使用的时候进行引用

```html
<sql id="select">
select * from
</sql>
```

### foreach标签

向sql传递数组或List,mybatis使用foreach解析

- collection为传入类型，数组为array list为list
- item为循环的参数
- separator为分隔符
- open和close标签

```html
<foreach collection="array" item="id" separator="," open="id in (" close=")">
#{id}
</foreach>
```







