本视图显示CM模块存储的CLUSTER INFO信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ID | VARCHAR(64)| 集群ID |
| NAME| VARCHAR(64) | 集群名 |
| VERSION | INTEGER | 集群静态信息版本号 |
| NEXT\_GROUP\_ID | INTEGER | 集群中下一个组的ID |
| CREATE\_TIME | TIMESTAMP(6) | 集群创建时间 |
| LAST\_UPDATE\_TIME | TIMESTAMP(6) | 集群信息最后更新时间 |
| MAX\_ENDPOINT | SMALLINT | 集群中endpoint最大值 |
