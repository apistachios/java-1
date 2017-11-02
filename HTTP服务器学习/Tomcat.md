# Tomcat

## Tomcat目录结构
- bin: 可执行的脚本程序  
  - 如启动脚本 `startup.bat` 
  - 停止脚本`shutdown.bat`
- conf: 配置信息文件  
  - `server.xml`核心配置文件:可以设Tomcat端口,编码格式,web应用的发布信息
  - `tomcat­users.xml`用户权限配置文件:用于设置用户的分组和用户的密码
  - `web.xml` web项目默认配置文件,可以配置缺省路径,配置`tomcat`默认`servlet`
- lib: 依赖库,tomcat和web项目中需要使用的jar包,如`jsp-api.jar` 和`servlet-api.jar`
- logs: 日志文件夹,如 `catalina.2017-07-11.log` 查看tomcat日志
- temp: 临时文件目录,其中内容可以任意删除
- webapps: 存放发布的web应用的目录
- work: tomcat解析jsp文件的工作目录,会将jsp解析成的servlet就存在于这个目录

## Tomcat启动

- 需要jdk的支持,并且需要配置环境变量`JAVA_HOME`
- 本机地址`127.0.0.1`或者`localhost`
- 端口配置,默认tomcat是8080端口
- 80端口:http协议默认的端口,我们可以通过修改servler.xml设置端口
- 配置用户名管理tomcat下所有的web应用,修改 `tomcat­-users.xml`添加
```html
<user password="admin" roles="manager-gui,tomcat,role1" username="admin"/>
```

## Eclipse绑定Tomcat
- 添加方法: Window -- Preferences -- Server --Runtime Environments 勾选Create a new local server
- 创建项目时 Dynamic web module version 选2.5

### 目录介绍

- eclipse中的｀WebContent｀目录中所有的内容会被发布到tomcat的webapp下
- 文件夹的名字和当前的工程名字相同
- tomcat文件夹中的内容只有eclipse下WebContent下的所有内容
- `META-­INF`文件夹是用来配置应用程序、扩展程序、类加载器和服务
- `WEB­-INF`文件夹是受保护的,外界不能直接访问,里面class文件夹存放的class文件,lib文件夹存放web应用需要的jar包还有负责配置web工程的web.xml文件

