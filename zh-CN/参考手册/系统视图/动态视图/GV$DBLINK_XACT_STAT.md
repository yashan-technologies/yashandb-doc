本视图显示所有database link在沙箱进程yex_server上链接组及对应链接的的相关统计信息。

|  字段| 类型| 描述|
|------------------------|---------|----------------------------------|
| GROUP_ID               | NUMBER  | 组ID                              |
| GROUP_NODE_ID          | NUMBER  | 组内节点ID                           |
| INST_ID                | NUMBER  | 实例ID                             |
| EXT\_XACT\_ID          | INTEGER | 链接组ID                            |
| EXT\_CONNECTION\_COUNT | INTEGER | 链接组内与远程数据库建立连接的数量                |
| EXT\_XACT\_NEXT\_ID    | INTEGER | 下一个链接组的ID，如果值-1，表示当前链接组正在使用；如果值为1024，表示为空闲链接组中最后一个链接组      |
| EXT\_XACT\_IS\_FREE    | BOOLEAN | 链接组是否处于空闲状态，TRUE表示空闲，FALSE表示正在使用 |
