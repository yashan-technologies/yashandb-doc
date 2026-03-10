本视图显示CM模块存储的CLUSTER INFO信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ID | VARCHAR(64)| 集群ID |
| NAME| VARCHAR(64) | 集群名 |
| VERSION | INTEGER | 集群静态信息版本号 |
| NEXT_GROUP_ID | INTEGER | 集群中下一个组的ID |
| CREATE_TIME | TIMESTAMP | 集群创建时间 |
| LAST_UPDATE_TIME | TIMESTAMP | 集群信息最后更新时间 |
| MAX_ENDPOINT | SMALLINT | 集群中endpoint最大值 |
