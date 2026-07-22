本视图显示所有LSC表对象信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | LSC表所属用户名 |
| TABLESPACE\_NAME | VARCHAR(64) | LSC所在表空间名 |
| TABLE\_NAME | VARCHAR(64) | LSC表名 |
| SUB\_NAME | VARCHAR(64) | 如果是分区表，表示分区表名 |
| ACTIVE\_SLICES | BIGINT | 处于ACTIVE状态的slice个数 |
| FULL\_SLICES | BIGINT | 处于FULL状态的slice个数 |
| STALE\_SLICES | BIGINT | 处于STALE状态的slice个数 |
| STABLE\_SLICES | BIGINT | 处于STABLE状态的slice个数 |
| SORTED\_SLICES | BIGINT | 已排序的slice个数 |
| COMPACTED\_SLICES | BIGINT | 已合并的slice个数 |
| STABLE\_ROWGROUP\_NUM | NUMBER | 已经转换的行组个数 |
| STABLE\_ROW\_NUM | NUMBER | 已经转换成slice的行数 |
| STABLE\_FILE\_SIZE | NUMBER | 已经转换的slice总大小 |
| LEAST\_SCN             | BIGINT | 可以查询的最小SCN号    |
