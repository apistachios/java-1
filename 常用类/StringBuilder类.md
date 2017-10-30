## StringBuilder(StringBuffer)

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
sb.reverse(); //反转
sb.insert(1, "s"); //在索引为1的地方插入"s"
sb.delete(1, 2); //删除，左包含，右不包含
sb.replace(1, 2, "E"); //替换，左包含，右不包含
```

### StringBuilder（StringBuffer）与String相互转换

- StringBuilder to String
 - `public String toString()` 通过toString()就可以实现把StringBuilder转成String
```java
StringBuilder sb = new StringBuilder();
sb.append("hello");
String s = sb.toString();
```
- String to StringBuilder
 - `StringBuilder(String str)` 通过构造方法就可以实现把String转成StringBuilder
 - 通过`.append()`添加字符串
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
