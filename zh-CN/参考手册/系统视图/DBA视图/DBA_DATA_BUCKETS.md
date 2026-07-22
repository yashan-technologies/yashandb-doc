本视图显示所有LSC表空间的databucket（数据桶）文件信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| BUCKET\_ID | INTEGER | databucket ID |
| BUCKET\_NAME | VARCHAR(255) | databucket名称 |
| BUCKET\_TYPE | INTEGER | databucket类型<br>\* 0：LOCAL_FS |
| TABLESPACE\_NAME | VARCHAR(64) | databucket所属的表空间名 |
