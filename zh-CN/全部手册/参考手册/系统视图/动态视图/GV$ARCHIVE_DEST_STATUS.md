本视图显示所有备库的统计信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| DEST_ID | TINYINT | 备库ID，与ARCHIVE_DEST_x参数相对应 |
| DEST_NAME | VARCHAR(16)  | 参数名称 |
| CONNECTION | VARCHAR(16) | 该备库是否连接，连接状态 <br>\*   CONNECTED：已连接<br>\*   DISCONNECTED：未连接 |
| PEER_ADDR | VARCHAR(256) | 对端数据库的HA链路监听地址 |
| STATUS | VARCHAR(16) | 该备库的日志同步状态  <br>\*   NORMAL：正常<br>\*   PENDING：主备日志同步未就绪<br>\*   NEED REPAIR：需要修复备库<br>\*   UNKNOWN：未连接，状态未知 |
| DATABASE_MODE | VARCHAR(16) | 该备库的运行状态，即该备库V$INSTANCE视图的STATUS字段  <br>\*   STARTED：数据库进程启动，此状态下不能操作数据库<br>\*   MOUNTED：数据库进程已经加载物理文件，此状态下能进行少量的维护操作<br>\*   OPEN：数据库正常运行状态<br>\*   UNKNOWN：未连接，状态未知 |
| RECEIVED_SEQ# | INTEGER | 该备库的接收日志的ASN号 |
| RECEIVED_LFN | BIGINT | 该备库的接收日志序列号 |
| APPLIED_SEQ# | INTEGER | 该备库的回放日志的ASN号 |
| APPLIED_LFN | BIGINT | 该备库的回放日志序列号 |
| SYNCHRONIZATION_STATUS | VARCHAR(16) | 预留字段 |
| SYNCHRONIZED | VARCHAR(8) | 该备库日志是否与主库完成同步，YES/NO |
| GAP_STATUS | VARCHAR(16) | 表示备库是否存在redo GAP <br>\*   NO GAP：没有GAP<br>\*   HAS GAP：有GAP，即redo文件不连续 |
| DISCONNECT_TIME| DATE | 该备库断连的时间戳 |
| DEPOSIT_THREAD# | TINYINT | 托管实例编号<br>NULL：表示当前实例 |
| RECEIVED_SCN | BIGINT | 该备库的接收日志SCN |
| DB_UNIQUE_NAME     | VARCHAR(31)  | 备库的名称  |
| THREAD# | TINYINT | 实例id |
| FLUSH_LFN | BIGINT | 数据库当前日志刷盘序列号 |
