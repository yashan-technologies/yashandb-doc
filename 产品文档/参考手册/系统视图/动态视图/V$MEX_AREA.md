本视图显示当前节点MEX内存池AREA部分的信息。

| 字段             | 类型      | 说明             |
|----------------|---------|----------------|
| AREA_ID       | INTEGER | 区域ID           |
| HOLD_SIZE     | BIGINT  | 持有的内存（单位：字节）   |
| USING_SIZE    | BIGINT  | 使用中的内存（单位：字节）  |
| UNUSED_SIZE   | BIGINT  | 未使用的内存（单位：字节）  |
| DECAY_TIMES   | BIGINT  | 触发内存块整理的次数     |
| FILL_TIMES    | BIGINT  | 填充的次数（缓存未命中次数） |
| NUM_4K_PAGE  | BIGINT  | 缓存中4KB页面的数量    |
| NUM_8K_PAGE  | BIGINT  | 缓存中8KB页面的数量    |
| NUM_16K_PAGE | BIGINT  | 缓存中16KB页面的数量   |
| NUM_32K_PAGE | BIGINT  | 缓存中32KB页面的数量   |
| NUM_64K_PAGE | BIGINT  | 缓存中64KB页面的数量   |
| NUM_1M_PAGE  | BIGINT  | 缓存中1MB页面的数量    |
| NUM_2M_PAGE  | BIGINT  | 缓存中2MB页面的数量    |
