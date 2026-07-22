本视图显示共享内存区域的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
|  NAME | VARCHAR(32) | 内存池名称：<br />* data buffer：数据缓存<br />* temporary buffer：临时缓存<br />* large pool：大对象池<br />* redo buffer：日志缓存<br />* hot cache：热页缓存<br />* share pool：内存共享池<br />* dbwr buffer：数据库写盘缓存<br />* job pool：定时任务池<br />* audit queue buffer：审计队列缓存<br />* global application pool：全局应用池（自YashanDB v23.4.1起过时，仅用于兼容） |
| SIZE | BIGINT | 内存池的容量（单位：字节）  |
