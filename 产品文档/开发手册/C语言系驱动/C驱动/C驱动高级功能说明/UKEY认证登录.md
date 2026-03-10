UKEY认证登录（UKEY Auth）是一种新的客户端认证方式，可以借助UKEY硬件的签名加密等功能，完成更加安全的认证登录。

UKEY认证登录不能取代用户密码，使用UKEY认证登录时依然需要数据用户密码。

> **Note**:
>
> YashanDB不管理UKEY设备的密码，但要求：
>
>- UKEY的设备名必须和用户角色保持一致（DBA、SECURITY_ADMIN或AUDIT_ADMIN，三者之一）。
>
>- UKEY的app名和容器名都必须为YashanDB。

## 配置参数

参数名以及参数值均不区分大小写，各参数信息如下表所示。

| 参数名          | 合法值            | 说明                         |
|--------------|---------------------|------------------------------|
| UKEY_NAME    | \* DBA：如果登录的用户角色是DBA，则要求UKEY的设备名为DBA<br />\* SECURITY_ADMIN：如果登录的用户角色是安全管理员，则要求UKEY的设备名为SECURITY_ADMIN<br />\* AUDIT_ADMIN ：如果登录的用户角色是审计员，则要求UKEY的设备名为AUDIT_ADMIN | 该参数表示要认证的UKEY设备名，设备名要求和用户的角色名保持对应。 |
| UKEY_PIN     | 小于64位的密码值            | 该参数指定UKEY的用户密码。           |

## 配置UKEY认证登录

UKEY认证登录功能默认关闭，如需使用需联系我们的技术支持，协助配置环境，开启该功能。功能开启后，还需完成相关配置才能使用UKEY认证登录，配置方法如下：

### 方式一

1. 进入$YASDB_HOME/client路径，在yasc_service.ini文件中新增配置，配置示例如下：
   ```C
   REMOTE = 192.168.1.2:1688??UKEY_NAME=DBA&UKEY_PIN=Yashan123
   ```

2. 使用REMOTE数据源连接：
   ```c
   const YacChar* gSrvStr = "REMOTE";
   YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
   ```

### 方式二

直接在连接URL中配置：
```c
const YacChar* gSrvStr = "192.168.1.2:1688??UKEY_NAME=DBA&UKEY_PIN=Yashan123";
YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
```