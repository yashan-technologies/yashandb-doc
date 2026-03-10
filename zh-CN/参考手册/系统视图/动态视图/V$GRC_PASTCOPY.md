本视图显示PAST COPY BLOCK（多实例同时持有脏块时，只有最新版本的一个实例具备写权限，其他不具备写权限的历史版本实例所持有的脏块称为PAST COPY BLOCK）信息。 

|  字段| 类型| 描述|
| --- | --- | --- |
| TS# | INTEGER | 页面space id |
| FILE# | INTEGER | 页面file id |
| BLK# | INTEGER | 页面ID |
| RESOURCE_NAME | VARCHAR(128) | 资源名称，block资源\[space\]\[file\]\[id\] |
| INSTANCE_ID | TINYINT | 持有该PAST COPY BLOCK的节点 |
| LSN | BIGINT | PAST COPY BLOCK的LSN（Log Sequence Number） |
