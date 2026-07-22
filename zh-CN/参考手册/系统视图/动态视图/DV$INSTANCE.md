本视图显示存算一体分布式集群中所有节点实例状态的汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| STATUS | VARCHAR(8) | 实例状态<br>\* CLOSED: 数据库进程正在启动<br>\* STARTED：数据库进程启动，此状态下不能操作数据库<br>\* MOUNTED：数据库进程已经加载物理文件，此状态下能进行少量的维护操作<br>\* OPEN：数据库正常运行状态<br>\* OPEN UPGRADE：数据库进入升级模式 |
| VERSION | VARCHAR(64) | 数据库版本号 |
| STARTUP\_TIME | TIMESTAMP(6) | 数据库实例启动时间 |
| HOST\_NAME | VARCHAR(256) | 服务器用户名 |
| DATA\_HOME | VARCHAR(256) | 数据库DATA路径 |
| INSTANCE_ROLE | VARCHAR(16) | 当前实例角色 <br/>* MASTER_ROLE：当前实例为MASTER <br/>* NORMAL_ROLE：当前实例为NORMAL |
