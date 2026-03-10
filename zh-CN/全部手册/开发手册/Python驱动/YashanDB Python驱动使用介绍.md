## 连接数据库

通过python-yasdb模块提供的connect函数建立数据库连接，并返回一个连接对象（Connection）。

可使用方法如下：

```python
# 使用dsn、user、password参数
conn = yasdb.connect(dsn=self.getDsn(), user=self.user, password=self.pwd)
# 使用dsn参数
conn = yasdb.connect(self.user+"/"+self.pwd+"@"+self.getDsn())
# 使用dsn、password参数
conn = yasdb.connect(self.user+"@"+self.getDsn(), password=self.pwd)
# 使用host、port、user、password参数
conn = yasdb.connect(host=self.host, port=self.port, user=self.user, password=self.pwd)
```

### 可选参数

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| dsn      | 数据源名称（data source name），可包含数据库用户名、密码、IP地址、端口号等信息，选填项。格式如下：<br>\* 单机/单IP模式： host:port<br>\* HA多IP primary模式：host:port,host:port,host:port 或 PRIMARY:host:port,host:port,host:port<br>\* 多IP loadBalance模式：LOADBALANCE:host:port,host:port,host:port<br>参数含义：<br>\* host：服务器域名或IP地址，需配置为单机实例服务器地址或分布式服务器地址。<br>\* port：数据库服务端口，例如1688。<br>\* primary：该关键字表示多IP下采用primary模式连接，为多IP的默认模式，可省略此关键字。该模式下会轮询连接HA主库但不连接备库。<br>\* loadBalance：该关键字表示多IP下采用loadBalance模式连接。该模式下会连接最优节点（即连接个数最少的节点）。 |
| user     | 数据库用户名，选填项。                                    |
| password | 数据库用户密码，选填项。                                 |
| host    | 数据库IP地址，选填项。                         |
| port | 数据库端口号，默认为1688，选填项。                      |

如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：
| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 127.0.0.1:1688                            | 未指定 | 未指定     | 127.0.0.1 | 1688 |
| sys@127.0.0.1:1688                        | sys    | 未指定     | 127.0.0.1 | 1688 |
| sys/yasdb_123@127.0.0.1:1688              | sys    | yasdb_123  | 127.0.0.1 | 1688 |
| sys/yasdb\\@_123@127.0.0.1:1688           | sys    | yasdb@_123 | 127.0.0.1 | 1688 |
| sys\\//yasdb\\@_123@127.0.0.1:1688        | sys/   | yasdb@_123 | 127.0.0.1 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@127.0.0.1:1688 | s/ys@\\ | yasdb@_123 | 127.0.0.1 | 1688 |

## 执行SQL

调用Connection的cursor()方法创建并返回一个游标对象（Cursor），该游标对象可用于执行语句和获取结果。

```python
cursor =connection.cursor()
```

### 执行SQL语句

调用Cursor的execute()方法执行SQL语句，并通过commit()方法将挂起的事务提交到数据库。

```python
cursor.execute("drop table if exists example_table")
cursor.execute("create table example_table(id int , num int)")
cursor.execute("insert into example_table values(1,'test1')")
connection.commit()
```

### 执行带参数的SQL语句

```python
cursor.execute("insert into example_table values(?,?)",(2,'test2'))
data=(3,'test3')
cursor.execute("insert into example_table values(?,?)",data)
connection.commit()
```

### 关闭游标对象

调用Cursor的close()方法后，该游标将不再可用。

```python
cursor.close()
```

### 关闭数据库连接

调用Connection的close()方法后，该连接将不再可用。

```python
connection.close()
```

## 示例

```python
import yasdb
# 连接数据库
connection=yasdb.connect(
	dsn='127.0.0.1:1688',
	user='sales',
	password='sales',
)
# 创建游标对象
cursor =connection.cursor()
# 执行SQL语句
cursor.execute("drop table if exists example_table")
cursor.execute("create table example_table(id int , name varchar(32))")
cursor.execute("insert into example_table values(1,'test1')")
connection.commit()
cursor.execute("insert into example_table values(?,?)",(2,'test2'))
data=(3,'test3')
cursor.execute("insert into example_table values(?,?)",data)
connection.commit()
# 关闭游标对象
cursor.close()
# 关闭数据库连接
connection.close()
```
