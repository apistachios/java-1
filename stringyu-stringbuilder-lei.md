## String字符串

构造方法

- `String(String original)` 把字符串数据封装成字符串对象
- `String(char[] value)` 把字符数组的数据封装成字符串对象
- `String(char[] value, int index, int count)` 把字符数组中的一部分数据封装成字符串对象

```java
//String(String original):把字符串数据封装成字符串对象
String s1 = new String("hello");
//String(char[] value):把字符数组的数据封装成字符串对象
char[] chs = {'h','e','l','l','o'};
String s2 = new String(chs);
//String(char[] value, int index, int count):把字符数组中的一部分数据封装成字符串对象
String s3 = new String(chs,0,chs.length); //(数组名,起始位置,长度)
// 直接赋值
String s4 = "hello";
``` 


- ==
 - 基本数据类型：比较的是基本数据类型的值是否相同
 - 引用数据类型：比较的是引用数据类型的地址值是否相同
- 通过过构造方法创建的字符串对象和直接赋值方式创建的字符串不相同
 - 通过构造方法创建字符串对象是在堆内存。
 - 直接赋值方式创建对象是在方法区的常量池。

###  String类的判断功能

Object:是类层次结构中的根类，所有的类都直接或者间接的继承自该类。

- `boolean equals(Object obj)` 比较字符串的内容是否相同
- `boolean equalsIgnoreCase(String str)` 比较字符串的内容是否相同,忽略大小写
- `boolean startsWith(String str)` 判断字符串对象是否以指定的str开头
- `boolean endsWith(String str)` 判断字符串对象是否以指定的str结尾

```java
String s1 = "hello";
String s2 = "hello";
//boolean equals(Object obj):比较字符串的内容是否相同
System.out.println(s1.equals(s2));
//boolean equalsIgnoreCase(String str):比较字符串的内容是否相同,忽略大小写
System.out.println(s1.equalsIgnoreCase(s2));
//boolean startsWith(String str):判断字符串对象是否以指定的str开头和ll结尾
System.out.println(s1.startsWith("he"));
System.out.println(s1.endtsWith("lo"));
```

###  String类的获取功能

* String类的获取功能：
* `int length()` 获取字符串的长度，其实也就是字符个数
* `char charAt(int index)` 获取指定索引处的字符
* `int indexOf(String str)` 获取str在字符串对象中第一次出现的索引
* `String substring(int start)` 从start开始截取字符串
* `String substring(int start,int end)` 从start开始，到end结束截取字符串。包括start，不包括end

- 注意事项
 - `int indexOf(String str)`获取的内容不存在时返回-1
 - `int indexOf(String str)`获取的内容为字符串的话按第一个字符计算
 - `String substring(int start,int end)` 包括start，不包括end

```java
String s = "helloworld";
//int length():获取字符串的长度，其实也就是字符个数
System.out.println(s.length());
//char charAt(int index):获取指定索引处的字符
System.out.println(s.charAt(1));
//int indexOf(String str):获取str在字符串对象中第一次出现的索引(首字符出现位置)
System.out.println(s.indexOf("owo"));
System.out.println(s.indexOf("ak")); //不存在的话为-1
//String substring(int start):从start开始截取字符串
System.out.println(s.substring(0));
//String substring(int start,int end):从start开始，到end结束截取字符串
System.out.println(s.substring(0, s.length()));
```
###  遍历字符串

- 原始做法
```java
String s = "abcde";
System.out.println(s.charAt(0));
System.out.println(s.charAt(1));
System.out.println(s.charAt(2));
System.out.println(s.charAt(3));
System.out.println(s.charAt(4));
```

- 用循环改进
```java
for(int x=0; x<5; x++) {
	   System.out.println(s.charAt(x));
}
```

- 用length()方法获取字符串的长度
```java
for(int x=0; x<s.length(); x++) {
	   System.out.println(s.charAt(x));
}
```
- 把字符串转换为字符数组，然后遍历数组
```java
char[] chs = s.toCharArray();
for(int x=0; x<chs.length; x++) {
	   System.out.println(chs[x]);
}
```

统计一个字符串中大写字母字符，小写字母字符，数字字符出现的次数。(不考虑其他字符)
```java
String s = FSESEGSEddd111;
int bigCount = 0;
int smallCount = 0;
int numberCount = 0;

//遍历字符串，得到每一个字符
for(int x=0; x<s.length(); x++) {
	char ch = s.charAt(x);
	//拿字符进行判断
	if(ch>='A' && ch<='Z') {
		bigCount++;
	}else if(ch>='a' && ch<='z') {
		smallCount++;
	}else if(ch>='0' && ch<='9') {
		numberCount++;
	}else {
		System.out.println("该字符"+ch+"非法");
	}
}
```


