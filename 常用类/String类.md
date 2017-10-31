# String字符串

## 构造方法

- `object`代表任意引用类型，不代表基本类型
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

## String的format方法
format为静态方法    
整形的占位符为%d，浮点类型的为%f
```java
String s = String.format("%02d%.1f", i,1.2);
```

##  String类的判断功能

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

##  String类的获取功能

* String类的获取功能：
* `int length()` 获取字符串的长度，其实也就是字符个数
* `char charAt(int index)` 获取指定索引处的字符
* `int indexOf(String str)` 获取str在字符串对象中第一次出现的索引
* `String substring(int start)` 从start开始截取字符串
* `String substring(int start,int end)` 从start开始，到end结束截取字符串。包括start，不包括end
* `boolean contains(CharSequence s) `当前字符串是否包含另一个字符串 

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
//boolean contains(CharSequence s) 当前字符串是否包含另一个字符串，返回true或者false
System.out.println(s.contains("llo"));
```

## String类的其他方法

### 字符串转换为数组
```java
char[] ch = s.toCharArray();
byte[] by = s.getBytes();
```

### 字符串的分割
```java
String str = "1:2:3:4";
String[] strArr = str.split(":");
//参数中传的字符串是正则表达式，在正则中，.的含义是任意字符
//遇到拆分. 要写成\\.
String st = "1.2.3.4";
String[] stArr = st.split(".");
String[] stArr1 = st.split("\\.");
```

### 转换字符串的大小写

```java
String strr = "HelloWorld";
//toUpperCase()全部变为大写
System.out.println(strr.toUpperCase());
//toLowerCase()全部变为小写
System.out.println(strr.toLowerCase());
```

### 去除字符串两端空格
去除字符串两端空格 `String trim()`
```java
String s = "  helloworld  ";
System.out.println("---"+s.trim()+"---");
```

##  遍历字符串

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

##  String类的转换功能

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

## string的api
```java
boolean equals(Object obj) 判断两个字符串中的内容是否相同
boolean equalsIgnoreCase(String str)判断两个字符串中的内容是否相同, 忽略大小写
boolean contains(String str) 判断该字符串中是否包含给定的字符串
boolean startsWith(String str) 判断该字符串是否以给定的字符串开头
boolean endsWith(String str) 判断该字符串是否以给定的字符串结尾
boolean isEmpty() 判断该字符串的内容是否为空的字符串""
int length() 获取该字符串的长度
char charAt(int index) 获取该字符串中指定位置上的字符
String substring(int start) 从指定位置开始,到末尾结束,截取该字符串,返回新字符串
String substring(int start,int end) 从指定位置开始,到指定位置结束,截取该字符串,返回新字符串
int indexOf(int ch ) 获取给定的字符,在该字符串中第一次出现的位置
int indexOf(String str) 获取给定的字符串,在该字符串中第一次出现的位置
int indexOf(int ch,int fromIndex) 从指定位置开始,获取给定的字符,在该字符
byte[] getBytes() 把该字符串 转换成 字节数组
char[] toCharArray() 把该字符串 转换成 字符数组
String replace(char old,char new) 在该字符串中,将给定的旧字符,用新字符替换
String replace(String old,String new) 在该字符串中, 将给定的旧字符串,用新字符串替换
String trim() 去除字符串两端空格,中间的不会去除,返回一个新字符串
String toLowerCase() 把该字符串转换成 小写字符串
String toUpperCase() 把该字符串转换成 大写字符串
int indexOf(String str,int fromIndex) 从指定位置开始,获取给定的字符串,在该字符串中第一次出现的位置
```













