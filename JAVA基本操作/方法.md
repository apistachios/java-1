## 方法

### 创建方法

1. public 共有的，可以被别人调用的方法
2. 返回修饰符： 表示方法的访问权限，可以省略
 - static： 让当前方法被任意调用
 - void： 方法没有返回值
3. 方法名： 首字母小写，其余单词首字母大写 
4. 形参： 调用这个方法时必须给定一个参数 
 - 多个参数用英文逗号, 隔开 
5. 形参： 运行结束后会返回一个结果，可以让调用这个方法的得到一个返回值
 -  void： 方法没有返回值，当调用这个方法时，调用结果不会有任何结果
 -  String及其他都必须有返回值
6. 返回值： return 返回给调用者一个和方法名前面声明的类型相同的结果
7. 实参： 在调用方法的时候，()里面传递过去的参数

### 使用方法

```java
public static int sum(int a, int b) {
	int sum = a +b;
	return sum;
}
public static void main(String[] args) {
	System.out.println(sum(1, 2));
}
```

### 方法重载

- 同一个类中，方法名相同，参数列表不同
- 方法是重载和修饰符，返回值都没关系
```java
// 三个数相加
public static int sum(int a,int b,int c) {
    return a + b + c; 
}
//两个数相加
public static int sum(int a, int b) {
    return a + b; 
}
public static void main(String[] args) {
    System.out.println(sum(1, 2));
    System.out.println(sum(1, 2, 3));
} 
```
### 方法递归

一个方法想递归自己，一定会带有一个返回值，让第二次调用的方法可以得到第一次的结果

```java
// 求阶乘
public static void main(String[] args) {	
    System.out.println(fac(5));
} 

public static int fac(int i) {
    if(i==1) {
        return 1;
    }
    return i*fac(i-1);
}
```
```java
// Fibonacci数列第40项的值
public static int fib(int i) {
    if (i==1 || i==2 ) {
        return 1;
    }
    return fib(i-1)+fib(i-2);
}
public static void main(String[] args) {
    System.out.println(fib(40));
}
```     
```java
// 1!+2!+3!+……+n!
static long fac(int n) {
        return n < 2 ? 1 : n * fac(n - 1); 
    }
	static long sum(int n) {
        return n < 2 ? 1 : fac(n) + sum(n - 1);
    }
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int i = sc.nextInt();
		System.out.println(sum(i));
	}
```


#### 注意事项
- 方法的参数是基本类型的时候：形式参数的改变不影响实际参数
- 方法的参数是引用类型的时候：形式参数的改变直接影响实际参数
- 如果有局部变量名和成员变量名相同，在局部使用的时候，采用的是就近的原则。
