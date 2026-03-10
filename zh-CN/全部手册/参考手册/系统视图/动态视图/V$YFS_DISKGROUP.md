本视图显示 YFS 磁盘组（Diskgroup）信息。

|  字段| 类型| 说明|
|-------|------|------|
|ID|INTEGER|diskgroup的ID|
|NAME|VARCHAR(32)|名称|
|TYPE|VARCHAR(32)|类型：<br>\* SYSTEM：系统专用diskgroup<br>\* USER：普通用户diskgroup|                   
|AU_SIZE|INTEGER|Allocate Unit Size，最小空间单元大小。|
|BLOCK_SIZE|INTEGER|文件数据块大小|
|REDUNDANCY|VARCHAR(16)|diskgroup的冗余级别：<br>\* External：YFS不提供镜像，数据保护由外部存储系统实现<br>\* Normal：YFS提供两份镜像来保护数据<br>\* High：YFS提供三份数据来保护数据|
|STATE| VARCHAR(16)|diskgroup状态<br>\* BROKEN：diskgroup故障<br>\* MOUNTED：diskgroup状态正常<br>\* DISMOUNTED：正常状态的diskgroup被卸载后状态|
|TOTAL_MB|BIGINT|总空间|
|FREE_MB|BIGINT|可用空间|
|USABLE_FILE_MB|BIGINT|可用文件大小，Diskgroup可用空间扣除多副本|
