## Cursor游标对象

为数据库游标对象，用于管理对数据库中具体内容的操作，如执行SQL语句和获取语句执行结果。

该对象通过Connection的cursor()方法创建。

### 属性

| 属性        |
| ----------- |
| description |
| rowcount    |
| arraysize   |

### 已支持的方法

| 方法          |
| ------------- |
| close()       |
| execute()     |
| executemany() |
| fetchone()    |
| fetchmany()   |
| fetchall()    |
| var()         |
