# aop(面向切面编程)

Aspect Oriented Programming是一种编程思想,进行重复的代码进行横向的抽取.

## Spring中两种代理模式

动态代理技术基于接口（java原生），cglib代理技术基于继承（第三方）

- 动态代理
```java
@Test
public void Test1() {
    UserService us = new UserServiceImpl();
    //生成代理对象
    UserService proxy = (UserService) Proxy.newProxyInstance(UserServiceImpl.class.getClassLoader(), 
            UserServiceImpl.class.getInterfaces(), 
            new InvocationHandler() {
                //当调用代理对象的方法的时候,会调用invoke方法
                //第一个参数代表代理对象
                //第二个参数代表代理的方法
                //第三个对象代表代理对象的参数
                //返回值代表调用方法后的结果
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    System.out.println("开启事务");
                    Object obj = method.invoke(us, args);
                    System.out.println("提交事务");
                    return obj;
                }
            });
    proxy.ok();
}
```
- cglib代理
```java
public Object cglibProxy() {
    //创建生成代理工具类
    Enhancer eh = new Enhancer();
    //设置代理对象的父类
    eh.setSuperclass(UserServiceImpl.class);
    //调用方法的时候进行回调
    eh.setCallback(this);
    //创建代理对象
    Object usProxy = eh.create();
    return usProxy;
}
@Override
public Object intercept(Object proxyObj, Method method, Object[] arg2, MethodProxy methodProxy) throws Throwable {
    System.out.println("开启事务");
    Object returnObj = methodProxy.invokeSuper(proxyObj, arg2);
    System.out.println("关闭事务");
    return returnObj;
}
```

## aop中的名词解释

1. Joinpoint(连接点): 目标对象中所有可以增强的方法叫做连接点
2. Pointcut(切入点): 目标对象中要增强的方法
3. Advice(通知/增强): 增强的代码
4. Target(目标对象): 被代理对象
5. Weaving(织入): 将通知应用到连接点的过程
6. Proxy(代理): 生成的代理对象
7. Aspect(切面): 切入点+通知就形成了切面

写一个接口,再写一个实现类,在接口中定义若干方法,方法中做某些事情,当我们去调用这个实现类对象的某个方法的时候,其结果显示增强内容和原来的内容

## aop­xml配置

### 导包
- spring包中的aop包
- spring中的aspect包
- spring依赖包中aopalliance包
- spring依赖包中org.spectj包中的weaver包

### 编写通知代码

在spring中通知代码是通过方法的功能来实现的,需要写一个类将需要的通知写在不同的方法中

```java
public class MyAdvice {
	
	public void beforeAdvice() {
		System.out.println("前置通知");
	}
	public void exceptionAdvice() {
		System.out.println("异常拦截通知");
	}
	public void afterInterruptedExceptionAdvice() {
	System.out.println("会被异常阻断的后置通知");
	}
	public void afterAdvice() {
		System.out.println("不会被异常阻断的后置通知");
	}
	public void aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
	System.out.println("环绕前通知");
	pjp.proceed();
	System.out.println("环绕后通知");
	}
}
```

### 编写目标对象

写UserService与UserServiceImpl

### 进行文件配置

配置切入点, id表示给切入点起的名字

* test.service.impl..*ServiceImpl.*(..)

```html
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">

<bean name="userService" class="test.service.impl.UserServiceImpl"></bean>
<bean name="myAdvice" class="test.aop.MyAdvice"></bean>

<aop:config>

    <aop:pointcut expression="execution(* service.impl..*.*(..))" id="pt"/>

    <aop:aspect ref="myAdvice">
        <!-- 前置通知 -->
        <aop:before method="beforeAdvice" pointcut-ref="pt"/>
         <!-- 会被异常阻断的后置通知-->
        <aop:after-returning method="afterInterruptedExceptionAdvice" pointcut-ref="pt"/>
        <!-- 不会被异常阻断的后置通知 -->
        <aop:after method="afterAdvice" pointcut-ref="pt"/>
        <!-- 异常拦截通知 -->
        <aop:after-throwing method="exceptionAdvice" pointcut-ref="pt"/>
       <!-- 环绕通知 -->
        <aop:around method="aroundAdvice" pointcut-ref="pt"/>
    </aop:aspect>

</aop:config>
```

### aop­注解配置

#### 配置文件

```html
<bean name="userService" class="test.service.impl.UserServiceImpl"></bean>
<bean name="myAdvice" class="test.annotation.MyAdvice"></bean>

<context:component-scan base-package="test.service"></context:component-scan>
<!-- 完成将通知织入到切点中的操作 -->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

#### 在通知类中进行注解

```java
//表示该类是一个通知类
@Aspect
public class MyAdvice {

	@Pointcut("execution(* test.service.impl..*.*(..))")
	public void pct() {}
	
	@Before("MyAdvice.pct()")
	public void beforeAdvice() {
		System.out.println("前置通知");
	}
	@AfterThrowing("MyAdvice.pct()")
	public void exceptionAdvice() {
		System.out.println("异常拦截通知");
	}
	@AfterReturning("MyAdvice.pct()")
	public void afterInterruptedExceptionAdvice() {
	System.out.println("会被异常阻断的后置通知");
	}
	@After("MyAdvice.pct()")
	public void afterAdvice() {
		System.out.println("不会被异常阻断的后置通知");
	}
	@Around("MyAdvice.pct()")
	public void aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
	System.out.println("环绕前通知");
	pjp.proceed();
	System.out.println("环绕后通知");
	}

}
```














