本视图显示当前实例状态的汇总信息。

|  字段| 类型| 说明|
| --- |--------------| --- |
| STATUS | VARCHAR(12)  | 实例状态<br/>* CLOSED：数据库进程正在启动<br/>* STARTED：数据库进程启动，此状态下不能操作数据库<br/>* MOUNTED：数据库进程已经加载物理文件，此状态下能进行少量的维护操作<br/>* OPEN：数据库正常运行状态<br>\* OPEN UPGRADE：数据库进入升级模式 |
| VERSION | VARCHAR(64)  | 数据库版本号 |
| STARTUP_TIME | TIMESTAMP    | 数据库实例启动时间 |
| HOST_NAME | VARCHAR(256) | 服务器用户名 |
| DATA_HOME | VARCHAR(256) | 数据库DATA路径 |
| INSTANCE_NUMBER | INTEGER      | 实例编号 |
| INSTANCE_NAME | VARCHAR(64)  | 实例名称 |
| PARALLEL | BOOLEAN      | 实例是否以集群数据库模式挂载(YES)或(NO) |
| INSTANCE_ROLE | VARCHAR(64)  | 当前实例角色 <br/>* MASTER_ROLE：当前实例为MASTER <br/>* NORMAL_ROLE：当前实例为NORMAL <br/> 该字段仅在共享集群部署下有意义，其它部署模式下该字段值恒为MASTER_ROLE |
| IN_REFORM | VARCHAR(8) | 是否正在处理集群实例的加入，退出或者故障恢复 <br/> 该字段仅在共享集群部署下有意义，其它部署模式下该字段值恒为NO |
| IGNORE_OPEN | BOOLEAN      | 实例是否忽略了OPEN启动参数，实际以NOMOUNT模式启动 <br/> 该字段仅在共享集群主备部署下有意义，仅在备集群非MASTER实例，以OPEN模式启动时为TRUE |
| DATABASE_STATUS | VARCHAR(17)  | 数据库的状态 <br/>* ACTIVE：正常运行 <br/>* INSTANCE RECOVERY：数据库在做实例恢复（共享集群下，有实例退出或加入） |
