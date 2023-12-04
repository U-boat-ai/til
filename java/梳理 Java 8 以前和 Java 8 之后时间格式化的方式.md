# 梳理 Java 8 以前和 Java 8 及之后时间格式化的方式

在 Java 8 之前，进行时间格式化通常使用 `SimpleDateFormat` 类。

```java
DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

String formattedDate = dateFormat.format(new Date());
```

需要注意的是，`SimpleDateFormat` 在多线程环境下可能存在线程安全性问题。

在 Java 8 及之后，推荐使用 `DateTimeFormatter` 类。

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

String dateTimeString = formatter.format(LocalDateTime.now());
```
