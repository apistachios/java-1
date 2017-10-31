# Arrays类

arrays是集合的工具类,用来操作数组

- 使用二分查找int数组指定元素,必须根据元素的自然顺序对数组进行升序排序
```java
int[] arr = {1,3,4,5,6};
int index = Arrays.binarySearch(arr, 4); //index=2
int index2= Arrays.binarySearch(arr, 2); //index=-2
```
- 数组升序排序
```java
int[] arr = {1,5,9,3,7};
Arrays.sort(arr);
System.out.println(Arrays.toString(arr));
```
- 将数组的内容以字符串形式呈现
```java
System.out.println(Arrays.toString(arr));
```






