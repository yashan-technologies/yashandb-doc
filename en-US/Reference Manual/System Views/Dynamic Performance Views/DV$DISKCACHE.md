This view shows the status information of disk buffers on each node in a distributed cluster.

|Field |Type |Description |
|:----------------------|:----------|:---------------------------|
| GROUP_ID              | INTEGER   | Group ID                     |
| GROUP_NODE_ID         | INTEGER   | Node ID within the group     |
| REGION_ID             | INTEGER   | Region ID of the disk buffer  |
| REGION_GROUP_ID       | INTEGER   | Group ID where the disk buffer region is located |
| REGION_HIT            | INTEGER   | Total hit count of the disk buffer domain |
| ITEM_NUM              | INTEGER   | Number of buffer objects in the disk buffer domain |
| REGION_LOAD_TIMESTAMP | TIMESTAMP | SCN where the disk buffer domain starts loading |