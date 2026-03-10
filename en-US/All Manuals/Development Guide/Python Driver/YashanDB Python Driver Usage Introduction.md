## Connect to Database

Establish a database connection using the connect function provided by the python-yasdb module, which returns a connection object (Connection).

The method can be used as follows:

```python
# Using dsn, user, password parameters
conn = yasdb.connect(dsn=self.getDsn(), user=self.user, password=self.pwd)
# Using dsn parameter
conn = yasdb.connect(self.user+"/"+self.pwd+"@"+self.getDsn())
# Using dsn, password parameters
conn = yasdb.connect(self.user+"@"+self.getDsn(), password=self.pwd)
# Using host, port, user, password parameters
conn = yasdb.connect(host=self.host, port=self.port, user=self.user, password=self.pwd)
```

### Optional Parameters

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| dsn       | Data Source Name (data source name), which may include database username, password, IP address, port number, and other information. It is optional. The format is as follows:<br>\* Standalone/single IP mode: host:port<br>\* HA multi-IP primary mode: host:port,host:port,host:port or PRIMARY:host:port,host:port,host:port<br>\* Multi-IP load balance mode: LOADBALANCE:host:port,host:port,host:port<br>Parameter meanings:<br>\* host: The server domain name or IP address, which must be configured as the address of the standalone instance server or the distributed server.<br>\* port: The database service port, such as 1688.<br>\* primary: This keyword indicates that the primary mode is used for connecting in multi-IP, which is the default mode for multi-IP and can omit this keyword. In this mode, it will poll to connect to the HA primary database but not connect to the standby database.<br>\* loadBalance: This keyword indicates that the load balance mode is used for connecting in multi-IP. In this mode, it will connect to the optimal node (i.e., the node with the fewest connections). |
| user      | Database username, optional.                                 |
| password  | Database user password, optional.                           |
| host      | Database IP address, optional.                              |
| port      | Database port number, default is 1688, optional.           |

If there are special characters in user and password such as /, @, \\ , they need to be escaped using the symbol \\, as shown below:
| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 127.0.0.1:1688                            | Not Specified | Not Specified | 127.0.0.1 | 1688 |
| sys@127.0.0.1:1688                        | sys    | Not Specified | 127.0.0.1 | 1688 |
| sys/yasdb_123@127.0.0.1:1688              | sys    | yasdb_123  | 127.0.0.1 | 1688 |
| sys/yasdb\\@_123@127.0.0.1:1688           | sys    | yasdb@_123 | 127.0.0.1 | 1688 |
| sys\\//yasdb\\@_123@127.0.0.1:1688        | sys/   | yasdb@_123 | 127.0.0.1 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@127.0.0.1:1688 | s/ys@\\ | yasdb@_123 | 127.0.0.1 | 1688 |

## Execute SQL

Call the cursor() method of Connection to create and return a cursor object (Cursor), which can be used to execute statements and retrieve results.

```python
cursor =connection.cursor()
```

### Execute SQL Statement

Call the execute() method of Cursor to execute the SQL statement and use the commit() method to submit the pending transaction to the database.

```python
cursor.execute("drop table if exists example_table")
cursor.execute("create table example_table(id int , num int)")
cursor.execute("insert into example_table values(1,'test1')")
connection.commit()
```

### Execute Parameterized SQL Statement

```python
cursor.execute("insert into example_table values(?,?)",(2,'test2'))
data=(3,'test3')
cursor.execute("insert into example_table values(?,?)",data)
connection.commit()
```

### Close Cursor Object

After calling the close() method of Cursor, the cursor will no longer be available.

```python
cursor.close()
```

### Close Database Connection

After calling the close() method of Connection, the connection will no longer be available.

```python
connection.close()
```

## Example

```python
import yasdb
# Connect to database
connection=yasdb.connect(
	dsn='127.0.0.1:1688',
	user='sales',
	password='sales',
)
# Create cursor object
cursor =connection.cursor()
# Execute SQL statement
cursor.execute("drop table if exists example_table")
cursor.execute("create table example_table(id int , name varchar(32))")
cursor.execute("insert into example_table values(1,'test1')")
connection.commit()
cursor.execute("insert into example_table values(?,?)",(2,'test2'))
data=(3,'test3')
cursor.execute("insert into example_table values(?,?)",data)
connection.commit()
# Close cursor object
cursor.close()
# Close database connection
connection.close()
```
