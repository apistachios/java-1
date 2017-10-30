## Math类


- `abs()`求绝对值
```java
double d1 = Math.abs(4);
double d2 = Math.abs(-4);
```
- `ceil`方法,结果为比参数值大的最小整数的double值
```java
double d1 = Math.ceil(4.1); //d1的值为5.0
double d2 = Math.ceil(-3.3); //d2的值为-3.0
```
- `floor`方法,结果为比参数值小的最大整数的double值
```java
double d1 = Math.floor(3.3); //d1的值为3.0
double d2 = Math.floor(-3.3); //d2的值为-4.0
```
- `max`方法,返回两个参数值中较大的值
```java
double d1 = Math.max(3.3, 5.5); //d1的值为5.5
```
- `min`方法,返回两个参数值中较小的值
```java
double d1 = Math.min(3.3, 5.5); //d1的值为3.3
```
- `pow`方法,返回第一个参数的第二个参数次幂的值
```java
double d1 = Math.pow(2.0, 3.0); //d1的值为8.0
```
- `round`方法,返回参数值四舍五入的结果
```java
double d1 = Math.round(5.5); //d1的值为6.0
double d2 = Math.round(5.4); //d2的值为5.0
```
- `random`方法,产生一个大于等于0.0且小于1.0的double小数
```
//获取3到5之间的随机数
int a = (int) (Math.random() * 3 + 3);
System.out.println(a);
```
