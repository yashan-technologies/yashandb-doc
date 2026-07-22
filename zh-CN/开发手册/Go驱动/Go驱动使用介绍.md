## 连接数据库

使用标准库 `database/sql`提供的Open函数建立数据库连接：

```go
func Open(driverName, dataSourceName string) (*DB, error)
```

参数说明：

|  参数| 描述|
| :------------- | :------------------------------------------------------- |
| driverName     | 数据库驱动名称。YashanDB的Go驱动名称为 `yasdb`。     |
| dataSourceName | 数据源名称。配置连接数据库的参数。                       |
| *DB            | 数据库连接对象，代表一个具有零到多个数据库连接的连接池。 |
| error          | 错误信息，若连接成功，返回error值为nil。                 |

示例

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)
func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}
```

### 数据源名称格式

数据源名称是用来连接YashanDB，具有一定格式的字符串，里面包含了数据库用户名、密码、数据库服务端的监听地址、连接参数等信息。数据源名称分为TCP和UDS两种方式，详细介绍如下：

TCP数据源名称格式为 `user/password@url[?param1=value1&...&paramN=valueN]`。

* user：用户名称，必填参数。
* password：用户密码，必填参数。

    > **Note**: 
    >
    > 如果`user`和`password`中有特殊字符`/`、`@`、`\`，需要使用符号 `\`进行转义。例如`user`为`sys@132`，需要转义为`sys\\@132`。

* url：服务的监听地址，格式如下：

  * 单地址连接：`host:port[/pdb_name]`。

  * 多地址连接（primary类型）：`host:port,host:port,host:port[/pdb_name]`或`PRIMARY:host:port,host:port,host:port[/pdb_name]`。
  * 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port[/pdb_name]`。
    参数含义：
    * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理)或[VIP](../../数据库管理/集群管理/VIP管理)，还可以使用相应的域名或IP地址。
    * port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。
    * pdb_name：仅用于容器数据库，指定连接到具体某个PDB，省略则默认连接至根容器。
    * primary：驱动会按指定监听地址的先后顺序连接节点，并通过执行`SELECT * FROM DATABASE_ROLE`判断节点角色，保留首次与主节点建立的连接。
    * loadBalance：驱动会将指定的监听地址随机打乱顺序后进行连接，获取每个节点当前的会话数，选取会话数最小值对应的节点作为目标节点（若最小值存在多个节点则取最先建立连接的节点），保留目标节点的连接并关闭其他连接。

    

>**Note**:
>
> - 多组监听地址连接的故障切换效率低于多个监听地址连接，分组主要用于尽可能保障仅通过第一组地址建立数据库连接，请根据实际需求选择是否分组。
> - 在共享集群/分布式集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../数据库管理/集群管理/VIP管理.md)，直接使用数据库服务端提供的高可用能力即可，**无需**额外配置多地址连接。
> - 配置多地址连接共享集群/分布式集群时：
>   - 若为单集群部署，驱动会将其所有实例视作主节点。
>   - 若为主备集群部署，驱动会将主集群的所有实例视作主节点、备集群的所有实例视作备节点。如需在未配置SCAN或VIP的场景下实现负载均衡，可考虑配置多组地址连接并按需指定primaryLoadBalance或standbyLoadBalance，主集群的所有实例为一个组，备集群的所有实例为另一个组。




* parameters：连接设置的参数，非必填项。可设置的参数如下：

    |  **参数名**| **默认值**| **可选范围**| **说明**|
    | -------------------- | ---------------- | -------------------- | ------------------------------------------------------------------------------------- |
    | autocommit       | false        | true/false   | 是否设置语句结束时自动提交事务。                                                      |
    | heartbeat_enable | false        | true/false   | 是否开启心跳检测功能。                                                                |
    | number_as_string | false        | true/false   | 设置查询 `NUMBER` 类型字段时，返回值是否为字符串类型；默认返回double数值类型。      |
    | ukey_name        | -                | 任意字符串           | 指定要认证的UKEY设备名称（例如：`ukey_name=DBA`）。             |
    | ukey_pin         | -                | 任意字符串           | 设置UKEY的用户密码（例如：`ukey_pin=Yashan123`）。               |

