# System类

- 获取当前时间的毫秒值,当前系统时间与1970年01月01日00:00点
之间的毫秒差值
```java
System.currentTimeMillis();
```
- 结束当前程序,通常传入0记为正常状
态,其他为异常状态
```java
System.exit(0);
```
- 数组的拷贝
```java
int[] src = new int[]{1,2,3,4,5};
int[] dest = new int[]{6,7,8,9,10};
//源数组,源数组的起始位置,目标数组,目标数组的起始位置,目标数组的长度
System.arraycopy( src, 0, dest, 0, 3);
System.out.println(Arrays.toString(src)); //[1,2,3,4,5]
System.out.println(Arrays.toString(dest)); //[1,2,3,9,10]
```

