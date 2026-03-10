本视图显示集群中当前多租户容器实例中的所有容器（根容器和所有PDB）的信息。

|  字段| 类型| 说明|
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | 组ID               |
| GROUP_NODE_ID | NUMBER       | 组内节点ID                |
| INST_ID       | NUMBER       | 实例ID                   |
| CON_ID        | BIGINT       | 容器ID：<br/> * 0：表示根容器（CDB$ROOT）的编号<br/> * 1：表示种子容器（PDB$SEED）的编号<br/> * n：表示标准PDB容器的编号             |
| OID           | BIGINT       | 容器所属对象的ID                      |
| CON_UID       | VARCHAR(33)  | 容器的全局唯一标识符                  |
| NAME          | VARCHAR(68)  | 容器名称                   |
| TYPE          | VARCHAR(16)  | 容器的类型，包括ROOT、SEED、PDB            |
| STATUS        | VARCHAR(16)  | 容器的实例状态：<br/> * CLOSED：容器进程已经关闭<br/> * STARTED：容器进程已经启动，此状态下不能操作数据库<br/> * MOUNTED：容器进程已经加载物理文件，此状态下能进行少量的维护操作<br/> * OPEN：容器正常运行状态 |
| FLAGS         | TINYINT      | 容器的标识               |
| HOME          | VARCHAR(256) | 容器的DATA目录，存放其数据文件             |
| COMPAT_MODE | VARCHAR(16) | 容器实例的语法模式：<br/>* YASHAN：yashan模式<br/>* MYSQL：mysql模式 |
