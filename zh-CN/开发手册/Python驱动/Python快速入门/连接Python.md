YashanDB Python驱动可以通过池化连接或独立连接与数据库通信。

- 独立连接：在每次应用程序需要与数据库交互时建立一个全新的独立的连接，并在操作完成后关闭该连接。适合小规模应用、并发请求较少或短期运行的操作场景。

- 池化连接：先将多个数据库连接预先创建并保存在连接池中，在每次应用程序需要与数据库交互时从池中获取连接，使用后将连接归还到连接池中。可以高效地复用连接，减少频繁建立和关闭数据库连接的开销。适合高并发、大量数据库操作或可能需要长时间运行的应用。仅yaspy模块支持池化连接。

## 独立连接

独立连接是指通过连接对象创建驱动与数据库的单一连接，并通过该连接对象执行SQL、管理事务以及关闭连接。

### 建立数据库连接

连接数据库时，通过python-yaspy模块提供的connect()建立数据库连接，并返回一个[连接对象](../Python参考信息/Python接口支持说明/连接对象.md)（Connection）。

可使用方法如下：

```python
# 方法一：使用dsn参数（推荐）
conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

# 方法二：使用dsn、user、password参数
conn = yaspy.connect(dsn="192.168.1.2:1688", user="sales", password="sales")
```

相关参数如下表所示。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| dsn      | 数据源名称（Data Source Name），格式为`host:port[/pdb_name]`。<br/><br/>URL格式如下：<br>\* 单地址连接： `host:port[/pdb_name]`<br>\* 多地址连接（primary类型）：`host:port,host:port,host:port[/pdb_name]`或`PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>**注意**：`SessionPool`的`dsn`参数**仅支持**`host:port`格式（不含`user/password@`前缀），用户名和密码必须通过独立的`user`和`password`参数指定。<br/><br/>参数含义：<br> \*host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../../数据库管理/集群管理/VIP管理.md)，还可以使用相应的域名或IP地址。  <br> \*port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。  <br> \*pdb_name：仅用于容器数据库，指定连接到具体某个PDB，省略则默认连接至根容器。  <br> \*primary：驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与主节点建立的连接。 <br> \*loadBalance：驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |
| user     | 数据库用户名。<br/>可选参数，若dsn中未指定则必选。                                  |
| password | 数据库用户密码。<br/>可选参数，若dsn中未指定则必选。                                 |

>**Note**:
>
> - 多组监听地址连接的故障切换效率低于多个监听地址连接，分组主要用于尽可能保障仅通过第一组地址建立数据库连接，请根据实际需求选择是否分组。
> - 在共享集群/分布式集群部署中，若已配置[SCAN](../../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../../数据库管理/集群管理/VIP管理.md)，直接使用数据库服务端提供的高可用能力即可，**无需**额外配置多地址连接。
> - 配置多地址连接共享集群/分布式集群时：
>   - 若为单集群部署，驱动会将其所有实例视作主节点。
>   - 若为主备集群部署，驱动会将主集群的所有实例视作主节点、备集群的所有实例视作备节点。如需在未配置SCAN或VIP的场景下实现负载均衡，可考虑配置多组地址连接并按需指定primaryLoadBalance或standbyLoadBalance，主集群的所有实例为一个组，备集群的所有实例为另一个组。



如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | 未指定 | 未指定     | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | 未指定     | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |

> **Note**:
>
> 上述DSN转义表中"user"和"password"列标注为"未指定"仅表示YAC层解析语义。经`yaspy.connect`时，dsn、user、password**均非空**，若DSN中未指定user/password，则必须通过`user`/`password`形参补全，否则会报 `invalid connect info`错误。

### 完整示例

```python
import yaspy

# 方法一：使用dsn参数连接（推荐）
try:
    conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")
    print("Connection established successfully!")
    # 执行数据库操作
    cursor = conn.cursor()
    cursor.execute("SELECT SYSDATE FROM DUAL")
    result = cursor.fetchone()
    print(f"Current database time: {result[0]}")
    # 关闭游标和连接
    cursor.close()
    conn.close()
    print("Connection closed.")
except Exception as e:
    print(f"Connection failed: {e}")

