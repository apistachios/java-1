# JDBC+c3p0+DBUtil

## JDBC

**JDBC（Java Data Base Connectivity)**, java数据库连接是一种用于执行SQL语句的JavaAPI，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。是Java访问数据库的标准规范

### 开发步骤

- 在工程中导入jar包
- 注册驱动 `Class.forName("com.mysql.jdbc.Driver");`
- 获得连接 `Connection con = DriverManager.getConnection(“jdbc:mysql://localhost:3306/mydb”,”root”,”root”);` ,如果是本机路径可以写为 `Connection con = DriverManager.getConnection(“jdbc:mysql:///mydb”,”root”,”root”);`
- 获取执行平台` Statement st = con.createStatement();`
- 执行sql语句 `int num = st.executeUpdate(String sql);` 或者`ResultSet rs = st.executeQuery(String sql);`
- 处理结果集
```java
while( rs.next() ){
	//方法参数为数据库表中的列名
	String sid = rs.getString("pid");
	//获取当前行的分类名称
	String sname = rs.getString("pname");
}
```
- 释放资源
```java
rs.close();
st.close();
con.close();
```

#### 注册驱动

`Class.forName`通过名称加载一个类
```java
Class.forName("com.mysql.jdbc.Driver");
```

- 正常情况系我们注册一个类是通过`DriverManager.registerDriver(Drive实现类);`
- 因为在`com.mysql.jdbc.Driver`，有一个静态代码块，已经做了`DriverManager.registerDriver(Drive实现类)`
- 如果我们创建这个类的对象，在调用注册方法,就会注册两次

```java
//会注册两次,不采用
Driver d = new Driver();
DriverManager.registerDriver(d);
```

#### 获取连接
```java
Connection con = DriverManager.getConnection(“jdbc:mysql://localhost:3306/mydb”,”root”,”root”);
```
		
制定url建立连接, 如果是本机地址可以写成///
```java
Connection con = DriverManager.getConnection("jdbc:mysql:///bigdata","root","root" );
```		
		
### 获取执行平台
```java
Statement st = con.createStatement();
```

- 使用`con.createStatement()`会有sql注入的问题
```java
//如果在登录界面中的密码文本框中输入 111'or'1=1 就会出现 sql 注入的问题
select * from product where pname ='电风扇' and price = '10' or '1=1'
```

- 预处理对象
	1. 防止sql注入 
	2. 如果是文本类型,我们不用去担心 ' ' 的问题
	3. 执行是时候直接调用execute方法,不传参数
```java
Connection con = JDBCUtil.getCon();
String sql = "select * from user where name=? and password=?";
PreparedStatement pst = con.prepareStatement(sql);
pst.setString(1, name);
pst.setString(2, password);
ResultSet rs = pst.executeQuery();
```
		
#### 执行sql语句
```java
String sql = "insert into t_student values(null,'zh',25)";
st.execute(sql); //返回boolean类型
```

- `executeQuery()`适用查询语句
- `executeUpdate()`适用于增删改,返回值是`int`,代表影响到行数
- `execute()` 所有sql语录都适用
  - 如果返回值为ture,代表结果是`resultSet`
  - 如果返回值是false代表增删改, `st.getResultSet();`

#### JDBC工具类

```java
import java.sql.Connection;
import java.sql.DriverManager;

public class JDBCUtils {

	private static final String DRIVER = "com.mysql.jdbc.Driver";
	private static final String URL = "jdbc:mysql:///bigdata";
	private static final String NAME = "root";
	private static final String PASSWORD = "root";

	//加载驱动只用一次,所以使用静态代码块
	static {
		try {
			Class.forName(DRIVER);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	//提供获取连接的方法
	public static Connection getCon()  {
		Connection con = null;
		try {
			//获得连接
			con  = DriverManager.getConnection(URL,NAME,PASSWORD );
		} catch (Exception e) { e.printStackTrace(); }
		//返回连接
		return con;
	}
	
}
```

