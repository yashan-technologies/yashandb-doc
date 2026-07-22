本视图显示YFS中的磁盘信息。

|  字段| 类型| 说明|
|-------|------|------|
|  ID|INTEGER| 磁盘在其所属磁盘组内的唯一编号  |
|GLOBAL_ID  |INTEGER    | 磁盘的全局ID|
|NAME|VARCHAR(32)| 磁盘的名称|
|GROUP_NUMBER|INTEGER| 磁盘的所属磁盘组ID |
|FAILGROUP_ID|INTEGER| 磁盘的所属故障组ID|
|FAILGROUP_LABEL|VARCHAR(32)|磁盘的所属故障组名称|
|MOUNT_STATUS|VARCHAR(16)| 磁盘的状态<br/> * NORMAL：磁盘状态正常<br/> * OFFLINE_SYNC：磁盘上线过程中的临时状态，此状态的磁盘可写不可读。上线完成后，磁盘变为正常状态<br/> * OFFLINE：磁盘已离线  |
|REDUNDANCY|VARCHAR(16)| 磁盘所属磁盘组的冗余度<br>\* External：YFS不提供数据冗余副本 <br>\* Normal：系统磁盘组在该配置下提供[2,3]份副本（具体份数取决于故障组数量）；数据盘组在该配置下提供[2,3]份YFS元数据副本（具体份数取决于故障组数量）+2份用户数据副本  <br>\* High：系统磁盘组在该配置下提供5份副本；数据盘组在该配置下提供[3,5]份YFS元数据副本（具体份数取决于故障组数量）+3份用户数据副本 |
|PARTNERS|VARCHAR(161)|当前磁盘的伙伴磁盘列表，磁盘组内的唯一编号（ID字段）间使用空格隔开|
|TOTAL_MB|BIGINT|磁盘的总容量（单位为MB）|
|FREE_MB|BIGINT|磁盘当前可用容量（单位为MB）|
|PATH|VARCHAR(32)|磁盘路径|
|READS|BIGINT| 当前实例读取该磁盘的总次数 |
|WRITES|BIGINT|当前实例写入该磁盘的总次数|
|BYTES_READ|BIGINT|当前实例读取该磁盘的总字节数|
|BYTES_WRITTEN|BIGINT|当前实例写入该磁盘的总字节数|
|READ_TIME|BIGINT|当前实例读取该磁盘的总时长（单位为微秒） |
|WRITE_TIME|BIGINT|当前实例写入该磁盘的总时长（单位为微妙）  |