UDS数据源名称格式 `yasdb_data[?param1=value1&...&paramN=valueN]`，UDS方式只能连接本地数据库服务，必须配置YASDBA用户组免密，默认使用sys用户免密登入。

* yasdb_data：本地数据库服务的路径。

* parameters：连接设置的参数，与TCP方式相同。

### 数据源名称示例

TCP数据源名称：

|  数据源名称| user| password| url| parameters|
| ---------------------------------------------------------------------------- | ------- | --------- | -------------------------------------------------------------- | ------------------------------------------ |
| sys/yasdb_123@127.0.0.1:1688                                                 | sys     | yasdb_123 | 127.0.0.1:1688                                                 | 无                                         |
| sys\\\\@123/yasdb_123@127.0.0.1:1688                                         | sys@123 | yasdb_123 | 127.0.0.1:1688                                                 |                                            |
| sys/yasdb_123@127.0.0.1:1688?autocommit=true&number_as_string=true           | sys     | yasdb_123 | 127.0.0.1:1688                                                 | autocommit=true<br />number_as_string=true |
| sys/yasdb_123@192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688             | sys     | yasdb_123 | 192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688             | 无                                         |
| sys/yasdb_123@loadBalance:192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688 | sys     | yasdb_123 | loadBalance:192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688 | 无                                         |

UDS数据源名称：

|  数据源名称| user| password| yasdb_data| parameters|
| ------------------------------------------------------------- | ---- | -------- | ----------------------- | ------------------------------------------ |
| /home/yashan/yasdb_data                                       | sys  | 免密     | /home/yashan/yasdb_data | 无                                         |
| /home/yashan/yasdb_data?autocommit=true&number_as_string=true | sys  | 免密     | /home/yashan/yasdb_data | autocommit=true<br />number_as_string=true |

## 执行SQL

使用标准库 `database/sql`执行SQL的函数为：

```go
func (db *DB) Exec(query string, args ...any) (Result, error)
```

参数说明：

|  参数| 描述|
| :----- | :-------------------------------------- |
| query  | 执行的SQL命令，包括删除、更新、插入等。 |
| args   | 表示query中的占位参数。                 |
| Result | 是对已执行的SQL命令的总结。             |
| error  | 错误信息。                              |

示例

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)

func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}

func main(){
    db := Connect()
    result, err := db.Exec("CREATE TABLE users(id int, age int, name char(20))")
    if err != nil {
        log.Fatal(err)
    }
}
```

## 获取结果集

使用标准库 `database/sql`获取结果集的函数为：

```go
func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
func (rs *Rows) Next() bool
func (rs *Rows) Scan(dest ...interface{}) error
```

参数说明：

|  参数| 描述|
| :---- | :-------------------------------------------------------------------------------------------- |
| query | 执行的查询SQL命令。                                                                           |
| args  | 表示query中的占位参数。                                                                       |
| *Rows | 查询的结果。                                                                                  |
| dest  | 目标值。                                                                                      |
| bool  | Next准备用于Scan方法的下一行结果。如果成功会返回true，如果没有下一行或者出现错误会返回false。 |
| error | 错误信息。                                                                                    |

示例

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)

func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}

func main(){
    db := Connect() 
    age := 27
    rows, err := db.Query("SELECT name FROM users WHERE age=?", age)
    if err != nil {
        log.Fatal(err)
    }
    defer rows.Close()
    for rows.Next() { 
        var name string
        if err := rows.Scan(&name); err != nil { 
            log.Fatal(err)
        }
        fmt.Printf("%s is %d\n", name, age)
    }
    if err := rows.Err(); err != nil {
        log.Fatal(err)
    }
}
```
