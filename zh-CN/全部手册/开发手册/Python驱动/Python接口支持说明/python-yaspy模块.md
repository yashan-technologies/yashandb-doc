## 方法 

```python
connect( parameters… )
```

connect()用于创建与数据库连接类的构造函数，相关参数如下表所示。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
| connname     | 连接的名称。                                    |
| dsn      | 数据源名称（data source name），可包含数据库用户名、密码、IP地址、端口号等信息。格式如下：<br>\* 单机/单IP模式： host:port<br>\* HA多IP primary模式：host:port,host:port,host:port 或 PRIMARY:host:port,host:port,host:port<br>\* 多IP loadBalance模式：LOADBALANCE:host:port,host:port,host:port<br>参数含义：<br>\* host：服务器域名或IP地址，需配置为单机实例服务器地址或分布式服务器地址。<br>\* port：数据库服务端口，例如1688。<br>\* primary：该关键字表示多IP下采用primary模式连接，为多IP的默认模式，可省略此关键字。该模式下会轮询连接HA主库但不连接备库。<br>\* loadBalance：该关键字表示多IP下采用loadBalance模式连接。该模式下会连接最优节点（即连接个数最少的节点）。 |
| user     | 数据库用户名，可选参数。                                    |
| password | 数据库用户密码，可选参数。                                 |
| host    | 数据库IP地址，可选参数。                         |
| port | 数据库端口号，可选参数，默认为1688。                      |



如果user和password中有特殊字符/、@、\\，需要使用符号\\进行转义，举例如下：

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | 未指定 | 未指定     | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | 未指定     | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |




## 属性 

Python DB API v2.0规范中要求数据库模块都应该定义如下3个属性变量：

|  名称| 含义| 取值|
| ------------ | -------------------------------------- | ----- |
| apilevel     | 模块支持的Python DB API版本               | 2.0   |
| paramstyle   | 参数标记的格式风格<br/>目前固定为named，表示格式为`where name = :name` | named |
| threadsafety | 模块接口支持的线程安全级别<br/>目前固定为2，表示线程可以共享模块和连接但不能共享游标             | 2     |

Python开发人员可通过如下代码查看变量的值：

```python
>>> import yaspy
>>> yaspy.threadsafety
2
>>> yaspy.apilevel
'2.0'
>>> yaspy.paramstyle
'named'
```
