本视图显示存算一体分布式集群中所有节点备份或恢复的进度信息汇总。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP\_ID | INTEGER | 组ID |
| GROUP\_NODE\_ID | INTEGER | 组内节点ID |
| TYPE | VARCHAR(16) | 备份恢复的类型<br>\*   NONE：当前未执行备份恢复<br>\*   BACKUP：正在备份<br>\*   RESTORE：正在恢复 |
| STAGE | VARCHAR(16) | 备份（恢复）当前阶段<br>\*   none：未执行备份恢复<br>\*   start：已开始<br>\*   base data file：基线备份集的数据文件阶段<br>\*   base bucket file：基线备份集的LSC表不可变数据文件阶段<br>\*  ctrl file：控制文件阶段<br>\*   data file：数据文件阶段<br>\*   archive file：归档文件阶段<br>\*   bucket file：LSC表的不可变数据文件阶段<br>\*   profile：总结文件阶段<br>\*   extend：文件扩展阶段<br>\*   wait open: 等待备库open<br>\*   end: 已结束 |
| STAGE\_PROGRESS | NUMBER | 当前阶段完成百分比，范围：\[0, 100\] |
| TOTAL\_PROGRESS | NUMBER | 总体进度完成百分比，范围：\[0, 100\]，为预估值，可能与实际进度有误差 |
| START\_TIME | TIMESTAMP(6) | 备份恢复开始时间，为服务器本地时间 |
| END\_TIME | TIMESTAMP(6) | 备份恢复结束时间，为服务器本地时间 |
| ELAPSED\_TIME | BIGINT | 备份恢复的时长（单位：秒） |
| INPUT\_BYTES | BIGINT | 读取的总大小（单位：字节） |
| OUTPUT\_BYTES | BIGINT | 写入的总大小（单位：字节） |
| COMPRESSION\_RATIO | NUMBER | 压缩率百分比。备份时为OUTPUT\_BYTES / INPUT\_BYTES，恢复时为INPUT\_BYTES / OUTPUT\_BYTES |
| AVG\_INPUT\_RATE | NUMBER | 平均IO读取速度（单位：MB/s）。该值表示所有备份恢复子线程的IO读取平均速度，与INPUT\_BYTES / ELAPSED\_TIME得到的值不同 |
| CUR\_INPUT\_RATE | NUMBER | 最近一次IO读取速度（单位：MB/s） |
| MAX\_INPUT\_RATE | NUMBER | 最大IO读取速度（单位：MB/s） |
| MIN\_INPUT\_RATE | NUMBER | 最小IO读取速度（单位：MB/s） |
| AVG\_OUTPUT\_RATE | NUMBER | 平均IO写入速度（单位：MB/s）。该值表示所有备份恢复子线程的IO写入平均速度，与OUTPUT\_BYTES / ELAPSED\_TIME得到的值不同 |
| CUR\_OUTPUT\_RATE | NUMBER | 最近一次IO写入速度（单位：MB/s） |
| MAX\_OUTPUT\_RATE | NUMBER | 最大IO写入速度（单位：MB/s） |
| MIN\_OUTPUT\_RATE | NUMBER | 最小IO写入速度（单位：MB/s） |
