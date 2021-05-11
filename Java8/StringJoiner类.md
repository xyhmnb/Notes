### 介绍

StringJoiner是Java8新出的一个类，用于构造由分隔符分隔的字符序列，并可选择性地从提供的前缀开始和以提供的后缀结尾。省的我们开发人员再次通过StringBuffer或者StingBuilder拼接。

### 原理

使用StringBuilder进行拼接字符串的封装处理

### 使用

```java
StringJoiner stringJoiner = new StringJoiner(":", "[", "]");
stringJoiner.add("firstStr").add("secondStr").add("thirdStr");
System.out.println(stringJoiner.toString());
//输出
[firstStr:secondStr:thirdStr]
```

### 源码

```java
public StringJoiner(CharSequence delimiter,CharSequence prefix,CharSequence suffix){}
//delimiter 间隔符
//prefix 前缀
//suffix 后缀
```

### 补充

同时，String.join()方法可以拼接分隔符，就是没有前后缀

```java
String join = String.join(", ", "join1","join2","join3");
System.out.println(join);
//输出
join1, join2, join3
```

