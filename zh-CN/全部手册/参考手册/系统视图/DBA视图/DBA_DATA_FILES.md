本视图显示所有数据文件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| FILE_NAME | VARCHAR(255) | 文件名 |
| FILE_ID | INTEGER | 文件ID |
| TABLESPACE_NAME | VARCHAR(64) | 文件所属的表空间名 |
| BYTES | BIGINT | 文件大小（单位：字节） |
| BLOCKS | INTEGER | 文件包含block的数量 |
| STATUS | VARCHAR(8) | 文件状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线<br>\* RECOVER：待恢复 |
| MAXBYTES | BIGINT | 文件最大大小（单位：字节） |
| MAXBLOCKS | NUMBER | 文件最大包含block的数量 |
| AUTO_EXTEND | VARCHAR(8) | 文件是否可以自动扩展<br>\*   ON：可以自动扩展<br>\*   OFF：不可以自动扩展 |
| NEXT_SIZE | BIGINT | 文件每次自动扩展的大小（单位：字节） |
| USER_BYTES | BIGINT | 该文件当前可用的空闲空间（单位：字节） |
| USER_BLOCKS | INTEGER | 该文件当前可用的空闲block数量 |
| ONLINE_STATUS | VARCHAR(8) | 文件状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线 |
| AUTOEXTENSIBLE | VARCHAR(3) | 文件是否可以自动扩展<br>\*   YES：可以自动扩展<br>\*   NO：不可以自动扩展 |
