---
title: Java基础：各种类型间的转换
date: 2017-08-08 11:48:56
toc: true
categories:
- CS
- PL
- Java
tags:
- Java
---

 

本文总结一些基础的 Java 类型转换的方法。

### Map & List     

```java
/* ----- Map to List ----- */
Map<Key,Value> map;
List<Key> keyList = new ArrayList<Value>(map.keySet());
List<Value> valList = new ArrayList<Value>(map.values());
```

### Array & List

```java
/* ----- Array to List ----- */
List<Foo> list = Arrays.asList(array);

/* ----- List to Array ----- */
//1.use List.toArray
Foo[] array = list.toArray(new Foo[list.size()]);

//2.use Collection.stream (jdk 1.8)
Foo[] array = list.stream().toArray(Foo[]::new);
```
### Date & String

```java
/* ----- Date to String ----- */

//1.use jdk DateFormat
Date today = Calendar.getInstance().getTime();
DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String dateStr = df.format(today);

//2.use apache common-lang DateFormatUtils
Date today = Calendar.getInstance().getTime();
String dateStr = DateFormatUtils.format(today, "yyyy-MM-dd HH:mm:ss");

//3.use DateTimeFormatter (jdk 1.8)

/* ----- String to Date ----- */
//1.use jdk DateFormat
String dateStr = "1900-01-01 01:01:01";
DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date date = df.parse(dateStr);

```



