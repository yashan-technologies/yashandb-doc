本视图描述数据库中的所有临时文件信息。

|  字段| 类型| 说明|
| --- |--------------| --- |
| FILE_ID | INTEGER      | 临时文件ID |
| FILE_NAME | VARCHAR(255) | 临时文件名 |
| STATUS | VARCHAR(8)   | 文件状态<br>\*   OFFLINE：离线<br>\*   ONLINE：在线 |
| BYTES | BIGINT       | 临时文件大小（单位：字节） |
| BLOCKS | INTEGER      | 临时文件包含block的个数 |
| RELATIVE_FNO | INTEGER      | 在表空间内部的编号 |
| AUTOEXTENSIBLE | VARCHAR(8)   | 临时文件是否可以自动扩展<br>\*   ON：可以自动扩展<br>\*   OFF：不可以自动扩展 |
| TABLESPACE_NAME | VARCHAR(64)  | 临时文件所属表空间名 |
| MAXBYTES | BIGINT       | 文件最大大小（单位：字节） |
| MAXBLOCKS | NUMBER       | 文件最大包含block的个数 |
| INCREMENT_BY | NUMBER       | 自动扩展的默认增量（单位是1个block） |
| USER_BYTES | BIGINT       | 临时文件当前空闲可用数据大小（单位：字节） |
| USER_BLOCKS | INTEGER      | 临时文件当前空闲可用block个数 |
| SHARED | VARCHAR(16)  | 临时文件对应的临时表空间类型<br>\*   SHARED：共享临时表空间<br>\*   LOCAL_ON_ALL：本地临时表空间 |
| INST_ID | VARCHAR(44)  | 本地临时表空间中的临时文件对应的实例ID |
