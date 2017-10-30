
## Date类

- 使用无参构造方法创建的日期对象默认就是当前系统时间
```java
Date date = new Date();
System.out.println(date);
```
- 指定日期,参数为自1970年1月1日00:00:00 GMT 以来的毫秒数。负数指示在1970年1月1日00:00:00 GMT 之前的毫秒数
```java
Date date1 = new Date(1000l); //long类型
System.out.println(date1);
```
- getTime,获取1970年1月1日00:00:00 GMT 以来此Date对象表示的毫秒数
```java
long time = date.getTime();
System.out.println(time);
```
- after 测试此日期是否在指定日期之后。
```java
System.out.println(date1.after(date2)); //date1<date2返回false
```java
- before 测试此日期是否在指定日期之前。
```java
System.out.println(date1.before(date2)); //date1<date2返回ture
```
- compareTo比较两个日期,相同返回0
```java
System.out.println(date1.compareTo(date2)); //date1<date2返回-1
```
- toString 把此 Date 对象转换为以下形式的 `String：
 dow mon dd hh:mm:ss zzz yyyy `输出时自动调用

### DateFormat 类
- 创建对象

|标志| 含义|
|:-:|:-:|
|G |年代标志符
|y |年
|M |月
|d |日
|h |时 在上午或下午 (1~12)
|H |时 在一天中 (0~23)
|m |分
|s |秒
|S |毫秒
|E |星期
|D |一年中的第几天
|F |一月中第几个星期几
|w |一年中第几个星期
|W |一月中第几个星期
|a |上午 / 下午 标记符
|k |时 在一天中 (1~24)
|K |时 在上午或下午 (0~11)
|z |时区



- 将date对象转换为字符串
```java
DateFormat df = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss a");
String str =  df.format(date);
System.out.println(str);
```
- 字符串转换为date
```java
String st = "2020年11月11日";
DateFormat df1 = new SimpleDateFormat("yyyy年MM月dd日");
// 处理异常
try {
    Date date3 = df1.parse(st);
    System.out.println(date3);
} catch (ParseException e) {
    e.printStackTrace();
}
```
