# JDBC

**JDBC（Java Data Base Connectivity)**, java数据库连接是一种用于执行SQL语句的JavaAPI，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。是Java访问数据库的标准规范

## 开发步骤

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

### 注册驱动

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

### 获取连接
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
		
### 执行sql语句
```java
String sql = "insert into t_student values(null,'zh',25)";
st.execute(sql); //返回boolean类型
```

- `executeQuery()`适用查询语句
- `executeUpdate()`适用于增删改,返回值是`int`,代表影响到行数
- `execute()` 所有sql语录都适用
  - 如果返回值为ture,代表结果是`resultSet`
  - 如果返回值是false代表增删改, `st.getResultSet();`


















