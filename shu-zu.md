## 数组
数组： 用来存储一组相同类型数据的数据结构

- 数组中元素类型必须相同
- 数组一旦创建长度不可改变

#### 定义格式
- 数据类型[] 数组名;
 - `int[] a;` 定义一个int类型的数组，数组名是a
- 数据类型 数组名[];
 - `int a[];` 定义一个int类型的变量，变量名是a数组

#### 数组初始化
所谓初始化，就是为数组开辟内存空间，并为数组中的每个元素赋予初始值

- 动态初始化	只指定长度，由系统给出初始化值
 - `int[] arr = new int[3]; //数据类型[] 数组名 = new 数据类型[数组长度];`
- 静态初始化	给出初始化值，由系统决定长度
 - `int[] i = new int[] {1,2,3,4};`
 - 一般写简化格式 `int[] i = {1,2,3,4};`

#### 二维数组 
- `int[][] arr = new int[2][2];` 
- `int[][] arr = {{1,2}, };`
- 定义二维不定长数组
```java
int[][] arr = new int[2][];
arr[0] = new int[] {1,2,3,4};
arr[1] = new int[] {1,2,3};
 ````



#### 数组元素访问

- 遍历数组元素
```java
int[] a = {1,2,3,4,5,6};
for (int b : a) { 
	System.out.println(b);
}
```

- 遍历二维数组元素
```java
int[][] arr = {{1,2},{1,2,3}};
for (int i = 0; i < arr.length; i++) {
    for (int j = 0; j < arr[i].length; j++) {
        System.out.println(arr[i][j]);
    }
}
for (int i[] : arr) {
    for (int j : i) {
        System.out.println(j);
    }
}
```

- 选择排序（从大到小）
```java
int[] arr = {3,5,36,9,56,0,34,6};
for(int i=0; i<arr.length; i++) {
	for(int j=i+1; j<arr.length; j++) {
		if (arr[j]>arr[i]) {
			int max = arr[j];
			arr[j] = arr[i];
			arr[i] = max;
		} 
	}
}
```

- 将数组转为字符串
```java
//String(String original):把字符串数据封装成字符串对象
String s1 = new String("hello");
System.out.println("s1:"+s1);
//String(char[] value):把字符数组的数据封装成字符串对象
char[] chs = {'h','e','l','l','o'};
String s2 = new String(chs);
System.out.println("s2:"+s2);
//String(char[] value, int index, int count):把字符数组中的一部分数据封装成字符串对象
String s3 = new String(chs,1,4); //从chs[1]开始，共四个
System.out.println("s3:"+s3);
String s4 = "hello";
System.out.println("s4:"+s4);
```

 