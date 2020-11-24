# 删除元素

> 根据value删除元素

## list

```java

```

==推荐使用==

```java
//根据value删除list元素
list.removeIf(str->str.equals("data"));
```

```java
//其他方法
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String s = iterator.next();
    if ("data".equals(s)) {
        iterator.remove();//使用迭代器的删除方法删除
    }
}
```



`错误示范`

```java
//foreach循环不要做元素的增删，会导致下标错误抛出异常
for(String str:list){
   //输出或判断逻辑
}
```



## map

```java
//遍历map，根据value查找key，可能会有多个值
for (Map.Entry<String, SemiAutoMachine> entry : listSemiAutoToken.entrySet()) {
    if (entry.getValue().getToken().equals(token)) {
        key = entry.getKey();
    }
}
```

```java
//根据value删除key元素
listSemiAutoToken.values().removeIf(value->value.getToken.equals("data"));
```

