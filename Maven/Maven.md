# Maven

maven的主要作用是管理项目中所需要的jar包

## maven的仓库

maven将用到的所有jar包,保存到仓库中

- 本地仓库是我们自己进行管理,在本机的电脑中存的
- 远程仓库(私服)有的公司会自己搭建一个属于公司内部的仓库
- 中央仓库,有maven团队进行管理,维护着大概两个亿的jar包

本地仓库没有所需要的jar包,会一级一级进行下载,如果没有远程仓库,本地仓库就会直接从中央仓库进行下载

### 配置本地仓库

maven中的conf文件夹,编辑 `settings.xml`
```html
<localRepository>/home/siyu/eclipse/JarPackage/maven/repository</localRepository>
```

在 `<profiles></profiles>` 标签中添加 jdk1.8 的支持, 默认是jdk1.5
```html
<profile>  
<id>jdk18</id>  
<activation>  
    <activeByDefault>true</activeByDefault>  
    <jdk>1.8</jdk>  
</activation>  
<properties>  
    <maven.compiler.source>1.8</maven.compiler.source>  
    <maven.compiler.target>1.8</maven.compiler.target>  
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>  
</properties>   
</profile> 
```

## maven项目

### maven工程的目录结构

- `src/main/java` 存放项目的.java文件
- `src/main/resources` 存放项目的资源文件,如spring和mybatis中的xml文件
- `src/main/webapp` web工程存放web资源
- `src/main/webapp/WEB-INF` 存放web.xml文件
- `src/test/java` 存放单元测试的.java文件,如JUnit测试类
- `src/test/resouces` 测试的资源文件,如果没有会从main中寻找
- `target` 项目输出位置,编译后的class文件,打成的jar和war
- `pom`  maven项目的核心配置文件

### maven常用命令

- `mvn compile` 编译src/main/java下的文件生成class文件输出到target文件夹,不会编译test的文件
- `mvn test` 编译src/test/java下的单元测试类
- `mvn clean` 清除target下的文件
- `mvn package` 可以将web项目打包成war,java项目打包成jar,放在target目录下,使用此命令会执行 `compile`,`test`
- `mvn install` 可以将对应的应用打包成war和jar并发布到本地仓库中, 内部会执行 `compile`,`test`,`package`
- `tomcat :run` 使用内置的tomcat插件运行web应用
- `mvn clean install` 同时执行两个命令

## maven的依赖管理

- `<dependencies>` 标签表示当前项目所依赖的jar包,其内部是 `<dependency>` 标签
- `<dependency>` 表示具体的依赖其内部有4个标签
- `<groupId>` 表示组织名+项目名
- `<artifactId>` 模块名称
- `<version>` 表示版本号
- `<scope>` 表示依赖范围

|依赖范围 |编译 | 测试 |运行 |例子|
|:-:|:-:|:-:|:-:|:-:|
|compile| √ | √| √| spring-core|
|test | | √| | Junit|
|provided| √| √| | servlet-api|
|runtime| | √| √| JDBC驱动|
|system| √| √| | 本地Maven仓库之外的类库|

- `compile` 表示编译时依赖,是默认值,会在编译,测试,运行的时候都要使用
- `provided` 只在编译和测试时依赖,运行的时候不依赖,如 servlet-api 因为tomcat已经提供,所以运行的时候如果使用就会出现错误,所以设置为 `provided`
- `runtime` 运行和测试的时候需要,编译的时候不需要,如jdbc驱动设置为runtime
- `test` 测试的时候依赖,编译和运行的时候不依赖,如Junit
- `system` 依赖范围和provided相同,但是需要提供一个磁盘路径,不推荐使用

```html
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## maven的坐标查找

### 网页查找
如果我们需要使用jar包,就需要知道对应jar包的坐标,可以通过https://mvnrepository.com/进行查找

### 本地仓库查找

maven - Add Dependency

## 配置插件

- `<build>` 项目构建配置,配置编译,运行插件等
- `<plugins>` 内部放 `<plugin>` ,是管理的插件
- `<plugin>` 插件标签,内部配置该插件内部有 `<configuration><configuration>` 表示配置信息

### jdk版本插件

 maven中默认jdk为1.5, 通过修改settings将默认的jdk版本改成1.8, 也可以通过在pom文件中配置jdk的编译插件

```html
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.5.1</version>  
    <configuration>
        <source>1.7</source>
        <target>1.7</target>
        <encoding>UTF-8</encoding>
    </configuration>
</plugin>
```

### tomcat插件

```html
<plugins>
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
            <port>8081</port>
        </configuration>
    </plugin>
</plugins>
```

## 依赖传递

当我们的项目依赖了 `spring-aop `的包的时候,通过查看依赖关系会发现,aop会依赖core,core又会依赖commons­logging,所以最后我们的项目中这些jar包都会被依赖进来,这种现象叫做传递依赖

### 依赖冲突

两个jar包对同一个依赖的版本不同就会出现冲突

- 路径近者优先原则： 采用依赖的路径最短的
- 第一声明优先原则： 路径相同的情况下，采用在POM中依赖声明的顺序靠前的依赖

### 解决冲突

#### 排除依赖

在依赖内部添加 `<exclusions>` 其内部写 `<exclusion>` 标签指定组名称和模块名称,不需要再去指定版本号

```html
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>3.0.5.RELEASE</version>
    <exclusions>
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

#### 版本锁定

我们可以在pom文件的 `<dependencies>` 标签的外部定义标签 `<dependencyManagement>` 用来进行版本的直接锁定,其内部的标签为 `<dependencies>`在其内部可以添加具体的依赖,指定版本号

```html
<dependencyManagement>
    <dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
    </dependencies>
</dependencyManagement>
```

如果使用了版本的锁定,在其下面进行依赖的时候又添加 `spring-beans` ,是不需要指定版本号的,如果指定了就会报警告, 会使用指定的版本

### 属性值设置

spring版本的号都一样的情况下， 可以通过属性标签直接定一个`spring`版本号的变量,让其他依赖都引用这个变量

```html
<properties>
    <spring.version>4.2.4.RELEASE</spring.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
    </dependency>
</dependencies>
```




