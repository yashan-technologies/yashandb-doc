本视图显示分区表的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_CATALOG | VARCHAR(3) | 分区表所属目录名称，此值始终为def |
| TABLE\_SCHEMA | VARCHAR(64) | 分区表所在的模式名 |
| TABLE\_NAME | VARCHAR(64) | 分区表的名称 |
| PARTITION\_NAME | VARCHAR(64) | 分区的名称 |
| SUBPARTITION\_NAME | VARCHAR(64) | 子分区的名称 |
| PARTITION\_ORDINAL\_POSITION | BIGINT | 分区的顺序位置 |
| SUBPARTITION\_ORDINAL\_POSITION | BIGINT | 子分区的顺序位置 |
| PARTITION\_METHOD | VARCHAR(9) | 分区的方式（RANGE、HASH、LIST、REFERENCE） |
| SUBPARTITION\_METHOD | VARCHAR(9) | 子分区的方式（NONE、RANGE、HASH、LIST、REFERENCE） |
| PARTITION\_EXPRESSION | VARCHAR(2048) | 分区的表达式 |
| SUBPARTITION\_EXPRESSION | VARCHAR(2048) | 子分区的表达式 |
| PARTITION\_DESCRIPTION | VARCHAR(4000) | 分区的描述 |
| TABLE\_ROWS | BIGINT | 表的行数 |
| AVG\_ROW\_LENGTH | INT | 平均行长度 |
| DATA\_LENGTH | VARCHAR(1) | 数据长度<br>仅语法兼容，无实际功能含义 |
| MAX\_DATA\_LENGTH | VARCHAR(1) | 最大数据长度<br>仅语法兼容，无实际功能含义 |
| INDEX\_LENGTH | VARCHAR(1) | 索引长度<br>仅语法兼容，无实际功能含义 |
| DATA\_FREE | VARCHAR(1) | 未使用空间<br>仅语法兼容，无实际功能含义 |
| CREATE\_TIME | TIMESTAMP | 创建时间 |
| UPDATE\_TIME | DATE | 更新时间 |
| CHECK\_TIME | DATE | 检查时间 |
| CHECKSUM | VARCHAR(1) | 校验和<br>仅语法兼容，无实际功能含义 |
| PARTITION\_COMMENT | VARCHAR(1) | 分区注释<br>仅语法兼容，无实际功能含义 |
| NODEGROUP | VARCHAR(1) | 节点组<br>仅语法兼容，无实际功能含义 |
| TABLESPACE\_NAME | VARCHAR(64) | 表空间名称 |
