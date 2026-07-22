本视图显示当前节点上显示内存扩展池中内存区域管理器的信息。

|  字段| 类型| 说明|
|----------------|---------|----------------|
|  AREA\_ID       | INTEGER | 区域ID           |
| HOLD\_SIZE     | BIGINT  | 当前区域所持有的内存容量（单位：字节）   |
| USING\_SIZE    | BIGINT  | 当前区域已分配的内存容量（单位：字节）  |
| UNUSED\_SIZE   | BIGINT  | 当前区域空闲的内存容量（单位：字节）  |
| DECAY\_TIMES   | BIGINT  | 当前区域已触发内存块整理的次数     |
| FILL\_TIMES    | BIGINT  | 当前区域缓存未命中的次数 |
| NUM\_4K\_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为4KB的页面数量    |
| NUM\_8K\_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为8KB的页面数量    |
| NUM\_16K\_PAGE | BIGINT  | 当前区域的空闲内存中，容量为16KB的页面数量    |
| NUM\_32K\_PAGE | BIGINT  | 当前区域的空闲内存中，容量为32KB的页面数量   |
| NUM\_64K\_PAGE | BIGINT  | 当前区域的空闲内存中，容量为64KB的页面数量   |
| NUM\_1M\_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为1MB的页面数量   |
| NUM\_2M\_PAGE  | BIGINT  | 当前区域的空闲内存中，容量为2MB的页面数量    |
