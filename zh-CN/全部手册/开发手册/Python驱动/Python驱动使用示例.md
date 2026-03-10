示例：基于Python DB API开发应用程序。

::: tabs
== 采用池化连接

```python
#Example.py
#演示基于Python开发的主要步骤，涉及连接数据库、创建表、插入数据等。
from decimal import Decimal
import yaspy

pool = yaspy.SessionPool(
	user="sales",
	password="sales",
	dsn="192.168.1.2:1688",
	min=2, 
	max=10,
	increment=1,
	getmode=0,
)
connection = pool.acquire()
cursor=connection.cursor()

cursor.execute("drop table if exists bind")
cursor.execute("create table bind(id int , name varchar(256))")

def bind_param():
    cursor = connection.cursor()
    cursor.execute("drop table if exists bind_param_heap_1")
    cursor.execute("create table bind_param_heap_1(a int, b double, c int)")
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 30)",(1,10))
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 40)",(2,20))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchmany(2)
    print(result)
    cursor.execute("delete from bind_param_heap_1 where a=:1",(1,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchall()
    print(result)
    cursor.execute("update bind_param_heap_1 set c=:1", (50,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchone()
    print(result)

bind_param()

cursor.close()
pool.release(connection)
```

== 采用独立连接

```python
#Example.py
#演示基于Python开发的主要步骤，涉及连接数据库、创建表、插入数据等。
from decimal import Decimal
import yaspy

connection=yaspy.connect(
	dsn='192.168.1.2:1688',
	user='sales',
	password='sales',
)
cursor=connection.cursor()

cursor.execute("drop table if exists bind")
cursor.execute("create table bind(id int , name varchar(256))")

def bind_param():
    cursor = connection.cursor()
    cursor.execute("drop table if exists bind_param_heap_1")
    cursor.execute("create table bind_param_heap_1(a int, b double, c int)")
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 30)",(1,10))
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 40)",(2,20))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchmany(2)
    print(result)
    cursor.execute("delete from bind_param_heap_1 where a=:1",(1,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchall()
    print(result)
    cursor.execute("update bind_param_heap_1 set c=:1", (50,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchone()
    print(result)

bind_param()

cursor.close()
connection.close()
```
:::

Linux环境为例运行应用程序：

```shell
python3 Example.py
```

Windows环境运行应用程序：

```shell
python Example.py
```
