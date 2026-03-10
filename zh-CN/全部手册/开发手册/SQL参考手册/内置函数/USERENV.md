```ebnf+diagram
userenv::= USERENV "(" "'" parameter "'" ")"
```

USERENV函数根据输入的parameter参数返回当前会话的相关信息。  

**parameter**

为表达参数的字符串字面量，不区分大小写。包括如下值：

*   SID：当前会话的SID，一个SID能够唯一标识一个已连接的会话。函数对该参数返回一个NUMBER类型的数值。
*   SCHEMAID：当前的USER ID。函数对该参数返回一个NUMBER类型的数值。
*   LANGUAGE：当前连接的服务端所使用的字符集。函数对该参数返回一个VARCHAR类型的数值。
*   TERMINAL：当前会话所在设备或终端的标识。函数对该参数返回一个VARCHAR类型的数值。
*   CLIENT_INFO：当前会话所登录的用户名称与当前会话程序所在路径。函数对该参数返回一个VARCHAR类型的数值。
*   GSID：分布式下当前会话的全局会话ID，每一个会话具有不同的GSID，不同CN间亦不相同。非分布式下返回默认值SID。函数对该参数返回一个NUMBER类型的数值。
*   CURRENT_SCHEMAID：当前的USER ID。函数对该参数返回一个VARCHAR类型的数值。
*   INSTANCE：当前的实例ID。函数对该参数返回一个NUMBER类型的数值。
*   SESSIONID: 当前会话的审计会话ID。
*   GROUP_ID: 存算一体分布式集群部署模式下当前节点所在节点组的ID，单机/共享集群/分布式集群部署模式下显示为0。
*   GROUP_NODE_ID: 存算一体分布式集群部署模式下当前节点所在节点组内的节点ID，单机/共享集群/分布式集群部署模式下显示为0。

如parameter指定为上述之外的其他值，函数返回invalid parameter错误。

示例

```sql
SELECT USERENV('CLIENT_INFO') res FROM DUAL;
RES                                            
----------------------------------------------------------------
user: SYS
program path: /home/yasdb/bin/yasql
```
