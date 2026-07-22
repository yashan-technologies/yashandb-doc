## 连接数据库与断连

YashanDB Python驱动可以通过池化连接或独立连接与数据库通信。

- 池化连接：先将多个数据库连接预先创建并保存在连接池中，在每次应用程序需要与数据库交互时从池中获取连接，使用后将连接归还到连接池中。可以高效地复用连接，减少频繁建立和关闭数据库连接的开销。适合高并发、大量数据库操作或可能需要长时间运行的应用。仅yaspy模块支持池化连接。

- 独立连接：在每次应用程序需要与数据库交互时建立一个全新的独立的连接，并在操作完成后关闭该连接。适合小规模应用、并发请求较少或短期运行的操作场景。

### 池化连接 

如需使用连接池，YashanDB Python驱动需运行在Python3.6及以上环境中。

#### 创建连接池 

连接池通过SessionPool类进行管理，包括配置池的大小、获取连接、归还连接等操作。

```python
poolname = yaspy.SessionPool(
	user="username",
	password="password",
	dsn="dsn_string",
	min=2, 
	max=10,
	increment=1,
	getmode=0,
)
```

相关参数如下表所示。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| poolname     | 连接池的名称。                                    |
| user     | 数据库用户名。<br/>可选参数，若dsn中未指定则必选。                                  |
| password | 数据库用户密码。<br/>可选参数，若dsn中未指定则必选。                                 |
| dsn      | 数据源名称（Data Source Name），可包含数据库用户名、密码、数据库URL等信息，完整格式为`user/password@url`，URL格式如下：<br>\* 单地址连接： `host:port`<br>\* 多地址连接（primary类型）：`host:port,host:port,host:port`或`PRIMARY:host:port,host:port,host:port`<br>\* 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port`<br>参数含义：<br> * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理)或[VIP](../../数据库管理/集群管理/VIP管理)，还可以使用相应的域名或IP地址。 <br>\* port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。 <br>\* primary：驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与主节点建立的连接。<br>\* loadBalance：驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |
| min      | 当前连接池的最小连接数，取值范围为`(0,4294967295]`，可选参数，默认值为2。         |
| max     |  当前连接池的最大连接数，取值范围为`[min,4294967295]`，可选参数，默认值为10。                                    |
| increment     | 单次可创建的连接数，可选参数，默认值为1。<br/>当池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接。                                   |
| getmode     | 连接获取模式，可选参数，目前固定为1，具体表现为：<br/>- 如果池内存在空闲连接，直接获得连接。<br/>- 如果池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接。<br/>- 如果池内连接耗尽且总连接数已达到max值时，直接报错。                                    |



如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | 未指定 | 未指定     | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | 未指定     | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |





#### 获取连接 

连接池创建完成后，可以调用acquire()方法获取连接。

```python
connection = poolname.acquire()
```

#### 归还连接 

若应用与数据库的交互完成，可调用release()方法归还连接，该连接回到连接池供其他请求使用。

```python
poolname.release(connection)
```

#### 关闭连接池 

调用close()方法可以立即关闭数据库连接池，执行成功后该连接池中的所有连接将不再可用。
	
```python
poolname.close()
```

### 独立连接 

独立连接是指通过连接对象创建驱动与数据库的单一连接，并通过该连接对象执行SQL、管理事务以及关闭连接。

#### 建立数据库连接 

连接数据库时，通过python-yaspy模块提供的connect()建立数据库连接，并返回一个[连接对象](./Python接口支持说明/连接对象)（Connection）。

可使用方法如下：

```python
# 使用dsn、user、password参数
connname = yaspy.connect(dsn=self.getDsn(), user=self.user, password=self.pwd)
# 使用dsn参数
connname = yaspy.connect(self.user+"/"+self.pwd+"@"+self.getDsn())
# 使用dsn、password参数
connname = yaspy.connect(self.user+"@"+self.getDsn(), password=self.pwd)
# 使用host、port、user、password参数
connname = yaspy.connect(host=self.host, port=self.port, user=self.user, password=self.pwd)
```

connect()方法的参数如下表所示。

相关参数如下表所示。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| connname     | 连接的名称。                                    |
| dsn      | 数据源名称（Data Source Name），可包含数据库用户名、密码、数据库URL等信息，完整格式为`user/password@url`，URL格式如下：<br>\* 单地址连接： `host:port`<br>\* 多地址连接（primary类型）：`host:port,host:port,host:port`或`PRIMARY:host:port,host:port,host:port`<br>\* 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port`<br>参数含义：<br> * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理)或[VIP](../../数据库管理/集群管理/VIP管理)，还可以使用相应的域名或IP地址。 <br>\* port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。 <br>\* primary：驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与主节点建立的连接。<br>\* loadBalance：驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。 |
| user     | 数据库用户名。<br/>可选参数，若dsn中未指定则必选。                                  |
| password | 数据库用户密码。<br/>可选参数，若dsn中未指定则必选。                                 |
| host    | host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理)或[VIP](../../数据库管理/集群管理/VIP管理)，还可以使用相应的域名或IP地址。<br/>可选参数，若dsn参数中未指定则必选。<br/>如需配置多地址连接，只能通过dsn参数进行配置。                         |
| port | 数据库端口号<br/>可选参数，默认为1688。                      |





>**Note**:
>
> - 多组监听地址连接的故障切换效率低于多个监听地址连接，分组主要用于尽可能保障仅通过第一组地址建立数据库连接，请根据实际需求选择是否分组。
> - 在共享集群/分布式集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理)或[VIP](../../数据库管理/集群管理/VIP管理)，直接使用数据库服务端提供的高可用能力即可，**无需**额外配置多地址连接。
> - 配置多地址连接共享集群/分布式集群时：
>   - 若为单集群部署，驱动会将其所有实例视作主节点。
>   - 在主备集群部署中，驱动会将主集群的所有实例视作主节点、备集群的所有实例视作备节点。如需在未配置SCAN或VIP的场景下实现负载均衡，可考虑配置多组地址连接并按需指定primaryLoadBalance或standbyLoadBalance，主集群的所有实例为一个组，备集群的所有实例为另一个组。






如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | 未指定 | 未指定     | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | 未指定     | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |



#### 断开数据库连接

调用Connection的close()方法可以立即关闭数据库连接，执行成功后该连接将不再可用。

```python
connection.close()
```

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
