## Map接口
Map是集合容器,存放的元素由键与值两部分组成,通
过键可以找对所对应的值,键和值必须是引用类型,键
唯一不能重复,没有顺序

- map结构中没有索引  
- 通过key获取value  
- key不能重复,value可以重复  
- key和value的数据类型为 `Object`  

#### HashMap
```java
HashMap<String, String> map = new HashMap<>();
map.put("a", "张三"); //添加元素
map.put("b", "李四");
map.put("b", "王五"); //key相同便为替换相应元素
System.out.println(map.get("a")); //获取元素
System.out.println(map.get("s")); //没有的话返回nul
System.out.println(map.size()); //获取长度
System.out.println(map.remove("a")); //删除对应key,返回value
System.out.println(map.remove("s")); //没有对应key的话返回null
```

#### 遍历map
- `keySet`方法返回的是一个集合,集合中放到是所有的key
```java
Set<String> keys = map.keySet();
for (String key : keys) {
    System.out.println(map.get(key));
}
```
- `entrySet`返回此映射中包含的映射关系的 Set 视图
```java
Set<Entry<String, String>> entrySet = map.entrySet(); //ctrl+2 L快速生成
for (Entry<String, String> entry : entrys) {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
}
```