###  String类的转换功能

* `char[] toCharArray()` 把字符串转换为字符数组
* `String toLowerCase()` 把字符串转换为小写字符串 
* `String toUpperCase()` 把字符串转换为大写字符串

```java
String s = "abcde";

//char[] toCharArray():把字符串转换为字符数组
char[] chs = s.toCharArray();
for(int x=0; x<chs.length; x++) {
    System.out.println(chs[x]);
}

//String toLowerCase():把字符串转换为小写字符串
System.out.println("HelloWorld".toLowerCase());

//String toUpperCase():把字符串转换为大写字符串
System.out.println("HelloWorld".toUpperCase());
```

把字符串的首字母转成大写，其余为小写
```java
String s = "helloWORLD";

//截取首字母
String s1 = s.substring(0, 1);

//截取除了首字母以外的字符串
String s2 = s.substring(1);

//首字母转成大写，其余为小写
String s3 = s1.toUpperCase()+s2.toLowerCase();

//输出即可
System.out.println("s3:"+s3);
```

### String 的其他功能

* 去除字符串两端空格 `String trim()`
```java
String s = "  helloworld  ";
System.out.println("---"+s.trim()+"---");
```
* 按照指定符号分割字符串 `String[] split(String str)`
```java
String s = "aa,bb,cc";
String[] strArray = s.split(",");
for(int i=0; i<strArray.length; i++) {
	   System.out.println(strArray[i]);
}
 ```

## Stringbuilder

 StringBuilder:是一个可变的字符串。字符串缓冲区类。  
- String和StringBuilder的区别：
 - String的内容是固定的
 - StringBuilder的内容是可变的
- += 拼接字符串每次拼接都会产生新的字符串对象,而利用StringBuilder来拼接字符串自始至终用的都是同一个StringBuilder容器

### 构造方法
- 构造方法 `StringBuilder()`
- 成员方法
 - `public int capacity()` 返回当前容量 (理论值，默认16)
 - `public int length()` 返回长度(已经存储的字符个数，默认0)
 - `public StringBuilder append(任意类型)` 添加数据，并返回自身对象
 - `public StringBuilder reverse()` 反转功能

```java
StringBuilder sb = new StringBuilder();

sb.append("hello");
sb.append(true);
sb.append("hello").append(true); //链式编程
sb.reverse();
```

### StringBuilder与String相互转换

- StringBuilder to String
 - `public String toString()` 通过toString()就可以实现把StringBuilder转成String
```java
StringBuilder sb = new StringBuilder();
sb.append("hello");
String s = sb.toString();
```
- String to StringBuilder
 - `StringBuilder(String str)` 通过构造方法就可以实现把String转成StringBuilder
```java
String s = "hello";
StringBuilder sb = new StringBuilder(s);
```

### 数组拼接成字符串
将数组{1,2,3}转换为字符串[1, 2, 3]
```java
public static void main(String[] args) {
    int[] arr = {1,2,3};
    String s = arrayToString(arr);
    System.out.println(s);
}
	
public static String arrayToString(int[] arr) {
    StringBuilder sb = new StringBuilder();
    sb.append("[");
    for (int i = 0; i < arr.length; i++) {
        if (i==arr.length-1) {
            sb.append(arr[i]);
        } else {
            sb.append(arr[i]).append(", ");
        }
    }
    sb.append("]");
    String result = sb.toString();
    return result;
	}
```

### 字符串反转
String转换为StringBuilder，再reverse()，再转换为String
```java
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    String s = sc.nextLine();
    String resout = myReverse(s);
    System.out.println(resout);

}

public static String myReverse(String s) {
    StringBuilder sb = new StringBuilder(s);
    sb.reverse();
    String resout = sb.toString();
    return resout;
}
```

### 判断一个字符串是否是对称字符串
将字符串反转，然后判断是否相等
```java
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		String s = sc.nextLine();
		boolean b = isSymmetry(s);
		System.out.println(b);
	}
	
	public static boolean isSymmetry(String s){
		StringBuilder sb = new StringBuilder(s);
		sb.reverse();
		String resout = sb.toString();
		
		return resout.equals(s);
	}
    ```












