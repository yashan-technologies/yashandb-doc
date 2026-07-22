本视图显示所有数据文件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| FILE\_NAME | VARCHAR(255) | 文件名 |
| FILE\_ID | INTEGER | 文件ID |
| TABLESPACE\_NAME | VARCHAR(64) | 文件所属的表空间名 |
| BYTES | BIGINT | 文件大小（单位：字节） |
| BLOCKS | INTEGER | 文件包含block的数量 |
| STATUS | VARCHAR(8) | 文件状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线<br>\* RECOVER：待恢复 |
| MAXBYTES | BIGINT | 文件最大大小（单位：字节） |
| MAXBLOCKS | NUMBER | 文件最大包含block的数量 |
| AUTO\_EXTEND | VARCHAR(8) | 文件是否可以自动扩展<br>\*   ON：可以自动扩展<br>\*   OFF：不可以自动扩展 |
| NEXT\_SIZE | BIGINT | 文件每次自动扩展的大小（单位：字节） |
| USER\_BYTES | BIGINT | 文件中可用于存储用户数据的空间大小。文件的实际大小减去元数据所得到的空间（单位：字节） |
| USER\_BLOCKS | INTEGER | 文件中可用于存储用户数据的块数 |
| ONLINE\_STATUS | VARCHAR(8) | 文件状态<br>\*   ONLINE：在线<br>\*   OFFLINE：离线<br>\*  RECOVER：需要执行recover恢复 |
| AUTOEXTENSIBLE | VARCHAR(3) | 文件是否可以自动扩展，与AUTO\_EXTEND字段一致，用于语法兼容<br>\*   YES：可以自动扩展<br>\*   NO：不可以自动扩展 |
