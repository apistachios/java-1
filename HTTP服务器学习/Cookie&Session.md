# Cookie&Session

HTTP协议是无状态的协议,也就是说每次客户端访问服务器,对于服务器而言,其实并不知道是谁在访问,如果要保证每次客户端访问时候,让我们的服务器能够区分出来是谁在访问,就需要运用会话技术

- 会话
  - 从用户打开客户端访问我们的web应用,到用户关闭客户端这个过程称作为一次会
  - 因为客户端访问我们的web应用,就会在操作系统的内存中开辟一块内存空间存储访问过程中的数据,直到用户关闭浏览器,内存中的数据才会清空.
- 会话技术的分类
  - `Cookie`:数据存储在客户端本地,减少服务器端的存储的压力,安全性不好,客户端 可以清除cookie
  - `Session`:将数据存储到服务器端,安全性相对好,增加服务器的压力

## Cookie
将数据存入客户端,特点是安全性不好,但是减少了服务器端的压力

### cookie存储过程
服务器端将需要存储的数据放在cookie中,通过响应头的形式发送给客户端,客户端收到了这个头信息,就会将数据进行保存,客户端再次请求服务的时候就会将此信息放在请求头中,那么服务器端通过获取请求头就能得到不同用户的cookie,从而区分出了不同的用户,做出不同的处理

- 创建cookie
```java
Cookie cookie = new Cookie("name","lisi");
```
- 发送cookie
```java
response.addCookie(cookie);
```

- cookie存储到本地磁盘: 设置的cookie默认是会话级别的,也就是说浏览器关闭后,cookie就没有了(因为会话级别的cookie存储在本机的内存当中),我们可以设置cookie的存取时间(设置后就会将cookie存储到本地磁盘当中)
```java
//单位是秒
cookie.setMaxAge(10*60);
```
- 设置cookie默认会在请求和servlet**同路径**的情况下才会携带cookie中存储的数据,包含**同级目录**和**下级目录**,我们也可以设置cookie携带路径
```java
//代表访问当前应用中的任何资源都携带cookie
cookie.setPath(request.getContextPath());
//代表访问WebTest中的cookieServlet时才携带cookie信息
cookie.setPath("/WebTest/cookieServlet");
```
 - 如果设置了时间那么客户端会自动删除cookie,如果未满时间,想要删除cookie,那么要使用同名同路径的持久化时间为0的cookie进行覆盖即可
 ```java
Cookie cookie = new Cookie("name","");
cookie.setPath(request.getContextPath());
cookie.setMaxAge(0);
response.addCookie(cookie);
 ```
- 客户端接收cookie
```java
for (Cookie ck : cookies) {
    if (ck.getName().equals("name")) {
        System.out.println(ck.getValue());
    }
}
```

## Session

将用户的数据存储到服务器,会为每一个客户端创建一块内存空间,在用户第一请求的时候通过响应头的形式将这块内存空间的编号发送给客户端,客户端每次请求将`JSESSIONID`放在请求头中访问服务器,服务器会找到客户端对应的那块内存空间

### 获得Session对象
```java
HttpSession session = request.getSession();
//获取JSESSIONID
String sessionID = session.getId();
System.out.println(sessionID);
```
- 如果当前的客户端没有在服务器开辟session空间,通过这个方法就会在服务器内部开辟内存空间
- 如果当前客户端已经开辟session空间,通过这个方法就能直接获取已有的Session对象  


- 然后将session的`JSESSIONID`放入cookie,并设置cookie的携带路径为当前的web应用,自动通过response返回.下次再通过浏览器访问该web应用就会携带这个`JSESSIONID`,找到客户端对应的那块内存中的session

### Session为域对象
session和request,servletContext相同都是一个域对象,可以存放读取删除数据

- 向request域中放入数据 `setAtrribute(String name,Object obj);`
- 从request域中获取数据 `getAttribute(String name);`
- 从request域中删除数据 `removeAttribute(String name);`

###　Session的生命周期

- 创建:第一次调用 `request.getSession();`
- 销毁有3中方式:  
  1. 服务器关闭
  2. session过期,默认30分钟(是从不操作服务器端的资源开始计算),可以在web.xml中进行修改,我们可以在web应用的web.xml进行同样的配置
```java
<session-config>
<session-timeout>30</session-timeout>
</session-config>
```
  3. 调用 `session.invalidate();`

存放JSESSIONID的cookie默认是会话级别,所以关闭了浏览器,再次访问JSESSIONID就不存在了,所以会重新创建新的session, 我们可以按照系统的格式创建一个同名称的cookie,添加一个存取时间
```java
HttpSession session = request.getSession();
String sessionID = session.getId();
Cookie cookie = new Cookie("JSESSIONID", sessionID);
cookie.setPath(request.getContextPath());
cookie.setMaxAge(10*60);
response.addCookie(cookie);
```