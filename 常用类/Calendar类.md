## Calendar类

- 创建对象
```java
Calendar c = Calendar.getInstance();
```

- 获取年月日等信息
```java
Calendar c = Calendar.getInstance();
int year = c.get(Calendar.YEAR);
int month = c.get(Calendar.MONTH);//从0开始算起,最大11;0代表1月,11代表12月
int date = c.get(Calendar.DATE);
int hour = c.get(Calendar.HOUR);
int min = c.get(Calendar.MINUTE);
int sec = c.get(Calendar.SECOND);
System.out.println(year+"--"+month+"--"+date+"--"+hour+"--"+min+"--"+sec);
```

- 指定字段增加值
```java
c.add(Calendar.DATE, 3); //修改当前时间为3天后
c.add(Calendar.HOUR, 5); //修改当前时间为5小时后
```

- 设置指定字段的值
```java
//设置时间为2020年5月20日
//c.set(2020,4,20);
c.set(Calendar.YEAR, 2020);
c.set(Calendar.MONTH, 4);
c.set(Calendar.DATE, 20);
```

- 转换为日期对象
```java
Date d = c.getTime();
System.out.println(d);
```

- 使用日历计算平年闰年
```java
Calendar c = Calendar.getInstance();
c.set(2000, 2, 1);
c.add(Calendar.DATE, -1);
int a = c.get(Calendar.DATE);
System.out.println(a);
```