# 方法二：使用dsn、user、password参数连接
try:
    conn = yaspy.connect(
        dsn="192.168.1.2:1688",
        user="sales",
        password="sales"
    )
    print("Connection established successfully via DSN!")
    cursor = conn.cursor()
    cursor.execute("SELECT SYSDATE FROM DUAL")
    result = cursor.fetchone()
    print(f"Current database time: {result[0]}")
    cursor.close()
    conn.close()
except Exception as e:
    print(f"Connection failed: {e}")
```

执行：

```python
$ python connect_example.py
Connection established successfully!
Current database time: 2026-05-12
Connection closed.
```

## 池化连接

连接池是一种管理数据库连接的技术，通过预先建立并维护一组连接，减少频繁创建和销毁连接带来的性能开销，提高应用响应效率。连接池适用于高并发场景，可有效提升数据库访问性能。

### 创建连接池

连接池通过SessionPool类进行管理，包括配置池的大小、获取连接、归还连接等操作。

相关参数如下表所示。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| user     | 数据库用户名。必填参数。                                  |
| password | 数据库用户密码。必填参数。                                 |
| dsn      | 数据源名称（Data Source Name），可包含数据库用户名、密码、数据库URL等信息，完整格式为`user/password@url`。在容器数据库中，如需连接PDB只能通过该参数指定数据库URL且必须使用v23.5.1.100及以上版本的Python驱动。<br/><br/>URL格式如下：<br>\* 单地址连接： `host:port[/pdb_name]`<br>\* 多地址连接（primary类型）：`host:port,host:port,host:port[/pdb_name]`或`PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>参数含义：<br> *数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../../数据库管理/集群管理/VIP管理.md)，还可以使用相应的域名或IP地址。  <br> *port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。  <br>*pdb_name：仅用于容器数据库，指定连接到具体某个PDB，省略则默认连接至根容器。  <br>\*primary：驱动会按指定监听地址的先后顺序连接节点，并通过执行SELECT * FROM DATABASE_ROLE判断节点角色，保留首次与主节点建立的连接。 <br>\*loadBalance：驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。  |
| min      | 当前连接池的最小连接数，取值范围为正整数，可选参数，默认值为1。         |
| max     |  当前连接池的最大连接数，取值范围为正整数，可选参数，默认值为4。                                    |
| increment     | 单次可创建的连接数，可选参数，默认值为1。<br/>当池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接。 |
| getmode     | 连接获取模式，可选参数，目前固定为1，具体表现为：<br/>- 如果池内存在空闲连接，直接获得连接。<br/>- 如果池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接。<br/>- 如果池内连接耗尽且总连接数已达到max值时，直接报错。  |



如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | 未指定 | 未指定     | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | 未指定     | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |

> **Note**:
>
> 上述DSN转义表中"user"和"password"列标注为"未指定"仅表示YAC层解析语义。经`yaspy.connect`时，dsn、user、password**均非空**，若DSN中未指定user/password，则必须通过`user`/`password`形参补全，否则会报`invalid connect info`错误。

```python
import yaspy

pool = yaspy.SessionPool(
    user="sales",
    password="sales",
    dsn="192.168.1.2:1688",
    min=1,
    max=4,
    increment=1,
    getmode=1,
)
```

### 获取连接

连接池创建完成后，可以调用acquire()方法获取连接。


```python
conn = pool.acquire()
# 执行数据库操作
cursor = conn.cursor()
cursor.execute("SELECT SYSDATE FROM DUAL")
result = cursor.fetchone()
print(f"Current database time: {result[0]}")
```

### 归还连接

若应用与数据库的交互完成，可调用release()方法归还连接，该连接回到连接池供其他请求使用。

> **Note**：
>
>`release()`推荐使用`connection=`关键字参数，亦支持位置参数写法`pool.release(conn)`。

```python
pool.release(connection=connection)
# 或: pool.release(connection)
```

### 完整示例

```python
from decimal import Decimal
import yaspy

pool = yaspy.SessionPool(
	user="sales",
	password="sales",
	dsn="192.168.1.2:1688",
	min=2,
	max=10,
	increment=1,
	getmode=1,
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
pool.release(connection=connection)
pool.close()
```

执行：

```python
python3 Example.py
```
