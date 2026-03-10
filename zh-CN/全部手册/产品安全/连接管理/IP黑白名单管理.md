YashanDB提供连接请求IP访问管控功能，对远程连接请求进行准入与否的管控（使用UDS本地连接的请求不受影响），增强数据库访问的安全性。

YashanDB支持基于IP白名单配置实现仅允许指定IP发起的远程连接请求，也支持基于IP黑名单配置实现仅不允许指定IP发起的远程连接请求。黑名单优先级高于白名单，若黑/白名单中存在相同IP将按黑名单生效。

同时，基于用户配置文件profile还可以实现IP黑白名单仅针对特定用户生效。

>  **Note**: 
>
> 本功能仅对远程连接生效，使用UDS本地连接的请求将一直被允许。

## 全局IP黑白名单管理

全局IP黑白名单管理针对所有向数据库发起的远程连接请求生效，修改配置后需重启数据库才能生效。

### 开启IP黑白名单管理功能

该功能开关由数据库配置文件yasdb_net.ini文件中的TCP.VALIDNODE_CHECKING参数的取值控制，默认为关闭。

YashanDB安装后，yasdb_net.ini配置文件不会自动生成。如需修改配置，应先在$YASDB_DATA/config路径下手动新建该文件再修改配置。将TCP.VALIDNODE_CHECKING参数配置为yes表示启用该功能（系统忽略大小写），配置为其他值表示不启用。

开启该功能后，还必须配置IP白名单或黑名单。

```ini
## 路径为$YASDB_DATA/config/yasdb_net.ini
TCP.VALIDNODE_CHECKING = YES
TCP.INVITED_NODES = 192.168.1.2/16
TCP.EXCLUDED_NODES = 192.168.1.111/32
```

### 配置IP黑/白名单

> **Warn**:
>
> 黑/白名单中的IP必须配置为系统认可的有效格式，非法IP将导致数据库实例启动失败。

#### IP白名单TCP.INVITED\_NODES

IP白名单列表通过TCP.INVITED_NODES参数进行配置，多个IP以`,`隔开。

IP的格式为：IP[/MASK]，其中，MASK表示从左向右匹配二进制IP的比特数。IPv4地址的MASK取值范围为[0,32]，默认为32全匹配；IPv6地址的MASK取值范围为[0,128]，默认为128位全匹配。例如127.0.0.1/8表示只匹配IP的第一个字节127，fc00:7::126/64则表示匹配IP的前8个字节。

- 不允许使用通配符表示IP，例如配置127.\*.\*.0将被认为是非法IP。

- 不允许使用域名代替IP。

- 不允许使用127.0.0.1代指当前服务器（本机）IP，必须使用实际的IP地址。

#### IP黑名单TCP.EXCLUDED\_NODES

IP黑名单列表通过TCP.EXCLUDED_NODES参数进行配置，多个IP以`,`隔开，IP的格式同白名单。

## 特定IP黑白名单管理

特定IP黑白名单管理仅针对用户配置文件所关联的用户向数据库发起的远程连接请求生效，创建相应的profile后需关联至用户才能生效，修改已有profile时立即对其关联用户生效。

- 执行[CREATE PROFILE](../../开发手册/SQL参考手册/SQL语句/CREATE PROFILE.html#ip)和[ALTER PROFILE](../../开发手册/SQL参考手册/SQL语句/ALTER PROFILE.html#ip)语句可以配置IP黑/白名单列表。

- 执行[CREATE USER](../../开发手册/SQL参考手册/SQL语句/CREATE USER.html#profile)或[ALTER USER](../../开发手册/SQL参考手册/SQL语句/ALTER USER.html#profile)语句可以关联user与profile，即配置IP黑/白名单的生效用户。

### IP白名单INVITED\_NODES

IP白名单列表通过INVITED_NODES参数进行配置，格式为`'IP/mask,IP'`，多个IP以`,`隔开，非重复的IP个数不得超过64个。

- IP：实际IP地址，例如192.168.4.11。

- MASK：表示从左向右匹配二进制IP的比特数，取值范围为[0,32]。如果不加MASK，则默认表示全匹配。

### IP黑名单EXCLUDED\_NODES

IP黑名单列表通过EXCLUDED_NODES参数进行配置，格式与约束同白名单。
