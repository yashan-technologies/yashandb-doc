本视图显示所有表空间的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ID  | INTEGER | 表空间ID |
| TABLESPACE\_NAME | VARCHAR(64) | 表空间名称 |
| BLOCK\_SIZE | INTEGER | 单个block的大小（单位：字节） |
| MAX\_SIZE | NUMBER | 该表空间可达到的最大容量（单位：字节） |
| TOTAL\_BYTES | NUMBER | 该表空间的实际容量（单位：字节） |
| USER\_BYTES | NUMBER | 该表空间当前可用的空闲空间（单位：字节） |
| USER\_BLOCKS | NUMBER | 该表空间当前可用的空闲block数量 |
| STATUS | VARCHAR(16) | 表空间状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线<br>*   READ ONLY: 只读 |
| CONTENTS | VARCHAR(16) | 表空间的存储类型<br>\*   SWAP：SWAP表空间<br>\*   UNDO：UNDO表空间<br>\*   TEMPORARY：临时表空间<br>\*   PERMANENT：持久化表空间 |
| LOGGING | VARCHAR(9) | 表空间是否记录日志<br>\*   LOGGING：记录日志<br>\*   NOLOGGING：不记录日志 |
| ALLOCATION\_TYPE | VARCHAR(8) | 表空间extent分配方式<br>\*   AUTO：动态扩展extent<br>\*   UNIFORM：定长extent |
| SEGMENT\_SPACE\_MANAGEMENT | CHAR(6) | 表空间的管理方式<br>\*   BITMAP |
| ENCRYPTED | VARCHAR(1) | 表空间是否加密<br>\*   Y<br>\*   N |
| COMPRESSED | VARCHAR(1) | 表空间是否压缩<br>\*   Y：表示压缩<br>\*   N：表示不压缩 |
| SHARED | VARCHAR(16) | 该临时表空间类型<br>\*   SHARED：共享临时表空间<br>\*   LOCAL_ON_ALL：本地临时表空间 |
| BIGFILE | CHAR(2) | 字段兼容，无实际意义 |
