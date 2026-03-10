本视图显示系统共享内存池信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| NAME | VARCHAR(32) | 内存池名称 |
| SIZE | BIGINT | 内存池大小（单位：字节） |
| MEMORY_INIT_TYPE | VARCHAR(16) | 初始化内存池的方式<br>* PERCENTAGE：按照百分比初始化 <br>* NUMBER：按照固定大小初始化 |
| MEMORY_MANAGE_TYPE | VARCHAR(16) | 内存管理方式<br>* VARIABLE：内存可动态伸缩 <br>* FIXED：固定内存大小 |
| MEMORY_REQUEST_COUNT | BIGINT | 内存池动态申请内存的次数 |
| MEMORY_REQUEST_SZIE | BIGINT | 内存池动态申请内存的大小（单位：字节） |
| MEMORY_FREE_COUNT | BIGINT | 内存池动态释放内存的次数 |
| MEMORY_FREE_SIZE | BIGINT | 内存池动态释放内存的大小（单位：字节） |
