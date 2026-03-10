本视图显示当前节点上显示内存扩展池中内存区域管理器的信息。

|  字段| 类型| 说明|
|----------------|---------|----------------|
|  AREA_ID       | INTEGER | 区域ID           |
| HOLD_SIZE     | BIGINT  | 当前区域所持有的内存容量（单位：字节）   |
| USING_SIZE    | BIGINT  | 当前区域已分配的内存容量（单位：字节）  |
| UNUSED_SIZE   | BIGINT  | 当前区域空闲的内存容量（单位：字节）  |
| DECAY_TIMES   | BIGINT  | 当前区域已触发内存块整理的次数     |
| FILL_TIMES    | BIGINT  | 当前区域缓存未命中的次数 |
| NUM_4K_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为4KB的页面数量    |
| NUM_8K_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为8KB的页面数量    |
| NUM_16K_PAGE | BIGINT  | 当前区域的空闲内存中，容量为16KB的页面数量    |
| NUM_32K_PAGE | BIGINT  | 当前区域的空闲内存中，容量为32KB的页面数量   |
| NUM_64K_PAGE | BIGINT  | 当前区域的空闲内存中，容量为64KB的页面数量   |
| NUM_1M_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为1MB的页面数量   |
| NUM_2M_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为2MB的页面数量    |