## C3P0连接池

- 导入jar包
- 将`c3p0config.xml`的配置文件添加到src下进行配置
```html
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
  <default-config>
    <property name="driverClass">com.mysql.jdbc.Driver</property>
	<property name="jdbcUrl">jdbc:mysql://localhost:3306/bigdata</property>
	<property name="user">root</property>
	<property name="password">root</property>
	<property name="initialPoolSize">5</property> <!-- 初始化池子数量 -->
	<property name="maxPoolSize">20</property>
	<property name="minPoolSize">2</property>
  </default-config>
  <named-config name="oracle"> 
    <property name="driverClass">com.oracle.jdbc.Driver</property>
	<property name="jdbcUrl">jdbc:mysql:///mydatabase</property>
	<property name="user">root</property>
	<property name="password">root</property>
  </named-config>
</c3p0-config>
```
- 创建DBUtils工具类
```java
import java.sql.Connection;
import java.sql.SQLException;
import javax.sql.DataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
public class DBUtil {
	//连接池对象需要静态方法中使用,所以是静态成员变量
	private static final DataSource DB = new ComboPooledDataSource();
	public static DataSource getDataSource() {
		return DB;
	}
	public static Connection getCon() {
		Connection con = null;
		try {
			con = DB.getConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return con;
	}
}
```

## DBUtils工具类

DBUtils是JDBC的简化开发工具包

### 增删改

- 创建QueryRunner类的对象,创建对象的时候可以直接指定数据库的连接池
- 调用`update(String sql,Object...params)` 方法执行
```java
QueryRunner qr = new QueryRunner(DBUtils.getDataSource());
int a = qr.update("insert into staff values (null,?,?)", "kkk",1);
```

### 查

- 创建`QueryRunner`类的对象,创建对象的时候可以直接指定数据库的连接池对象
- 调用`query(String sql, ResultSetHandler<T> rsh, Object... params)` 方法执行

ResultSetHandler是一个接口,当我们使用的时候需要他的实现类.


|名称| 用法|
|:-:|:-:|
|ArrayHandler |将结果集中的第一条记录封装到一个Object[]数组中，数组中的每一个元素就是这条记录中的每一个字段的值
|ArrayListHandler| 将结果集中的每一条记录都封装到一个Object[]数组中，将这些数组在封装到List集合中。
|BeanHandler| 将结果集中第一条记录封装到一个指定的javaBean中。
|BeanListHandler| 将结果集中每一条记录封装到指定的javaBean中，将这些javaBean在封装到List集合中
|ColumnListHandler| 将结果集中指定的列的字段值，封装到一个List集合中
|ScalarHandler| 它是用于单数据。例如select count(*) from 表操作。
|MapHandler| 将结果集第一行封装到Map集合中,Key 列名, Value 该列数据
|MapListHandler| 将结果集每一行封装到Map集合中,Key 列名, Value 该列数据,Map集合存储到List集合

- JavaBean是一个类，在开发中常用封装数据
  - 需要实现接口：java.io.Serializable ，通常实现接口这步骤省略了，不会影响程序.
  - 提供私有字段：private 类型 字段名;
  - 提供getter/setter方法：
  - 提供无参构造


- BeanHandler 封装结果的第一条记录
```java
QueryRunner qr = new QueryRunner(DBUtils.getDataSource());
String theSql = "select * from staff";
Staff staff = qr.query(theSql, new BeanHandler<>(Staff.class));
```
- ScalarHandler 用于单数据
```java
QueryRunner qr = new QueryRunner(DBUtils.getDataSource());
String theSql = "select * from staff";
Long num = qr.query("select count(*) from staff", new ScalarHandler<Long>());
```
- BeanListHandler 将结果封装到List集合中
```java
QueryRunner qr = new QueryRunner(DBUtils.getDataSource());
String theSql = "select * from staff";
List<Staff> list = qr.query(theSql, new BeanListHandler<>(Staff.class));
```



