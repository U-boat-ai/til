# SQLiteDatabase.execSQL 方法参数化查询中 IN 操作符的使用

首先需要写一个参数化查询中动态生成占位符字符串的辅助方法。
```java
public String getPlaceholders(int count) {
        if (count <= 0) {
            throw new IllegalArgumentException("Invalid count value: " + count);
        }

        StringBuilder builder = new StringBuilder(count * 2 - 1);
        builder.append("?");
        for (int i = 1; i < count; i++) {
            builder.append(",?");
        }
        return builder.toString();
    }
```

然后就可以编写`SQL` 语句。
```java
SQLiteDatabase db = super.getWritableDatabase();
sql = "DELETE FROM table WHERE ID IN (" + getPlaceholders(idList.length) + ")";
db.execSQL(sql, idList);
```