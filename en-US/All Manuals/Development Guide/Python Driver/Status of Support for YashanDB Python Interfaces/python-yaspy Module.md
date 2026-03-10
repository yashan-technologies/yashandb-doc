##  Method

```python
connect( parameters… )
```

The connect() method is the constructor for creating database connection classes. The related parameters are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| connname      | Connection name.                                 |
| dsn       | Data source name, which may include database username, password, IP address, port number, and other information. The format is as follows:<br>\* Standalone/single IP mode: host:port<br>\* HA multi-IP primary mode: host:port,host:port,host:port or PRIMARY:host:port,host:port,host:port<br>\* Multi-IP load balance mode: LOADBALANCE:host:port,host:port,host:port<br>Parameter meanings:<br>\* host: The server domain name or IP address, which must be configured as the address of the standalone instance server or the distributed server.<br>\* port: The database service port, such as 1688.<br>\* primary: This keyword indicates that the primary mode is used for connecting in multi-IP, which is the default mode for multi-IP and can omit this keyword. In this mode, it will poll to connect to the HA primary database but not connect to the standby database.<br>\* loadBalance: This keyword indicates that the load balance mode is used for connecting in multi-IP. In this mode, it will connect to the optimal node (i.e., the node with the fewest connections). |
| user      | Database username, optional parameter.                                 |
| password  | Database user password, optional parameter.                           |
| host      | Database IP addres, optional parameter.                              |
| port      | Database port number, optional parameter with a default value of 1688.           |



If there are special characters in user and password such as /, @, \\, they need to be escaped using the symbol \\, as shown below:

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | Not Specified | Not Specified | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | Not Specified | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |




##  Attributes

The Python DB API v2.0 specification requires that database modules define the following three attributes:

|Name |Meaning |Value |
| ------------ | -------------------------------------- | ----- |
| apilevel    | The Python DB API version supported by the module | 2.0    |
| paramstyle  | The type of parameter marker formatting expected by the interface <br/> Currently named as in `where name = :name` | named  |
| threadsafety | The level of thread safety supported by the module <br/> Currently 2, which means that threads may share the module and connections, but not cursors | 2      |

Python developers can check the values of the variables using the following code:

```python
>>> import yaspy
>>> yaspy.threadsafety
2
>>> yaspy.apilevel
'2.0'
>>> yaspy.paramstyle
'named'
```
