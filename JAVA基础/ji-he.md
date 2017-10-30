## Java集合

### 对象数组
- 基本类型的数组:存储的元素为基本类型 `int[] arr={1,2,3,4}`
- 对象数组:存储的元素为引用类型 `Student[] stus=new Student[3];`    
 Student代表一个自定义类,Stus数组中stus[0],stus[1],stus[2]的元素数据类型为Student,
  都可以指向一个Student对象    
  
  
- 自动生成构造方法
 - 代码区域右键 -- Source -- Generate Constructors from Superclass...	
- 无参构造方法 
 - 代码区域右键 -- Source -- Generate Constructor using Fields...
- 带参构造方法(自动生成getXxx()/setXxx())
 - 代码区域右键 -- Source -- Generate Getters and Setters...

### 第1章	集合类之ArrayList

为了方便对多个对象进行操作，我们就必须对这多个对象进行存储，而要想对多个对象进行存储，	就不能是一个基本的变量，而应该是一个容器类型的变量。

- `ArrayList<E>`: 大小可变数组的实现
 - `<E>`:是一种特殊的数据类型，泛型。
 - 举例：`ArrayList<String>`,`ArrayList<Student>`
- 构造方法：ArrayList()
 - `public boolean add(E e)`:添加元素
 - `public void add(int index,E element)`:在指定的索引处添加一个元素
 
 ```java
 ArrayList<String> array = new ArrayList<String>();
array.add("hello");
array.add("world");
array.add(1,",");
System.out.println(array);
```

#### ArrayList 的增删改查

1. 获取元素
 -  `public E get(int index)` :返回指定索引处的元素
2. 集合长度
 - `public int size()`:返回集合中的元素的个数
3. 删除元素
 - `public boolean remove(Object o)`:删除指定的元素，返回删除是否成功
 - `public E remove(int index)`:删除指定索引处的元素，返回被删除的元素
4. 修改元素
 - `public E set(int index,E element)`:修改指定索引处的元素，返回被修改的元素

```java
System.out.println(array.get(2));
System.out.println(array.size());
System.out.println(array.remove("hello"));
System.out.println(array.remove(0));
System.out.println(array.set(1, "android"));
```

#### ArrayList集合的遍历
```java
for (int i = 0; i < array.size(); i++) {
    String s = array.get(i);
    System.out.println(s);
}
 ```
 
 

