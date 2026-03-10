***Example***: Develop applications based on the Python DB API.

```python
#Example.py
#Demonstrates the main steps of development in Python, including connecting to the database, creating tables, inserting data, etc.
from decimal import Decimal
import yasdb

connection=yasdb.connect(
	dsn='127.0.0.1:1688',
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

Run the application in a Linux environment:

```shell
python3 Example.py
```

Run the application in a Windows environment:

```shell
python Example.py
```
